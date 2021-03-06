---
title: Руководство по интеграции Azure Active Directory с Sage Intacct | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Sage Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28390dba009226d493f5bfc6a5270b067f5bba0
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "73570469"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Руководство по интеграции Sage Intacct с Azure Active Directory

В этом руководстве описано, как интегрировать Sage Intacct с Azure Active Directory (Azure AD). Интеграция Sage Intacct с Azure AD обеспечивает следующие возможности.

* С помощью Azure AD вы можете контролировать доступ к Sage Intacct.
* Вы можете включить автоматический вход пользователей в Sage Intacct с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Sage Intacct с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Sage Intacct поддерживает единый вход, инициированный **поставщиком удостоверений**.

## <a name="adding-sage-intacct-from-the-gallery"></a>Добавление Sage Intacct из коллекции

Чтобы настроить интеграцию Sage Intacct с Azure AD, необходимо добавить Sage Intacct из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Sage Intacct**.
1. Выберите **Sage Intacct** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sage-intacct"></a>Настройка и проверка единого входа в Azure AD для Sage Intacct

Настройте и проверьте единый вход Azure AD в Sage Intacct с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Sage Intacct.

Чтобы настроить и проверить единый вход Azure AD в Sage Intacct, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
2. **[Настройка единого входа в Sage Intacct](#configure-sage-intacct-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Sage Intacct](#create-sage-intacct-test-user)** требуется для создания пользователя B.Simon в Sage Intacct, связанного с соответствующим представлением в Azure AD.
6. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Sage Intacct** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    В текстовом поле **URL-адрес ответа** введите URL-адрес: `https://www.intacct.com/ia/acct/sso_response.phtml`

1. Приложение Sage Intacct ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию. Щелкните значок **Изменить**, чтобы открыть диалоговое окно "Атрибуты пользователя".

    ![image](common/edit-attribute.png)

1. В дополнение к описанному выше приложение Sage Intacct ожидает несколько дополнительных атрибутов в ответе SAML. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** выполните следующие действия, чтобы добавить атрибут токена SAML, как показано в приведенной ниже таблице.

    | ИМЯ  |  Исходный атрибут|
    | ---------------| --------------- |
    | Название организации | **Идентификатор компании Sage Intacct** |
    | name | Значение должно совпадать со значением **идентификатора клиента** Sage Intacct, которое вы вводите в разделе **Создание тестового пользователя Sage Intacct**, что объясняется в руководстве позже. |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | Значение должно совпадать со значением **идентификатора клиента федеративного единого входа** Sage Intacct, которое вы вводите в разделе **Создание тестового пользователя Sage Intacct**, что объясняется в руководстве позже. |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Требуемые URL-адреса можно скопировать из раздела **Настройка Sage Intacct**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к Sage Intacct.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **Sage Intacct**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-sage-intacct-sso"></a>Настройка единого входа Sage Intacct

1. В другом окне веб-браузера войдите на корпоративный сайт Sage Intacct в качестве администратора.

1. Откройте вкладку **Company** (Компания) и выберите **Company Info** (Сведения о компании).

    ![Организация](./media/intacct-tutorial/ic790037.png "Организация")

1. Откройте вкладку **Security** (Безопасность) и нажмите кнопку **Edit** (Изменить).

    ![Безопасность](./media/intacct-tutorial/ic790038.png "Безопасность")

1. В разделе **Единый вход** сделайте следующее:

    ![Единый вход](./media/intacct-tutorial/ic790039.png "Единый вход")

    a. Установите флажок **Включить единый вход**.

    b. Выберите для параметра **Identity provider type** (Тип поставщика удостоверений) значение **SAML 2.0**.

    c. В текстовое поле **Issuer URL** (URL-адрес издателя) вставьте **идентификатор Azure AD**, скопированный на портале Azure.

    d. В текстовое поле **URL-адрес входа** вставьте значение **URL-адреса входа**, скопированное с портала Azure.

    д. Откройте в блокноте **сертификат в кодировке Base-64**, скопируйте его содержимое в буфер обмена, а затем вставьте его в поле **Сертификат**.

    Е. Выберите команду **Сохранить**.

### <a name="create-sage-intacct-test-user"></a>Создание тестового пользователя Sage Intacct

Чтобы пользователи Azure AD могли входить в Sage Intacct, их необходимо подготовить для Sage Intacct. Эта подготовка для Sage Intacct выполняется вручную.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Войдите в клиент **Sage Intacct**.

1. Откройте вкладку **Company** (Компания) и выберите **Users** (Пользователи).

    ![Пользователи](./media/intacct-tutorial/ic790041.png "Пользователи")

1. Откройте вкладку **Добавить**.

    ![Добавление](./media/intacct-tutorial/ic790042.png "Добавление")

1. В разделе **Информация о пользователе** сделайте следующее:

    ![Сведения о пользователе](./media/intacct-tutorial/ic790043.png "Сведения о пользователе")

    a. В разделе **User Information** (Сведения пользователя) введите значения **User ID** (Идентификатор пользователя), **Last Name** (Фамилия), **First name** (Имя), **Email address** (Адрес электронной почты), **Title** (Обращение) и **Phone** (Телефон) учетной записи Azure AD, которую вы хотите подготовить.

    > [!NOTE]
    > Убедитесь, что **идентификатор пользователя** на снимке экрана и значение **исходного атрибута**, сопоставленное с атрибутом **имя** в разделе **Атрибуты пользователя** на портале Azure совпадают.

    b. Выберите параметр **Полномочия администратора** для учетной записи Azure AD, которую требуется подготовить.

    c. Выберите команду **Сохранить**. 
    
    d. Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи.

1. Щелкните вкладку **Единый вход** и убедитесь, что **идентификатор пользователя федеративного единого входа** на приведенном ниже снимке экрана, и **исходный атрибут**, сопоставленный с `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` в разделе **Атрибуты пользователя** на портале Azure совпадают.

    ![Сведения о пользователе](./media/intacct-tutorial/ic790044.png "Сведения о пользователе")

> [!NOTE]
> Для подготовки учетных записей пользователя Azure AD можно использовать любые другие средства создания учетной записи пользователя Sage Intacct или API, предоставляемые Sage Intacct.

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Sage Intacct на Панели доступа, вы автоматически войдете в приложение Sage Intacct, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

