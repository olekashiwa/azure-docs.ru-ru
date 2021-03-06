---
title: Рекомендации по выбору идентификатора временных рядов (Azure Time Series Insights) | Документация Майкрософт
description: Ознакомьтесь с рекомендациями по выбору идентификатора временного ряда в предварительной версии службы "аналитика временных рядов Azure".
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083533"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Лучшие методики при выборе идентификатора временного ряда

В этой статье приводятся сведения о важности идентификатора временного ряда для среды предварительного просмотра службы "аналитика временных рядов Azure" и рекомендации по выбору одного из них.

## <a name="choose-a-time-series-id"></a>Выбор идентификатора временных рядов

Выбор соответствующего идентификатора временного ряда является критическим. Выбор идентификатора временного ряда подобен выбору ключа раздела для базы данных. Он необходим при создании среды предварительного просмотра аналитики временных рядов. 

> [!IMPORTANT]
> Идентификаторы временных рядов:
> * Свойство *с учетом регистра* . регистр букв и символов используется при поиске, сравнениях, обновлениях и при секционировании.
> * *Неизменяемое* свойство: после создания его нельзя изменить.

> [!TIP]
> Если источником событий является центр Интернета вещей, идентификатор временных рядов, скорее всего, будет ***iothub-Connection-Device-ID***.

Ниже приведены основные рекомендации.

* Выберите ключ секции с множеством различных значений (например, сотни или тысячи). Во многих случаях это может быть идентификатор устройства, идентификатор датчика или идентификатор тега в JSON.
* Идентификатор временного ряда должен быть уникальным на уровне листового узла вашей [модели временных рядов](./time-series-insights-update-tsm.md).
* Ограничение по символам для строки имени свойства идентификатора временного ряда — 128. Для значения свойства идентификатора временных рядов ограничение составляет 1 024 символов.
* Если отсутствует уникальное значение свойства для идентификатора временных рядов, оно обрабатывается как значение NULL и соответствует тому же правилу ограничения уникальности.
* Можно также выбрать до *трех* ключевых свойств в качестве идентификатора временного ряда. Их сочетание будет составным ключом, представляющим идентификатор временных рядов.  
  > [!NOTE]
  > Три ключевых свойства должны быть строками.
  > Вам придется запрашивать по этому составному ключу, а не по одному свойству за раз.

## <a name="select-more-than-one-key-property"></a>Выбрать более одного ключевого свойства

В следующих сценариях описывается выбор более одного ключевого свойства в качестве идентификатора временного ряда.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Пример 1. идентификатор временного ряда с уникальным ключом

* У вас есть устаревшие ресурсы ресурсов. Каждый из них имеет уникальный ключ.
* Один парк однозначно идентифицируется свойством **deviceId**. Для другого парка свойство Unique имеет значение **ObjectID**. Ни один из этих свойств не содержит уникального свойства. В этом примере в качестве уникальных ключей нужно выбрать два ключа: **deviceId** и **ObjectID**.
* Мы принимаем значения NULL, и отсутствие присутствия свойства в полезных данных события считается значением NULL. Это также подходящий способ для отправки данных в два источника событий, где данные в каждом источнике событий имеют уникальный идентификатор временного ряда.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Пример 2. идентификатор временного ряда с составным ключом

* Вам требуется,чтобы несколько свойств были уникальными в пределах одного парка ресурсов. 
* Вы являетесь производителем интеллектуальных зданий и развертываете датчики в каждом помещении. В каждой комнате обычно используются одинаковые значения для **сенсорид**. Примеры: **sensor1**, **sensor2**и **sensor3**.
* В вашем здании перекрываются номера этажей и помещений для сайтов в свойстве **флррм**. Эти числа имеют такие значения, как **1A**, **2b**и **3a**.
* У вас есть свойство **Location**, которое содержит такие значения, как **Redmond**, **Барселоне**и **Токио**. Чтобы создать уникальность, назначьте следующие три свойства в качестве ключей ИДЕНТИФИКАТОРов временных рядов: **сенсорид**, **флррм**и **Location**.

Пример необработанного события:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

В портал Azure можно ввести составной ключ следующим образом: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о [моделировании данных](./time-series-insights-update-tsm.md).

* Спланируйте [среду предварительной версии службы "аналитика временных рядов Azure](./time-series-insights-update-plan.md)".