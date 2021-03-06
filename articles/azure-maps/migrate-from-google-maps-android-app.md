---
title: Учебник. Перенос приложения Android | Microsoft Azure Maps
description: Как перенести приложение Android из Google Карт в Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6e54d8ea44b6c322f311cc1baeb6ca3ab6715aee
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989966"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Перенос приложения Android из Google Карт

Пакет SDK Azure Maps для Android содержит интерфейс API, который похож на веб-пакет SDK. Если вы использовали один из таких пакетов SDK для разработки, большинство основных понятий, рекомендаций и архитектур будут аналогичными. Благодаря этому вы сможете с легкостью перенести навыки работы с одним пакетом SDK на другой.

Пакет SDK для Android в Azure Maps поддерживает минимальную версию API 21 для Android: Android 5.0.0 (Lollipop).

Все примеры предоставлены на языке Java, но в пакете SDK Azure Maps для Android можно также использовать Kotlin.

Дополнительные сведения о разработке с помощью пакета SDK Azure Maps для Android см. в [этих практических руководствах](how-to-use-android-map-control-library.md).

## <a name="load-a-map"></a>Загрузка карты

Загрузка карты в приложении Android с помощью Google Карт или Azure Maps состоит из многих одних и тех же этапов. Для использования любого из пакетов SDK потребуется следующее:

- Получить ключ API или подписки для доступа к любой из платформ.
- Добавить XML-код в действие, чтобы указать, где должна отображаться карта и как она должна быть размещена.
- Переслать все методы жизненного цикла из действия, содержащего представление карты, в соответствующие объекты в классе map. В частности, необходимо переопределить следующие методы.
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Дождаться готовности схемы, прежде чем пытаться получить к ней доступ программным способом.

**До: Google Карты**

Чтобы отобразить карту с помощью пакета SDK для Android в Google Картах, необходимо сделать следующее:

1.  Убедитесь, что службы Google Play установлены.
2.  Добавьте зависимость для службы Google Карт в файл **gradle.build** модуля: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Добавьте ключ API Google Карт в раздел приложения файла **google\_maps\_api.xml**:
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Добавьте фрагмент карты в основное действие:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  В файл MainActivity.java следует добавить операции импорта для пакета SDK для Google Карт. Перешлите все методы жизненного цикла из действия, содержащего представление карты, в соответствующие объекты в классе map. Экземпляр `MapView` можно извлечь из фрагмента карты с помощью метода `getMapAsync(OnMapReadyCallback)`. `MapView` автоматически инициализирует систему карт и представление. Измените файл **MainActivity.java** следующим образом:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

При запуске приложения будет загружаться элемент управления картой, как показано ниже.

<center>

