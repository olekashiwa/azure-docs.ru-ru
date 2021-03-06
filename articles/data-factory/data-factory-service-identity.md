---
title: Управляемое удостоверение для Фабрики данных
description: Сведения об управляемом удостоверении для фабрики данных Azure.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 45699680ad2003c034bce588857f8b102a0b6d26
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121800"
---
# <a name="managed-identity-for-data-factory"></a>Управляемое удостоверение для Фабрики данных

Эта статья поможет вам понять, что такое управляемое удостоверение для фабрики данных (прежнее название — Управляемое удостоверение службы/MSI) и как это работает.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Обзор

При создании фабрики данных можно создать управляемое удостоверение вместе с созданием фабрики. Управляемое удостоверение является управляемым приложением, зарегистрированным в Azure Active Directory, и представляет эту конкретную фабрику данных.

Управляемое удостоверение для фабрики данных предоставляет следующие возможности:

- [Храните учетные данные в Azure Key Vault](store-credentials-in-key-vault.md). в этом случае для Azure Key Vault проверки подлинности используется управляемое удостоверение фабрики данных.
- Соединители, включая [хранилище BLOB-объектов Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage 1-го поколения](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md), [Базу данных SQL Azure](connector-azure-sql-database.md) и [Хранилище данных SQL Azure](connector-azure-sql-data-warehouse.md).
- [Веб-действия](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Создать управляемое удостоверение

Управляемое удостоверение для фабрики данных создается следующим образом:

- При создании фабрики данных с помощью **портал Azure или PowerShell**управляемое удостоверение всегда будет создаваться автоматически.
- При создании фабрики данных с помощью **пакета SDK**управляемое удостоверение создается только в том случае, если в объекте фабрики будет указано "Identity = New FactoryIdentity ()" для создания. Пример см. в инструкциях по [созданию фабрики данных из краткого руководства по .NET](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- При создании фабрики данных с помощью **REST API**управляемое удостоверение будет создано только при указании раздела Identity в тексте запроса. Пример см. в инструкциях по [созданию фабрики данных из краткого руководства по REST](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Если у фабрики данных нет управляемого удостоверения, связанного после [получения управляемой](#retrieve-managed-identity) инструкции по идентификации, вы можете явно создать ее, обновив фабрику данных с помощью инициатора удостоверений программным путем.

- [Создание управляемого удостоверения с помощью PowerShell](#generate-managed-identity-using-powershell)
- [Создание управляемого удостоверения с помощью REST API](#generate-managed-identity-using-rest-api)
- [Создание управляемого удостоверения с помощью шаблона Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Создание управляемого удостоверения с помощью пакета SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Невозможно изменить управляемое удостоверение. Обновление фабрики данных, у которой уже есть управляемое удостоверение, не оказывает никакого влияния, управляемое удостоверение остается неизменным.
>- Если вы обновляете фабрику данных, которая уже имеет управляемое удостоверение, не указывая параметр "Identity" в объекте фабрики или не указывая раздел "Identity" в теле запроса, возникнет ошибка.
>- При удалении фабрики данных связанное управляемое удостоверение будет удалено вместе с.

### <a name="generate-managed-identity-using-powershell"></a>Создание управляемого удостоверения с помощью PowerShell

Вызовите команду **Set-AzDataFactoryV2** еще раз, после чего отобразятся только что созданные поля Identity:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Создание управляемого удостоверения с помощью REST API

Вызовите API с разделом identity в тексте запроса, как в примере ниже.

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Текст запроса**: add "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Ответ**. управляемое удостоверение создается автоматически, а раздел "Identity" заполняется соответствующим образом.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Создание управляемого удостоверения с помощью шаблона Azure Resource Manager

**Шаблон:** add "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Создание управляемого удостоверения с помощью пакета SDK

Вызовите функцию create_or_update фабрики данных с Identity=new FactoryIdentity(). Пример кода с использованием .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Получение управляемого удостоверения

Управляемое удостоверение можно получить из портал Azure или программно. В разделах ниже приведено несколько примеров.

>[!TIP]
> Если управляемое удостоверение не отображается, [Создайте управляемое удостоверение](#generate-managed-identity) , обновив фабрику.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Получение управляемого удостоверения с помощью портал Azure

Вы можете найти сведения об управляемом удостоверении из портал Azure-> свойств фабрики данных >.

- Идентификатор объекта управляемого идентификатора
- Управляемый клиент удостоверений
- Идентификатор приложения управляемого удостоверения

Сведения об управляемом удостоверении также будут отображаться при создании связанной службы, которая поддерживает проверку подлинности управляемого удостоверения, например, большой двоичный объект Azure, Azure Data Lake Storage, Azure Key Vault и т. д.

При предоставлении разрешения используйте идентификатор объекта или имя фабрики данных (в качестве имени управляемого удостоверения), чтобы найти это удостоверение.

### <a name="retrieve-managed-identity-using-powershell"></a>Получение управляемого удостоверения с помощью PowerShell

Идентификатор управляемого участника удостоверений и идентификатор клиента будут возвращены при получении определенной фабрики данных, как показано ниже. Используйте **PrincipalId** для предоставления доступа:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Идентификатор приложения можно получить, скопировав выше ИД участника, а затем выполните следующую команду Azure Active Directory с ИДЕНТИФИКАТОРом субъекта в качестве параметра.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Дальнейшие действия
В следующих разделах представлены сведения о том, когда и как использовать управляемое удостоверение фабрики данных.

- [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md)
- [Копирование данных в Azure Data Lake Storage Gen1 и из него с помощью фабрики данных Azure](connector-azure-data-lake-store.md)

Дополнительные сведения об управляемых удостоверениях для ресурсов Azure, на которых основан управляемый идентификатор фабрики данных, см. в статье [управляемые удостоверения для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview) . 
