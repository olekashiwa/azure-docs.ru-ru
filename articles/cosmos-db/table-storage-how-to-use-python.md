---
title: Использование API таблиц Azure Cosmos DB и Хранилища таблиц Azure с Python
description: Хранение структурированных данных в облаке с помощью хранилища таблиц Azure или API таблиц Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 04/05/2018
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: b3a6f4397ca1b8c56f06f6d967804c94096ee308
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76770987"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Начало работы с хранилищем таблиц Azure и API таблиц Azure Cosmos DB с помощью Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Хранилище таблиц Azure и Azure Cosmos DB — это службы, которые хранят структурированные NoSQL-данные в облаке, предоставляя хранилище для ключей и атрибутов с бессхемной структурой. Поскольку хранилище таблиц и Azure Cosmos DB являются бессхемными, это позволяет легко адаптировать данные по мере расширения приложения. Разным типам приложений может быть предоставлен быстрый и экономный доступ к хранилищу таблиц и API таблиц. Такое хранилище обычно дешевле, чем традиционные хранилища SQL для сравнимых объемов данных.

Хранилище таблиц или Azure Cosmos DB можно использовать для хранения гибких наборов данных, например пользовательских данных для веб-приложений, адресных книг, сведений об устройстве или метаданных любого другого типа, которые требуются вашей службе. В таблице можно хранить любое количество сущностей, а учетная запись хранения может содержать любое количество таблиц в пределах емкости учетной записи.

### <a name="about-this-sample"></a>Об этом примере
Здесь вы узнаете, как использовать [пакет SDK для таблиц Azure Cosmos DB для Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) в распространенных сценариях для хранилища таблиц Azure. Имя пакета SDK указывает на то, что он предназначен для использования с Azure Cosmos DB. Но его можно использовать и с Azure Cosmos DB, и с Хранилищем таблиц Azure, просто для каждой службы предусмотрена уникальная конечная точка. Эти сценарии рассматриваются с использованием примеров Python, которые демонстрируют выполнение следующих задач:
* создание и удаление таблиц;
* вставка и запрос сущностей;
* изменение сущностей.

