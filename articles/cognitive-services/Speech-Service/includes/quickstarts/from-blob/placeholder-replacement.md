---
title: Краткое руководство. Распознавание речи из файлов, находящихся в хранилище BLOB-объектов, в службе "Речь"
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 95ff115cfc2cc70deabf66d984bb02991239c2a7
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931959"
---
Необходимо заменить следующие значения:

 - `YourSubscriptionKey`: найдено на странице портала Azure **Ключи** для ресурса "Речь".
 - `YourServiceRegion`: найдено на странице портала Azure **Обзор** для ресурса "Речь".
 - `YourFileUrl`: находится в папке **службы BLOB-объектов / Контейнеров** на портале Azure для ресурса учетной записи хранения.
     - Выберите соответствующий контейнер
     - Выберите необходимый большой двоичный объект
     - Скопируйте **URL-адрес** на странице **Свойства**
