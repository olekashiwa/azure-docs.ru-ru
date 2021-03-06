---
title: Устройство службы "Миграция Azure"
description: Содержит общие сведения об устройстве миграции Azure, используемом при оценке и миграции сервера.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 652fe9d379d6e2ba50e9e282f384905e154368d8
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031669"
---
# <a name="azure-migrate-appliance"></a>Устройство службы "Миграция Azure"

В этой статье описывается устройство миграции Azure. Вы развертываете устройство при использовании службы ["миграция Azure": средство оценки серверов](migrate-services-overview.md#azure-migrate-server-assessment-tool) для обнаружения и оценки приложений, инфраструктуры и рабочих нагрузок, предназначенных для миграции на Microsoft Azure. Устройство также используется при переносе виртуальных машин VMware в Azure с помощью службы ["миграция Azure": Оценка серверов](migrate-services-overview.md#azure-migrate-server-migration-tool) с [миграцией без агента](server-migrate-overview.md).

## <a name="appliance-overview"></a>Общие сведения об устройстве

Устройство "миграция Azure" используется в следующих сценариях.

**Сценарий** | **Инструмент** | **Область использования** 
--- | --- | ---
Виртуальная машина VMware | Служба "миграция Azure": Оценка сервера<br/><br/> Миграция Azure: миграция сервера | Обнаружение виртуальных машин VMware<br/><br/> Обнаружение приложений и зависимостей компьютера<br/><br/> Собирайте метаданные компьютера и метаданные производительности для оценки.<br/><br/> Репликация виртуальных машин VMware с миграцией без агента.
Виртуальная машина Hyper-V | Служба "миграция Azure": Оценка сервера | Обнаружение виртуальных машин Hyper-V<br/><br/> Собирайте метаданные компьютера и метаданные производительности для оценки.
Физический компьютер |  Служба "миграция Azure": Оценка сервера |  Обнаружение физических серверов<br/><br/> Собирайте метаданные компьютера и метаданные производительности для оценки.

## <a name="appliance---vmware"></a>Устройство — VMware 

**Требование** | **VMware** 
--- | ---
**Формат скачивания** | . OVA 
**Ссылка для скачивания** | https://aka.ms/migrate/appliance/vmware 
**Размер скачивания** | 11,2 ГБ
**Лицензия** | Загруженный шаблон устройства поставляется с лицензией на ознакомительную версию Windows Server 2016, которая действительна в течение 180 дней. Если период оценки близок к истечению срока действия, рекомендуется скачать и развернуть новое устройство или активировать лицензию операционной системы виртуальной машины устройства.
**Deployment** | Вы развертываете устройство как виртуальную машину VMware. Вам потребуется достаточно ресурсов на vCenter Server, чтобы выделить виртуальную машину с 32 ГБ ОЗУ, 8 виртуальных ЦП, около 80 ГБ дискового накопителя и внешним виртуальным коммутатором.<br/> Устройству требуется доступ к Интернету либо напрямую, либо через прокси-сервер.<br/> Устройство может подключаться к одной vCenter Server.
**Оборудование** | Ресурсы в vCenter, позволяющие выделить виртуальную машину размером 32 ГБ ОЗУ 8 виртуальных ЦП, около 80 ГБ дискового накопителя и внешним виртуальным коммутатором. 
**Значение хэша** | MD5: c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256:4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c
**сервер vCenter Server/узел** | Виртуальная машина устройства должна быть развернута на узле ESXi, работающем под управлением версии 5,5 или более поздней.<br/><br/> vCenter Server с 5,5, 6,0, 6,5 или 6,7.
**Проект службы "миграция Azure"** | Устройство может быть связано с одним проектом. <br/> С одним проектом можно связать любое количество устройств.<br/> 
**Маршрутизатор** | Устройство может обнаруживать до 10 000 виртуальных машин VMware на vCenter Server.<br/> Устройство может подключаться к одной vCenter Server.
**Компоненты устройства** | Приложение управления: веб-приложение в устройстве для ввода данных пользователем во время развертывания.<br/> Агент обнаружения: собирает данные конфигурации компьютера.<br/> Агент оценки: получение данных о производительности.<br/> DRA: управляет репликацией виртуальной машины и координирует обмен данными между компьютерами и Azure.<br/> Шлюз: отправляет реплицированные данные в Azure.<br/> Служба автоматического обновления: обновление компонентов (выполняется каждые 24 часа).
**ВДДК (миграция без агента)** | Если вы используете миграцию без агента с миграцией Azure Migration Server, на виртуальной машине устройства должен быть установлен VMware vSphere ВДДК.


## <a name="appliance---hyper-v"></a>Устройство — Hyper-V

**Требование** | **Hyper-V** 
--- | ---
**Формат скачивания** | ZIP-папка (с VHD)
**Ссылка для скачивания** | https://aka.ms/migrate/appliance/hyperv 
**Размер скачивания** | 10 ГБ
**Лицензия** | Загруженный шаблон устройства поставляется с лицензией на ознакомительную версию Windows Server 2016, которая действительна в течение 180 дней. Если период оценки близок к истечению срока действия, рекомендуется скачать и развернуть новое устройство или активировать лицензию операционной системы виртуальной машины устройства.
**Развертывание устройства**   |  Вы развертываете устройство как виртуальную машину Hyper-V.<br/> Виртуальная машина устройства, предоставляемая службой "миграция Azure", — это виртуальная машина Hyper-V версии 5,0.<br/> Узел Hyper-V должен работать под управлением Windows Server 2012 R2 или более поздней версии.<br/> Ведущему приложению требуется достаточно места для выделения 16 ГБ ОЗУ, 8 виртуальных ЦП, около 80 ГБ дискового пространства и внешнего коммутатора для виртуальной машины устройства.<br/> Устройству требуется статический или динамический IP-адрес, а также доступ к Интернету.
**Оборудование** | Ресурсы на узле Hyper-V, позволяющие выделить 16 ГБ ОЗУ, 8 виртуальных ЦП, около 80 ГБ дискового пространства и внешний коммутатор для виртуальной машины устройства.
**Значение хэша** | MD5:29a7531f32bcf69f32d964fa5ae950bc<br/><br/> SHA256:37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31
**Узел Hyper-V** | Под Windows Server 2012 R2 или более поздней версии.
**Проект службы "миграция Azure"** | Устройство может быть связано с одним проектом. <br/> С одним проектом можно связать любое количество устройств.<br/> 
**Маршрутизатор** | Устройство может обнаруживать до 5000 виртуальных машин VMware на vCenter Server.<br/> Устройство может подключаться к узлам Hyper-V до 300.
**Компоненты устройства** | Приложение управления: веб-приложение в устройстве для ввода данных пользователем во время развертывания.<br/> Агент обнаружения: собирает данные конфигурации компьютера.<br/> Агент оценки: получение данных о производительности.<br/>  Служба автоматического обновления: обновление компонентов (выполняется каждые 24 часа).


## <a name="appliance---physical"></a>Устройство — физическое

**Требование** | **Реальной** 
--- | ---
**Формат скачивания** | ZIP-папка (с скриптом установщика на основе PowerShell)
**Ссылка для скачивания** | [Ссылка для скачивания](https://go.microsoft.com/fwlink/?linkid=2105112)
**Размер скачивания** | 59,7 МБ
**Оборудование** | Выделенный физический компьютер или использование виртуальной машины. Компьютеру, на котором работает устройство, требуется 16 ГБ ОЗУ, 8 виртуальных ЦП, около 80 ГБ дискового пространства и внешний коммутатор.<br/> Устройству требуется статический или динамический IP-адрес, а также доступ к Интернету.
**Значение хэша** | MD5:1e92ede3e87c03bd148e56a708cdd33f<br/><br/> SHA256: a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c
**Операционная система** | Компьютер устройства должен работать под Windows Server 2016. 
**Развертывание устройства**   |  Сценарий установщика устройства загружается с портала (в ZIP-папке). <br/> Вы распакуйте папку и выполните сценарий PowerShell (Азуремигратеинсталлер. ps1).
**Маршрутизатор** | Устройство может обнаруживать до 250 физических серверов.
**Компоненты устройства** | Приложение управления: веб-приложение в устройстве для ввода данных пользователем во время развертывания.<br/> Агент обнаружения: собирает данные конфигурации компьютера.<br/> Агент оценки: получение данных о производительности.<br/>  Служба автоматического обновления: обновление компонентов (выполняется каждые 24 часа).


## <a name="url-access"></a>доступ по URL-адресу

Устройству миграции Azure требуется подключение к Интернету.

- При развертывании устройства служба "миграция Azure" выполняет проверку подключения к URL-адресам, приведенным в таблице ниже.
- Если вы используете прокси на основе URL-адресов для подключения к Интернету, предоставьте доступ к этим URL-адресам, убедившись, что прокси-сервер разрешает любые записи CNAME, полученные при поиске URL-адресов.

**URL-адрес** | **Сведения**  
--- | --- |
*.portal.azure.com  | Перейдите на портал Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Войдите в свою подписку Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Создание Active Directory приложений для взаимодействия устройства с помощью службы "миграция Azure".
management.azure.com | Создание Active Directory приложений для взаимодействия устройства с службой "миграция Azure".
dc.services.visualstudio.com | Отправка журналов приложений, используемых для внутреннего мониторинга.
*.vault.azure.net | Управление секретами в Azure Key Vault.
aka.ms/* | Разрешение доступа к связям. Используется для переноса обновлений устройства в Azure.
download.microsoft.com/download | Разрешить загрузку из центра загрузки Майкрософт.
*.servicebus.windows.net | Обмен данными между устройством и службой "миграция Azure".
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Подключение к Azure миграция URL-адресов службы.
*.hypervrecoverymanager.windowsazure.com | **Используется для миграции без агента VMware**<br/><br/> Подключение к Azure миграция URL-адресов службы.
*.blob.core.windows.net |  **Используется для миграции без агента VMware**<br/><br/>Отправка данных в хранилище для миграции.




## <a name="collected-data---vmware"></a>Собранные данные — VMware

### <a name="collected-performance-data-vmware"></a>Собранные данные производительности — VMware

Ниже приведены данные производительности виртуальной машины VMware, которые устройство собирает и отправляет в Azure.

**Data** | **Счетчик** | **Влияние на оценку**
--- | --- | ---
Использование ЦП | cpu.usage.average | Рекомендуемый размер и стоимость виртуальной машины
Использование памяти | mem.usage.average | Рекомендуемый размер и стоимость виртуальной машины
Пропускная способность чтения с диска (МБ в секунду) | virtualDisk.read.average | Вычисление размера диска, стоимости хранилища, размера виртуальной машины
Пропускная способность записи на диск (МБ в секунду) | virtualDisk.write.average | Вычисление размера диска, стоимости хранилища, размера виртуальной машины
Количество операций чтения с диска в секунду | virtualDisk.numberReadAveraged.average | Вычисление размера диска, стоимости хранилища, размера виртуальной машины
Операций записи на диск в секунду | virtualDisk.numberWriteAveraged.average  | Вычисление размера диска, стоимости хранилища, размера виртуальной машины
Пропускная способность чтения сетевого адаптера (МБ в секунду) | net.received.average | Вычисление размера виртуальной машины
Пропускная способность записи сетевого адаптера (МБ в секунду) | net.transmitted.average  |Вычисление размера виртуальной машины


### <a name="collected-metadata-vmware"></a>Собранные метаданные — VMware

> [!NOTE]
> Метаданные, обнаруженные устройством "миграция Azure", используются для того, чтобы масштабировать приложения по мере их переноса в Azure, выполнять анализ соответствия требованиям для Azure, анализ зависимостей приложений и планирование затрат. Корпорация Майкрософт не использует эти данные в отношении аудита соответствия лицензионным требованиям.

Ниже приведен полный список метаданных виртуальных машин VMware, которые устройство собирает и отправляет в Azure.

**Data** | **Счетчик**
--- | --- 
**Сведения о компьютере** | 
Идентификатор виртуальной машины | vm.Config.InstanceUuid 
имя виртуальной машины; | vm.Config.Name
Идентификатор vCenter Server | Вмвареклиент. instance. UUID
Описание виртуальной машины | vm.Summary.Config.Annotation
Имя продукта лицензии | vm.Client.ServiceContent.About.LicenseProductName
Тип операционной системы | машину. Суммариконфиг. Гуестфуллнаме
Тип загрузки | vm.Config.Firmware
Число ядер | vm.Config.Hardware.NumCPU
Память (МБ) | vm.Config.Hardware.MemoryMB
Количество дисков | машину. Файл config. Hardware. Device. ToList (). FindAll (x = > — VirtualDisk). Count
Список размеров диска | машину. Файл config. Hardware. Device. ToList (). FindAll (x = > — VirtualDisk)
Список сетевых адаптеров | машину. Файл config. Hardware. Device. ToList (). FindAll (x = > — Виртуалесернет). Count
Использование ЦП | cpu.usage.average
Использование памяти |mem.usage.average
**Сведения о диске** | 
Значение ключа диска | disk.Key
Номер дикунит | disk.UnitNumber
Значение ключа контроллера диска | disk.ControllerKey.Value
Подготовленных гигабайт | virtualDisk.DeviceInfo.Summary
Имя диска | Значение, созданное с помощью диска. Унитнумбер, Disk. Ключ, диск. Контроллеркэй. VAlue
Число операций чтения за секунду. | virtualDisk.numberReadAveraged.average
Число операций записи за секунду. | virtualDisk.numberWriteAveraged.average
Пропускная способность чтения (МБ в секунду) | virtualDisk.read.average
Пропускная способность записи (МБ в секунду) | virtualDisk.write.average
**Сведения для каждого сетевого адаптера** | 
Имя сетевого адаптера | nic.Key
MAC-адрес. | ((VirtualEthernetCard)nic).MacAddress
IPv4-адреса | vm.Guest.Net
IPv6-адреса | vm.Guest.Net
Пропускная способность чтения (МБ в секунду) | net.received.average
Пропускная способность записи (МБ в секунду) | net.transmitted.average
**Сведения о пути инвентаризации** | 
Имя | container.GetType().Name
Тип дочернего объекта | container.ChildType
Справочные сведения | container.MoRef
Сведения о родительском объекте | Container.Parent
Сведения о папке на виртуальную машину | ((Folder)container).ChildEntity.Type
Сведения о центре обработки данных на виртуальную машину | ((Datacenter)container).VmFolder
Сведения о центре обработки данных на папку узлов | ((Datacenter)container).HostFolder
Сведения о кластере на узле | ((Клустеркомпутересаурце) контейнер). Хост
Сведения об узле на виртуальную машину | ((Хостсистем) контейнер). МАШИНУ

## <a name="collected-data---hyper-v"></a>Собранные данные — Hyper-V

### <a name="collected-performance-data-hyper-v"></a>Собранные данные производительности — Hyper-V

> [!NOTE]
> Метаданные, обнаруженные устройством "миграция Azure", используются для того, чтобы масштабировать приложения по мере их переноса в Azure, выполнять анализ соответствия требованиям для Azure, анализ зависимостей приложений и планирование затрат. Корпорация Майкрософт не использует эти данные в отношении аудита соответствия лицензионным требованиям.

Ниже приведены данные о производительности виртуальной машины Hyper, которые устройство собирает и отправляет в Azure.

**Класс счетчика производительности** | **Счетчик** | **Влияние на оценку**
--- | --- | ---
Виртуальный процессор гипервизора Hyper-V | % Времени выполнения гостевой работы | Рекомендуемый размер и стоимость виртуальной машины
Виртуальная машина Hyper-V динамическая память | Текущая нагрузка (%)<br/> Физическая доступная Гостевая память (МБ) | Рекомендуемый размер и стоимость виртуальной машины
Виртуальное запоминающее устройство Hyper-V | Байтов чтения в секунду | Вычисление размера диска, стоимости хранилища, размера виртуальной машины
Виртуальное запоминающее устройство Hyper-V | Записано байт в секунду | Вычисление размера диска, стоимости хранилища, размера виртуальной машины
Виртуальный сетевой адаптер Hyper-V | Получено байт/сек | Вычисление размера виртуальной машины
Виртуальный сетевой адаптер Hyper-V | Отправлено байтов в секунду | Вычисление размера виртуальной машины

- Загрузка ЦП — это сумма всех использований для всех виртуальных процессоров, подключенных к виртуальной машине.
- Использование памяти — (текущее давление * видимая физическая память)/100.
- Значения использования диска и сети собираются из перечисленных счетчиков производительности Hyper-V.

### <a name="collected-metadata-hyper-v"></a>Собранные метаданные — Hyper-V

Ниже приведен полный список метаданных виртуальной машины Hyper-V, которые устройство собирает и отправляет в Azure.

**Data** | **Класс WMI** | **Свойство класса WMI**
--- | --- | ---
**Сведения о компьютере** | 
Серийный номер BIOS _ Msvm_BIOSElement | биоссериалнумбер
Тип виртуальной машины (GEN 1 или 2) | Msvm_VirtualSystemSettingData | виртуалсистемсубтипе
Отображаемое имя виртуальной машины | Msvm_VirtualSystemSettingData | ElementName
Версия виртуальной машины | Msvm_ProcessorSettingData | виртуалкуантити
Память (байт) | Msvm_MemorySettingData | виртуалкуантити
Максимальный объем памяти, который может потребляться виртуальной машиной | Msvm_MemorySettingData | Предельное количество
Динамическая память включена | Msvm_MemorySettingData | динамикмеморенаблед
Имя, версия или полное доменное название операционной системы | Msvm_KvpExchangeComponent | Данные имени Гуестинтринсицексчанжеитемс
Состояние питания виртуальной машины | Msvm_ComputerSystem | Отличается
**Сведения о диске** | 
Идентификатор диска | Msvm_VirtualHardDiskSettingData | виртуалдискид
Тип виртуального жесткого диска | Msvm_VirtualHardDiskSettingData | Тип
Размер виртуального жесткого диска | Msvm_VirtualHardDiskSettingData | максинтерналсизе
Родительский элемент виртуального жесткого диска | Msvm_VirtualHardDiskSettingData | парентпас
**Сведения для каждого сетевого адаптера** | 
IP-адреса (синтетические сетевые карты) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP включен (синтетические сетевые карты) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ИДЕНТИФИКАТОР сетевого адаптера (синтетические сетевые карты) | Msvm_SyntheticEthernetPortSettingData | InstanceID
MAC-адрес NIC (синтетические сетевые карты) | Msvm_SyntheticEthernetPortSettingData | Адрес
ИДЕНТИФИКАТОР сетевого адаптера (устаревшие сетевые карты) | Данные Мсвмемулатедесернетпортсеттинг | InstanceID
MAC ID NIC (устаревшие сетевые карты) | Данные Мсвмемулатедесернетпортсеттинг | Адрес




## <a name="discovery-and-collection-process"></a>Процесс обнаружения и сбора

Устройство взаимодействует с серверами vCenter и узлами или кластером Hyper-V, используя следующий процесс.

1. **Запустить обнаружение**:
    - При запуске обнаружения на устройстве Hyper-V оно взаимодействует с узлами Hyper-V на портах WinRM 5985 (HTTP) и 5986 (HTTPS).
    - При запуске обнаружения на устройстве VMware оно по умолчанию взаимодействует с сервером vCenter через TCP-порт 443. Если сервер vCenter Server прослушивает другой порт, его можно настроить в веб-приложении устройства.
2. **Сбор данных о метаданных и производительности**:
    - Устройство использует сеанс модель CIM (CIM) для сбора данных виртуальной машины Hyper-v с узла Hyper-V на портах 5985 и 5986.
    - По умолчанию устройство взаимодействует с портом 443 для сбора данных виртуальной машины VMware из vCenter Server.
3. **Send Data (отправить данные**). устройство отправляет собранные данные в Azure Migration Server оценок и выполняет миграцию сервера миграции Azure через порт SSL 443. Устройство может подключаться к Azure через Интернет. также можно использовать ExpressRoute с общедоступным или пирингом Майкрософт.
    - Для данных производительности устройство собирает данные об использовании в режиме реального времени.
        - Данные о производительности собираются каждые 20 секунд для VMware и каждые 30 секунд для Hyper-V для каждой метрики производительности.
        - Собранные данные суммируются для создания одной точки данных в течение 10 минут.
        - Значение Пиковая загрузка выбирается со всех 20-секундных точек данных и отправляется в Azure для вычисления оценки.
        - На основе значения процентиля, указанного в свойствах оценки (50/90/95 процентиль/99-м), точки с десятью минутами сортируются в возрастающем порядке, а соответствующее значение процентиля используется для вычисления оценки.
    - Для миграции сервера устройство начинает сбор данных виртуальной машины и реплицирует их в Azure.
4. **Оценка и миграция**. Теперь вы можете создавать оценки на основе метаданных, собранных устройством, с помощью функции "Миграция серверов Azure". Кроме того, можно также приступить к переносу виртуальных машин VMware с помощью миграции сервера миграция Azure для координации репликации виртуальных машин без агента.


![Архитектура](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Обновление устройства

Устройство обновляется, так как агенты службы "миграция Azure", работающие на устройстве, обновляются.

- Это происходит автоматически, так как автоматическое обновление включено на устройстве по умолчанию.
- Вы можете изменить этот параметр по умолчанию, чтобы обновить агенты вручную.
- Чтобы отключить автоматическое обновление, перейдите в редактор реестра > HKEY_LOCAL_MACHINE \Софтваре\микрософт\азуреапплианце и установите раздел реестра — "автоматическое обновление" в значение 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Установка обновлений агента вручную

Для ручного обновления убедитесь, что одновременно обновляются все агенты на устройстве, с помощью кнопки **Обновить** для каждого устаревшего агента на устройстве. Вы можете в любое время переключить параметр обновления обратно на автоматические обновления.

## <a name="next-steps"></a>Следующие шаги

[Узнайте, как](tutorial-assess-vmware.md#set-up-the-appliance-vm) настроить устройство для VMware.
[Узнайте, как](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) настроить устройство для Hyper-V.

