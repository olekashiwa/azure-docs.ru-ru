---
title: Планирование развертывания службы файлов Azure | Документы Майкрософт
description: Узнайте, что необходимо учесть при планировании развертывания службы "Файлы Azure".
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9b71c4a5c0f245d9da97dc8f096d15c5386bf919
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368616"
---
# <a name="planning-for-an-azure-files-deployment"></a>Планирование развертывания службы файлов Azure

[Служба файлов Azure](storage-files-introduction.md) предлагает полностью управляемые общие файловые ресурсы в облаке, доступ к которым можно получить с помощью стандартного отраслевого протокола SMB. Так как служба файлов Azure является полностью управляемой, ее развертывание в рабочих сценариях гораздо проще развертывания файлового сервера или устройства NAS и управления им. В этой статье рассматриваются аспекты, которые следует учитывать при развертывании файлового ресурса Azure для использования в рабочей среде организации.

## <a name="management-concepts"></a>Основные принципы управления

 На схеме ниже показаны компоненты управления службой файлов Azure.

![Структура файла](./media/storage-files-introduction/files-concepts.png)

* **Учетная запись хранения**. Весь доступ к хранилищу Azure осуществляется с помощью учетной записи хранения. Дополнительные сведения о емкости учетной записи хранения см. в разделе [целевые показатели масштабируемости и производительности для учетных записей](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) хранения класса Standard.

* **Общая папка.** Общая папка хранилища файлов представляет собой общую папку с файлами в Azure, использующую протокол SMB. Все каталоги и файлы должны быть созданы в родительской общей папке. Учетная запись может содержать неограниченное количество общих папок, а общий ресурс может хранить неограниченное количество файлов, вплоть до общей емкости общей папки. Общая емкость для файловых ресурсов уровня "Премиум" и "Стандартный" составляет 100 тиб.

* **Каталог.** Необязательная иерархия каталогов.

* **Файл.** Файл в совместно используемом ресурсе. Файлы могут иметь размер до 1 ТБ.

* **Формат URL-адреса.** В запросах к файловому ресурсу Azure, выполняемых с помощью протокола REST, к файлам можно обращаться, используя URL-адрес в следующем формате:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Метод доступа к данным

Служба файлов Azure предлагает два встроенных удобных метода доступа к данным, которые можно использовать по отдельности или в сочетании друг с другом.

1. **Прямой доступ к облаку**. Файловый ресурс Azure можно подключить в ОС [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) или [Linux](storage-how-to-use-files-linux.md) через SMB или REST API службы файлов. При использовании SMB операции чтения и записи выполняются непосредственно в файловом ресурсе в Azure. Для подключения к виртуальной машине в Azure клиент SMB в операционной системе должен поддерживать по меньшей мере протокол SMB 2.1. Для локального подключения, например к рабочей станции пользователя, клиент SMB на рабочей станции должен поддерживать по меньшей мере протокол SMB 3.0 (с шифрованием). Кроме SMB, новые приложения или службы могут напрямую обращаться к файловому ресурсу посредством File REST, который является простым и масштабируемым API-интерфейсом для разработки программного обеспечения.
2. **Служба синхронизации Azure файлов**. С помощью службы синхронизации файлов Azure можно реплицировать файловые ресурсы на локальные серверы Windows Server или в Azure. Ваши пользователи могут получить доступ к файловому ресурсу через Windows Server, например через общий ресурс SMB или NFS. Это полезно в сценариях, когда доступ к данным и их изменения выполняются далеко за пределами центров обработки данных Azure, например как в сценарии с филиалом. Данные можно реплицировать между несколькими конечными точками Windows Server, например между несколькими филиалами. Наконец, данные могут быть размещены на нескольких уровнях в службе файлов Azure так, что они по-прежнему будут доступны через сервер, однако на сервере отсутствует полная копия данных. Данные можно легко извлечь при открытии пользователем.

В следующей таблице показано, как пользователи и приложения могут обращаться к файловому ресурсу Azure.

