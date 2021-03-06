---
title: Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" с помощью Node.js | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Сведения из этого краткого руководства помогут вам приступить к выполнению запросов к результатам поиска из экземпляра службы "Пользовательский поиск Bing" с помощью Node.js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 97bf62c61aee63ce97496e54b99ad641d6150876
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972713"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" с помощью Node.js

Сведения из этого краткого руководства помогут вам приступить к созданию запросов результатов поиска из экземпляра "Пользовательский поиск Bing". Хотя это приложение создается на языке JavaScript, API пользовательского поиска Bing представляет собой веб-службу RESTful, совместимую с большинством языков программирования. Исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Предварительные требования

- Экземпляр службы "Пользовательский поиск Bing". См. [Краткое руководство. Создание первого экземпляра службы "Пользовательский поиск Bing"](quick-start.md), чтобы получить дополнительные сведения.

- [Node.js](https://www.nodejs.org/)

- [Библиотека запросов JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл JavaScript в избранной интегрированной среде разработки или редакторе и добавьте оператор `require()` для библиотеки запросов. Создайте переменные для ключа подписки, идентификатор пользовательской конфигурации и условие поиска. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Отправка и получение поискового запроса 

1. Создайте переменную для хранения сведений, отправляемых в запросе. Создайте URL-адрес запроса, добавив условие поиска к параметру запроса `q=` и идентификатор пользовательской конфигурации экземпляра поиска к `customconfig=`. Разделяйте параметры символом `&`. Вы можете использовать указанную ниже глобальную конечную точку или конечную точку [пользовательского поддомена](../../cognitive-services/cognitive-services-custom-subdomains.md), отображаемого на портале Azure для вашего ресурса.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Используйте библиотеку запросов JavaScript для отправки поискового запроса к экземпляру службы "Пользовательский поиск Bing" и выведите сведения о результатах, включая его имя, URL-адрес и дату последнего просмотра веб-страницы.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание веб-страницы пользовательского поиска](./tutorials/custom-search-web-page.md)
