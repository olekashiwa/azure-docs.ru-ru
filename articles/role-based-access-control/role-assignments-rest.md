---
title: Добавление или удаление назначений ролей с помощью RBAC и REST API
description: Узнайте, как предоставить доступ к ресурсам Azure для пользователей, групп, субъектов-служб или управляемых удостоверений с помощью управления доступом на основе ролей (RBAC) Azure и REST API.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ae6fce5ab962637fe477ade75cf81b6ac237bdd2
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138312"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Добавление и удаление назначений ролей с помощью Azure RBAC и REST API

в [!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] этой статье описывается назначение ролей с помощью REST API.

## <a name="prerequisites"></a>предварительные требования

Для добавления или удаления назначений ролей необходимо иметь следующее:

- `Microsoft.Authorization/roleAssignments/write` и `Microsoft.Authorization/roleAssignments/delete`, такие как [Администратор доступа пользователей](built-in-roles.md#user-access-administrator) или [Владелец](built-in-roles.md#owner).

## <a name="add-a-role-assignment"></a>Добавление назначения роли

В RBAC для предоставления доступа необходимо добавить назначение роли. Чтобы добавить назначение ролей, используйте [назначение ролей — создайте](/rest/api/authorization/roleassignments/create) REST API и укажите субъект безопасности, определение роли и область. Чтобы вызвать этот API, нужен доступ к операции `Microsoft.Authorization/roleAssignments/write`. Из встроенных ролей эту операцию могут выполнять только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator).

1. Используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list) или изучите раздел [Встроенные роли](built-in-roles.md), чтобы получить идентификатор для определения роли, которое требуется назначить.

1. Используйте средство GUID для создания уникального идентификатора, который будет использоваться для назначения роли. Этот идентификатор имеет следующий формат: `00000000-0000-0000-0000-000000000000`

1. Можете начать со следующего запроса и основного текста:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Внутри URI замените *{scope}* областью для назначения роли.

    | Область | Тип |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | группа ресурсов. |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Ресурс |

1. Замените *{roleAssignmentName}* идентификатором GUID для назначения роли.

1. В тексте запроса замените *{Scope}* областью для назначения роли.

    | Область | Тип |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | группа ресурсов. |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Ресурс |

1. Замените *{roleDefinitionId}* идентификатором определения роли.

1. Замените *{principalId}* идентификатором объекта для пользователя, группы или субъекта-службы, которым будет назначена роль.

## <a name="remove-a-role-assignment"></a>Удаление назначения ролей

В RBAC, чтобы удалить доступ, нужно удалить назначение роли. Чтобы удалить назначение роли, используйте REST API [Назначения ролей — удаление](/rest/api/authorization/roleassignments/delete). Чтобы вызвать этот API, нужен доступ к операции `Microsoft.Authorization/roleAssignments/delete`. Из встроенных ролей эту операцию могут выполнять только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator).

1. Получите идентификатор (GUID) для назначения роли. Его можно получить при первом создании назначения роли, а также при перечислении назначений ролей.

1. Можете начать со следующего запроса:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Внутри URI замените *{scope}* областью для удаления назначения роли.

    | Область | Тип |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | группа ресурсов. |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Ресурс |

1. Замените *{roleAssignmentName}* идентификатором GUID для назначения роли.

## <a name="next-steps"></a>Дальнейшие действия

- [Вывод списка назначений ролей с помощью Azure RBAC и REST API](role-assignments-list-rest.md)
- [Развертывание ресурсов с использованием шаблонов и REST API Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
- [Справочник по REST API Azure](/rest/api/azure/)
- [Создание пользовательских ролей для ресурсов Azure с помощью REST API](custom-roles-rest.md)