Работая над сценариями в этом примере, можно использовать [справочные материалы по пакету SDK Azure Cosmos DB для API Python](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим примером требуются следующие компоненты:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 или 3.6.
- [Пакет SDK таблиц Azure Cosmos DB для Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Этот пакет SDK подключается к Хранилищу таблиц Azure и API таблиц Azure Cosmos DB.
- [Учетная запись хранения Azure](../storage/common/storage-account-create.md) или [учетная запись Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/).

## <a name="create-an-azure-service-account"></a>Создание учетной записи службы Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Создание учетной записи API таблиц Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Установка пакета SDK таблиц Azure Cosmos DB для Python.

После создания учетной записи хранения установите [пакет SDK таблиц Azure Cosmos DB для Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Дополнительные сведения см. в файле [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) в репозитории GitHub на странице пакета SDK таблиц Cosmos DB для Python.

## <a name="import-the-tableservice-and-entity-classes"></a>Импорт классов TableService и Entity

Для работы с сущностями в службе таблиц Azure с помощью Python необходимо использовать классы [TableService][py_TableService] и [Entity][py_Entity]. Добавьте следующий код в начало файла Python, чтобы импортировать модуль и класс:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Подключение к службе таблиц Azure

Чтобы подключиться к службе таблиц в службе хранилища Azure, создайте объект [TableService][py_TableService], а затем передайте имя и ключ учетной записи хранения. Замените `myaccount` и `mykey` именем учетной записи и ключом.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Подключение к Azure Cosmos DB

Чтобы подключиться к Azure Cosmos DB, скопируйте первичную строку подключения c портала Azure и с ее помощью создайте объект [TableService][py_TableService].

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;')
```

## <a name="create-a-table"></a>Создание таблицы

Вызовите метод [create_table][py_create_table], чтобы создать таблицу.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу

Чтобы добавить сущность, сначала создайте объект, который представляет ее, затем передайте объект в метод [TableService.insert_entity][py_TableService]. Объект сущности может быть словарем или объектом типа [Entity][py_Entity]. Он определяет имена и значения свойств сущности. Каждая сущность должна включать требуемые свойства [PartitionKey и RowKey](#partitionkey-and-rowkey) помимо других свойств, определенных для сущности.

В этом примере создается объект словаря, который представляет сущность, а затем передает его в метод [insert_entity][py_insert_entity], чтобы добавить его в таблицу:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

В этом примере создается объект [Entity][py_Entity], который затем передается в метод [insert_entity][py_insert_entity] для добавления в таблицу:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey и RowKey

Для каждой сущности необходимо указать свойства **PartitionKey** и **RowKey**. Это уникальные идентификаторы сущностей, так как вместе они формируют первичный ключ сущности. С помощью этих значений можно отправлять запросы быстрее, чем к другим свойствам, так как индексируются только эти свойства.

Служба таблиц использует **PartitionKey** для интеллектуального распределения сущностей таблицы по узлам хранилища. Сущности с одним значением **PartitionKey** хранятся на одном узле. **RowKey** — это уникальный идентификатор сущности в разделе, которому она принадлежит.

## <a name="update-an-entity"></a>Обновление сущности

Чтобы обновить все значения свойств сущности, вызовите метод [update_entity][py_update_entity]. Этот пример показывает, как заменить сущность обновленной версией:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Если обновляемая сущность больше не существует, операция обновления завершается ошибкой. Чтобы сохранить сущность независимо от того, существует она или нет, используйте метод [insert_or_replace_entity][py_insert_or_replace_entity]. В следующем примере первый вызов заменит существующую сущность. Второй вызов вставит новую сущность, так как в таблице нет сущности с указанными свойствами PartitionKey и RowKey.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage again', 'priority': 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003',
        'description': 'Buy detergent', 'priority': 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> Метод [update_entity][py_update_entity] заменяет все свойства и значения сущности. Его также можно использовать для удаления свойств из существующей сущности. Чтобы обновить сущность с помощью новых или измененных значений свойств без замены сущности используйте метод [merge_entity][py_merge_entity].

## <a name="modify-multiple-entities"></a>Изменение нескольких сущностей

Для атомарной обработки запроса службой таблиц можно отправить сразу несколько операций в пакете. Сначала используйте класс [TableBatch][py_TableBatch], чтобы добавить несколько операций в один пакет. Затем вызовите метод [TableService][py_TableService].[commit_batch][py_commit_batch], чтобы отправить операции в атомарной операции. Все сущности, которые должны быть изменены в пакете, должны находиться в одном разделе.

В этом примере показано добавление двух сущностей в пакете:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004',
           'description': 'Go grocery shopping', 'priority': 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005',
           'description': 'Clean the bathroom', 'priority': 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Для пакетов также можно использовать синтаксис диспетчера контекста:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006',
           'description': 'Go grocery shopping', 'priority': 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007',
           'description': 'Clean the bathroom', 'priority': 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Запрос сущности

Чтобы запросить сущность в таблице, передайте ее свойства PartitionKey и RowKey в метод [TableService][py_TableService].[get_entity][py_get_entity].

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Запрос набора сущностей

Можно запросить набор сущностей, указав строку фильтра с помощью параметра **filter**. Этот пример находит все задачи в Сиэтле, используя фильтр PartitionKey:

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Запрос подмножества свойств сущности

Также можно ограничить свойства, возвращаемые для каждой сущности в запросе. Этот метод, который называется *проекцией*, снижает потребление пропускной способности и может повысить производительность запросов, особенно для больших сущностей и наборов результатов. Используйте параметр **select** и передайте имена свойств, которые необходимо вернуть клиенту.

Запрос в следующем коде возвращает только описания сущностей в таблице.

> [!NOTE]
> Следующий фрагмент работает только для службы хранилища Azure. Его не поддерживает эмулятор хранения.

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Удаление сущности

Чтобы удалить сущность, передайте ее свойства **PartitionKey** и **RowKey** в метод [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Удаление таблицы

Если вам больше не нужна таблица или сущность в ней, вызовите метод [delete_table][py_delete_table], чтобы окончательно удалить таблицу из службы хранилища Azure.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Дальнейшие действия

* [Разработка с использованием API таблиц (вопросы и ответы)](https://docs.microsoft.com/azure/cosmos-db/faq)
* [Azure CosmosDB SDK for Python](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python) (Справочник по пакету SDK Azure Cosmos DB для Python)
* [Центр по разработке для Python](https://azure.microsoft.com/develop/python/)
* [Обозреватель службы хранилища Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md). Это бесплатное кроссплатформенное приложение для визуализации данных службы хранилища Azure в Windows, macOS и Linux.
* [Работа с Python в Visual Studio (Windows)](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio)


[py_commit_batch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_create_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_get_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_or_replace_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_Entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?view=azure-python
[py_merge_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_update_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableService]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
