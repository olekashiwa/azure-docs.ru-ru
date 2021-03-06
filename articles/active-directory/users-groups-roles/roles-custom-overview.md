---
title: Роли настраиваемого администратора в Azure Active Directory | Документация Майкрософт
description: Предварительный просмотр пользовательских ролей Azure AD для делегирования управления удостоверениями. Управляйте ролями Azure в портал Azure, PowerShell или API Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5c7919dcc89e34831cb4cae7921b60b35eb4c69
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024967"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Роли настраиваемого администратора в Azure Active Directory (Предварительная версия)

В этой статье описывается, как определить пользовательские роли Azure AD в Azure Active Directory (Azure AD) с помощью управления доступом на основе ролей и областей ресурсов. Пользовательские роли Azure AD имеют базовые разрешения [встроенных ролей](directory-assign-admin-roles.md), позволяющие создавать и упорядочивать собственные пользовательские роли. Такой подход позволяет предоставить доступ более детализированным способом, чем встроенные роли, когда они понадобятся. В первом выпуске настраиваемых ролей Azure AD можно создать роль для назначения разрешений на Управление регистрацией приложений. Со временем будут добавлены дополнительные разрешения для ресурсов Организации, таких как корпоративные приложения, пользователи и устройства.  

Кроме того, пользовательские роли Azure AD поддерживают назначения для каждого ресурса в дополнение к более традиционным назначениям в рамках всей Организации. Такой подход дает возможность предоставлять доступ для управления некоторыми ресурсами (например, одной регистрацией приложения), не предоставляя доступ ко всем ресурсам (все регистрации приложений).

Управление доступом на основе ролей Azure AD — это общедоступная Предварительная версия Azure AD. она доступна с любым платным планом лицензирования Azure AD. См. подробные сведения о [дополнительных условиях использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Общие сведения об управлении доступом на основе ролей в Azure AD

Предоставление разрешений с помощью пользовательских ролей Azure AD — это двухэтапный процесс, включающий создание пользовательского определения роли и последующее его назначение с помощью назначения ролей. Определение пользовательской роли — это коллекция разрешений, добавляемых в предустановленный список. Эти разрешения являются теми же разрешениями, которые используются во встроенных ролях.  

После создания определения роли его можно назначить пользователю, создав назначение роли. Назначение роли предоставляет пользователю разрешения в определении роли в указанной области. Этот двухэтапный процесс позволяет создать одно определение роли и назначить его несколько раз в разных областях. Область определяет набор ресурсов Azure AD, к которым у члена роли есть доступ. Наиболее распространенной областью является область в масштабе всей Организации. Пользовательская роль может быть назначена в области организации, то есть ее роль имеет разрешения для всех ресурсов в Организации. Пользовательская роль также может быть назначена в области объекта. Примером области объекта может быть одно приложение. Одну и ту же роль можно назначить одному пользователю для всех приложений в Организации, а затем другому пользователю с областью только приложения "отчеты о расходах Contoso".  

Встроенные и пользовательские роли Azure AD работают с такими концепциями, как [Управление доступом на основе ролей в Azure](../../role-based-access-control/overview.md). [Различие между этими двумя системами управления доступом на основе ролей](../../role-based-access-control/rbac-and-directory-admin-roles.md) заключается в том, что Azure RBAC управляет доступом к ресурсам Azure, таким как виртуальные машины или хранилище, с помощью управления ресурсами Azure, а пользовательские роли Azure AD управляют доступом к ресурсам Azure AD с помощью API Graph. Обе системы используют концепцию определений ролей и назначений ролей.

### <a name="role-assignments"></a>Назначения ролей

Назначение роли — это объект, который прикрепляет определение роли к пользователю в определенной области для предоставления доступа к ресурсам Azure AD. Доступ предоставляется путем создания назначения ролей, а отзывается путем его удаления. По сути, назначение роли состоит из трех элементов:

- Пользователь (лицо, имеющее профиль пользователя в Azure Active Directory)
- Определение роли
- Область действия ресурса

[Назначения ролей можно создать](roles-create-custom.md) с помощью портал Azure, Azure AD PowerShell или API Graph. Можно также [просмотреть назначения для пользовательской роли](roles-view-assignments.md#view-the-assignments-of-a-role).

На приведенной ниже схеме показан пример назначения ролей. В этом примере Олегу «зеленый» назначена настраиваемая роль администратора регистрации приложений в области регистрации приложения в построителе Contoso Widget. Назначение предоставляет ресурсу права администратора регистрации приложений только для данной регистрации конкретного приложения.

![Назначение ролей заключается в том, как применяются разрешения и состоит из трех частей.](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Субъект безопасности

Субъект безопасности представляет пользователя, которому будет назначен доступ к ресурсам Azure AD. Пользователь является отдельным *пользователем* , у которого есть профиль пользователя в Azure Active Directory.

### <a name="role"></a>Роль

Определение роли, или роль, представляет собой набор разрешений. В определении роли перечислены операции, которые можно выполнять с ресурсами Azure AD, такие как создание, чтение, обновление и удаление. В Azure AD существует два типа ролей:

- Встроенные роли, созданные корпорацией Майкрософт, которые невозможно изменить.
- Настраиваемые роли, созданные и управляемые вашей организацией.

### <a name="scope"></a>Область

Область — это ограничение разрешенных действий для конкретного ресурса Azure AD в рамках назначения роли. При назначении роли можно указать область, которая ограничивает доступ администратора к определенному ресурсу. Например, если вы хотите предоставить разработчику настраиваемую роль, но только для управления регистрацией конкретного приложения, можно включить регистрацию конкретного приложения в качестве области назначения роли.

  > [!Note]
  > Пользовательские роли можно назначать в области действия каталога и ресурса. Они еще не могут быть назначены в области административной единицы.
  > Встроенные роли можно назначать в области каталога, а в некоторых случаях — в области административной единицы. Они еще не могут быть назначены в области ресурсов Azure AD.

## <a name="required-license-plan"></a>Требуемый план лицензирования

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Дополнительная информация

- Создание настраиваемых назначений ролей с помощью [портал Azure, Azure AD PowerShell и API Graph](roles-create-custom.md)
- [Просмотр назначений для пользовательской роли](roles-view-assignments.md#view-assignments-of-single-application-scope)
