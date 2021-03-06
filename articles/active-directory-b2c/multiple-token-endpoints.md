---
title: Миграция веб-API на основе OWIN в b2clogin.com
titleSuffix: Azure AD B2C
description: Узнайте, как включить поддержку маркеров, выдаваемых несколькими поставщиками маркеров, в веб-API .NET при переносе приложений в b2clogin.com.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e6a92d12e7f2f24289aafa7e4a9acc9edccbd34c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840406"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Перенос веб-API на основе OWIN в b2clogin.com

В этой статье описывается способ включения поддержки нескольких издателей маркеров в веб-API, реализующих [открытый веб-интерфейс для .NET (OWIN)](http://owin.org/). Поддержка нескольких конечных точек маркеров полезна при переносе API Azure Active Directory B2C (Azure AD B2C) и их приложений из *Login.microsoftonline.com* в *b2clogin.com*.

Добавив поддержку в API для приема маркеров, выдаваемых как в b2clogin.com, так и в login.microsoftonline.com, можно выполнить миграцию веб-приложений в промежуточном виде перед удалением поддержки маркеров, выпущенных login.microsoftonline.com, из API.

В следующих разделах представлен пример включения нескольких издателей в веб-интерфейсе API, который использует компоненты по промежуточного слоя [Microsoft OWIN][katana] (Katana). Хотя примеры кода специфичны для по промежуточного слоя Microsoft OWIN, общий метод должен быть применим к другим библиотекам OWIN.

> [!NOTE]
> Эта статья предназначена для Azure AD B2C клиентов с развернутыми интерфейсами API и приложениями, которые ссылаются на `login.microsoftonline.com` и которые хотят перейти на рекомендуемую конечную точку `b2clogin.com`. Если вы настраиваете новое приложение, используйте [b2clogin.com](b2clogin.md) в качестве направленного.

## <a name="prerequisites"></a>Технические условия

Перед продолжением действий, описанных в этой статье, необходимо выполнить следующие Azure AD B2C ресурсы.

* [Потоки пользователей](tutorial-create-user-flows.md) или [пользовательские политики](custom-policy-get-started.md) , созданные в клиенте

## <a name="get-token-issuer-endpoints"></a>Получение конечных точек издателя маркера

Сначала необходимо получить URI конечной точки издателя маркера для каждого издателя, который требуется поддерживать в API. Чтобы получить конечные точки *b2clogin.com* и *Login.microsoftonline.com* , поддерживаемые клиентом Azure AD B2C, выполните следующую процедуру в портал Azure.

Для начала выберите один из существующих потоков пользователя:

1. Перейдите к клиенту Azure AD B2C в [портал Azure](https://portal.azure.com)
1. В разделе **политики**выберите **пользовательские потоки (политики)** .
1. Выберите существующую политику, например *B2C_1_signupsignin1*, а затем выберите пункт **запустить поток пользователя** .
1. Под заголовком **Запуск потока пользователя** в верхней части страницы выберите гиперссылку, чтобы открыть конечную точку обнаружения OpenID Connect Connect для этого потока пользователя.

    ![Ссылка на известный URI на странице "Запуск" портал Azure](media/multi-token-endpoints/portal-01-policy-link.png)

1. На странице, которая открывается в браузере, запишите значение `issuer`, например:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Используйте раскрывающийся список **Выбор домена** , чтобы выбрать другой домен, а затем снова выполните предыдущие два шага и запишите значение `issuer`.

Теперь у вас должны быть записаны два URI, которые похожи на:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Пользовательские политики

При наличии пользовательских политик вместо потоков пользователей можно использовать аналогичную процедуру для получения URI издателя.

1. Перейдите к вашему клиенту Azure AD B2C
1. Выберите **Инфраструктура процедур идентификации**.
1. Выберите одну из политик проверяющей стороны, например *B2C_1A_signup_signin*
1. Используйте раскрывающийся список **Выбор домена** , чтобы выбрать домен, например *yourtenant.b2clogin.com*
1. Выберите гиперссылку, отображаемую в разделе **OpenID Connect подключение конечной точки обнаружения**
1. Запись значения `issuer`
1. Выполните шаги 4-6 для другого домена, например *Login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Получение кода примера

Теперь, когда у вас есть оба URI конечной точки маркера, необходимо обновить код, чтобы указать, что обе конечные точки являются действительными поставщиками. Чтобы проанализировать пример, скачайте или клонировать пример приложения, а затем обновите пример для поддержки обеих конечных точек в качестве действительных поставщиков.

Скачайте архив: [Active-Directory-B2C-DotNet-webapp-and-webapi-Master. zip.][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Включение нескольких издателей в веб-API

В этом разделе вы обновите код, чтобы указать, что обе конечные точки поставщика маркеров являются допустимыми.

1. Открытие решения **B2C-WebAPI-DotNet. sln** в Visual Studio
1. В проекте **TaskService** откройте файл *TaskService\\App_Start\\* ** * * * * * * * в редакторе.
1. Добавьте следующую директиву `using` в начало файла:

    `using System.Collections.Generic;`
1. Добавьте свойство [`ValidIssuers`][validissuers] в определение [`TokenValidationParameters`][tokenvalidationparameters] и укажите оба URI, записанные в предыдущем разделе:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters` предоставляется MSAL.NET и используется по промежуточного слоя OWIN в следующем разделе кода в *Startup.auth.CS*. Если указано несколько допустимых издателей, конвейер приложения OWIN учитывает, что обе конечные точки маркеров являются действительными поставщиками.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Как упоминалось ранее, другие библиотеки OWIN обычно предоставляют аналогичные средства для поддержки нескольких издателей. Хотя предоставление примеров для каждой библиотеки выходит за рамки этой статьи, для большинства библиотек можно использовать аналогичную методику.

## <a name="switch-endpoints-in-web-app"></a>Переключение конечных точек в веб-приложении

Теперь, когда оба URI поддерживаются веб-API, вам потребуется обновить веб-приложение, чтобы оно получало маркеры от конечной точки b2clogin.com.

Например, можно настроить пример веб-приложения для использования новой конечной точки, изменив значение `ida:AadInstance` в файле *TaskWebApp\\* * Web. config** * проекта **TaskWebApp** .

Измените значение `ida:AadInstance` в *файле Web. config* для TaskWebApp, чтобы оно ссылалось `{your-b2c-tenant-name}.b2clogin.com` вместо `login.microsoftonline.com`.

Перед следующей операцией.

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

После (замените `{your-b2c-tenant}` именем клиента B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Когда строки конечной точки создаются во время выполнения веб-приложения, конечные точки на основе b2clogin.com используются при запросе токенов.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье представлен метод настройки веб-API, реализующий по промежуточного слоя Microsoft OWIN (Katana), для приема маркеров из нескольких конечных точек поставщика. Как вы можете заметить, в файлах *Web. config* для проектов TaskService и TaskWebApp есть несколько других строк, которые необходимо изменить, если требуется собрать и запустить эти проекты для собственного клиента. Вы можете соответствующим образом изменить проекты, если вы хотите увидеть их в действии, но полное пошаговое руководство выходит за рамки этой статьи.

Дополнительные сведения о различных типах маркеров безопасности, порожденных Azure AD B2C, см. [в разделе Обзор маркеров в Azure Active Directory B2C](tokens-overview.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
