---
title: Масштабирование ресурсов
description: В этой статье содержатся сведения о масштабировании базы данных путем добавления или удаления выделенных ресурсов.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: c4366b2718271b1e27325e6946c5016e9230cea4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76835918"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Динамическое масштабирование ресурсов базы данных с минимальным временем простоя

База данных SQL Azure позволяет динамически добавлять ресурсы в базу данных с минимальным [временем простоя](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/); Тем не менее в течение короткого промежутка времени, которое можно уменьшить с помощью логики повторных попыток, существует переключение между периодами, в которых подключение к базе данных теряется.

## <a name="overview"></a>Обзор

Когда спрос на ваше приложение увеличится с нескольких устройств и клиентов до миллионов, базу данных SQL Azure можно будет моментально масштабировать с минимальным временем простоя. Масштабируемость является одной из важнейших характеристик PaaS, которая позволяет при необходимости динамически добавлять дополнительные ресурсы в службу. База данных SQL Azure позволяет легко изменять выделенные для баз данных ресурсы (загрузку ЦП, объем памяти, пропускную способность операций ввода-вывода и объем хранения).

При этом вы можете избежать проблем с производительностью, которые возникают при более интенсивном использовании приложения и которые нельзя устранить с помощью индексации или методов переопределения запросов. Благодаря возможности добавления ресурсов вы можете оперативно реагировать на ситуации, когда база данных достигает текущих ограничений ресурсов и ей требуются дополнительные ресурсы для обработки входящих рабочих нагрузок. База данных SQL Azure также позволяет уменьшать объем ресурсов, когда они не нужны, и, таким образом, снижать расходы.

Вам не нужно беспокоиться о приобретении оборудования и изменении базовой инфраструктуры. С помощью ползунка на портале Azure вы сможете легко выполнить масштабирование базы данных.

![Масштабирование производительности базы данных](media/sql-database-scalability/scale-performance.svg)

База данных SQL Azure предоставляет [модель приобретения на основе единиц DTU](sql-database-service-tiers-dtu.md) и [модель приобретения на основе виртуальных ядер](sql-database-service-tiers-vcore.md).

- В [модели приобретения на основе единиц DTU](sql-database-service-tiers-dtu.md) набор вычислительных операций, памяти и ресурсов ввода-вывода предоставляется на трех уровнях обслуживания: "Базовый", "Стандартный" и "Премиум". Каждый уровень предусматривает поддержку различных рабочих нагрузок баз данных. Для каждого уровня производительности на уровнях обслуживания предусмотрено отдельное сочетание этих ресурсов, к которым можно добавить ресурсы хранилища.
- [Модель приобретения на основе виртуальных ядер](sql-database-service-tiers-vcore.md) позволяет выбрать количество виртуальных ядер, объем памяти и размер и скорость хранилища. Эта модель приобретения предлагает три уровня служб: общего назначения, критически важный для бизнеса и масштабирование.

Вы можете создать первое приложение на основе небольшой отдельной базы данных на уровне служб "Базовый", "Стандартный" или "Общего назначения" по оптимальной ежемесячной цене, а затем в любое время изменить уровень служб на "Премиум" или "Критически важный для бизнеса" вручную или программным способом в соответствии с требованиями вашего решения. Вы можете настроить производительность без простоя для приложения и работы клиентов. Динамическая масштабируемость позволяет базе данных прозрачно реагировать на быстро меняющиеся требования к ресурсам. Кроме того, таким образом вы можете платить только за необходимые ресурсы, и только когда они вам нужны.

> [!NOTE]
> Динамическое масштабирование отличается от автомасштабирования. Автомасштабирование происходит, когда служба автоматически масштабируется на основе критериев, а динамическая масштабируемость позволяет вручную масштабироваться с минимальным временем простоя.

