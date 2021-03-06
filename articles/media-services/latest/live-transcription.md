---
title: Динамическая транскрипция служб мультимедиа Azure | Документация Майкрософт
description: В этой статье объясняется, что такое динамическая транскрипция служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: 95cd759a5ef4f5f67ecf56e60595e709bbc8b44f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845683"
---
# <a name="live-transcription-preview"></a>Динамическая транскрипция (Предварительная версия)

Служба мультимедиа Azure доставляет видео, аудио и текст в разные протоколы. Когда вы публикуете динамический поток с помощью MPEG-ТИРЕ или HLS/КМАФ, а также видео и аудио, наша служба доставляет текст расшифрованной в ИМСК 1.1, совместимый с TTML, упакованный в фрагменты MPEG-4 части 30 (ISO/IEC 14496-30). Если используется доставка через HLS/TS, то текст доставляется как фрагментированный ВТТ. 

В этой статье описывается, как включить динамическую транскрипцию при потоковой передаче события в реальном времени с помощью служб мультимедиа Azure v3. Прежде чем продолжить, убедитесь, что вы знакомы с использованием API-интерфейсов службы мультимедиа v3 версии 3 (Дополнительные сведения см. в [этом руководстве](stream-files-tutorial-with-rest.md) ). Также следует ознакомиться с концепцией [потоковой передачи в реальном времени](live-streaming-overview.md) . Мы рекомендуем завершить работу [со службой "поток Live" с помощью служб мультимедиа](stream-live-tutorial-with-api.md) . 

> [!NOTE]
> Сейчас запись в реальном времени доступна только в качестве функции предварительной версии в регионе "Западная часть США 2". Он поддерживает транскрипцию слов на английском языке в тексте. Справочник по API для этой функции находится в этом документе, так как он находится на этапе предварительной версии, сведения не доступны в наших документах. 

## <a name="creating-the-live-event"></a>Создание события Live 

Чтобы создать событие Live, необходимо отправить операцию размещения в версию 2019-05-01-Preview, например: 

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Эта операция имеет следующий текст (где в качестве протокола приема создается передаваемое событие с помощью RTMP). Обратите внимание на добавление свойства "транскрипции". Единственным допустимым значением для Language является en-US. 

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

Следует опрашивать состояние события Live, пока оно не перейдет в состояние "выполняется", которое указывает, что теперь можно отправить Вкладный веб-канал RTMP. Теперь вы можете выполнить те же действия, что и в этом руководстве, например проверить канал предварительной версии и создать выходные данные в реальном времени. 

## <a name="delivery-and-playback"></a>Доставка и воспроизведение 

Ознакомьтесь со статьей [Обзор динамической упаковки](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) , в которой наша служба использует динамическую упаковку для передачи видео, аудио и текста в разные протоколы. Когда вы публикуете динамический поток с помощью MPEG-ТИРЕ или HLS/КМАФ, а также видео и аудио, наша служба доставляет текст расшифрованной в ИМСК 1.1, совместимый с TTML, упакованный в фрагменты MPEG-4 части 30 (ISO/IEC 14496-30). Если используется доставка через HLS/TS, то текст доставляется как фрагментированный ВТТ. Для воспроизведения потока можно использовать веб-проигрыватель, например [проигрыватель мультимедиа Azure](use-azure-media-player.md) .  

> [!NOTE]
>  При использовании Проигрыватель мультимедиа Azure используйте версию 2.3.3 или более позднюю.

## <a name="known-issues"></a>Известные проблемы 

На этапе предварительной версии ниже приведены известные проблемы с интерактивными транскрипциями. 

* Эта функция доступна только в западной части США 2.
* Приложения должны использовать API предварительной версии, описанные в спецификации [служб мультимедиа v3 OpenAPI спецификация спецификации](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json) .
* Единственным поддерживаемым языком является английский (EN-US).
* В отношении защиты содержимого поддерживается только шифрование с помощью алгоритма AES.

## <a name="next-steps"></a>Дальнейшие действия

[Общие сведения о службах мультимедиа](media-services-overview.md)
