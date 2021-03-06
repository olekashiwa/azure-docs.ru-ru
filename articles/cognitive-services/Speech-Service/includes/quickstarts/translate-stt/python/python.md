---
title: Краткое руководство. Перевод речи в речь с помощью Python в службе "Речь"
description: TBD
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: chlandsi
ms.openlocfilehash: 57898160206d16945304a2c358a6db52099f3c2b
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156373"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настроить среду разработки.](../../../../quickstarts/setup-platform.md?tabs=python)
> * [Создать пустой пример проекта.](../../../../quickstarts/create-project.md?tabs=python)

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл `quickstart.py` и замените все содержимое приведенным ниже кодом.

    ````python
    import azure.cognitiveservices.speech as speechsdk

    speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"

    def translate_speech_to_text():

        # Creates an instance of a speech translation config with specified subscription key and service region.
        # Replace with your own subscription key and service region (e.g., "westus", use the one of SpeechSDKParameters
        # from here: https://aka.ms/speech/sdkregion).   
        translation_config = speechsdk.translation.SpeechTranslationConfig(subscription=speech_key, region=service_region)

        # Sets source and target languages.
        # Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
        fromLanguage = 'en-US'
        toLanguage = 'de'
        translation_config.speech_recognition_language = fromLanguage
        translation_config.add_target_language(toLanguage)

        # Creates a translation recognizer using and audio file as input.
        recognizer = speechsdk.translation.TranslationRecognizer(translation_config=translation_config)

        # Starts translation, and returns after a single utterance is recognized. The end of a
        # single utterance is determined by listening for silence at the end or until a maximum of 15
        # seconds of audio is processed. It returns the recognized text as well as the translation.
        # Note: Since recognize_once() returns only a single utterance, it is suitable only for single
        # shot recognition like command or query.
        # For long-running multi-utterance recognition, use start_continuous_recognition() instead.
        print("Say something...")
        result = recognizer.recognize_once()

        # Check the result
        if result.reason == speechsdk.ResultReason.TranslatedSpeech:
            print("RECOGNIZED '{}': {}".format(fromLanguage, result.text))
            print("TRANSLATED into {}: {}".format(toLanguage, result.translations['de']))
        elif result.reason == speechsdk.ResultReason.RecognizedSpeech:
            print("RECOGNIZED: {} (text could not be translated)".format(result.text))
        elif result.reason == speechsdk.ResultReason.NoMatch:
            print("NOMATCH: Speech could not be recognized: {}".format(result.no_match_details))
        elif result.reason == speechsdk.ResultReason.Canceled:
            print("CANCELED: Reason={}".format(result.cancellation_details.reason))
            if result.cancellation_details.reason == speechsdk.CancellationReason.Error:
                print("CANCELED: ErrorDetails={}".format(result.cancellation_details.error_details))

    translate_speech_to_text()
    ````

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](../../../../regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные изменения в `quickstart.py`.

## <a name="build-and-run-your-app"></a>Создание и запуск приложения

1. Запустите пример из консоли или в интегрированной среде разработки:

   ```
   python quickstart.py
   ```

2. Произнесите фразу или предложение на английском языке. Приложение передает речь в службу "Речь", которая выполняет перевод и транскрибирование в текст (в нашем примере на немецкий язык). Затем служба "Речь" отправляет текст обратно в приложение для отображения.

   ````
   Say something...
   RECOGNIZED 'en-US': What's the weather in Seattle?
   TRANSLATED into 'de': Wie ist das Wetter in Seattle?
   ````

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [footer](./footer.md)]