Единая база данных Azure SQL поддерживает только динамическую масштабируемость, а не автомасштабирование. Чтобы изучить *автомасштабирование* более детально, необходимо рассмотреть возможность использования эластичных пулов, которые позволяют базам данных обмениваться ресурсами в пуле, исходя из потребностей конкретной базы данных.
Однако существуют сценарии, которые могут помочь автоматизировать масштабируемость для отдельной базы данных SQL Azure. С ними можно ознакомится в статье [Мониторинг и масштабирование отдельной базы данных SQL с помощью PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

Можно изменять [уровни служб DTU](sql-database-service-tiers-dtu.md) или [характеристики виртуального ядра](sql-database-vcore-resource-limits-single-databases.md) в любое время с минимальным простоем приложения (обычно в среднем не более четырех секунд). Для многих организаций и приложений достаточно иметь возможность создавать и уменьшать или увеличивать производительность баз данных по запросу, особенно если закономерности использования базы данных относительно хорошо прогнозируются. Но если закономерности использования непредсказуемы, это может усложнить управление расходами и бизнес-моделью. В этом сценарии вы используете эластичный пул с определенным числом единиц eDTU, распределенных между несколькими базами данных в пуле.

![Общие сведения о базе данных SQL: единицы DTU отдельных баз данных по категориям и уровням](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Все три вида базы данных SQL Azure предлагают определенные возможности динамического масштабирования баз данных.

- Для [отдельной базы данных](sql-database-single-database-scale.md) можно воспользоваться моделями на основе [DTU](sql-database-dtu-resource-limits-single-databases.md) или [виртуальных ядер](sql-database-vcore-resource-limits-single-databases.md), чтобы определить максимальное количество ресурсов, которые будут назначены каждой базе данных.
- [Управляемый экземпляр](sql-database-managed-instance.md) использует режим [виртуальных ядер](sql-database-managed-instance.md#vcore-based-purchasing-model) и позволяет определять максимальное число ядер ЦП и максимальный объем хранилища, выделяемый экземпляру. Ресурсы, выделенные экземпляру, будут общими для всех баз данных в этом экземпляре.
- [Эластичные пулы](sql-database-elastic-pool-scale.md) позволяют определять максимальный предел использования ресурсов для каждой группы баз данных в пуле.

Запуск действия увеличения или уменьшения масштаба в любой из разновидностей приведет к перезапуску процесса ядра СУБД и перемещению его на другую виртуальную машину, если это необходимо. Процесс перемещения процесса ядра СУБД на новую виртуальную машину — это **оперативный процесс** , в котором можно продолжать использовать имеющуюся службу базы данных SQL Azure во время выполнения процесса. После того как целевое ядро СУБД полностью инициализировано и готово к обработке запросов, подключения будут [переключаться с источника на целевое ядро СУБД](sql-database-single-database-scale.md#impact). 


> [!NOTE]
> При завершении процесса увеличения или уменьшения масштаба можно избежать короткого разрыва соединения. Если вы реализовали [логику повторных попыток для стандартных временных ошибок](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), вы не увидите отработку отказа.

## <a name="alternative-scale-methods"></a>Альтернативные методы масштабирования

Масштабирование ресурсов — самый простой и эффективный способ повысить производительность базы данных, не изменяя код базы данных или приложения. В некоторых случаях даже самые высокие уровни служб, размеры вычислений и оптимизации производительности могут не обеспечивать успешное и экономичное выполнение рабочей нагрузки. В этом случае вы можете масштабировать базу данных с помощью следующих дополнительных параметров:

- Функция [горизонтального масштабирования для чтения](sql-database-read-scale-out.md) доступна, когда вы получаете одну реплику данных только для чтения, где можно выполнять ресурсоемкие запросы только для чтения, например отчеты. Реплика только для чтения будет обрабатывать рабочую нагрузку только для чтения, не влияя на использование ресурсов базы данных-источника.
- [Сегментирование базы данных](sql-database-elastic-scale-introduction.md) — это набор методов, который позволяет разделять данные по нескольким базам данных и масштабировать их независимо друг от друга.

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о повышении производительности базы данных путем изменения кода базы данных см. в статье [Поиск и применение рекомендаций по производительности](sql-database-advisor-portal.md).
- Сведения об оптимизации базы данных с помощью встроенной системы аналитики см. в статье [Автоматическая настройка базы данных SQL Azure](sql-database-automatic-tuning.md).
- Сведения о применении функции масштабирования для чтения в службе "База данных SQL Azure" см. в статье [Использование реплик только для чтения для распределения рабочих нагрузок запросов только для чтения](sql-database-read-scale-out.md).
- Сведения о сегментировании базы данных см. в статье [Развертывание с помощью Базы данных SQL Azure](sql-database-elastic-scale-introduction.md).
