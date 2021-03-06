---
title: Использование API Graph в Azure Active Directory B2C
description: Управление пользователями в клиенте Azure AD B2C путем вызова API Graph Azure AD и использования удостоверения приложения для автоматизации процесса.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 71b437f57f9d9e6e18af88d6413269cac6f66c47
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161670"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C. Использование API Graph Azure AD

Клиенты Azure Active Directory B2C (Azure AD B2C) могут иметь тысячи или миллионы пользователей. Это значит, что многие распространенные задачи управления клиентами необходимо решать программным путем. Основным примером может служить управление пользователями.

Возможно, вам потребуется перенести существующее хранилище пользователя в клиент B2C. Кроме того, может потребоваться разместить регистрацию пользователей на своей странице и создавать учетные записи пользователей каталога Azure AD B2C в фоновом режиме. Для выполнения этих задач требуется возможность создания, чтения, обновления и удаления учетных записей пользователей. Такие задачи можно выполнять с помощью API Graph Azure AD.

Для клиентов B2C существует два основных режима взаимодействия с API Graph.

* Для **интерактивных**задач, выполняемых единожды, при выполнении задач следует действовать как учетная запись администратора в клиенте B2C. Для этого прежде чем выполнять вызовы в API Graph, администратор должен войти в систему со своими учетными данными.
* Для **автоматизированных**непрерывных задач следует использовать некоторый тип учетной записи службы, предоставляемый с необходимыми правами для выполнения задач управления. В Azure AD это можно сделать путем регистрации приложения и выполнения проверки подлинности. Для этого нужен *идентификатор приложения* , который использует [предоставление учетных данных клиента OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). В этом случае приложение вызывает API Graph, действуя само по себе, а не под именем конкретного пользователя.

В этой статье вы узнаете, как выполнить автоматический вариант использования. Вы создадите `B2CGraphClient` .NET 4.5, который выполняет операции пользователя: создание, чтение, обновление и удаление (CRUD). В клиенте предусмотрен интерфейс командной строки Windows, позволяющий вызывать различные методы. Однако код написан для себя в неинтерактивном, автоматизированном режиме.

## <a name="prerequisites"></a>предварительные требования

Перед созданием приложений или пользователей вам потребуется клиент Azure AD B2C. [Создайте клиент Azure Active Directory B2C](tutorial-create-tenant.md), если он еще не создан.

## <a name="register-an-application"></a>Регистрация приложения

