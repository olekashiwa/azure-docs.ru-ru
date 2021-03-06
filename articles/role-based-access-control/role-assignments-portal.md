---
title: Добавление или удаление назначений ролей с помощью RBAC и портал Azure
description: Узнайте, как предоставить доступ к ресурсам Azure для пользователей, групп, субъектов-служб или управляемых удостоверений с помощью управления доступом на основе ролей (RBAC) Azure и портал Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e2458b07602bca3b6f12b2f486b509c11d705461
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138344"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Добавление и удаление назначений ролей с помощью Azure RBAC и портал Azure

в [!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] этой статье описывается назначение ролей с помощью портал Azure.

Если необходимо назначить роли администратора в Azure Active Directory, см. раздел [Просмотр и назначение ролей администратора в Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>предварительные требования

Для добавления или удаления назначений ролей необходимо иметь следующее:

- `Microsoft.Authorization/roleAssignments/write` и `Microsoft.Authorization/roleAssignments/delete`, такие как [Администратор доступа пользователей](built-in-roles.md#user-access-administrator) или [Владелец](built-in-roles.md#owner).

## <a name="access-control-iam"></a>Управление доступом (IAM)

**Управление доступом (IAM)** — это колонка, с помощью которой можно назначать роли для предоставления доступа к ресурсам Azure. Она также называется управлением удостоверениями и доступом и отображается в нескольких расположениях в портал Azure. Ниже показан пример колонки "Управление доступом (IAM)" для подписки.

![Колонка "Управление доступом (IAM)" для подписки](./media/role-assignments-portal/access-control-subscription.png)

Чтобы наиболее эффективно использовать колонку управления доступом (IAM), можно ответить на следующие три вопроса при попытке назначения роли:

1. **Кому нужен доступ?**

    Кто ссылается на пользователя, группу, субъект-службу или управляемое удостоверение. Это также называется *субъектом безопасности*.

1. **Какие роли требуются им?**

    Разрешения объединяются в роли. Вы можете выбрать из списка несколько [встроенных ролей](built-in-roles.md) или использовать собственные пользовательские роли.

1. **Где им нужен доступ?**

    Где относится к набору ресурсов, к которым применяется доступ. Где может быть группа управления, подписка, Группа ресурсов или один ресурс, например учетная запись хранения. Это называется *областью*.

## <a name="add-a-role-assignment"></a>Добавление назначения роли

В Azure RBAC для предоставления доступа к ресурсу Azure необходимо добавить назначение роли. Чтобы назначить роль, выполните следующие действия.

1. В портал Azure щелкните **все службы** , а затем выберите область, к которой требуется предоставить доступ. Например, можно выбрать **Группы управления**, **Подписки**, **Группы ресурсов** или конкретный ресурс.

1. Щелкните конкретный ресурс для этой области.

1. Щелкните **Управление доступом (IAM)** .

1. Перейдите на вкладку **назначения ролей** , чтобы просмотреть назначения ролей в этой области.

    ![Вкладка "Управление доступом (IAM) и назначение ролей"](./media/role-assignments-portal/role-assignments.png)

1. Нажмите кнопку **добавить** > **добавить назначение ролей**.

   Если у вас нет прав назначать роли, функция "Добавить назначение роли" будет неактивна.

   ![Меню "Добавить"](./media/role-assignments-portal/add-menu.png)

    Откроется область Добавление назначения ролей.

   ![Область "Добавить назначение ролей"](./media/role-assignments-portal/add-role-assignment.png)

1. В раскрывающемся списке **Роли** выберите роль, например **Участник виртуальных машин**.

1. В списке **Выбор** выберите пользователя, группу, субъект-службу или управляемое удостоверение. Если субъект безопасности не отображается в списке, введите его имя в поле **Выбор**, чтобы найти в каталоге отображаемые имена, адреса электронной почты и идентификаторы объектов.

1. Чтобы присвоить роль, щелкните **Сохранить**.

   Через несколько секунд субъекту безопасности будет назначена роль в выбранной области.

    ![Добавление назначения роли сохранено](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Предоставление пользователю прав администратора подписки

Чтобы предоставить пользователю права администратора подписки Azure, назначьте ему роль [Владелец](built-in-roles.md#owner) в этой подписке. Роль Владелец предоставляет пользователю полный доступ ко всем ресурсам в подписке, включая разрешение на предоставление доступа другим пользователям. Этот процесс не отличается от любой процедуры назначения ролей.

1. На портале Azure щелкните **Все службы**, а затем **Подписки**.

1. Щелкните подписку, для которой нужно предоставить доступ.

1. Щелкните **Управление доступом (IAM)** .

1. Перейдите на вкладку **назначения ролей** , чтобы просмотреть назначения ролей для этой подписки.

    ![Вкладка "Управление доступом (IAM) и назначение ролей"](./media/role-assignments-portal/role-assignments.png)

1. Нажмите кнопку **добавить** > **добавить назначение ролей**.

   Если у вас нет прав назначать роли, функция "Добавить назначение роли" будет неактивна.

   ![Меню "Добавить"](./media/role-assignments-portal/add-menu.png)

    Откроется область Добавление назначения ролей.

   ![Область "Добавить назначение ролей"](./media/role-assignments-portal/add-role-assignment.png)

1. В раскрывающемся списке **Роль** выберите роль **Владелец**.

1. В списке **Выбор** выберите пользователя. Если пользователь не отображается в списке, введите его имя в поле **Выбор**, чтобы найти в каталоге отображаемые имена и адреса электронной почты.

1. Чтобы присвоить роль, щелкните **Сохранить**.

   Через несколько секунд пользователю будет назначена роль владельца в области действия подписки.

## <a name="remove-a-role-assignment"></a>Удаление назначения ролей

В Azure RBAC для удаления доступа из ресурса Azure вы удаляете назначение роли. Выполните следующие действия, чтобы удалить назначение роли.

1. Откройте **Управление доступом (IAM)** для области, например группы управления, подписки, группы ресурсов или отдельного ресурса, чтобы заблокировать доступ.

1. Щелкните вкладку **Назначения ролей**, чтобы просмотреть все назначения ролей для этой подписки.

1. В списке назначений ролей добавьте флажок рядом с именем субъекта безопасности, для которого нужно удалить назначение ролей.

   ![Сообщение об удалении назначения роли](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Щелкните **Удалить**.

   ![Сообщение об удалении назначения роли](./media/role-assignments-portal/remove-role-assignment.png)

1. В появившемся сообщении об отзыве назначения роли щелкните **Да**.

    Если появится сообщение о том, что наследование назначений ролей не может быть удалено, вы пытаетесь удалить назначение роли в дочерней области. Следует открыть элемент управления доступом (IAM) в области, в которой была назначена роль, и повторить попытку. Чтобы быстро открыть элемент управления доступом (IAM) в нужной области, просмотрите столбец **область** и щелкните ссылку рядом с **(наследуется)** .

   ![Сообщение об удалении назначения роли](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Вывод списка назначений ролей с помощью Azure RBAC и портал Azure](role-assignments-list-portal.md)
- [Руководство. предоставление пользователю доступа к ресурсам Azure с помощью RBAC и портал Azure](quickstart-assign-role-user-portal.md)
- [Устранение неполадок RBAC в Azure](troubleshooting.md).
- [Упорядочение ресурсов с помощью групп управления Azure](../governance/management-groups/overview.md)
