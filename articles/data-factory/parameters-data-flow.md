---
title: Параметризация потоков данных для сопоставления
description: Узнайте, как параметризовать поток данных сопоставления из конвейеров фабрики данных.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c717d9ab44493d15589030073cd2ab260ef52e1c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760221"
---
# <a name="parameterizing-mapping-data-flows"></a>Параметризация потоков данных для сопоставления

Сопоставление потоков данных в фабрике данных Azure поддерживает использование параметров. В определении потока данных можно определить параметры, которые затем можно использовать в выражениях. Значения параметров могут быть заданы вызывающим конвейером через действие "выполнение потока данных". Задать значения в выражениях действия потока данных можно тремя способами:

* Использование языка выражений потока управления конвейера для задания динамического значения
* Использование языка выражений потока данных для задания динамического значения
* Использовать любой язык выражений для установки статического литерального значения

Используйте эту возможность, чтобы потоки данных были общими, гибкими и пригодными для многократного использования. С помощью этих параметров можно параметризовать параметры потока данных и выражения.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Создание параметров в потоке данных сопоставления

Чтобы добавить параметры в поток данных, щелкните пустую часть холста потока данных, чтобы просмотреть общие свойства. В области Параметры появится вкладка с именем **параметр**. Выберите **создать** , чтобы создать новый параметр. Для каждого параметра необходимо назначить имя, выбрать тип и при необходимости задать значение по умолчанию.

![Создание параметров потока данных](media/data-flow/create-params.png "Создание параметров потока данных")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Использование параметров в потоке данных сопоставления 

На параметры можно ссылаться в любом выражении потока данных. Параметры начинаются с $ и являются неизменяемыми. Список доступных параметров можно найти в построителе выражений на вкладке **Параметры** .

![Выражение параметра потока данных](media/data-flow/parameter-expression.png "Выражение параметра потока данных")

Можно быстро добавить дополнительные параметры, выбрав **новый параметр** и указав имя и тип.

![Выражение параметра потока данных](media/data-flow/new-parameter-expression.png "Выражение параметра потока данных")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Передача имени столбца в качестве параметра

Распространенным шаблоном является передача имени столбца в качестве значения параметра. Для ссылки на столбец, связанный с параметром, используйте функцию `byName()`. Не забудьте привести столбец к соответствующему типу с помощью функции приведения, например `toString()`.

Например, если требуется сопоставлять строковый столбец на основе параметра `columnName`, можно добавить преобразование «Производный столбец», равное `toString(byName($columnName))`.

![Передача имени столбца в качестве параметра](media/data-flow/parameterize-column-name.png "Передача имени столбца в качестве Language")

## <a name="assign-parameter-values-from-a-pipeline"></a>Назначение значений параметров из конвейера

После создания потока данных с параметрами его можно выполнить из конвейера с помощью действия "выполнение потока данных". После добавления действия на холст конвейера вы увидите доступные параметры потока данных на вкладке **Параметры** действия.

![Задание параметра потока данных](media/data-flow/parameter-assign.png "Задание параметра потока данных")

Если тип данных параметра — String, то при щелчке текстового поля для задания значений параметров можно указать конвейер или выражение потока данных. Если выбрать выражение конвейера, отобразится панель выражения конвейера. Не забудьте включить в синтаксис интерполяции строк функции конвейера с помощью `'@{<expression>}'`, например:

```'@{pipeline().RunId}'```

Если параметр имеет тип, отличный от String, всегда будет представлен построитель выражений потока данных. Здесь можно ввести любое выражение или литеральные значения, которые должны соответствовать типу данных параметра. Ниже приведены примеры выражений потока данных и литеральной строки из построителя выражений.

* ```toInteger(Role)```
* ```'this is my static literal string'```

Каждый поток данных сопоставления может иметь любое сочетание параметров конвейера и выражения потока данных. 

![Пример параметров потока данных](media/data-flow/parameter-example.png "Пример параметров потока данных")



## <a name="next-steps"></a>Дальнейшие действия
* [Выполнение действия потока данных](control-flow-execute-data-flow-activity.md)
* [Выражения потока управления](control-flow-expression-language-functions.md)
