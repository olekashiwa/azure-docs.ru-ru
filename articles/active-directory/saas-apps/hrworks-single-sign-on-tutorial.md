---
title: Руководство. Интеграция единого входа Azure Active Directory с HRworks Single Sign-On | Документация Майкрософт
description: Из этой статьи вы узнаете, как настроить единый вход Azure Active Directory в HRworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b20ce5f754333aec78513e32901b0608f8bee3b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638771"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Руководство. Интеграция единого входа Azure Active Directory с HRworks Single Sign-On

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с HRworks Single Sign-On для включения единого входа. Интеграция HRworks Single Sign-On с Azure AD обеспечивает следующие возможности.

* С помощью Azure AD вы можете контролировать доступ к HRworks Single Sign-On.
* Можно включить автоматический вход пользователей в HRworks Single Sign-On с учетными записями AAD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD; Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* подписка HRworks Single Sign-On с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Приложение HRworks поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Добавление HRworks из коллекции

Чтобы настроить интеграцию Azure AD с HRworks для включения единого входа, вам необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **HRworks Single Sign-On**.
1. Выберите **HRworks Single Sign-On** в области результатов и добавьте приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Настройка и проверка единого входа Azure AD для HRworks Single Sign-On

Настройте и проверьте единый вход Azure AD в HRworks Single Sign-On с помощью тестового пользователя **B.Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в HRworks Single Sign-On.

Чтобы настроить и проверить единый вход Azure AD в HRworks Single Sign-On, выполните инструкции ниже.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в HRworks Single Sign-On](#configure-hrworks-single-sign-on-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя в HRworks Single Sign-On](#create-hrworks-single-sign-on-test-user)** требуется для того, чтобы в HRworks Single Sign-On существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **HRworks Single Sign-On** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`.

    > [!NOTE]
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов HRworks](mailto:nadja.sommerfeld@hrworks.de). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и выберите **Скачать**, чтобы скачать сертификат и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Требуемый URL-адрес можно скопировать из раздела о **настройке HRworks Single Sign-On**.

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

В этом разделе описано, как разрешить пользователю B.Simon использовать единый вход Azure, предоставив доступ к HRworks Single Sign-On.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **Единый вход в HRworks**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-hrworks-single-sign-on-sso"></a>Настройка единого входа в HRworks Single Sign-On

1. Чтобы автоматизировать настройку HRworks Single Sign-On, установите **Расширение браузера "Безопасный вход в мои приложения"** , щелкнув **Установить расширение**.

    ![Расширение "Мои приложения"](common/install-myappssecure-extension.png)

1. Чтобы перейти к приложению HRworks Single Sign-On после добавления расширения в браузер, щелкните **Настройка HRworks Single Sign-On**. В открывшемся окне укажите учетные данные администратора для входа в HRworks Single Sign-On. Расширение браузера автоматически настроит приложение и автоматизирует шаги 3–4.

    ![Настройка конфигурации](common/setup-sso.png)

1. Если необходимо вручную настроить HRworks Single Sign-On, откройте новое окно веб-браузера, зайдите на корпоративный сайт HRworks Single Sign-On с правами администратора и выполните следующие действия.

1. Выберите **Administrator (Администратор)**  > **Basics (Основы)**  > **Security (Безопасность)**  > **Single Sign-on (Единый вход)** в левой части строки меню и выполните следующие шаги:

    ![Настройка единого входа](./media/hrworks-single-sign-on-tutorial/configure01.png)

    а. Установите флажок **Use Single Sign-on** (Использовать единый вход).

    b. Выберите значение **XML Metadata** (Метаданные XML) для параметра **Meta data input method** (Метод ввода метаданных).

    c. Выберите значение **Individual NameID identifier** (Индивидуальный идентификатор NameID) для параметра **Value for NameID** (Значение NameID).

    d. В Блокноте откройте скачанный с портала Azure XML-файл метаданных, скопируйте его содержимое, а затем вставьте его в текстовое поле **Metadata** (Метаданные).

    д) Выберите команду **Сохранить**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Создание тестового пользователя для HRworks для включения единого входа

Чтобы пользователи Azure AD могли выполнять вход в HRworks (единый вход), они должны быть подготовлены в нем. В HRworks подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в HRworks с правами администратора.

1. Выберите **Administrator (Администратор)**  > **Persons (Лица)**  > **Persons (Лица)**  > **New person (Создать лицо)** в левой части строки меню.

     ![Настройка единого входа](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. Во всплывающем окне нажмите **Next** (Далее).

    ![Настройка единого входа](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. Во всплывающем элементе **Create new person with country for legal terms** (Создание нового лица с указанием страны для соответствия юридическим условиям) введите нужные данные, например **First name** (Имя), **Last name** (Фамилия), и нажмите **Create** (Создать).

    ![Настройка единого входа](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку HRworks на панели доступа, вы автоматически войдете в HRworks, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте работу HRworks Single Sign-On с Azure Active Directory](https://aad.portal.azure.com/)