![Простые Google Карты](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**После: Azure Maps**

Чтобы отобразить карту с помощью пакета SDK для Android в Azure Maps, необходимо сделать следующее:

1. Откройте файл верхнего уровня **build.gradle** и добавьте приведенный ниже код в раздел блока **all projects** (все проекты):

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Обновите файл **app/build.gradle** и добавьте в него следующий код.
    
    1. Убедитесь, что **minSdkVersion** проекта находится на уровне версии API 21 или более поздней.

    2. Добавьте в раздел Android следующий код:

        ```JAVA
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Обновите блок зависимостей и добавьте новую строку зависимости реализации для последнего пакета SDK для Android в Azure Maps:

        ```JAVA
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Пакет SDK для Android в Azure Maps регулярно обновляется и улучшается. Последний номер версии Azure Maps можно узнать в документации по [началу работы с элементом управления картой в Android](how-to-use-android-map-control-library.md). Кроме того, для номера версии можно задать значение с "0.2" до "0+", чтобы в вашем коде всегда использовалась последняя версия.
    
    4. Перейдите в раздел **Файл** на панели инструментов, а затем щелкните **Sync Project with Gradle Files** (Синхронизировать проект с файлами Gradle).
3. Добавьте фрагмент карты в основное действие (ресурсы \> макет \> activity\_main.xml):
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. В файле **MainActivity.java** нужно сделать следующее:
    
    * добавить операции импорта для пакета SDK в Azure Maps;
    * настроить сведения о проверке подлинности Azure Maps;
    * получить экземпляр элемента управления картой в методе **onCreate**.

    Если задать сведения о проверке подлинности в классе `AzureMaps` глобально с помощью методов `setSubscriptionKey` или `setAadProperties`, не нужно будет добавлять эти сведения в каждое представление. 

    Элемент управления картой содержит собственные методы жизненного цикла для управления жизненным циклом OpenGL Android, которые должны вызываться непосредственно из содержащегося действия. Чтобы правильно вызывать методы жизненного цикла элемента управления картой, переопределите следующие методы жизненного цикла в действии, содержащем элемент управления картой, и вызовите соответствующий метод управления картой. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Измените файл **MainActivity.java** следующим образом:
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

При запуске приложения элемент управления картой будет загружаться следующим образом.

<center>

![Простые службы Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Обратите внимание, что элемент управления Azure Maps поддерживает более масштабное отображение и предоставляет большее представление целого мира.

> [!TIP]
> При использовании эмулятора Android в Windows карта может не отображаться из-за конфликтов с OpenGL и прорисовкой графики с программным ускорением. Ниже описано, как решить эту проблему. Откройте диспетчер AVD и выберите виртуальное устройство, которое необходимо изменить. Прокрутите страницу вниз до панели **Проверить конфигурацию**. В разделе **Emulated Performance** (Эмулированная производительность) для параметра **Графика** задайте значение **Оборудование**.

## <a name="localizing-the-map"></a>Локализация карты

Если ваша аудитория распределена по нескольким странам или говорит на разных языках, необходима локализация.

**До: Google Карты**

Язык карты можно задать в методе `onCreate` основного действия, добавив следующий код. Этот код должен размещаться перед фрагментом, который задает представление контекста карты. Ниже показано, как ограничить язык до французского с использованием кода fr.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Ниже приведен пример использования Google Карт с языком, имеющим значение fr.

<center>

![Локализация Google Карт](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**После: Azure Maps**

Azure Maps предоставляет три разных способа настройки языка и регионального представления для карты. Первый вариант — передать сведения о языке и региональном представлении в класс `AzureMaps`, используя статические методы `setLanguage` и `setView` глобально. Это позволит задать язык и региональное представление по умолчанию для всех элементов управления Azure Maps, загруженных в приложение. Ниже показано, как ограничить язык до французского с использованием кода fr-FR.

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

Второй вариант — передать сведения о языке и представлении в XML-коде элемента управления карты.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Третий вариант — задать язык и региональное представление карты программным образом с помощью метода `setStyle` карт. Этот параметр можно задать в любое время, чтобы изменить язык и региональное представление для карты.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Ниже приведен пример использования Azure Maps с языком, имеющим значение fr-FR.

<center>

![Локализация Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Полный список поддерживаемых языков и региональных представлений приведен [здесь](supported-languages.md).

## <a name="setting-the-map-view"></a>Настройка представления карты

Динамические карты в Azure Maps и Google Картах можно программно перемещать в новые географические расположения путем вызова соответствующих методов. В приведенных ниже примерах показано, как сделать так, чтобы на карте отображались спутниковые аэроснимки, выровнять карту по центру по местоположению с использованием координат (широта: 35,0272, долгота: −111,0225) и изменить уровень масштабирования до 15 в Google Картах.

> [!NOTE]
> В Google Картах используются фрагменты размером 256 пикселей, а в Azure Maps — более крупные фрагменты размером 512 пикселей. Благодаря этому сокращается количество сетевых запросов, необходимых Azure Maps для загрузки той же области карты, что и в Google Картах. Однако из-за того, как пирамиды фрагментов работают в элементах управления карты, большие фрагменты в Azure Maps означают, что для достижения той же видимой области, что и на карте в Google Картах, при использовании Azure Maps необходимо вычесть единицу от уровня масштабирования, используемого в Google Картах. 

**До: Google Карты**

Камеру элемента управления картой в Google Картах можно перемещать программным способом с помощью метода `moveCamera`, который позволяет указать центр карты и уровень масштабирования. Для изменения типа отображаемой карты можно использовать метод `setMapType`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Заданное представление Google Карт](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**После: Azure Maps**

Как отмечалось ранее, чтобы получить ту же видимую область в Azure Maps, вычтите от уровня масштабирования, используемого в Google Картах, единицу. В данном случае используйте уровень масштабирования 14.

Начальное представление карты можно задать в атрибутах XML в элементе управления картой.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

Представление карты можно обновить программно с помощью методов `setCamera` и `setStyle` карт.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Заданное представление Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Дополнительные ресурсы:**

- [Поддерживаемые стили карт](supported-map-styles.md)

## <a name="adding-a-marker"></a>Добавление метки

Данные точек часто отображаются на карте с помощью изображения. Эти изображения часто называются метками, вешками, кнопками или символами. В следующих примерах данные точек отображаются в виде меток на карте по координатам (широта: 51,5, долгота: −0,2).

**До: Google Карты**

При использовании Google Карт метки добавляются с помощью метода `addMarker` карт.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Метка Google Карт](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**После: Azure Maps**

Чтобы отобразить данные точек в Azure Maps, для начала добавьте данные в источник данных. Затем подключите этот источник данных к слою символов. Источник данных позволяет оптимизировать управление пространственными данными в элементе управления картой. Слой символов определяет способ отображения данных (в виде изображения и (или) текста).

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![Метка Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Добавление настраиваемой метки

Для представления точек на карте можно использовать пользовательские изображения. В приведенных ниже примерах карты пользовательское изображение применяется для отображения точки на карте. Координаты точки (широта: 51,5, долгота: −0,2). На этой карте позиция маркера смещается таким образом, чтобы значок кнопки был правильно размещен на карте.

<center>

![Изображение желтой вешки](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

В обоих примерах приведенное выше изображение добавляется в папку с прорисовываемыми ресурсами приложений.

**До: Google Карты**

В Google Картах пользовательские изображения можно использовать в качестве меток. Для этого загрузите их с помощью параметра `icon` метки. Параметр `anchor` позволяет сопоставить точку изображения с координатами. Привязка задается относительно измерений изображения. В данном случае 0,2 единицы в ширину и 1 единица в высоту.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Пользовательская метка Google Карт](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**После: Azure Maps**

Слои символов в Azure Maps также поддерживают пользовательские изображения, но для этого необходимо заранее загрузить изображение в ресурсы карты и назначить ему уникальный идентификатор. Затем слой символов сможет ссылаться на этот идентификатор. Задайте смещение для символа, чтобы он указывал на нужную точку на изображении, используя параметр `iconOffset`. Смещение значка указывается в пикселях. По умолчанию смещение задается относительно нижней центральной точки изображения, но это можно изменить с помощью параметра `iconAnchor`. В этом примере параметру `iconAnchor` присваивается значение `"center"` и используется смещение значка для перемещения изображения на 5 пикселей вправо и на 15 пикселей вверх в соответствии с точкой изображения вешки.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Пользовательская метка Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Добавление ломаной линии

Ломаные линии используются для представления линий или траекторий на карте. В следующих примерах показано, как создать пунктирную ломаную линию на карте.

**До: Google Карты**

В Google Картах ломаную линию можно создать с помощью класса `PolylineOptions` и добавить ее на карту с использованием метода `addPolyline`. Цвет штрихов можно задать с помощью параметра `color`, ширину штриха — с помощью параметра ширины, а массив штриха — с помощью параметра `pattern`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Ломаная линия в Google Картах](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**После: Azure Maps**

В Azure Maps ломаные линии называются объектами `LineString` или `MultiLineString`. Эти объекты можно добавлять в источник данных и отображать с помощью слоя линий. Единицы "пикселей", которые задаются в массиве значений ширины и штрихов, соответствуют единицам в веб-пакете SDK для Azure Maps. Это означает, что применение одинаковых значений в обоих пакетах SDK дает идентичные результаты.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Ломаная линия в Azure Maps](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Добавление многоугольника

Многоугольники используются для представления области на карте. В следующих примерах показано, как создать многоугольник, который образует треугольник на основе центральной координаты на карте.

**До: Google Карты**

В Google Картах многоугольник можно создать с помощью класса `PolygonOptions` и добавить его на карту с использованием метода `addPolygon`. Цвета заливки и обводки можно задать с помощью параметров `fillColor` и `strokeColor`. Толщина штриха задается с помощью параметра `strokeWidth`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Многоугольник в Google Картах](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**После: Azure Maps**

В Azure Maps объекты `Polygon` и `MultiPolygon` можно добавить в источник данных и преобразовать для просмотра на карте с помощью слоев. Площадь многоугольника можно отобразить на слое многоугольников. Контур многоугольника можно визуализировать с помощью слоя линий. Единицы "пикселей", которые задаются в массиве значений ширины и штрихов, соответствуют единицам в веб-пакете SDK для Azure Maps. Это означает, что применение одинаковых значений в обоих пакетах SDK дает идентичные результаты.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Многоугольник в Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Наложение слоя фрагментов

 Они позволяют накладывать изображения слоев, разбитые на небольшие фрагменты, которые соответствуют системе разбиения карты. Это распространенный подход для наложения изображений слоев или больших наборов данных. Слои фрагментов в Google Картах называются наложениями изображений.

В приведенных ниже примерах накладывается слой фрагментов радара погоды из лаборатории окружающей среды Университета штата Айова. Размер фрагментов составляет 256 пикселей.

**До: Google Карты**

В Google Картах слой фрагментов можно перекрыть на карте, используя класс `TileOverlayOptions`, и добавить на карту с помощью метода `addTileLauer`. Чтобы сделать фрагменты карты полупрозрачными, для параметра `transparency` необходимо задать значение 0,2 или 20 % прозрачности.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![Слой фрагментов в Google Картах](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**После: Azure Maps**

Слой фрагментов можно добавить на карту почти так же, как любой другой слой. Форматированный URL-адрес с заполнителями значений x, y и масштаба (`{x}`, `{y}`, `{z}` соответственно) используется для указания уровню места доступа к фрагментам. Слои фрагментов в Azure Maps также поддерживают заполнители `{quadkey}`, `{bbox-epsg-3857}` и `{subdomain}`. Чтобы слой фрагментов был частично прозрачным, используется значение непрозрачности 0,8. Обратите внимание, что непрозрачность и прозрачность обозначают один и тот же параметр, но их значения противоположны. Чтобы преобразовать одно из них в другое, вычтите исходное значение из единицы.

> [!TIP]
> В Azure Maps слои можно легко отобразить под другими слоями, в том числе под базовыми слоями карт. Часто желательно отображать слои фрагментов под метками карты, чтобы их можно было легко читать. Метод `map.layers.add` принимает в качестве второго параметра идентификатор слоя, под которым нужно вставить новый слой. Чтобы вставить слой фрагментов под метками карты, можно использовать следующий код: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Слой фрагментов в Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Отображение данных дорожного движения

Данные о дорожном движении можно наложить на карту как в Azure Maps, так и в Google Картах.

**До: Google Карты**

В Google Картах данные дорожного движения можно переносить на карту, передавая значение true в метод `setTrafficEnabled` карты.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Данные дорожного движения в Google Картах](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**После: Azure Maps**

Azure Maps предоставляет несколько различных вариантов отображения дорожного движения. Дорожно-транспортные происшествия, такие как закрытие дорог и аварии, можно отображать на карте в виде значков. На карту можно наложить сведения о транспортном потоке с цветовым кодированием сегментов дорог. Цвета сегментов можно изменять в соответствии с ограничениями скорости, относительно нормальной ожидаемой или абсолютной задержки. Данные о дорожно-транспортных происшествиях в Azure Maps обновляются каждую минуту, а данные о дорожном движении — каждые две минуты.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Данные дорожного движения в Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о пакете SDK для Android в Azure Maps.

> [!div class="nextstepaction"]
> [Как использовать элемент управления картой в Android](how-to-use-android-map-control-library.md)
