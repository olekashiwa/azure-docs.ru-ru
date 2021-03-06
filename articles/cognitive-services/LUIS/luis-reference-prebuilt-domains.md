---
title: Предварительно созданная ссылка на домен — LUIS
titleSuffix: Azure Cognitive Services
description: Справочник по предварительно созданным доменам, которые представляют собой предварительно собранные коллекции намерений и сущностей из Интеллектуальных служб распознавания речи (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 17d72ad51974bb0fb741ae19ebb19f313e646c62
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677641"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Справочник по предварительно созданным доменам для приложения LUIS
В этом справочнике представлены сведения о [предварительно созданных доменах](luis-how-to-use-prebuilt-domains.md), которые представляют собой предварительно созданные коллекции намерений и сущностей, предлагаемые приложением LUIS.

[Пользовательские домены](luis-how-to-start-new-app.md), напротив, сначала не имеют намерений и моделей. Вы можете добавить любые намерения и сущности предварительно созданных доменов в пользовательскую модель.

## <a name="custom-domains-per-language"></a>Пользовательские домены на язык

В таблице ниже перечислены поддерживаемые в настоящее время домены. Поддержка английского языка обычно более полная, чем другие. 

| Тип сущности       | EN-US      | ZH-CN   | DE    | СВ     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Календарь  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Связь  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Адрес эл. почты     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Примечания     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Места   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Столбце     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Коммунальные службы      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Веб    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Предварительно созданные домены **не поддерживаются** в:

* Французский (Канада)
* Хинди
* Испанский (Мексика)

## <a name="next-steps"></a>Следующие шаги

Изучите [простую сущность](reference-entity-simple.md). 