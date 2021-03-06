---
title: Различия между MSAL.js и ADAL.js | Azure
titleSuffix: Microsoft identity platform
description: Из этой статьи вы узнаете о различиях между библиотекой аутентификации Майкрософт для JavaScript (MSAL.js) и библиотекой аутентификации Azure AD для JavaScript (ADAL.js), а также о критериях выбора одной из них.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 7238a78279528b4522d09178d00bf916f14bad88
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696424"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Различия между ADAL и MSAL для JavaScript

Как MSAL.js, так и ADAL.js используются для аутентификации сущностей Azure AD и маркеров запроса из Azure AD. До настоящего времени большинство разработчиков использовали для аутентификации удостоверений Azure AD (рабочих и учебных учетных записей) платформу Azure AD для разработчиков (версия 1.0), запрашивая маркеры через ADAL. Теперь с помощью MSAL.js вы можете выполнять проверку подлинности для более широкого набора удостоверений Майкрософт (удостоверения Azure AD, учетные записи Майкрософт и социальных сетей, а также локальные учетные записи в сочетании с Azure AD B2C) с помощью платформы удостоверений Майкрософт (версии 2.0).

Эта статья описывает алгоритм выбора между библиотекой аутентификации Майкрософт для JavaScript (MSAL.js) и библиотекой аутентификации Azure AD для JavaScript (ADAL.js), а также дает их сравнительный анализ.

## <a name="choosing-between-adaljs-and-msaljs"></a>Выбор между ADAL.js и MSAL.js

В большинстве случаев лучше использовать платформу удостоверений Майкрософт и MSAL.js, так как это последнее поколение библиотек проверки подлинности Майкрософт. Используя MSAL.js, вы получаете маркеры для пользователей, входящих в приложение с учетной записью Azure AD (рабочей или учебной), учетной записью Майкрософт (личной MSA) или через Azure AD B2C.

Если вы уже знакомы с конечной точкой версии 1.0 и библиотекой ADAL.js, ознакомьтесь со статьей [об отличиях конечной точки версии 2.0](active-directory-v2-compare.md).

Но ADAL.js пока остается обязательной для тех случаев, когда приложению нужно обрабатывать вход пользователей из более ранних версий [служб федерации Active Directory (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Основные различия в процессах проверки подлинности с помощью MSAL.js

### <a name="core-api"></a>Core API

* ADAL.js с помощью [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) предоставляет серверу авторизации или поставщику удостоверений экземпляр подключения, который использует приложение, через URL-адрес центра сертификации. С другой стороны, API MSAL.js основан на клиентском приложении агента пользователя (один из вариантов общедоступного клиентского приложения, которое выполняет код клиента через агент пользователя, например обычный веб-браузер). Он предоставляет класс `UserAgentApplication` для передачи серверу авторизации экземпляра контекста проверки подлинности, используемого в приложении. Дополнительные сведения см. в статье [об инициализации с помощью MSAL.js](msal-js-initializing-client-applications.md).

* В ADAL.js методы для получения маркеров связаны с одним центром сертификации, который задан в `AuthenticationContext`. В MSAL.js запросы получения маркеров могут использовать другие центры сертификации, отличные от значения `UserAgentApplication`. Это позволяет MSAL.js получать и кэшировать в одном приложении маркеры отдельно для нескольких клиентов и учетных записей пользователей.

* Метод автоматического получения и обновления маркеров без направления запроса пользователю в ADAL.js называется `acquireToken`. В MSAL.js этот метод называется `acquireTokenSilent`, что лучше описывает эту функцию.

### <a name="authority-value-common"></a>Значение центра сертификации `common`

Если вы используете центр `https://login.microsoftonline.com/common` в версии 1.0, пользователи могут входить с любой учетной записью AAD (любой организации).

Если вы используете центр `https://login.microsoftonline.com/common` в версии 2.0, пользователи могут входить с любой корпоративной учетной записью (AAD) или личной учетной записью Майкрософт (MSA). Чтобы ограничить вход только учетными записями Azure AD (поведение, аналогичное ADAL.js), необходимо использовать `https://login.microsoftonline.com/organizations`. Дополнительные сведения см. в описании параметра конфигурации `authority` в статье [об инициализации в MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Области для получения маркеров
* Использование области вместо параметра ресурса в запросах проверки подлинности для получения маркеров

    В запросах протокола версии 2.0 вместо ресурсов используются области. Другими словами, когда приложению нужно запросить маркер с разрешениями для доступа к ресурсу, например к Microsoft Graph, методы разных библиотек передают разные значения, а именно:

    Версия 1.0: resource = https\://graph.microsoft.com

    Версия 2.0: scope = https\://graph.microsoft.com/User.Read

    Вы можете запросить области для любого API ресурсов, используя URI API-интерфейса в формате "URI_приложения/область", например https:\//mytenant.onmicrosoft.com/myapi/api.read.

    Только для MS API Graph, значение области `user.read` сопоставляется с HTTPS:\//graph.microsoft.com/User.Read и может использоваться взаимозаменяемы.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Динамические области для добавочного согласия

    При создании приложений с использованием версии 1.0 нужно было регистрировать полный набор необходимых приложению разрешений (статические области), чтобы пользователь подтвердил согласие во время входа. В версии 2.0 можно использовать параметр области, чтобы запросить разрешения в тот момент, когда они нужны. Такой механизм называется динамическими областями. Он позволяет пользователю предоставлять добавочное согласие для областей. Таким образом, в начале работы вы можете позволить пользователю просто войти в приложение без запроса прав доступа, если они пока не нужны. Позже, когда потребуется возможность чтения календаря пользователя, вы укажете область календаря в методе получения маркера и получите согласие пользователя. Пример.

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Области для API версии 1.0

    При получении маркеров для API версии 1.0 через библиотеку MSAL.js вы можете запросить все статические области, зарегистрированные для этого API, добавив область `.default` к URI идентификатора приложения API. Пример.

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о сравнении версий 1.0 и 2.0 см. [здесь](active-directory-v2-compare.md).
