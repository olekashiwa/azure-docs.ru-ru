---
title: Что такое Распознаватель документов?
titleSuffix: Azure Cognitive Services
description: Распознаватель документов Cognitive Services Azure позволяет выявлять и извлекать пары "ключ — значение" и табличные данные из документов форм.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 91ea2b68828ac54d4128a90550e9c60e065b719d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379451"
---
# <a name="what-is-form-recognizer"></a>Что такое Распознаватель документов?

Распознаватель документов Azure — это служба когнитивных вычислений, использующая технологию машинного обучения для идентификации и извлечения текста, пар "ключ — значение" и данных таблиц из документов-форм. Эта служба получает текстовые данные из форм и выводит структурированные данные, которые включают в себя связи в исходном файле. Вы можете быстро получать точные результаты с учетом специфики содержимого, не прибегая к сложному ручному вмешательству или продолжительной обработке и анализу данных. Распознаватель документов состоит из пользовательских моделей, предварительно построенной модели для обработки квитанций и API макета. Вы можете вызывать модели Распознавателя документов с помощью REST API, чтобы упростить систему и интегрировать модель в рабочий процесс или приложение.

Распознаватель документов состоит из следующих служб.
* **Пользовательские модели** извлекают из форм табличные данные и пары "ключ — значение". Эти модели обучаются по вашим данным, а значит хорошо адаптированы для работы с вашими формами.
* **Предварительно созданная модель для обработки квитанций** рассчитана на извлечение данных из типичных для США квитанций о продажах.
* **API макета** извлекает из документа структуры текста и таблиц наряду с координатами ограничивающих прямоугольников для них.

<!-- add diagram -->

## <a name="custom-models"></a>Настраиваемые модели

Настраиваемая модель Распознавателя документов обучается на основе ваших данных. Для начала работы достаточно указать пять примеров форм ввода. Обученная модель выводит структурированные данные, которые содержат связи из исходного документа формы. После обучения модели вы можете проверить ее, повторно обучить и, в конечном итоге, применить с целью надежного извлечения данных из нескольких форм в соответствии со своими потребностями.

При обучении настраиваемых моделей доступны следующие варианты: обучение с помеченными данными и без помеченных данных.

### <a name="train-without-labels"></a>Обучение без меток

По умолчанию Распознаватель документов использует неконтролируемое обучение, чтобы изучить макет и связи между полями и записями формы. При получении входных форм алгоритм кластеризует их по типам, обнаруживает имеющиеся ключи и таблицы, а также связывает значения с ключами и записями в таблицах. Этот процесс не требует ручной маркировки данных и больших усилий для написания кода и обслуживания, поэтому мы рекомендуем всегда начинать с него.

### <a name="train-with-labels"></a>Обучение с метками

При обучении с помеченными данными модель выполняет контролируемое обучение для извлечения важных значений из предоставленных форм с метками. Этот процесс повышает эффективность моделей и позволяет получить модели для достаточно сложных форм и (или) форм со значениями без ключей.

