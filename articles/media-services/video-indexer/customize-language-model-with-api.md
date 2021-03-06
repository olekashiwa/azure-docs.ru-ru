---
title: Использование API индексатора видео для настройки языковых моделей — Azure
titlesuffix: Azure Media Services
description: В этой статье описана настройка языковой модели с помощью API Индексатора видео.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 01ea4d9ef943183f09baa86b729ec69344d4309e
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049032"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Настройка языковой модели с помощью API Индексатора видео

Индексатор видео позволяет создавать пользовательские языковые модели для настройки распознавания речи путем передачи текста адаптации, а именно текста из предметной области, к словарю которой вы хотите адаптировать подсистему. Как только вы обучите свою модель, новые слова, появляющиеся в тексте адаптации, будут распознаваться. 

Дополнительные сведения и рекомендации для пользовательских языковых моделей см. в статье [Customize a Language model with the Video Indexer APIs](customize-language-model-overview.md) (Настройка языковой модели с помощью API Индексатора видео).

Вы можете использовать API Индексатора видео для создания и изменения пользовательских языковых моделей в своей учетной записи, как описано в этом разделе. Вы также можете использовать веб-сайт, как описано в статье [Customize a Language model with the Video Indexer APIs](customize-language-model-with-api.md) (Настройка языковой модели с помощью API Индексатора видео).

## <a name="create-a-language-model"></a>Создание языковой модели

API [создания языковой модели](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) создает новую пользовательскую языковую модель в указанной учетной записи. Вы можете передать файлы для языковой модели в этом вызове. Кроме того, можно создать модель языка и передать файлы для модели позже, обновив языковую модель.

> [!NOTE]
> По-прежнему следует провести обучение модели с поддержкой файлов для модели, чтобы освоить содержимое из файлов. Направления по обучению языку находятся в следующем разделе.

Чтобы передать файлы, которые будут добавлены в языковую модель, необходимо передать файлы в тексте, используя данные формы в дополнение к предоставлению значений для обязательных параметров выше. Это можно осуществить двумя путями: 

1. Ключом будет имя файла, а значением будет TXT-файл.
2. Ключом будет имя файла, а значением будет URL-адрес текстового файла.

### <a name="response"></a>Ответ

В ответе содержатся метаданные для вновь созданной языковой модели вместе с метаданными для каждого файла модели, используя формат в примере выходных данных JSON.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Обучение языковой модели

API [-интерфейс обучения языковой модели](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) обучает пользовательскую языковую модель в указанной учетной записи с содержимым в файлах, которые были переданы и включены в языковой модели. 

> [!NOTE]
> Сначала необходимо создать языковую модель и передать ее файлы. Передавать файлы можно либо при создании языковой модели, либо путем обновления языковой модели. 

### <a name="response"></a>Ответ

В ответе содержатся метаданные для только что обученной языковой модели вместе с метаданными для каждого файла модели, используя формат в примере выходных данных JSON.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Возвращенный **идентификатор** — это уникальный идентификатор, используемый для различения языковых моделей, тогда как **лангуажемоделид** используется как для [отправки видео для индексирования](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) , так и для [переиндексации](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API-интерфейсов видео (также известных как **лингуистикмоделид** в API-интерфейсах отправки и повторного индексирования индексатора видео).

## <a name="delete-a-language-model"></a>Удаление языковой модели

API [удаления языковой модели](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) удаляет пользовательскую языковую модель из указанной учетной записи. Любое видео, в котором использовалась удаленная языковая модель, будет хранить тот же индекс, пока вы не выполните повторную индексацию. При повторной индексации вы можете назначить новую языковую модель для видео. В противном случае для повторной индексации видео Индексатор видео будет использовать модель по умолчанию.

### <a name="response"></a>Ответ

При успешном удалении языковой модели содержимое не возвращается.

## <a name="update-a-language-model"></a>Обновление языковой модели

[Обновление API языковой модели](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) обновляет пользовательскую модель языка в указанной учетной записи.

> [!NOTE]
> Должна быть создана языковая модель. Этот вызов можно использовать, чтобы включить или отключить все файлы в модели, обновить имя языковой модели и передать файлы для добавления в языковую модель.

Чтобы передать файлы, которые будут добавлены в языковую модель, необходимо передать файлы в тексте, используя данные формы в дополнение к предоставлению значений для обязательных параметров выше. Это можно осуществить двумя путями: 

1. Ключом будет имя файла, а значением будет TXT-файл.
2. Ключом будет имя файла, а значением будет URL-адрес текстового файла.


### <a name="response"></a>Ответ

В ответе содержатся метаданные для только что обученной языковой модели вместе с метаданными для каждого файла модели, используя формат в примере выходных данных JSON.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Используйте **идентификаторы** файлов, возвращенных в ответе, чтобы скачать содержимое файла.

## <a name="update-a-file-from-a-language-model"></a>Обновление файла языковой модели

[Обновление файла](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) позволяет обновить имя и **включить** состояние файла в пользовательской языковой модели в указанной учетной записи.

### <a name="response"></a>Ответ

В ответе содержатся метаданные файла, который был обновлен в соответствии с форматом выходных данных JSON, приведенных в примере ниже.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
Используйте **идентификатор** файла, возвращенного в ответе, чтобы скачать содержимое файла.

## <a name="get-a-specific-language-model"></a>Получение конкретной языковой модели

API [Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) возвращает сведения о указанной модели языка в указанной учетной записи, например о языке и файлах, которые находятся в модели языка. 

### <a name="response"></a>Ответ

В ответе содержатся метаданные для указанной языковой модели вместе с метаданными для каждого файла модели, используя формат выходных данных JSON, приведенных в примере ниже.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Используйте **идентификатор** файла, возвращенного в ответе, чтобы скачать содержимое файла.

## <a name="get-all-the-language-models"></a>Получение всех языковых моделей

API [получения всех](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) функций возвращает все пользовательские языковые модели в указанной учетной записи в списке.

### <a name="response"></a>Ответ

В ответе содержатся метаданные для конкретной языковой модели вместе с метаданными для каждого файла модели, используя формат выходных данных JSON, приведенных в примере ниже.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Удаление файла из языковой модели

API [удаления](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) удаляет указанный файл из указанной модели языка в указанной учетной записи. 

### <a name="response"></a>Ответ

При успешном удалении файла из языковой модели содержимое не возвращается.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Получение метаданных файла из языковой модели

[Метаданные Get файла](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) API возвращают содержимое и метаданные указанного файла из выбранной языковой модели в вашей учетной записи.

### <a name="response"></a>Ответ

Ответ предоставляет содержимое и метаданные файла в формате JSON следующего вида.

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Содержимое этого файла примера – это слова "hello" и "world" в двух отдельных строках.

## <a name="download-a-file-from-a-language-model"></a>Загрузка файла из языковой модели

[Загрузка API файла](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) скачивает текстовый файл, содержащий содержимое указанного файла из указанной языковой модели в указанной учетной записи. Этот текстовый файл должен соответствовать содержимому текстового файла, который был изначально передан.

### <a name="response"></a>Ответ

Ответом будет загрузка текстового файла с содержимым файла в формате JSON. 

## <a name="next-steps"></a>Дальнейшие действия

[Customize a Language model with the Video Indexer website](customize-language-model-with-website.md) (Настройка языковой модели с помощью веб-сайта Индексатора видео)
