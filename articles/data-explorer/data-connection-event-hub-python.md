---
title: Создание подключения к данным концентратора событий для Azure обозреватель данных с помощью Python
description: Из этой статьи вы узнаете, как создать подключение к данным концентратора событий для Azure обозреватель данных с помощью Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 81aded7639cc0bed86c3d3ab3be9e6ef7b355734
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964538"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Создание подключения к данным концентратора событий для Azure обозреватель данных с помощью Python

> [!div class="op_single_selector"]
> * [Портал](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Шаблон Azure Resource Manager](data-connection-event-hub-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Azure обозреватель данных обеспечивает прием (загрузку данных) из концентраторов событий, центров Интернета вещей и больших двоичных объектов, записанных в контейнеры больших двоичных объектов. В этой статье вы создадите подключение к данным концентратора событий для Azure обозреватель данных с помощью Python.

## <a name="prerequisites"></a>Технические условия

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Создание [кластера и базы данных](create-cluster-database-python.md)
* Создание [сопоставления таблиц и столбцов](python-ingest-data.md#create-a-table-on-your-cluster)
* Задание [политик базы данных и таблиц](database-table-policies-python.md) (необязательно)
* Создание [концентратора событий с данными для приема](ingest-data-event-hub.md#create-an-event-hub). 

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Добавление подключения к данным концентратора событий

В следующем примере показано, как добавить подключение к данным концентратора событий программным способом. Дополнительные сведения о добавлении подключения к данным концентратора событий с помощью портал Azure см. в разделе [Подключение к концентратору событий](ingest-data-event-hub.md#connect-to-the-event-hub) .

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Параметр** | **Рекомендуемое значение** | **Описание поля**|
|---|---|---|
| tenant_id | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscriptionId | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор подписки, используемый для создания ресурсов.|
| client_id | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор клиента приложения, которое может получать доступ к ресурсам в клиенте.|
| client_secret | *кскскскскскскскскскскскскскс* | Секрет клиента приложения, которое может получить доступ к ресурсам в клиенте. |
| resource_group_name | *testrg* | Имя группы ресурсов, содержащей кластер.|
| cluster_name | *mykustocluster* | Имя кластера.|
| database_name | *mykustodatabase* | Имя целевой базы данных в кластере.|
| data_connection_name | *мевенсубконнект* | Требуемое имя подключения к данным.|
| имя_таблицы | *стормевентс* | Имя целевой таблицы в целевой базе данных.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Имя сопоставления столбцов, связанного с целевой таблицей.|
| data_format | *-* | Формат данных сообщения.|
| event_hub_resource_id | *Идентификатор ресурса* | Идентификатор ресурса концентратора событий, который содержит данные для приема. |
| consumer_group | *$Default* | Группа потребителей концентратора событий.|
| location | *Центральная часть США* | Расположение ресурса подключения к данным.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]