Получив клиент Azure AD B2C, необходимо зарегистрировать приложение управления с помощью [портал Azure](https://portal.azure.com). Кроме того, необходимо предоставить ему разрешения, необходимые для выполнения задач управления от имени автоматизированного сценария или приложения управления.

### <a name="register-application-in-azure-active-directory"></a>Регистрация приложения в Azure Active Directory

Чтобы использовать API Graph Azure AD с клиентом B2C, необходимо зарегистрировать приложение с помощью рабочего процесса регистрации приложения Azure Active Directory.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Назначение разрешений доступа API

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Создать секрет клиента

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Теперь у вас есть приложение, имеющее разрешение на *Создание*, *Чтение*и *обновление* пользователей в клиенте Azure AD B2C. Перейдите к следующему разделу, чтобы добавить разрешения на *Удаление* пользователя и *Обновление пароля* .

## <a name="add-user-delete-and-password-update-permissions"></a>Добавление разрешений на удаление и изменение пароля пользователя

Предоставленное ранее разрешение на *чтение и запись данных каталога* **не** включает в себя возможность удалять пользователей или обновлять пароли.

Если вы хотите предоставить приложению возможность удалять пользователей или обновлять пароли, необходимо предоставить ему роль *администратора пользователей* .

1. Войдите на [портал Azure](https://portal.azure.com).
1. Щелкните значок **Каталог + подписка** на панели инструментов портала, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. В портал Azure найдите и выберите **Azure AD B2C**.
1. В разделе **Управление**выберите **роли и администраторы**.
1. Выберите роль **администратора пользователей** .
1. Выберите **добавить назначение**.
1. В текстовом поле **Выбор** введите имя зарегистрированного ранее приложения, например *managementapp1*. Выберите приложение, которое появится в результатах поиска.
1. Выберите **Добавить**. Для полного распространения разрешений может потребоваться несколько минут.

Приложение Azure AD B2C теперь имеет дополнительные разрешения, необходимые для удаления пользователей или обновления паролей в клиенте B2C.

## <a name="get-the-sample-code"></a>Получение кода примера

Пример кода — это консольное приложение .NET, которое использует [Библиотека проверки подлинности Active Directory (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) для взаимодействия с Azure AD API Graph. В его коде показано, как вызывать API для программного управления пользователями в клиенте Azure AD B2C.

Вы можете [скачать пример архива](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*. zip) или клонировать репозиторий GitHub:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

После получения примера кода настройте его для своей среды, а затем выполните сборку проекта:

1. Откройте решение `B2CGraphClient\B2CGraphClient.sln` в Visual Studio.
1. В проекте **B2CGraphClient** откройте файл *app. config* .
1. Замените раздел `<appSettings>` следующим XML-кодом. Затем замените `{your-b2c-tenant}` именем своего клиента, а `{Application ID}` и `{Client secret}` значениями, записанными ранее.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Создайте решение. Щелкните правой кнопкой мыши решение **B2CGraphClient** в Обозреватель решений и выберите **Перестроить решение**.

Если сборка выполнена успешно, `B2C.exe` консольное приложение можно найти в `B2CGraphClient\bin\Debug`.

## <a name="review-the-sample-code"></a>Просмотр примера кода

Чтобы использовать B2CGraphClient, откройте командную строку (`cmd.exe`) и перейдите в каталог `Debug` проекта. Затем выполните команду `B2C Help`.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Команда `B2C Help` отображает краткое описание доступных подкоманд. Каждый раз при вызове одной из ее подкоманд `B2CGraphClient` отправляет запрос API Graph Azure AD.

В следующих разделах описывается, как код приложения выполняет вызовы API Graph Azure AD.

### <a name="get-an-access-token"></a>Получение маркера доступа

Любой запрос к API Graph Azure AD требует маркера доступа для проверки подлинности. `B2CGraphClient` использует Библиотека проверки подлинности Active Directory с открытым кодом (ADAL) для помощи при получении маркеров доступа. ADAL делает получение маркера проще, предоставляя вспомогательный API и принимая во внимание несколько важных сведений, таких как кэширование маркеров доступа. Однако не нужно использовать ADAL для получения маркеров. Вместо этого можно получить токены, вручную создав HTTP-запросы.

> [!NOTE]
> Для получения маркеров доступа, которые можно использовать с API Graph Azure AD, необходимо использовать ADAL v2 или более поздней версии. Нельзя использовать ADAL v1.

При выполнении `B2CGraphClient` создается экземпляр класса `B2CGraphClient`. Конструктор для этого класса настраивает формирование шаблонов проверки подлинности ADAL:

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Давайте воспользуемся командой `B2C Get-User` в качестве примера.

Если `B2C Get-User` вызывается без дополнительных аргументов, приложение вызывает метод `B2CGraphClient.GetAllUsers()`. затем `GetAllUsers()` вызывает `B2CGraphClient.SendGraphGetRequest()`, который отправляет запрос HTTP GET в API Graph Azure AD. Прежде чем `B2CGraphClient.SendGraphGetRequest()` отправляет запрос GET, он сначала получает маркер доступа с помощью ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

Чтобы получить маркер доступа для API Graph, вызовите метод ADAL `AuthenticationContext.AcquireToken()` . ADAL возвращает `access_token` , представляющий удостоверение приложения.

### <a name="read-users"></a>Чтение пользователей

Если вы хотите получить список пользователей или получить конкретного пользователя из API Graph Azure AD, вы можете отправить запрос HTTP `GET` в конечную точку `/users`. Для всех пользователей клиента запрос будет выглядеть следующим образом:

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Чтобы увидеть, как работает этот запрос, выполните следующую команду:

 ```cmd
 B2C Get-User
 ```

При этом необходимо обратить внимание на два важных момента.

* Маркер доступа, полученный с помощью ADAL, добавляется в заголовок `Authorization` с помощью схемы `Bearer`.
* Для клиентов B2C необходимо использовать параметр запроса `api-version=1.6`.

Оба фрагмента сведений обрабатываются в методе `B2CGraphClient.SendGraphGetRequest()` .

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Создание учетных записей пользователей-клиентов

При создании учетных записей пользователей в клиенте B2C можно отправить запрос HTTP `POST` в конечную точку `/users`. Следующий запрос HTTP `POST` показывает пример пользователя, который будет создан в клиенте.

Для создания пользователей-потребителей требуется большая часть свойств в следующем запросе. Для иллюстрации были включены `//` комментарии. не включайте их в фактический запрос.

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Чтобы просмотреть запрос, выполните следующие команды:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

Команда `Create-User` принимает в качестве входного параметра JSON-файл, содержащий представление JSON объекта пользователя. В примере кода есть два примера JSON файлов: `usertemplate-email.json` и `usertemplate-username.json`. Вы можете изменить эти файлы с учетом своих требований. В дополнение к обязательным полям, приведенным выше, в файлы включаются несколько необязательных полей.

Дополнительные сведения о обязательных и необязательных полях см. в разделе [Справочник по сущностям и сложным типам | Ссылка на API Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference).

В `B2CGraphClient.SendGraphPostRequest()`можно увидеть, как создается запрос POST.

* Сначала добавляется маркер доступа в заголовок `Authorization` запроса.
* Затем задается параметр `api-version=1.6`.
* После чего добавляется объект пользователя JSON в текст запроса.

> [!NOTE]
> Если учетные записи, которые требуется перенести из существующего хранилища пользователей, имеют более низкую стойкость паролей, чем [усиленный пароль, принудительно](user-flow-password-complexity.md)установленный Azure AD B2C, можно отключить требование надежного пароля, используя значение `DisableStrongPassword` в свойстве `passwordPolicies`. Например, можно изменить предыдущий запрос на создание пользователя следующим образом: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Обновление учетных записей пользователей-клиентов

При обновлении объектов пользователя процесс аналогичен тому, который используется для создания объектов-пользователей, но использует метод HTTP `PATCH`:

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Попробуйте обновить пользователя, изменив некоторые значения в файлах JSON, а затем используйте `B2CGraphClient` для выполнения одной из следующих команд:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Сведения о том, как отправить этот запрос, см. в методе `B2CGraphClient.SendGraphPatchRequest()`.

### <a name="search-users"></a>Поиск пользователей

Поиск пользователей в клиенте B2C можно выполнить следующими способами.

* Ссылка на **идентификатор объекта**пользователя.
* Сослаться на идентификатор входа, свойство `signInNames`.
* Сослаться на любой из допустимых параметров OData. Например, "givenName", "Фамилия", "displayName" и т. д.

Выполните одну из следующих команд, чтобы найти пользователя:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Пример:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>Удаление пользователей

Чтобы удалить пользователей, используйте метод HTTP `DELETE` и создайте URL-адрес с ИДЕНТИФИКАТОРом объекта пользователя:

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Чтобы увидеть пример, выполните следующую команду и просмотрите запрос delete, который будет выведен на консоль.

```cmd
B2C Delete-User <object-id-of-user>
```

Сведения о том, как отправить этот запрос, см. в методе `B2CGraphClient.SendGraphDeleteRequest()`.

Наряду с управлением пользователями API Graph позволяет выполнять и многие другие действия. Дополнительные сведения о каждом действии и примеры запросов см. в [справочнике по API Graph для Azure AD](/previous-versions/azure/ad/graph/api/api-catalog).

## <a name="use-custom-attributes"></a>Использование настраиваемых атрибутов

В большинстве клиентских приложений необходимо сохранять те или иные данные профилей пользователей-клиентов. Один из способов сделать это — определить настраиваемый атрибут в клиенте B2C. Затем этот атрибут можно рассматривать так же, как и любое другое свойство в объекте пользователя. Атрибут можно обновить, удалить, использовать для запроса, отправить как утверждение в маркере входа и т. д.

Инструкции по указанию настраиваемых атрибутов клиента B2C см. в [справочнике по настраиваемым атрибутам B2C](user-flow-custom-attributes.md).

Настраиваемые атрибуты, определенные в клиенте B2C, можно просмотреть с помощью следующих `B2CGraphClient` команд:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

В выходных данных раскрываются подробные сведения о каждом настраиваемом атрибуте. Пример:

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Полное имя, например `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, можно использовать как свойство объектов пользователей. Обновите JSON-файл, указав новое свойство и значение свойства, а затем выполните команду:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>Дальнейшие действия

Использование `B2CGraphClient`позволяет получить приложение-службу, которое обеспечивает управление пользователями клиента B2C программным путем. `B2CGraphClient` проходит аутентификацию в API Graph Azure AD по собственному удостоверению приложения. Кроме того, B2CGraphClient получает маркеры, используя секрет клиента.

При внедрении этой функции в собственное приложение помните несколько ключевых моментов для B2C приложений:

* Предоставьте приложению необходимые разрешения в клиенте.
* При вызове API Graph укажите `api-version=1.6`.
* При создании и обновлении пользователей-клиентов требуются некоторые свойства, как описано выше.
