---
title: Копирование данных из таблицы SAP
description: Узнайте, как копировать данные из таблицы SAP в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: fd363f7b685db5e309827a0c5e635264e676b388
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926181"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Копирование данных из таблицы SAP с помощью фабрики данных Azure

В этой статье описано, как с помощью действия копирования в фабрике данных Azure копировать данные из таблицы SAP. Дополнительные сведения см. в разделе [Общие сведения о действиях копирования](copy-activity-overview.md).

>[!TIP]
>Сведения о общей поддержке ADF в сценарии интеграции данных SAP см. в статье [Интеграция данных SAP с помощью фабрики данных Azure](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) с подробным введением, компарсион и рекомендациями.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель таблиц SAP поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из таблицы SAP можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, поддерживаемых действием копирования в качестве источников или приемников, см. в таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) .

В частности, этот соединитель таблиц SAP поддерживает:

- Копирование данных из таблицы SAP в:

  - Центральный компонент SAP ERP (SAP ECC) версии 7,01 или более поздней (в последнем стеке пакетов поддержки SAP, выпущенном после 2015).
  - SAP Business Warehouse (SAP BW) версии 7,01 или более поздней (в последнем стеке пакетов поддержки SAP, выпущенном после 2015).
  - SAP S/4HANA.
  - Другие продукты в SAP Business Suite версии 7,01 или более поздней (в последнем стеке пакетов поддержки SAP, выпущенном после 2015).

- Копирование данных из прозрачной таблицы SAP, таблицы с группировкой, кластеризованной таблицы и представления.
- Копирование данных с использованием обычной проверки подлинности или защищенного сетевого подключения (SNC), если установлен SNC.
- Подключение к серверу приложений SAP или серверу сообщений SAP.

## <a name="prerequisites"></a>Технические условия

Чтобы использовать этот соединитель таблиц SAP, необходимо выполнить следующие действия:

- Настройте локальную среду выполнения интеграции (версии 3,17 или более поздней). Дополнительные сведения см. [в разделе Создание и Настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md).

