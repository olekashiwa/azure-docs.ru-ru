---
title: Добавление проверки подлинности на Apache Cordova
description: Узнайте, как использовать мобильные приложения в службе приложений Azure для проверки подлинности пользователей Apache Cordova приложения с помощью поставщиков удостоверений, таких как Google, Facebook, Twitter и Майкрософт.
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f85c8e4f8eedccf3039038308f2262727fb18197
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668553"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Добавление проверки подлинности в приложение Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Центр приложений Visual Studio поддерживает комплексные и интегрированные службы, необходимые для разработки мобильных приложений. Разработчики могут использовать службы **Build**, **Test** и **Distribute** для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения разработчики могут отслеживать его состояние и использование с помощью служб **Analytics** и **Diagnostics**, а также взаимодействовать с пользователями с помощью службы **Push**. Разработчики также могут использовать **Auth** для реализации проверки подлинности пользователей и службу **Data** для хранения и синхронизации данных приложений в облаке.
>
> Если вы хотите интегрировать облачные службы с мобильным приложением, зарегистрируйтесь в [Центре приложений](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) сегодня.

## <a name="summary"></a>Резюме
В этом учебнике вы добавляете проверку подлинности в учебный проект ToDoList для Apache Cordova с помощью поддерживаемого поставщика удостоверений. Этот учебник создан на основе учебника [Начало работы с мобильными приложениями] , который необходимо изучить в первую очередь.

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка службы приложений
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Видео, демонстрирующее аналогичные действия](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Теперь можно убедиться, что анонимный доступ к серверной части был отключен. В Visual Studio:

* Откройте проект, созданный при выполнении заданий руководства [Начало работы с мобильными приложениями].
* Запустите приложение в **эмуляторе Google Android**.
* Проверьте, отображается ли сообщение о непредвиденном сбое подключения после запуска приложения.

Далее следует изменить приложение таким образом, чтобы оно аутентифицировало пользователей, прежде чем запрашивать ресурсы из серверной части мобильного приложения.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение
1. Откройте проект в **Visual Studio**, а затем откройте файл `www/index.html` для редактирования.
2. Найдите мета-тег `Content-Security-Policy` в разделе head.  Добавьте узел OAuth в список допустимых источников.

   | Поставщик | Имя поставщика SDK | Узел OAuth |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Майкрософт | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Пример политики безопасности содержимого (для Azure Active Directory):

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Замените `https://login.microsoftonline.com` узлом OAuth из приведенной выше таблицы.  Дополнительные сведения о метатеге политики безопасности содержимого см. в [Документация по политике безопасности содержимого].

    Некоторые поставщики аутентификации не требуют изменений в политике безопасности содержимого при использовании на соответствующих мобильных устройствах.  Например, изменения в политики безопасности содержимого не требуются, если на устройстве Android используется проверка подлинности Google.

3. Откройте файл `www/js/index.js` для редактирования, найдите метод `onDeviceReady()` и добавьте приведенный ниже код в код создания клиента.

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Этот код заменит существующий код, который создает ссылку на таблицу и обновляет пользовательский интерфейс.

    Метод login() запускает аутентификацию с использованием поставщика. Метод login() — это асинхронная функция, которая возвращает обещание JavaScript.  Остальная часть инициализации помещается в ответ обещания и до завершения метода login() не выполняется.

4. В коде, который вы только что добавили, замените `SDK_Provider_Name` именем своего поставщика входа. Например, для Azure Active Directory используйте `client.login('aad')`.
5. Запустите проект.  Когда инициализация проекта будет завершена, в приложении откроется страница входа OAth для выбранного поставщика аутентификации.

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше об [аутентификации] с использованием службы приложений Azure.
* Продолжите работу с учебником, добавив в приложение Apache Cordova [push-уведомления] .

Подробнее об использовании пакетов SDK.

* [Пакет SDK для Apache Cordova]
* [Серверный пакет SDK для ASP.NET]
* [Серверный пакет SDK для Node.js]

<!-- URLs. -->
[Начало работы с мобильными приложениями]: app-service-mobile-cordova-get-started.md
[Документация по политике безопасности содержимого]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Push-уведомления]: app-service-mobile-cordova-get-started-push.md
[аутентификации]: app-service-mobile-auth.md
[Пакет SDK для Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[Серверный пакет SDK для ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Серверный пакет SDK для Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md
