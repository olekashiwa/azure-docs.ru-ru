---
title: Выполнение нескольких зависимых служб с использованием .NET Core и Visual Studio Code
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: В этом руководстве описано, как использовать Azure Dev Spaces и Visual Studio Code для отладки приложения .NET Core с несколькими службами в службе Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 0bbb1aefe517c45207160b83b89f7207e8909666
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438320"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-code-with-azure-dev-spaces"></a>Выполнение нескольких зависимых служб с использованием .NET Core и Visual Studio Code с Azure Dev Spaces

Из этого руководства вы узнаете, как разрабатывать приложения на базе нескольких служб с помощью Azure Dev Spaces. Также здесь описываются некоторые дополнительные преимущества использования Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Вызов службы, запущенной в отдельном контейнере

В этом разделе описывается, как создать вторую службу `mywebapi`, которую будет вызывать `webfrontend`. Каждая служба будет выполняться в отдельных контейнерах. Затем вы выполните отладку в обоих контейнерах.

![Несколько контейнеров](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Скачайте пример кода для *mywebapi*.
Ради экономии времени давайте загрузим образец кода из репозитория GitHub. Перейдите к https://github.com/Azure/dev-spaces и выберите **Clone or download** (Клонировать или скачать), чтобы скачать репозиторий GitHub. Код для этого раздела находится в папке `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Запуск *mywebapi*
1. Откройте папку `mywebapi` в *отдельном окне VS Code*.
1. Откройте **палитру команд** (с помощью меню **Вид | Палитра команд**), включите автоматическое завершение ввода и выберите эту команду: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Не следует путать эту команду с командой `azds prep`, которая позволяет настроить проект для развертывания.
1. Нажмите клавишу F5 и подождите, пока выполнится сборка и развертывание службы. Вы узнаете, что оно готово, когда *запустится приложение. Нажмите Ctrl+C, чтобы завершить работу.* В консоли отладки появляется сообщение.
1. URL-адрес конечной точки будет выглядеть приблизительно так: `http://localhost:<portnumber>`. **Совет. Строка состояния VS Code станет оранжевой и в ней появится интерактивный URL-адрес.** Кажется, что контейнер выполняется локально, но фактически он выполняется в нашей среде разработки в Azure. В адресе указано localhost, потому что для `mywebapi` не определены общедоступные конечные точки и доступ осуществляется только в пределах экземпляра Kubernetes. Для вашего удобства и упрощения взаимодействия с закрытой службой на локальном компьютере служба Azure Dev Spaces создает временный туннель SSH для контейнера, запущенного в Azure.
1. Когда `mywebapi` будет готово, откройте в браузере адрес localhost. Добавьте `/api/values` в URL-адрес, чтобы вызвать API GET по умолчанию для `ValuesController`.
1. Если все шаги были успешными, вы должны увидеть ответ от службы `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Запрос из *webfrontend* к *mywebapi*
Давайте теперь напишем код в `webfrontend`, который отправляет запрос к `mywebapi`.
1. Перейдите в окно VS Code для `webfrontend`.
1. *Замените* код метода About в файле `HomeController.cs`:

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

        return View();
    }
    ```

В предыдущем примере код перенаправляет заголовок `azds-route-as` из входящего запроса в исходящий. Позже вы увидите, как это помогает повысить производительность при [коллективной разработке](team-development-netcore.md).

### <a name="debug-across-multiple-services"></a>Отладка в нескольких службах
1. На этом этапе служба `mywebapi` по-прежнему должна выполняться с подключенным отладчиком. Если это не так, нажмите клавишу F5 в проекте `mywebapi`.
1. Установите точку останова в методе `Get(int id)`, который обрабатывает запросы GET `api/values/{id}`, около [строки 23 в файле *Controllers/ValuesController.cs*](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/mywebapi/Controllers/ValuesController.cs#L23).
1. В проекте `webfrontend` установите точку останова до отправки запроса GET к `mywebapi/api/values`. Около строки 32 в файле [*Controllers/HomeController.cs*](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs), измененном в предыдущем разделе.
1. Нажмите клавишу F5 в проекте `webfrontend`.
1. Вызовите веб-приложение и пошагово выполните код в обеих службах.
1. В веб-приложении на странице About (Сведения) будет отображаться объединенное сообщение от двух служб: "Hello from webfrontend and Hello from mywebapi".

### <a name="well-done"></a>Все готово!
Теперь у вас есть многоконтейнерное приложение, где каждый контейнер можно разрабатывать и развертывать отдельно.


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Коллективная разработка с помощью Dev Spaces](team-development-netcore.md)
