---
title: Показать данные о трафике на карте Андроида (ru) Карты Microsoft Azure
description: В этой статье вы узнаете, как отображать данные о трафике на карте с помощью Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e5611eeb08ac370e12cf452d57a87e449fbd80da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335376"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Отображение данных о трафике на карте с помощью Azure Maps Android SDK

Данные потока и данные об инцидентах являются двумя типами данных о трафике, которые могут отображаться на карте. В этом руководстве показано, как отобразить оба типа данных о трафике. Данные об инцидентах состоят из точечных и линейных данных для таких вещей, как строительство, закрытие дорог и несчастные случаи. Данные потока показывают метрики о потоке трафика на дороге.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем показать трафик на карте, необходимо [создать учетную запись Azure](quick-demo-map-app.md#create-an-account-with-azure-maps)и [получить ключ подписки.](quick-demo-map-app.md#get-the-primary-key-for-your-account) Затем вам нужно установить [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) и загрузить карту.

## <a name="incidents-traffic-data"></a>Данные о трафике инцидентов 

Для вызова `setTraffic` и: `incidents`

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Следующий фрагмент кода показывает, как отобразить данные о трафике на карте. Мы передаем boolean `incidents` значение метода, и `setTraffic` передать, что метод. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Данные о движении потока

Сначала необходимо импортировать следующие библиотеки `setTraffic` `flow`для вызова и:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Используйте следующий фрагмент кода для настройки данных о движении трафика. Подобно коду в предыдущем разделе, мы передаем `flow` значение `setTraffic` возврата метода методу. Есть четыре `flow`значения, которые могут быть переданы, `flow` и каждое значение вызовет, чтобы вернуть соответствующее значение. Возвратное `flow` значение будет затем передано `setTraffic`в качестве аргумента . Смотрите таблицу ниже для этих четырех значений:

| | |
| :-- | :-- |
| TrafficFlow.NONE | Не отображает данные о трафике на карте |
| TrafficFlow.RELATIVE | Отображает данные о трафике, которые относительно скорости свободного потока дороги |
| TrafficFlow.RELATIVE_DELAY | Отображает области, которые медленнее, чем средняя ожидаемая задержка |
| TrafficFlow.ABSOLUTE | Показывает абсолютную скорость всех транспортных средств на дороге |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Отображать данные о трафике инцидентов, нажав на функцию

Чтобы получить инциденты для конкретной функции, вы можете использовать код ниже. При нажатии на объект логика кода проверяет инциденты и создает сообщение об инциденте. Сообщение отображается в нижней части экрана с деталями.

1. Во-первых, вам нужно отойти **от res > макет> activity_main.xml,** так что он выглядит как один ниже. Вы можете `mapcontrol_centerLat` `mapcontrol_centerLng`заменить, `mapcontrol_zoom` и с желаемыми значениями. Напомним, уровень зума составляет значение от 0 до 22. На уровне увеличения 0 весь мир помещается на одну плитку.

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

2. Добавьте следующий код в файл **MainActivity.java.** Пакет включен по умолчанию, поэтому убедитесь, что вы держите ваш пакет в верхней части.

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

3. После включения вышеуказанного кода в приложение вы сможете нажать на функцию и увидеть детали дорожно-транспортных происшествий. В зависимости от широты, долготы и значений уровня масштабирования, которые вы использовали в файле **activity_main.xml,** вы увидите результаты, аналогичные следующему изображению:

   <center>

   ![Инцидент-трафик на карте](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Дальнейшие действия

Посмотреть следующие руководства, чтобы узнать, как добавить больше данных на карту:

> [!div class="nextstepaction"]
> [Добавление слоя символов](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Добавление слоя фрагментов](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Добавить фигуры на карту андроида](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Отображение сведений о компоненте](display-feature-information-android.md)
