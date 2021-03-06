---
title: Данные точки кластеризации на карте | Карты Microsoft Azure
description: В этой статье вы узнаете, как использовать данные точки кластера и визуализировать их на карте с помощью веб-пакета SDK для карт Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: e65681aefc047ba540d4ad0d91ef6e4d2af5f3ca
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190259"
---
# <a name="clustering-point-data"></a>Данные точки кластеризации

При визуализации большого количества точек данных на карте точки данных могут перекрывать друг друга. Перекрытие может привести к тому, что эта схема станет нечитаемой и ее можно будет легко использовать. Кластеризация данных точек — это процесс объединения данных точек, находящихся рядом друг с другом, и представления их на карте в виде единой кластеризованной точки данных. По мере того как пользователь масштабирует карту, кластеры разбиваются на отдельные точки данных. При работе с большим количеством точек данных используйте процессы кластеризации, чтобы улучшить взаимодействие с пользователем.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Включение кластеризации в источнике данных

Включите кластеризацию в классе `DataSource`, задав для параметра `cluster` значение true. Задайте `ClusterRadius`, чтобы выбрать соседние точки и объединить их в кластер. Значение `ClusterRadius` находится в пикселях. Используйте `clusterMaxZoom`, чтобы указать уровень масштабирования для отключения логики кластеризации. Ниже приведен пример включения кластеризации в источнике данных.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Если две точки данных близки друг к другу, возможно, кластер никогда не будет разбиваться, независимо от того, насколько близки к масштабу пользователя. Для решения этой задачи можно задать параметр `clusterMaxZoom`, чтобы отключить логику кластеризации и просто отобразить все данные.

Ниже приведены дополнительные методы, предоставляемые классом `DataSource` для кластеризации:

| Метод | Возвращаемый тип | Описание |
|--------|-------------|-------------|
| Жетклустерчилдрен (clusterId: число) | Promise&lt;Массив&lt;Компонент&lt;Геометрия, любая&gt; \| Фигура&gt;&gt; | Извлекает дочерние элементы заданного кластера на следующем уровне масштабирования. Эти дочерние элементы могут быть сочетанием фигур и подкластеров. Подкластеры будут представлять собой компоненты со свойствами, соответствующими ClusteredProperties. |
| Жетклустерекспансионзум (clusterId: число) | Promise&lt;число&gt; | Вычисляет уровень масштабирования, при котором кластер начинает расширяться или разбиваться на части. |
| Жетклустерлеавес (clusterId: число, ограничение: число, смещение: число) | Promise&lt;Массив&lt;Компонент&lt;Геометрия, любая&gt; \| Фигура&gt;&gt; | Извлекает все точки в кластере. Задайте `limit`, чтобы вернуть подмножество точек, и используйте `offset`, чтобы пролистать страницы с точками. |

## <a name="display-clusters-using-a-bubble-layer"></a>Отображение кластеров с помощью пузырькового слоя

Пузырьковый слой — это отличный способ визуализации кластеризованных точек. Используйте выражения для масштабирования радиуса и изменения цвета в зависимости от количества точек в кластере. При отображении кластеров с помощью пузырькового слоя следует использовать отдельный слой для отображения некластеризованных точек данных.

Чтобы отобразить размер кластера поверх пузырька, используйте слой символов с текстом и не используйте значок.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Базовый уровень кластеризации пузырьков" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Дополнительные сведения см. в разделе <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>кластеризация уровня "базовый</a> " с помощью Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Отображение кластеров с помощью слоя символов

При визуализации точек данных слой символов автоматически скрывает символы, которые пересекаются друг с другом, чтобы обеспечить Улучшенный пользовательский интерфейс. Это поведение по умолчанию может оказаться нежелательным, если необходимо отобразить плотность точек данных на карте. Однако эти параметры можно изменить. Чтобы отобразить все символы, задайте для параметра `allowOverlap` в свойстве "слои символов" `iconOptions` значение `true`. 

