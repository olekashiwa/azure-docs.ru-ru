---
title: Создание Устойчивых функций с помощью портала Azure
description: Сведения о том, как установить расширение Устойчивых функций для Функций Azure для разработки с помощью портала.
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: 0060088acb100036c094406e01d0d736a4af88eb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769648"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Создание Устойчивых функций с помощью портала Azure

Расширение [устойчивых функций](durable-functions-overview.md) для Функций Azure предоставляется в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Это расширение необходимо установить в приложении-функции. В этой статье показано, как установить этот пакет, чтобы иметь возможность разрабатывать Устойчивые функции на портале Azure.

> [!NOTE]
> 
> * Если вы разрабатываете устойчивые C#функции в, вместо этого следует подумать о [разработке Visual Studio 2019](durable-functions-create-first-csharp.md).
> * Если вы разрабатываете Устойчивые функции на языке JavaScript, вместо этого рассмотрите возможность [разработки с помощью Visual Studio Code](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Создание приложения-функции

Для выполнения любых функций вам понадобится приложение-функция, Приложение-функция позволяет группировать функции как логическую единицу для упрощения управления, развертывания, масштабирования и совместного использования ресурсов. Вы можете создать приложение .NET или JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

По умолчанию для приложения-функции используется версия 2.x среды выполнения Функций Azure. Расширение Устойчивых функций работает для обеих версий 1.x и 2.x среды выполнения Функций Azure на C# и версии 2.x на JavaScript. Но шаблоны доступны, только если планируется использовать среду выполнения версии 2.x., независимо от выбранного языка.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Установка пакета npm для расширения "Устойчивые функции" (только JavaScript)

Если вы создаете устойчивые функции на JavaScript, необходимо установить [пакет npm `durable-functions`](https://www.npmjs.com/package/durable-functions).

1. Выберите имя приложения-функции, а затем — **Функции платформы** и **Дополнительные инструменты (Kudu)** .

   ![Выбор Функций, элемента "Функции платформы" и "Дополнительные инструменты (Kudu)"](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. В консоли Kudu выберите **Консоль отладки**, а затем — **CMD**.

   ![Консоль отладки Kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. Появится структура каталогов файлов приложения-функции. Перейдите в папку `site/wwwroot`. Из нее вы можете отправить файл `package.json`, перетащив его в окно каталогов файлов. Ниже приведен пример файла `package.json`.

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Отправка файла package.json в Kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Когда файл `package.json` будет передан, запустите команду `npm install` в консоли удаленного выполнения Kudu.

   ![Запуск команды npm install в Kudu](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Создание функции оркестратора

1. Разверните приложение-функцию и нажмите кнопку **+** рядом с элементом **Функции**. Если это первая функция в приложении-функции, последовательно выберите **На портале** и **Продолжить**. Если нет, перейдите к шагу 3.

   ![Страница быстрого начала работы с функциями на портале Azure](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Последовательно выберите **Дополнительные шаблоны** и **Finish and view templates** (Закончить и просмотреть шаблоны).

    ![Быстрый запуск функций: просмотр дополнительных шаблонов](./media/durable-functions-create-portal/add-first-function.png)

1. В поле поиска введите `durable` и выберите шаблон **Начальный объект HTTP устойчивых функций**.

1. При появлении запроса выберите **установить** , чтобы установить расширение Azure DurableTask и все зависимости в приложении функции. Установить расширение для приложения-функции необходимо всего один раз. По завершении установки щелкните **Продолжить**.

    ![Установка расширений привязки](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. После завершения установки присвойте новой функции имя `HttpStart` и выберите **Создать**. Созданная функция используется для запуска оркестрации.

1. Создайте другую функцию в приложении-функции, но на этот раз используя шаблон **Оркестратор устойчивых функций**. Присвойте новой функции оркестрации имя `HelloSequence`.

1. Создайте третью функцию с именем `Hello`, используя шаблон **Действие устойчивых функций**.

## <a name="test-the-durable-function-orchestration"></a>Тестирование оркестрации устойчивых функций

1. Вернитесь к функции **HttpStart**, выберите **</> Получить URL-адрес функции** и щелкните **Копировать**, чтобы скопировать URL-адрес. С помощью этого URL-адреса можно запустить функцию **HelloSequence**.

1. Используя средство HTTP, например Postman или cURL, отправьте запрос POST к скопированному URL-адресу. В следующем примере представлена команда cURL, которая отправляет запрос POST к устойчивой функции:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    В этом примере `{your-function-app-name}` — это домен, который является именем приложения-функции. Ответное сообщение содержит набор конечных точек URI, с помощью которых можно отслеживать и контролировать выполнение, которое выглядит следующим образом:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Вызовите URI конечной точки `statusQueryGetUri`, и вы увидите текущее состояние устойчивой функции, которое может выглядеть следующим образом:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Продолжайте вызывать конечную точку `statusQueryGetUri`, пока состояние не изменится на **Завершено**, и вы увидите ответ, подобный следующему:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Ваша первая устойчивая функция создана и успешно работает в Azure.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Дополнительные сведения о распространенных шаблонах устойчивых функций](durable-functions-overview.md#application-patterns)
