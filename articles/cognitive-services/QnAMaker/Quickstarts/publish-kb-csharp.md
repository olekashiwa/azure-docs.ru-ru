---
title: Краткое руководство. Публикация базы знаний QnA Maker с использованием REST и C#
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве по началу работы с C# и REST описывается, как опубликовать базу знаний и создать конечную точку, которую можно вызвать в приложении или чат-боте.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 33cc4caa8b5901177699368e2f31908951910a6f
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109574"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Краткое руководство. Публикация базы знаний в QnA Maker с использованием C#

В этом кратком руководстве по REST описано, как программным способом опубликовать базу знаний. В процессе публикации последняя версия базы знаний передается в индекс выделенной службы "Когнитивный поиск Azure" и создается конечная точка, которую можно вызывать в приложении или чат-боте.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Публикация.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) При использовании этого API в тексте запроса не нужно указывать какие-либо сведения.

## <a name="prerequisites"></a>Предварительные требования

* Последний [**выпуск Visual Studio Community**](https://www.visualstudio.com/downloads/).
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ и конечную точку (включая имя ресурса), щелкните **Быстрый запуск** для ресурса на портале Azure.
* Идентификатор базы знаний QnA Maker, который находится в URL-адресе в параметре строки запроса `kbid`, как показано ниже.

    ![Идентификатор базы знаний QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Если у вас еще нет базы знаний, ее можно создать для этого руководства. [Создать базу знаний](create-new-kb-csharp.md).

> [!NOTE]
> Полные файлы решения доступны в [репозитории GitHub **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Создание проекта базы знаний

1. Откройте Visual Studio 2019 Community Edition.
1. Создайте проект **Консольное приложение (.NET Core)** и присвойте ему имя `QnaMakerQuickstart`. Для остальных параметров оставьте значения по умолчанию.

## <a name="add-required-dependencies"></a>Добавление необходимых зависимостей

В начале файла Program.cs замените одиночный оператор using следующими строками, чтобы добавить необходимые зависимости в проект:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Добавление необходимых констант

В классе **Program** добавьте необходимые константы для доступа к QnA Maker.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Добавление метода Main для публикации базы знаний

Включив константы, добавьте следующий код, который отправляет HTTPS-запрос к API службы QnA Maker, чтобы опубликовать базу знаний и получить ответ:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=36-56 "Add HTTP Post request and response")]

Вызов API возвращает состояние 204 для успешной публикации без содержимого в тексте ответа.

## <a name="build-and-run-the-program"></a>Сборка и запуск проекта

Выполните сборку и запуск программы. Эта программа автоматически отправляет запрос к API службы QnA Maker на публикацию базы знаний. Ответ выводится в окно консоли.

После публикации базы знаний вы можете запросить ее из конечной точки с помощью клиентского приложения или чат-бота.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Опубликовав базу знаний, вам нужно использовать [URL-адрес конечной точки для создания ответа](./get-answer-from-knowledge-base-csharp.md).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))