Распознаватель документов использует [API макета](#layout-api) для изучения ожидаемых размеров и положений печатных и рукописных элементов текста. Затем он использует заданные пользователем метки для изучения связей "ключ — значение" в предоставленных документах. Мы рекомендуем использовать не менее пяти форм одного типа с проставленными вручную метками, чтобы начать обучение новой модели, и постепенно добавлять данные с метками для повышения ее точности.

## <a name="prebuilt-receipt-model"></a>Предварительно созданная модель для обработки квитанций

Распознаватель документов также включает модель для чтения типичных для США квитанций на английском языке, которые используются ресторанами, заправочными станциями, розничными магазинами и т. д. ([пример квитанции](./media/contoso-receipt-small.png)). Эта модель извлекает основные сведения, например сведения о времени и дате транзакции, о продавце, сумме налогов и общей сумме и многое другое. Кроме того, предварительно созданная модель для обработки квитанций обучается распознавать и возвращать весь текст в квитанции.

## <a name="layout-api"></a>API макета

Распознаватель документов также может извлекать текст и структуру таблиц (номера строк и столбцов, связанные с текстом) с помощью технологии оптического распознавания символов (OCR). 

## <a name="where-do-i-start"></a>С чего начать?

**Шаг 1**. Запрос доступа.

Распознаватель документов доступен в качестве предварительной версии с ограниченным доступом. Чтобы получить доступ к предварительной версии, заполните и отправьте [форму запроса на доступ к Распознавателю документов](https://aka.ms/FormRecognizerRequestAccess). В форме нужно указать сведения о себе, компании и сценарии работы с Распознавателем документов.

**Шаг 2**. Создайте ресурс Распознавателя документов на портале Azure.

Когда вам предоставляется доступ к Распознавателю документов, вы получаете приветственное сообщение электронной почты с несколькими ссылками и ресурсами. Используйте ссылку на портал Azure в этом сообщении, чтобы перейти на портал Azure и создать ресурс Распознавателя документов.

**Шаг 3**. Извлечение данных из форм.

* Пользовательское обучение модели по собственным формам
  * Обучение без меток
    * [Краткое руководство. по обучению модели Распознавателя документов и извлечению данных форм с помощью REST API и cURL](quickstarts/curl-train-extract.md)
    * [Краткое руководство. по обучению модели Распознавателя документов и извлечению данных из форм с помощью REST API и Python](quickstarts/python-train-extract.md)
  * Обучение с метками 
    * [Обучение модели Распознавателя документов по примерам с метками с помощью средства маркировки данных](quickstarts/label-tool.md)
    * [Обучение модели Распознавателя документов по примерам с метками с помощью REST API и Python](quickstarts/python-labeled-data.md) 
* Предварительно созданная модель квитанций — извлечение данных из типичных для США квитанций о продажах
  * [Краткое руководство. по извлечению данных квитанций с помощью cURL](quickstarts/curl-receipts.md)
  * [Краткое руководство. по извлечению данных квитанций с помощью Python](quickstarts/python-receipts.md)
* Макет — извлечение текста и структуры таблиц из форм
  * [Краткое руководство. Извлечение данных о макете с помощью Python](quickstarts/python-layout.md)

При изучении технологии мы рекомендуем использовать бесплатную версию службы. Имейте в виду, что количество бесплатных страниц ограничено до 500 страниц в месяц.

**Шаг 4**. Ознакомьтесь с интерфейсами REST API.

Воспользуйтесь следующими API для обучения моделей и извлечения структурированных данных из форм.

|Имя |Description |
|---|---|
| **Обучение пользовательской модели**| Обучите новую модель для анализа своих форм с помощью пяти форм такого же типа. Задайте для параметра _useLabelFile_ значение `true`, чтобы обучить данные с вручную проставленными метками. |
| **Анализ формы** |Проанализируйте один документ, переданный в виде потока, чтобы извлечь текст, пары "ключ — значение" и таблицы из формы с помощью настраиваемой модели.  |
| **Анализ квитанции** |Проанализируйте один документ квитанции, чтобы извлечь основные сведения и оставшийся текст квитанции.|
| **Анализ макета** |Проанализируйте макет формы для извлечения текста и структуры таблиц.|

Ознакомьтесь со [справочной документацией по REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm), чтобы узнать больше. Если вы уже знакомы с предыдущей версией API, обратитесь к статье [о новых возможностях и изменениях](./whats-new.md).

## <a name="input-requirements"></a>Требования к входным данным
### <a name="custom-model"></a>Настраиваемая модель

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Предварительно созданная модель для обработки квитанций

Требования к входным данным модели для обработки квитанций немного отличаются.

* Данные должны предоставляться в формате JPEG, PNG, BMP, PDF (текст или отсканированный) или TIFF.
* Размер файла не должен превышать 20 МБ.
* Изображения должны иметь размеры в пределах от 50 x 50 до 10000 x 10000 пикселей. 
* Размер страницы PDF-файла не должен превышать 17 x 17 дюймов, что соответствует размеру листа Legal или A3 и меньше.
* В файлах в форматах PDF и TIFF обрабатываются только первые 200 страниц (с подпиской уровня "Бесплатный" обрабатываются только первые две страницы).

## <a name="data-privacy-and-security"></a>Конфиденциальность и безопасность данных

Служба предлагается в качестве [предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) службы Azure [согласно условий использования веб-служб](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Как и в случае со всеми другими службами Cognitive Services, разработчикам, использующим службу Распознавателя документов, следует учитывать политику корпорации Майкрософт касательно клиентских данных. Дополнительные сведения см. на [странице о Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) Центра управления безопасностью Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия

Следуйте [краткому руководству](quickstarts/curl-train-extract.md), чтобы приступить к использованию [API Распознавателя документов](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).