Использование кластеризации для отображения плотности точек данных с сохранением чистого пользовательского интерфейса. В примере ниже показано, как добавить пользовательские символы и представить кластеры и отдельные точки данных с помощью слоя символов.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Кластеризованный слой символов" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Сведения о том, как Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>, см. в разделе <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>уровень кластеризованных символов</a> пера.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Кластеризация и уровень тепловых карт

Тепловые карты — это отличный способ отображения плотности данных на карте. Этот метод визуализации может самостоятельно управлять большим количеством точек данных. Если точки данных кластеризованы, а размер кластера используется в качестве веса тепловой карт, то тепловая схема может работать с еще большим объемом данных. Чтобы получить этот параметр, задайте для параметра `weight` слоя тепловой карте значение `['get', 'point_count']`. Если радиус кластера мал, то тепловая схема будет выглядеть почти так же, как на тепловой карте с помощью некластеризованных точек данных, но она будет работать гораздо лучше. Тем не менее, чем меньше радиус кластера, тем точнее будет тепловая схема, но с меньшим количеством преимуществ производительности.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Взвешенная тепловая схема кластера" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>взвешенная тепловая текстура кластера</a> с Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>События мыши на точках кластеризованных данных

Когда события мыши возникают на слое, содержащем кластеризованные точки данных, точка кластеризованных данных возвращается в событие в виде объекта функции геообъектной точки. Этот компонент будет иметь следующие свойства:

| Имя свойства             | Тип    | Описание   |
|---------------------------|---------|---------------|
| `cluster`                 | Логическое | Указывает, представляет ли компонент кластер. |
| `cluster_id`              | string  | Уникальный идентификатор кластера, который можно использовать с методами `getClusterExpansionZoom`, `getClusterChildren` и `getClusterLeaves` для DataSource. |
| `point_count`             | number  | Число точек, содержащихся в кластере.  |
| `point_count_abbreviated` | string  | Строка, которая сокращает значение `point_count`, если оно длинное. (например, 4000 преобразуется в 4K).  |

В этом примере используется пузырьковый слой, который отображает точки кластера и добавляет событие Click. При срабатывании события щелчка код вычисляет и масштабирует карту до следующего уровня масштаба, при котором кластер разбивается друг на друга. Эта функция реализуется с помощью метода `getClusterExpansionZoom` класса `DataSource` и свойства `cluster_id` в кластеризованной точке данных, которую щелкнули.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Жетклустерекспансионзум кластера" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Жетклустерекспансионзум Clustering</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Отобразить область кластера 

Данные точки, которые представляет кластер, распределяются по области. В этом примере при наведении указателя мыши на кластер происходит два основных поведения. Во-первых, отдельные точки данных, содержащиеся в кластере, будут использоваться для вычисления выпуклой поверхности. Затем выпуклой поверхности будет отображаться на карте для отображения области.  Выпуклой поверхности — это многоугольник, который заключает набор точек, например эластичную полосу, и может быть вычислен с помощью метода `atlas.math.getConvexHull`. Все точки, содержащиеся в кластере, можно получить из источника данных с помощью метода `getClusterLeaves`.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Выпуклой поверхности области кластера" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>кластерная область выпуклой поверхности</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Статистическая обработка данных в кластерах

Часто кластеры представляются с помощью символа с количеством точек в кластере. Но иногда желательно настроить стиль кластеров с дополнительными метриками. При использовании статистических выражений кластера пользовательские свойства можно создавать и заполнять с помощью вычисления [статистического выражения](data-driven-style-expressions-web-sdk.md#aggregate-expression) .  Агрегаты кластера можно определить в `clusterProperties` параметре `DataSource`.

В следующем примере используется статистическое выражение. Код вычисляет счетчик на основе свойства типа сущности каждой точки данных в кластере. Когда пользователь щелкает кластер, появляется всплывающее окно с дополнительными сведениями о кластере.

<iframe height="500" style="width: 100%;" scrolling="no" title="Статистические выражения кластера" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>агрегаты кластера</a> перьев по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Класс DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Объект DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [пространство имен Atlas. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

См. примеры кодов для добавления функций в приложение:

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя символов](map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление слоя тепловой карты](map-add-heat-map-layer.md)