| | Прямой доступ к облаку | Служба синхронизации файлов Azure |
|------------------------|------------|-----------------|
| Какие протоколы необходимо использовать? | Служба файлов Azure поддерживает SMB 2.1, SMB 3.0 и File REST API. | Доступ к файловому ресурсу Azure с помощью любого поддерживаемого протокола в Windows Server (SMB, NFS, FTPS и т. д.) |  
| Где выполняется рабочая нагрузка? | **В Azure**. Служба файлов Azure обеспечивает прямой доступ к данным. | **В локальной среде с медленным сетевым подключением**. Клиенты Windows, Linux и macOS могут подключить локальный файловый ресурс Windows в качестве быстрого кэша файлового ресурса Azure. |
| Какой уровень списков ACL требуется? | Уровень общего доступа и уровень файла. | Уровень общего доступа, уровень файла и уровень пользователя. |

## <a name="data-security"></a>Защита данных

Служба файлов Azure предлагает несколько встроенных возможностей для обеспечения безопасности данных.

* Поддержка шифрования для протоколов сетевой передачи: шифрование SMB 3.0 и File REST по HTTPS. По умолчанию: 
    * Клиенты, поддерживающие шифрование SMB 3,0, отправляют и получают данные по зашифрованному каналу.
    * Клиенты, не поддерживающие SMB 3,0 с шифрованием, могут взаимодействовать с внутренней системой обработки данных через SMB 2,1 или SMB 3,0 без шифрования. Клиенты SMB не могут взаимодействовать между ЦОД по протоколам SMB 2.1 или SMB 3.0 без шифрования.
    * Клиенты могут взаимодействовать через File REST по протоколу HTTP или HTTPS.
* Шифрование неактивных данных ([шифрование Службы хранилища Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)). Шифрование службы хранилища (SSE) включено во всех учетных записях хранения. Неактивные данные шифруются с помощью полностью управляемых ключей. Шифрование неактивных данных не увеличивает затраты на хранение и не снижает производительность. 
* Необязательное требование зашифрованных данных при передаче. Если выбран этот параметр, служба файлов Azure отклоняет доступ к данным через незашифрованные каналы. В частности, будут разрешены только подключения по протоколам HTTPS и SMB 3.0 с шифрованием.

    > [!Important]  
    > Необходимость безопасной передачи данных приведет к сбою старых клиентов SMB, не поддерживающих взаимодействие с SMB 3.0 с шифрованием. Дополнительные сведения см. в статьях [Использование общей папки Azure в Windows](storage-how-to-use-files-windows.md), [Использование файлов Azure в Linux](storage-how-to-use-files-linux.md), [Подключение общей папки Azure через протокол SMB в macOS](storage-how-to-use-files-mac.md).

Для обеспечения максимальной безопасности настоятельно рекомендуется всегда включать шифрование данных при хранении и шифрование данных во время передачи при каждом использовании современных клиентов для доступа к данным. Например, если необходимо подключить общую папку в виртуальной машине с Windows Server 2008 R2, которая поддерживает только SMB 2.1, необходимо разрешить передачу незашифрованного трафика в хранилище, так как SMB 2.1 не поддерживает шифрование.

Если для доступа к файловому ресурсу Azure вы используете службу "Синхронизация файлов Azure", для синхронизации данных с серверами Windows Server всегда будут использоваться протоколы HTTPS и SMB 3.0 с шифрованием (независимо от того, нужно ли вам шифровать неактивные данные).

## <a name="file-share-performance-tiers"></a>Уровни производительности файлового ресурса

Служба файлов Azure предлагает два уровня производительности: "Стандартный" и "Премиум".

### <a name="standard-file-shares"></a>Общие файлы уровня "Стандарт"

Стандартные общие файловые ресурсы поддерживаются жесткими дисками (HDD). Стандартные общие файловые ресурсы обеспечивают надежную производительность для рабочих нагрузок ввода-вывода, которые менее чувствительны к вариативности производительности, например общие файловые ресурсы общего назначения и среды разработки и тестирования. Файловые ресурсы уровня "Стандартный" доступны только в модели выставления счетов с оплатой по мере использования.

