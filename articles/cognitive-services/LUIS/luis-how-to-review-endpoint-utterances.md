---
title: Проверка пользователя фразы продолжительностью — LUIS
titleSuffix: Azure Cognitive Services
description: Просматривайте фразы продолжительностью, захваченные Active Learning, чтобы выбрать намерение и отметить сущности для чтения-мира фразы продолжительностью; принятие изменений, обучение и публикация.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775120"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Как улучшить приложение LUIS, просмотрев конечную точку фразы продолжительностью

Процесс проверки фразы продолжительностью конечных точек для правильных прогнозов называется [Active Learning](luis-concept-review-endpoint-utterances.md). Активное обучение записывает запросы конечной точки и выбирает конечную точку пользователя фразы продолжительностью, из-за чего это не известно. Вы просматриваете эти фразы продолжительностью, чтобы выбрать намерение и отметить сущности для этих фразы продолжительностью для чтения. Примите эти изменения в примере фразы продолжительностью, а затем Обучите и опубликуйте. Затем LUIS определяет фразы продолжительностью более точно.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Включить активное обучение

Чтобы включить активное обучение, необходимо вести журнал запросов пользователей. Это достигается путем вызова [запроса к конечной точке](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) с параметром строки запроса `log=true` и значением.

Используйте портал LUIS для создания правильного запроса конечной точки.

1. На [портале предварительной версии Luis](https://preview.luis.ai/)выберите свое приложение в списке приложений.
1. Перейдите к разделу **Управление** и выберите **ресурсы Azure**.
1. Для назначенного прогнозирующего ресурса выберите **изменить параметры запроса**.

    > [!div class="mx-imgBorder"]
    > ![использовать портал LUIS для сохранения журналов, необходимых для активного обучения.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Переключите параметр **сохранить журналы** , а затем сохраните, выбрав **Готово**.

    > [!div class="mx-imgBorder"]
    > ![использовать портал LUIS для сохранения журналов, необходимых для активного обучения.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Это действие изменяет URL-адрес примера, добавляя параметр `log=true` QueryString. Скопируйте и используйте URL-адрес измененного примера запроса при выполнении прогнозирующих запросов к конечной точке среды выполнения.

## <a name="correct-intent-predictions-to-align-utterances"></a>Исправьте прогнозы намерения, чтобы выстроить фразы продолжительностью

Каждый фрагмент речи включает предлагаемые намерения отображаемые в столбце **Aligned intent** (Выровненное намерение).

> [!div class="mx-imgBorder"]
> [![проверить конечную точку фразы продолжительностью, что LUIS не уверены](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Если вы согласны с этим намерением, установите флажок. Если вы не согласны с предложением, выберите правильное намерение из раскрывающегося списка выровненных намерений, а затем установите флажок справа от выровненного намерения. После установки флажка utterance перемещается в намерение и удаляется из списка **Проверка фразы продолжительностью конечных точек** .

> [!TIP]
> Для просмотра и исправления прогнозов сущностей из всех примеров фразы продолжительностью из списка **Просмотр фразы продолжительностью конечных точек** важно посетить страницу сведения о намерениях.

## <a name="delete-utterance"></a>Удаление фрагмента речи

Любой фрагмент речи можно удалить из списка проверки. После удаления он больше не отображается в списке. Это условие выполняется, даже если пользователь вводит тот же фрагмент речи из конечной точки.

Если вы не уверены, следует ли удалить utterance, переместите его в намерение None или создайте новую цель, например `miscellaneous` и переместите utterance на эту цель.

## <a name="disable-active-learning"></a>Отключить активное обучение

Чтобы отключить активное обучение, не заносить в журнал пользовательские запросы. Для этого нужно задать для [запроса к конечной точке](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) параметр `log=false` QueryString и значение, либо не использовать значение строки запроса, поскольку значение по умолчанию — false.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы убедиться в повышении производительности после установки меток для предлагаемых фрагментов речи, можно перейти к консоли тестирования, выбрав **Тестирование** на верхней панели. Инструкции по тестированию приложения с помощью консоли тестирования см. в разделе [Обучение и тестирование приложения](luis-interactive-test.md).
