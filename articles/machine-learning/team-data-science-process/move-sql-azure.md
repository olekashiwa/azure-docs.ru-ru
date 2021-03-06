---
title: Перемещение данных в базу данных SQL Azure — командный процесс обработки и анализа данных
description: Перемещение данных из неструктурированных файлов (форматы CSV или TSV) или из данных, хранящихся в локальной SQL Server, в базу данных SQL Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722464"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Перемещение данных в базу данных SQL Azure для машинного обучения Azure

В этой статье описываются варианты перемещения данных из неструктурированных файлов (форматы CSV или TSV) или из данных, хранящихся в локальной SQL Server в базе данных SQL Azure. Эти задачи перемещения данных в облако являются этапом процесса обработки и анализа данных группы.

Описание вариантов перемещения данных на локальный сервер SQL Server для машинного обучения см. в статье [Перемещение данных в SQL Server на виртуальной машине Azure](move-sql-server-virtual-machine.md).

В следующей таблице перечислены варианты перемещения данных в базу данных SQL Azure.

| <b>ИСТОЧНИК</b> | <b>Назначение: база данных SQL Azure</b> |
| --- | --- |
| <b>Неструктурированный файл (в формате CSV или TSV)</b> |[SQL-запрос на массовую вставку](#bulk-insert-sql-query) |
| <b>Локальный сервер SQL Server</b> |1. [Экспорт в неструктурированный файл](#export-flat-file).<br> 2. [Мастер миграции баз данных SQL](#insert-tables-bcp)<br> 3. [резервное копирование и восстановление базы данных](#db-migration)<br> 4. [фабрика данных Azure](#adf) |

## <a name="prereqs"></a>Предварительные требования
Для выполнения описанных процедур вам необходимы следующие компоненты:

* **Подписка Azure**. Если у вас нет подписки, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).
* **Azure storage account**. Учетная запись хранения Azure используется в этом учебнике для хранения данных. Если у вас ее нет, см. раздел [Создание учетной записи хранения](../../storage/common/storage-account-create.md). После создания учетной записи хранения необходимо получить ключ, используемый для доступа к хранилищу. См. [раздел Управление ключами доступа учетной записи хранения](../../storage/common/storage-account-keys-manage.md).
* Доступ к **базе данных SQL Azure**. Если требуется настроить базу данных SQL Azure, то обратитесь к статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](../../sql-database/sql-database-get-started.md) , которая содержит сведения о том, как подготовить новый экземпляр базы данных SQL Azure.
* Установленная и настроенная локальная среда **Azure PowerShell**. Инструкции см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/overview).

**Данные**: процессы миграции демонстрируются с помощью [набора данных о такси Нью-Йорка](https://chriswhong.com/open-data/foil_nyc_taxi/). Набор данных о такси Нью-Йорка содержит сведения о поездках и тарифах и доступен в хранилище BLOB-объектов Azure [здесь](https://www.andresmh.com/nyctaxitrips/). Пример и описание этих файлов приведены в [описании набора данных «Поездки такси Нью-Йорка»](sql-walkthrough.md#dataset).

Вы можете либо адаптировать описанные здесь процедуры к собственному набору данных, либо выполнить описанные действия с набором данных о такси Нью-Йорка. Для передачи набора данных о поездках в такси Нью-Йорка в локальную базу данных SQL Server выполните процедуру, описанную в разделе [Выполните массовый импорт данных в базу данных SQL Server](sql-walkthrough.md#dbload). Эти инструкции предназначены для SQL Server на виртуальной машине Azure. Процедура передачи для локального SQL Server идентична.

## <a name="file-to-azure-sql-database"></a>Перемещение данных из источника «Неструктурированный файл» в базу данных SQL Azure
Данные в неструктурированных файлах (CSV или TSV) можно переместить в базу данных SQL Azure с помощью SQL-запроса на выполнение инструкции INSERT.

### <a name="bulk-insert-sql-query"></a>SQL-запрос на массовую вставку
Действия для процедуры, использующей SQL-запрос на ввод неструктурированного файла, аналогичны указаниям по перемещению данных из источника «Плоский файл» в SQL Server на виртуальной машине Azure. Дополнительные сведения см. в разделе [SQL-запрос на массовую вставку](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a>Перемещение данных из локальной SQL Server в базу данных SQL Azure
Если исходные данные хранятся в локальном SQL Server, существуют различные возможности перемещения данных в базу данных SQL Azure.

1. [Экспорт в неструктурированный файл](#export-flat-file)
2. [Мастер миграции баз данных SQL](#insert-tables-bcp)
3. [Архивация и восстановление базы данных](#db-migration)
4. [Фабрика данных Azure](#adf).

Шаги, описанные в первых трех разделах, похожи на разделы в разделе [Перемещение данных в SQL Server на виртуальной машине Azure](move-sql-server-virtual-machine.md) , охватывающие эти же процедуры. В следующих инструкциях приводятся ссылки на соответствующие разделы этой статьи.

### <a name="export-flat-file"></a>Экспорт в неструктурированный файл
Действия по экспорту в неструктурированный файл похожи на инструкции, описанные в разделе [Экспорт в неструктурированный файл](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Мастер миграции баз данных SQL
Инструкции по использованию мастера миграции баз данных SQL похожи на инструкции, описанные в [мастере миграции баз данных SQL](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Архивация и восстановление базы данных
Шаги по использованию резервного копирования и восстановления базы данных аналогичны инструкциям, перечисленным в разделе [резервное копирование и восстановление базы данных](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Фабрика данных Azure
Сведения о перемещении данных в базу данных SQL Azure с помощью фабрики данных Azure (ADF) в этой статье [Перенос данных из локального сервера SQL Server в SQL Azure с помощью фабрики данных Azure](move-sql-azure-adf.md). В этом разделе показано, как использовать ADF для перемещения данных из локальной базы данных SQL Server в базу данных SQL Azure через хранилище BLOB-объектов Azure.

Если данные необходимо постоянно перенести с помощью гибридных локальных и облачных источников, рассмотрите возможность использования ADF.  ADF также помогает в том случае, когда данные должны преобразовываться, или требуется новая бизнес-логика во время миграции. ADF позволяет выполнять планирование и отслеживание заданий с помощью простых сценариев JSON, управляющих перемещением данных на периодической основе. ADF также обладает другими возможностями, такими как поддержка сложных операций.