> [!IMPORTANT]
> Если вы хотите использовать общие файловые ресурсы размером более 5 Тиб, ознакомьтесь с разделом подключение [к более крупным файловым ресурсам (уровень Standard)](#onboard-to-larger-file-shares-standard-tier) , чтобы выполнить действия по подключению, а также региональным требованиям и ограничениям.

### <a name="premium-file-shares"></a>Общие файловые ресурсы уровня "Премиум"

Файловые ресурсы уровня "Премиум" поддерживаются твердотельными накопителями (SSD). Файловые ресурсы уровня "Премиум" обеспечивают постоянную высокую производительность и низкую задержку для большинства операций ввода-вывода в миллисекундах для рабочих нагрузок с интенсивным ВВОДом-выводом. Это делает их пригодными для самых разнообразных рабочих нагрузок, таких как базы данных, размещение веб-сайтов и среды разработки. Файловые ресурсы уровня "Премиум" доступны только в подготовленной модели выставления счетов. Файловые ресурсы уровня "Премиум" используют модель развертывания отдельно от стандартных файловых ресурсов.

Azure Backup доступен для файловых ресурсов уровня "Премиум", а служба Azure Kubernetes поддерживает общие файловые ресурсы уровня "Премиум" в версии 1,13 и более поздних.

Если вы хотите узнать, как создать файловый ресурс уровня "Премиум", см. статью о [создании учетной записи хранения файлов Azure Premium](storage-how-to-create-premium-fileshare.md).

В настоящее время невозможно напрямую выполнить преобразование между стандартным общим файловым ресурсом и общим файловым ресурсом уровня "Премиум". Если вы хотите переключиться на любой из уровней, необходимо создать новый файловый ресурс на этом уровне и вручную скопировать данные из исходной папки в созданную вами общую папку. Это можно сделать с помощью любого из поддерживаемых в службе файлов Azure средств копирования, таких как Robocopy или AzCopy.

> [!IMPORTANT]
> Общие файловые ресурсы уровня "Премиум" доступны в LRS в большинстве регионов, которые предлагают учетные записи хранения и ZRS в меньшем подмножестве регионов. Чтобы узнать, доступны ли в вашем регионе общедоступные файловые ресурсы уровня "Премиум", см. страницу [доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=storage) для Azure. Сведения о регионах, поддерживающих ZRS, см. в статье [избыточность службы хранилища Azure](../common/storage-redundancy.md).
>
> Чтобы помочь нам определить приоритеты для новых регионов и функций уровня "Премиум", заполните этот [опрос](https://aka.ms/pfsfeedback).

#### <a name="provisioned-shares"></a>Подготовленные ресурсы

Файловые ресурсы уровня "Премиум" предоставляются на основе зафиксированной пропорции ГиБ/операции ввода-вывода в секунду/пропускная способность. Для каждого подготовленного ГиБ ресурс будет выдаваться на одну пропускную способность операций ввода-вывода в секунду и 0,1 МиБ/с пропускной способности в условиях максимальной нагрузки на ресурс. Минимально допустимый уровень подготовки составляет 100 ГиБ с минимальным числом операций ввода-вывода в секунду и пропускной способностью.

Наилучшим возможным образом все ресурсы могут ограничиваться до трех операций ввода-вывода в секунду на ГиБ подготовленного хранилища в течение 60 минут или дольше в зависимости от размера ресурса. Новые ресурсы начинаются с полного пакетного кредита на основе подготовленной мощности.

Общие ресурсы должны быть подготовлены с шагом в 1 гиб. Минимальный размер — 100 гиб, следующий размер — 101 гиб и т. д.

> [!TIP]
> Базовые операции ввода-вывода в секунду = 1 * подготовленные гиб. (Максимум 100 000 операций ввода-вывода в секунду).
>
> Ограничение количества пакетов = 3 * базовые операции ввода-вывода. (Максимум 100 000 операций ввода-вывода в секунду).
>
> скорость исходящего трафика = 60 MiB/s + 0,06 * подготовлена гиб
>
> скорость входящих данных = 40 MiB/s + 0,04 * подготовленная гиб

Подготовленный размер общего ресурса указывается квотой общего ресурса. Квоту общего доступа можно увеличить в любое время, но ее можно уменьшить только через 24 часа с момента последнего увеличения. По истечении 24 часов без увеличения квоты общий ресурс можно уменьшить столько раз, сколько вы хотите, пока вы не увеличите его снова. Изменения масштаба операций ввода-вывода и пропускной способности вступят в силу в течение нескольких минут после изменения размера.

Можно уменьшить размер подготовленного общего ресурса под используемой гиб. В этом случае данные не будут потеряны, но по-прежнему будет взиматься плата за используемый размер, а также производительность (базовые операции ввода-вывода, пропускная способность и число операций в секунду) подготовленной общей папки, а не используемый размер.

В следующей таблице приведено несколько примеров этих формул для подготовленных размеров общих ресурсов.

|Емкость (гиб) | Базовые показатели операций ввода-вывода в секунду | Ускоренные операции ввода-вывода | Исходящий трафик (MiB/s) | Входящий трафик (MiB/с) |
|---------|---------|---------|---------|---------|
|100         | 100     | 300     | 66   | 44   |
|500         | 500     | До 1 500   | 90   | 60   |
|1024       | 1024   | До 3 072   | 122   | 81   |
|5,120       | 5,120   | До 15 360  | 368   | 245   |
|10 240      | 10 240  | До 30 720  | 675 | 450   |
|33 792      | 33 792  | До 100 000 | 2 088 | 1 392   |
|51 200      | 51 200  | До 100 000 | 3132 | 2 088   |
|102 400     | 100 000 | До 100 000 | 6 204 | 4 136   |

> [!NOTE]
> Производительность общих файловых ресурсов зависит от ограничений сети компьютера, доступной пропускной способности сети, размера операций ввода-вывода, параллелизма и многих других факторов. Например, основываясь на внутреннем тестировании с 8 КИБ операций чтения и записи, одна виртуальная машина Windows, *стандартная F16s_v2*, подключенная к общему файловому ресурсу уровня "Премиум" через SMB, может достичь 20 000 операций ввода-вывода и 15 000 операций записи в секунду Если размер ввода-вывода для чтения и записи 512 MiB, то одна и та же виртуальная машина может 1,1 достичь исходящего трафика гиб/s и 370 MiB/s с пропускной способностью. Для достижения максимального масштаба производительности Распределите нагрузку между несколькими виртуальными машинами. Ознакомьтесь с [руководством по устранению неполадок](storage-troubleshooting-files-performance.md) для некоторых распространенных проблем с производительностью и способов их обхода.

#### <a name="bursting"></a>Bursting

Общие файловые ресурсы уровня "Премиум" могут выполнять свои операции ввода-вывода в течение трех факторов. Пакетная работа автоматизирована и работает на основе кредитной системы. Пакетная работа работает с максимальной производительностью, а ограничение на количество пакетов не гарантирует, что файловые ресурсы могут *занимать* ограничение.

Кредиты накапливаются в сегменте нагрузки, когда трафик для общей папки находится ниже базовых показателей операций ввода-вывода. Например, в общей папке 100 гиб есть 100 базовых операций ввода-вывода. Если фактический трафик в общей папке был 40 операций ввода-вывода в секунду в течение определенного интервала, то неиспользуемые операции ввода-вывода 60 кредитуются в сегмент нагрузки. Эти кредиты будут использоваться позже, когда операции будут превысить базовые числа операций ввода-вывода.

> [!TIP]
> Размер блока пакета = базовые операции ввода-вывода * 2 * 3600.

Каждый раз, когда общий ресурс превышает базовые операции ввода-вывода и имеет кредиты в сегменте нагрузки, он будет заполняться. Общие ресурсы могут продолжать занимать до тех пор, пока не останется кредитов, хотя общие ресурсы размером менее 50 Тиб будут находиться в пределах до одного часа. Размер общих ресурсов, превышающих 50 Тиб, может превышать это ограничение в час, не более двух часов, в зависимости от количества начисленных кредитов. Каждый ввод-вывод, превышающий базовые операции ввода-вывода, потребляет один кредит, а после того, как все кредиты потребляются, Общая папка вернется к базовым операциям

Общие кредиты имеют три состояния:

- Начисление, если общая папка использует меньше базовых операций ввода-вывода.
- Отклонение, если общая папка является пакетной.
- Оставшаяся константа, если не используются кредиты или базовые операции ввода-вывода.

Новые общие файловые ресурсы начинаются с полного количества кредитов в его сегменте нагрузки. Разбивка на пакеты не будет начисляться, если в течение операций ввода-вывода в секунду с помощью сервера выполняется регулирование общей нагрузки.

## <a name="file-share-redundancy"></a>Избыточность файловых ресурсов

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Если вы хотите использовать геоизбыточное хранилище с доступом на чтение (RA-GRS), следует убедиться, что файл Azure не поддерживает геоизбыточное хранилище с доступом на чтение (RA-GRS) в любом регионе в настоящее время. Общие файловые ресурсы в учетной записи хранения RA-GRS работают так же, как и в учетных записях GRS, и плата за GRS цены.

> [!Warning]  
> Если вы используете общую папку Azure в качестве конечной точки облака в учетной записи хранения GRS, нет необходимости выполнять отработку отказа учетной записи хранения. Это приведет к прекращению синхронизации или даже к непредвиденной потере данных, если некоторые файлы недавно стали многоуровневыми. В случае потери региона Azure корпорация Майкрософт активирует отработку отказа учетной записи хранения способом, который совместим со службой "Синхронизация файлов Azure".

Общие ресурсы службы файлов Azure поддерживают как LRS, так и ZRS, ZRS в настоящее время доступен в меньшем подмножестве регионов.

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Подключение к большим файловым ресурсам (уровень Standard)

Этот раздел относится только к стандартным общим файловым ресурсам. Все общие файловые ресурсы уровня "Премиум" доступны с емкостью 100 тиб.

### <a name="restrictions"></a>Ограничения

- Преобразование учетной записи LRS/ZRS в GRS или ГЗРС будет невозможно для всех учетных записей хранения с включенными большими файловыми ресурсами.

### <a name="regional-availability"></a>Доступность по регионам

Стандартные общие файловые ресурсы с ограничением емкости Тиб 100 доступны глобально во всех регионах Azure.

- LRS: все регионы, за исключением Южно-Африканского и Южно-Западной Африки.
- ZRS: все регионы, за исключением Восточная Япония, Северной Европы, Южной Африки.
- GRS/ГЗРС: не поддерживается.

### <a name="enable-and-create-larger-file-shares"></a>Включение и создание больших файловых ресурсов

Чтобы приступить к использованию общих файловых ресурсов, см. статью [как включить и создать большие файловые ресурсы](storage-files-how-to-create-large-file-share.md).

## <a name="data-growth-pattern"></a>Модель роста данных

Сейчас максимальный размер файлового ресурса Azure составляет 100 тиб. Из-за этого ограничения при развертывании файлового ресурса Azure следует учитывать ожидаемый рост объема данных.

Можно синхронизировать несколько файловых ресурсов Azure с одним файловым сервером Windows с Синхронизация файлов Azure. Это позволяет обеспечить возможность включения в Синхронизация файлов Azure более старых и больших файловых ресурсов, которые могут быть доступны в локальной среде. Дополнительные сведения см. в разделе [Планирование развертывания Синхронизация файлов Azure](storage-files-planning.md).

## <a name="data-transfer-method"></a>Метод передачи данных

Существует множество простых вариантов массовой передачи данных из существующего файлового ресурса, такого как файловый ресурс в локальной среде, в службу файлов Azure. В число распространенных методов входят следующие (список не является исчерпывающим).

* **[Синхронизация файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)** . в рамках первой синхронизации между файловым ресурсом Azure ("облачная конечная точка") и пространством имен каталогов Windows ("Серверная конечная точка") Синхронизация файлов Azure реплицирует все данные из существующей общей папки в файлы Azure.
* **[Служба "Импорт и экспорт Azure"](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** . Эта служба позволяет безопасно переносить большие объемы данных в файловый ресурс Azure, отправляя их на жестких дисках в центр обработки данных Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** . Это хорошо известное средство копирования, которое входит в состав Windows и Windows Server. Robocopy можно применять для передачи данных в службу файлов Azure путем локального подключения файлового ресурса и последующего использования подключенного расположения в качестве назначения в команде Robocopy.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** — это служебная программа командной строки. Она предназначена для копирования данных из службы файлов Azure, хранилища BLOB-объектов Azure и обратно с помощью простых команд, обеспечивающих оптимальную производительность.

## <a name="next-steps"></a>Следующие шаги
* [Planning for an Azure File Sync Deployment](storage-sync-files-planning.md) (Планирование развертывания службы синхронизации файлов Azure)
* [How to deploy Azure Files](storage-files-deployment-guide.md) (Развертывание службы "Файлы Azure")
* [Развертывание службы синхронизации файлов Azure (предварительная версия)](storage-sync-files-deployment-guide.md)
