---
title: Подготовка пропускной способности контейнера в Azure Cosmos DB
description: Узнайте, как подготавливать пропускную способность на уровне контейнера в Azure Cosmos DB с помощью портал Azure, CLI, PowerShell и других пакетов SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: bfd18a009ed9ab5edffafcd0327b1d846cae02d8
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75643854"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Подготовка пропускной способности для контейнера Azure Cosmos

Узнайте, как подготовить пропускную способность для контейнера (коллекции, графа или таблицы) в Azure Cosmos DB. Вы можете подготовить пропускную способность для одного контейнера или [базы данных](how-to-provision-database-throughput.md) и предоставить к ней общий доступ для контейнеров в пределах этой базы данных. Пропускную способность для контейнера можно подготовить с помощью портала Azure, Azure CLI или пакетов SDK Azure Cosmos DB.

## <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Azure Cosmos](create-sql-api-dotnet.md#create-account) или выберите существующую.

1. Откройте панель **Обозреватель данных** и выберите **Новая коллекция**. После этого предоставьте следующие сведения.

   * Укажите, создаете ли вы новую базу данных или используете существующую.
   * Укажите идентификатор контейнера (таблицы или графа).
   * Введите значение ключа секции (например, `/userid`).
   * Укажите подготавливаемую пропускную способность (например, 1000 ЕЗ/с).
   * Нажмите кнопку **ОК**.

    ![Снимок экрана обозревателя данных с выделенным элементом "Новая коллекция"](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI или PowerShell

Чтобы создать контейнер с выделенной пропускной способностью, см.

* [Создание контейнера с помощью Azure CLI](manage-with-cli.md#create-a-container)
* [Создание контейнера с помощью PowerShell](manage-with-powershell.md#create-container)

> [!Note]
> При подготовке пропускной способности для учетной записи Azure Cosmos, настроенной с помощью API Azure Cosmos DB для MongoDB, используйте `/myShardKey` для пути к ключу секции. При подготовке пропускной способности для контейнера в учетной записи Azure Cosmos, настроенной с помощью API Cassandra, используйте `/myPrimaryKey` для пути к ключу секции.

## <a name="net-sdk"></a>SDK .NET

> [!Note]
> Используйте пакеты SDK Cosmos для API SQL для подготовки пропускной способности для всех API Cosmos DB, кроме API Cassandra.

### <a id="dotnet-most"></a>Интерфейсы API SQL, MongoDB, Gremlin и API таблиц
### <a name="net-v2-sdk"></a>Пакет SDK для .Net версии 2

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>Пакет SDK для .Net версии 3
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

## <a name="javascript-sdk"></a>Пакет SDK JavaScript

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a id="dotnet-cassandra"></a>API Cassandra

Аналогичные команды могут выдаваться любым драйвером, совместимым с CQL.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Изменение или изменение пропускной способности для таблицы Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать о подготовке пропускной способности в Cosmos DB, обратитесь к следующим статьям:

* [Подготовка пропускной способности для базы данных](how-to-provision-database-throughput.md)
* [Пропускная способность и единицы запросов в Azure Cosmos DB](request-units.md)
