---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 10/07/2019
ms.author: orspodek
ms.openlocfilehash: 3cee966f4d7851415e5ae17faf4f3fafce434cae
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031556"
---
## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить подключение к данным, используйте следующую команду:

```csharp
kustoManagementClient.DataConnections.Delete(resourceGroupName, clusterName, databaseName, dataConnectionName);
```