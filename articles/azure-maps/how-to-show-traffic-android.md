---
title: Отображение данных трафика на карте Android | Карты Microsoft Azure
description: Из этой статьи вы узнаете, как отображать данные трафика на карте с помощью Microsoft Azure карт пакет SDK для Android.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/25/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5f7e67d159c2b7dea3ebac7fd4d0856f508cb298
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532760"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Отображение данных трафика на карте с помощью Azure Maps пакет SDK для Android

Данные потока и инциденты — это два типа данных трафика, которые могут отображаться на карте. В этом руководство показано, как отобразить оба типа данных трафика. Данные инцидентов состоят из данных на основе точек и строк для таких вещей, как конструкции, замыкания на пути и всякое. Данные о потоке показывают метрики потока трафика в дороге.

## <a name="prerequisites"></a>Предварительные требования

1. [Создайте учетную запись службы Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Получите первичный ключ подписки](quick-demo-map-app.md#get-the-primary-key-for-your-account), который иногда называется первичным ключом или ключом подписки.
3. Скачайте и установите [пакет SDK для Android Azure Maps](./how-to-use-android-map-control-library.md).

## <a name="incidents-traffic-data"></a>Данные трафика инцидентов

Вам потребуется импортировать следующие библиотеки для вызова `setTraffic` и `incidents` :

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 В следующем фрагменте кода показано, как отобразить данные трафика на карте. Мы передаем логическое значение в `incidents` метод и передаем его в `setTraffic` метод. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Потоковая передача данных

Сначала необходимо импортировать следующие библиотеки для вызова `setTraffic` и `flow` :

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Используйте следующий фрагмент кода для задания данных потока трафика. Аналогично коду в предыдущем разделе, мы передаем возвращаемое значение метода методу `flow` `setTraffic` . В можно передать четыре значения `flow` , и каждое значение будет `flow` возвращать соответствующее значение. Возвращаемое значение `flow` будет передано в качестве аргумента в `setTraffic` . Следующие четыре значения приведены в следующей таблице:

|Значение потока | Описание|
| :-- | :-- |
| Траффикфлов. NONE | Не отображает данные трафика на карте |
| Траффикфлов. RELATIVE | Показывает данные трафика, относящиеся к скорости произвольного потока в дороге |
| TrafficFlow.RELATIVE_DELAY | Отображает области, которые выполняются медленнее среднего ожидаемого значения задержки |
| Траффикфлов. ABSOLUTE | Показывает абсолютную скорость всех транспортных средств в дороге |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Отображение данных о трафике инцидента щелчком компонента

Чтобы получить инциденты для определенного компонента, можно использовать приведенный ниже код. При нажатии на эту функцию логика кода проверяет наличие инцидентов и создает сообщение об инциденте. В нижней части экрана появится сообщение с подробными сведениями.

1. Сначала необходимо изменить `res > layout > activity_main.xml` , чтобы он выглядел так, как показано ниже. Вы можете заменить `mapcontrol_centerLat` , `mapcontrol_centerLng` и `mapcontrol_zoom` нужными значениями. Помните, что уровень масштаба — это значение в диапазоне от 0 до 22. На уровне масштабирования 0 весь мир занимает одну плитку.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Добавьте следующий код в файл **MainActivity. Java** . Пакет включен по умолчанию, поэтому убедитесь, что пакет находится в верхней части.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
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

3. После включения приведенного выше кода в приложение вы сможете выбрать функцию и просмотреть подробные сведения о событиях трафика. В зависимости от широты, долготы и значений масштаба, используемых в файле **activity_main.xml** , вы увидите результаты, аналогичные следующему изображению:


    ![Инцидент — трафик на карте](./media/how-to-show-traffic-android/android-traffic.png)


## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как добавить дополнительные данные на карту, ознакомьтесь со следующими руководствами.

> [!div class="nextstepaction"]
> [Добавление слоя символов](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Добавление слоя фрагментов](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Добавить фигуры в карту Android](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Отображение сведений о компоненте](display-feature-information-android.md)