---
title: Руководство. Настройка 4ME для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в 4ME.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 423ba8c7aea9659a4c91f68a01392954c2ba6db2
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059178"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Учебник. Настройка 4ME для автоматической подготовки пользователей

Цель этого руководства — продемонстрировать шаги, которые необходимо выполнить в 4ME и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и групп в 4ME.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент 4ME](https://www.4me.com/trial/)
* Учетная запись пользователя в 4ME с разрешениями администратора.

## <a name="add-4me-from-the-gallery"></a>Добавление 4ME из коллекции

Перед настройкой 4ME для автоматической подготовки пользователей с помощью Azure AD необходимо добавить 4ME из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить 4ME из коллекции приложений Azure AD, выполните следующие действия.**

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **4ME**, выберите **4ME** на панели результатов и нажмите кнопку **добавить** , чтобы добавить это приложение.

    ![4me в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Назначение пользователей в 4ME

Azure Active Directory использует концепцию, называемую *назначениями* , чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, какие пользователи и (или) группы в Azure AD должны иметь доступ к 4ME. После принятия решения вы можете назначить этих пользователей и (или) группы для 4ME, следуя приведенным ниже инструкциям.

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Важные советы по назначению пользователей в 4ME

* Рекомендуется назначить одного пользователя Azure AD в 4ME для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в 4ME необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Настройка автоматической подготовки пользователей в 4ME 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в 4ME на основе назначений пользователей и групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для 4ME, следуя инструкциям, приведенным в [руководстве по единому входу 4ME](4me-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для 4ME в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **4me**.

    ![Ссылка на 4me в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. Чтобы получить **URL-адрес клиента** и **маркер секрета** учетной записи 4ME, выполните пошаговое руководство, как описано в шаге 6.

6. Войдите в консоль администратора 4ME. Перейдите к разделу **Параметры**.

    ![Параметры 4ME](media/4me-provisioning-tutorial/4me01.png)

    Введите в поле " **приложения** " в строке поиска.

    ![приложения 4ME](media/4me-provisioning-tutorial/4me02.png)

    Откройте раскрывающийся список **scim** , чтобы получить маркер секрета и конечную точку scim.

    ![4ME SCIM](media/4me-provisioning-tutorial/4me03.png)

7. После заполнения полей, показанных на шаге 5, нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к 4ME. Если подключение не выполняется, убедитесь, что у учетной записи 4ME есть разрешения администратора, и повторите попытку.

    ![Токен](common/provisioning-testconnection-tenanturltoken.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

9. Выберите команду **Сохранить**.

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с 4ME**.

    ![Сопоставления пользователей 4ME](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в 4ME в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в 4ME для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Сопоставления пользователей 4ME](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы с 4ME**.

    ![Сопоставления пользователей 4ME](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Проверьте атрибуты группы, которые синхронизированы из Azure AD в 4ME в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в 4ME для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Сопоставления групп 4ME](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Чтобы включить службу подготовки Azure AD для 4ME, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

16. Определите пользователей и (или) группы, которые вы хотите подготавливать к 4ME, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов по подготовке, в которых описаны все действия, выполняемые службой подготовки Azure AD в 4ME.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* 4ME имеет разные URL-адреса конечных точек SCIM для тестовых и рабочих сред. Первый заканчивается на **. qa** , а второй заканчивается на **. com**
* срок действия созданных секретных маркеров 4ME составляет месяц от поколения.
* 4ME не поддерживает операции **удаления**

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)