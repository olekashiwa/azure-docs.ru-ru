---
title: Создание политик с помощью Azure обозреватель данных C# SDK
description: В этой статье вы узнаете, как создавать политики с помощью C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667295"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Создание политик базы данных и таблиц для обозреватель данных Azure с помощьюC#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. В этой статье вы создадите политики базы данных и таблиц для обозреватель данных Azure с помощью C#.

## <a name="prerequisites"></a>Технические условия

* Visual Studio 2019. Если у вас нет Visual Studio 2019, вы можете скачать и использовать *бесплатную* версию [visual Studio Community 2019](https://www.visualstudio.com/downloads/). Не забудьте выбрать **разработку Azure** во время установки Visual Studio.
* Подписка Azure. Если вам нужно, можно создать [бесплатную учетную запись Azure](https://azure.microsoft.com/free/) перед началом работы.
* [Тестовый кластер и база данных](create-cluster-database-csharp.md).
* [Тестовая таблица](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>Установка C# NuGet

* Установите [пакет NuGet для Azure обозреватель данных (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Установите [пакет NuGet Microsoft. Azure. Kusto. Data. NETStandard](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Необязательно, для изменения политик таблиц.)
* Установите [пакет NuGet Microsoft. IdentityModel. Clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)для проверки подлинности.

## <a name="authentication"></a>Authentication
Для выполнения примеров в этой статье вам потребуется приложение Azure Active Directory (Azure AD) и субъект-служба, которые могут получить доступ к ресурсам. Вы можете использовать одно и то же приложение Azure AD для проверки подлинности из [тестового кластера и базы данных](create-cluster-database-csharp.md#authentication). Если вы хотите использовать другое приложение Azure AD, см. раздел [Создание приложения Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) для создания бесплатного приложения Azure AD и Добавление назначения роли в области подписки. В этой статье также показано, как получить `Directory (tenant) ID`, `Application ID`и `Client secret`. Может потребоваться добавить новое приложение Azure AD в качестве участника в базу данных. Дополнительные сведения см. в статье [Управление разрешениями для базы данных Azure обозреватель данных](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions).

## <a name="alter-database-retention-policy"></a>Изменение политики хранения базы данных
Задает политику хранения с 10-дневным периодом обратимого удаления.
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Изменение политики кэша базы данных
Задает политику кэширования для базы данных. Данные за последние пять дней будут находиться на SSD кластера.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>Политика изменения кэша таблиц
Задает политику кэширования для таблицы. Данные за последние пять дней будут находиться на SSD кластера.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>Добавление нового участника для базы данных
Добавляет новое приложение Azure AD в качестве участника администратора для базы данных.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>Дальнейшие действия

* [Дополнительные сведения о политиках баз данных и таблиц](https://docs.microsoft.com/azure/kusto/management/policies)
