---
title: Общие сведения о конечной точке потоковой передачи служб мультимедиа Azure | Документация Майкрософт
description: В этой статье приводятся общие сведения о конечных точках потоковой передачи служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 95d8d819aa1b418b4a7ec736cef64cb989f7e37b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74885642"
---
# <a name="streaming-endpoints-overview"></a>Общие сведения о конечных точках потоковой передачи  

> [!NOTE]
> В Cлужбы мультимедиа версии 2 больше не добавляются новые компоненты или функциональные возможности. <br/>Ознакомьтесь с новейшей версией Служб мультимедиа — [версией 3](https://docs.microsoft.com/azure/media-services/latest/). См. также [руководство по миграции из v2 в версии 3](../latest/migrate-from-v2-to-v3.md) .

В службах мультимедиа Microsoft Azure (AMS) **конечная точка потоковой передачи** — это служба потоковой передачи, которая может доставлять содержимое непосредственно в клиентское приложение проигрывателя или сеть доставки содержимого (CDN) для дальнейшего распространения. Службы мультимедиа также обеспечивают прозрачную интеграцию с Azure CDN. Поток, исходящий из службы конечной точки потоковой передачи, может быть активным потоком трансляции, видео по запросу или поэтапной загрузкой ресурса-контейнера в учетной записи служб мультимедиа. Каждая учетная запись служб мультимедиа Azure включает конечную точку потоковой передачи по умолчанию. В рамках учетной записи можно создавать дополнительные конечные точки потоковой передачи. Доступны две версии конечных точек потоковой передачи: 1.0 и 2.0. Начиная с 10 января 2017 г. все новые учетные записи AMS будут включать конечную точку потоковой передачи версии 2.0 **по умолчанию**. Добавленные в эту учетную запись дополнительные конечные точки потоковой передачи также будут версии 2.0. Это изменение не повлияет на существующие учетные записи. Существующие конечные точки потоковой передачи будут версии 1.0 с возможностью обновления до версии 2.0. Это изменение повлечет изменения в поведении, выставлении счетов и функциях (дополнительные сведения см. в разделе о **типах и версиях потоковой передачи** ниже).

Службы мультимедиа Azure добавили следующие свойства в сущность конечной точки потоковой передачи: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Подробный обзор этих свойств см. [здесь](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Когда вы создаете учетную запись служб мультимедиа Azure, стандартная конечная точка потоковой передачи создается в **остановленном** состоянии. Удалить стандартную конечную точку потоковой передачи нельзя. В зависимости от доступности Azure CDN в целевом регионе по умолчанию только что созданная стандартная конечная точка потоковой передачи также предусматривает интеграцию поставщика CDN StandardVerizon. 
                
> [!NOTE]
> Вы можете отключить интеграцию Azure CDN перед запуском конечной точки потоковой передачи. `hostname` и URL-адрес потоковой передачи остаются без изменений независимо от того, включена ли сеть CDN.

В этом разделе описаны основные возможности, предоставляемые конечными точками потоковой передачи.

## <a name="naming-conventions"></a>Соглашения об именовании.

Для конечной точки по умолчанию: `{AccountName}.streaming.mediaservices.windows.net`

Для всех дополнительных конечных точек: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Типы и версии потоковой передачи

### <a name="standardpremium-types-version-20"></a>Типы "Стандартный" и "Премиум" (версия 2.0)

Начиная с выпуска за Январь 2017 служб мультимедиа, у вас есть два типа потоковой передачи: **Standard** (Предварительная версия) и **Premium**. Эти типы реализованы в конечной точке потоковой передачи версии 2.0.


|Тип|Описание|
|--------|--------|  
|**Стандартный**|Конечной точкой потоковой передачи по умолчанию является **стандартный** тип, которую можно изменить на тип Premium, изменив единицы потоковой передачи.|
|**Премиальный** |Этот вариант подходит для специализированного использования, предусматривающего более крупный масштаб или управление данными. Вы переходите к типу **Premium** , настраивая единицы потоковой передачи.<br/>Выделенные конечные точки потоковой передачи находятся в изолированной среде и не конкурируют за ресурсы.|

Для клиентов, желающих доставлять содержимое в крупную аудиторию Интернета, рекомендуется включить CDN в конечной точке потоковой передачи.

Дополнительные сведения см. в следующем разделе со [сравнением типов потоковой передачи](#comparing-streaming-types).

### <a name="classic-type-version-10"></a>Классический тип (версия 1.0)

Пользователям, создавшим учетные записи AMS до 10 января 2017 г., предоставляется **классический** тип конечной точки потоковой передачи. Этот тип реализован в конечной точке потоковой передачи версии 1.0.

Если для конечной точки потоковой передачи **версии 1.0** выделено >= 1 единиц потоковой передачи типа "Премиум", такая конечная точка будет представлять тип "Премиум", предоставляя все функции AMS (так же, как и типы **"Стандартный" и "Премиум"** ) без необходимости выполнять дополнительную настройку.

>[!NOTE]
>**Классические** конечные точки потоковой передачи (версии 1.0 с 0 единиц потоковой передачи) характеризуются ограниченными возможности и не включают соглашение об уровне обслуживания. Рекомендуется перейти на тип **Стандартный**, чтобы оптимизировать возможности службы и использовать такие функции, как динамическая упаковка или шифрование, равно как и другие функции, доступные для типа **Стандартный**. Чтобы перейти на тип **Стандартный**, войдите на [портал Azure](https://portal.azure.com/) и выберите пункт **Перейти на ценовую категорию "Стандартный"** . Дополнительные сведения см. в [документации по миграции](#migration-between-types).
>
>Учтите, что для этой операции откат не предусмотрен, а ее выполнение связано с дополнительными расходами.
>
 
## <a name="comparing-streaming-types"></a>Сравнение типов потоковой передачи

### <a name="versions"></a>Версии

|Тип|Версия конечной точки потоковой передачи|Единицы масштабирования|Сеть кэширующих серверов (CDN)|Выставление счетов|
|--------------|----------|-----------------|-----------------|-----------------|
|Классический|1.0|0|Нет данных|Бесплатные|
|Стандартная конечная точка потоковой передачи (Предварительная версия)|2.0|0|ДА|Платные|
|Единицы потоковой передачи уровня "Премиум"|1.0|>0|ДА|Платные|
|Единицы потоковой передачи уровня "Премиум"|2.0|>0|ДА|Платные|

### <a name="features"></a>Компоненты

Компонент|Стандарт|"Премиум"
---|---|---
Пропускная способность |До 600 Мбит/с и может обеспечить более эффективную пропускную способность при использовании CDN.|200 Мбит/с на единицу потоковой передачи. Может обеспечить более эффективную пропускную способность при использовании CDN.
Сеть кэширующих серверов (CDN)|Azure CDN, CDN сторонних поставщиков или без CDN.|Azure CDN, CDN сторонних поставщиков или без CDN.
Пропорциональное выставление счетов| Ежедневно|Ежедневно
Динамическое шифрование|ДА|ДА
Динамическая упаковка|ДА|ДА
Масштабирование|Автоматически масштабируется до целевой пропускной способности.|Дополнительные единицы потоковой передачи.
IP-фильтрация/G20/пользовательский узел <sup>1</sup>|ДА|ДА
Прогрессивное скачивание|ДА|ДА
Рекомендуемый способ использования |Рекомендуется для большинства сценариев потоковой передачи.|Специализированное использование. 

<sup>1</sup> используется только непосредственно в конечной точке потоковой передачи, если сеть CDN не включена в конечной точке.<br/>

Сведения об уровне обслуживания см. в разделе [цены и соглашение об уровне обслуживания](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Миграция между типами

От | До | Действия
---|---|---
Классический|Стандарт|Необходима миграция.
Классический|"Премиум"| Масштабирование (дополнительные единицы потоковой передачи).
Стандартный/Премиум|Классический|Недоступно (если конечная точка потоковой передачи версии 1.0; допускается переход на классическую версию с настройкой значения 0 для единиц потоковой передачи).
"Стандартный" (с и без CDN)|"Премиум" (с теми же конфигурациями)|Допускается в **запущенном** состоянии (с использованием портала Azure).
"Премиум" (с и без CDN)|"Стандартный" (с теми же конфигурациями)|Допускается в **запущенном** состояния (с использованием портала Azure).
"Стандартный" (с и без CDN)|"Премиум" (с другой конфигурацией)|Допускается в **остановленном** состоянии (с использованием портала Azure). Не допускается в запущенном состоянии.
"Премиум" (с и без CDN)|"Стандартный" (с другой конфигурацией)|Допускается в **остановленном** состоянии (с использованием портала Azure). Не допускается в запущенном состоянии.
Версия 1.0: единиц потоковой передачи >= 1 (с CDN)|"Стандартный" и "Премиум" (без CDN)|Допускается в **остановленном** состоянии. Не допускается в **запущенном** состоянии.
Версия 1.0: единиц потоковой передачи >= 1 (с CDN)|"Стандартный" (с и без CDN)|Допускается в **остановленном** состоянии. Не допускается в **запущенном** состоянии. Версия 1.0 CDN будет удалена. Будет создана и запущена новая.
Версия 1.0: единиц потоковой передачи >= 1 (с CDN)|"Премиум" (с и без CDN)|Допускается в **остановленном** состоянии. Не допускается в **запущенном** состоянии. Классическая версия CDN будет удалена. Будет создана и запущена новая.

## <a name="next-steps"></a>Дальнейшие действия
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отправить отзыв
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