- Скачайте 64-разрядный [соединитель SAP для Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) с веб-сайта SAP и установите его на компьютере с локальной средой выполнения интеграции. Во время установки убедитесь, что в окне **необязательные шаги установки** выбран параметр **установить сборки в глобальный кэш сборок** .

  ![Установка соединителя SAP для .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Пользователь SAP, используемый в соединителе таблиц SAP фабрики данных, должен иметь следующие разрешения:

  - Авторизация для использования назначений удаленного вызова функций (RFC).
  - Разрешения на действие выполнение объекта авторизации S_SDSAUTH.

## <a name="get-started"></a>Начать

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю таблицы SAP.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SAP BW Open Hub поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| `type` | Для свойства `type` должно быть установлено значение `SapTable`. | ДА |
| `server` | Имя сервера, на котором расположен экземпляр SAP.<br/>Используйте для подключения к серверу приложений SAP. | Нет |
| `systemNumber` | Системный номер системы SAP.<br/>Используйте для подключения к серверу приложений SAP.<br/>Допустимое значение: двузначное десятичное число, представленное в виде строки. | Нет |
| `messageServer` | Имя узла сервера сообщений SAP.<br/>Используйте для подключения к серверу сообщений SAP. | Нет |
| `messageServerService` | Имя службы или номер порта сервера сообщений.<br/>Используйте для подключения к серверу сообщений SAP. | Нет |
| `systemId` | Идентификатор системы SAP, в которой находится таблица.<br/>Используйте для подключения к серверу сообщений SAP. | Нет |
| `logonGroup` | Группа входа для системы SAP.<br/>Используйте для подключения к серверу сообщений SAP. | Нет |
| `clientId` | Идентификатор клиента в системе SAP.<br/>Допустимое значение: трехзначный десятичное число, представленное в виде строки. | ДА |
| `language` | Язык, используемый системой SAP.<br/>По умолчанию имеет значение `EN`.| Нет |
| `userName` | Имя пользователя, имеющего доступ к серверу SAP. | ДА |
| `password` | Пароль пользователя. Пометьте это поле типом `SecureString`, чтобы безопасно хранить его в фабрике данных, или [сослаться на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | ДА |
| `sncMode` | Индикатор активации SNC для доступа к серверу SAP, на котором расположена таблица.<br/>Используйте, если вы хотите использовать SNC для подключения к серверу SAP.<br/>Допустимые значения: `0` (выключено, по умолчанию) или `1` (on). | Нет |
| `sncMyName` | SNC-имя инициатора для доступа к серверу SAP, на котором расположена таблица.<br/>Применяется, если `sncMode` включен. | Нет |
| `sncPartnerName` | SNC-имя коммуникационного партнера для доступа к серверу SAP, на котором расположена таблица.<br/>Применяется, если `sncMode` включен. | Нет |
| `sncLibraryPath` | Библиотека внешнего продукта безопасности для доступа к серверу SAP, на котором расположена таблица.<br/>Применяется, если `sncMode` включен. | Нет |
| `sncQop` | Применяемый уровень защиты SNC.<br/>Применяется, если `sncMode` включен. <br/>Допустимые значения: `1` (проверка подлинности), `2` (целостность), `3` (конфиденциальность), `8` (по умолчанию), `9` (максимум). | Нет |
| `connectVia` | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Требуется локальная среда выполнения интеграции, как упоминалось ранее в [предварительных требованиях](#prerequisites). |ДА |

**Пример 1. подключение к серверу приложений SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Пример 2. подключение к серверу сообщений SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Пример 3. подключение с помощью SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств для определения наборов данных см. в разделе [наборы данных](concepts-datasets-linked-services.md). В следующем разделе приведен список свойств, поддерживаемых набором данных таблицы SAP.

Чтобы скопировать данные из SAP BW открыть связанную службу центра или в нее, поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| `type` | Для свойства `type` должно быть установлено значение `SapTableResource`. | ДА |
| `tableName` | Имя таблицы SAP, из которой копируются данные. | ДА |

### <a name="example"></a>Пример

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств для определения действий см. в разделе [конвейеры](concepts-pipelines-activities.md). В следующем разделе приведен список свойств, поддерживаемых источником таблицы SAP.

### <a name="sap-table-as-source"></a>Таблица SAP в качестве источника

Для копирования данных из таблицы SAP поддерживаются следующие свойства:

| Свойство                         | Описание                                                  | Обязательно для заполнения |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | Для свойства `type` должно быть установлено значение `SapTableSource`.         | ДА      |
| `rowCount`                         | Число строк для извлечения.                              | Нет       |
| `rfcTableFields`                   | Поля (столбцы) для копирования из таблицы SAP. Пример: `column0, column1`. | Нет       |
| `rfcTableOptions`                  | Параметры для фильтрации строк в таблице SAP. Пример: `COLUMN0 EQ 'SOMEVALUE'`. См. также таблицу операторов запросов SAP далее в этой статье. | Нет       |
| `customRfcReadTableFunctionModule` | Пользовательский модуль функции RFC, который может использоваться для чтения данных из таблицы SAP.<br>Вы можете использовать пользовательский модуль функции RFC, чтобы определить, как данные извлекаются из системы SAP и возвращены в фабрику данных. Пользовательский модуль функции должен иметь реализованный интерфейс (импорт, экспорт, таблицы), похожий на `/SAPDS/RFC_READ_TABLE2`, который является интерфейсом по умолчанию, используемым фабрикой данных. | Нет       |
| `partitionOption`                  | Механизм секционирования для чтения из таблицы SAP. Поддерживаемые варианты перечислены ниже. <ul><li>`None`</li><li>`PartitionOnInt` (обычные целые или целочисленные значения с нулевым отступом слева, например `0000012345`)</li><li>`PartitionOnCalendarYear` (4 цифры в формате «гггг»)</li><li>`PartitionOnCalendarMonth` (6 цифр в формате "ИИИИММ")</li><li>`PartitionOnCalendarDate` (8 цифр в формате ГГГГММДД)</li></ul> | Нет       |
| `partitionColumnName`              | Имя столбца, используемого для секционирования данных.                | Нет       |
| `partitionUpperBound`              | Максимальное значение столбца, указанного в `partitionColumnName`, который будет использоваться для продолжения секционирования. | Нет       |
| `partitionLowerBound`              | Минимальное значение столбца, указанного в `partitionColumnName`, который будет использоваться для продолжения секционирования. | Нет       |
| `maxPartitionsNumber`              | Максимальное число секций для разделения данных.     | Нет       |

>[!TIP]
>Если таблица SAP содержит большой объем данных, например несколько миллиардов строк, используйте `partitionOption` и `partitionSetting`, чтобы разделить данные на более мелкие секции. В этом случае данные считываются для каждой секции, и каждый раздел данных извлекается с сервера SAP с помощью одного вызова RFC.<br/>
<br/>
>`partitionOption` в качестве примера `partitionOnInt` число строк в каждой секции вычисляется с помощью следующей формулы: (всего строк, попадающих между `partitionUpperBound` и `partitionLowerBound`)/`maxPartitionsNumber`.<br/>
<br/>
>Чтобы параллельно загружать секции данных для ускорения копирования, степень параллелизма определяется параметром [`parallelCopies`](copy-activity-performance.md#parallel-copy) действия копирования. Например, если задать для `parallelCopies` значение 4, фабрика данных будет создавать и выполнять четыре запроса на основе указанного параметра секции и параметров, и каждый запрос извлекает часть данных из таблицы SAP. Настоятельно рекомендуется `maxPartitionsNumber` несколько значений свойства `parallelCopies`. При копировании данных в хранилище данных на основе файлов оно также передается в папку в виде нескольких файлов (указывается только имя папки). в этом случае производительность лучше, чем при записи в один файл.

В `rfcTableOptions`можно использовать следующие стандартные операторы запросов SAP для фильтрации строк:

| operator | Описание |
| :------- | :------- |
| `EQ` | Равно |
| `NE` | Не равно |
| `LT` | Менее |
| `LE` | Меньше или равно |
| `GT` | Более |
| `GE` | Больше или равно |
| `LIKE` | Как в `LIKE 'Emma%'` |

### <a name="example"></a>Пример

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Сопоставления типов данных для таблицы SAP

При копировании данных из таблицы SAP используются следующие сопоставления типов данных таблицы SAP с промежуточными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных ABAP в SAP | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| `C` (строка) | `String` |
| `I` (целое число) | `Int32` |
| `F` (float) | `Double` |
| `D` (дата) | `String` |
| `T` (время) | `String` |
| `P` (пакет BCD, валюта, десятичное, Кол-во) | `Decimal` |
| `N` (числовой) | `String` |
| `X` (двоичный и необработанный) | `String` |

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Дальнейшие действия

Список хранилищ данных, поддерживаемых действием копирования в фабрике данных Azure в качестве источников и приемников, см. в разделе [Supported Data Stores (поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats)).
