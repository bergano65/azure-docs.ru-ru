---
title: Грубая релокализация
description: Узнайте об использовании coarse relocalization, чтобы найти якоря рядом с вами.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844400"
---
# <a name="coarse-relocalization"></a>Простое уточнение расположения

Грубая переимеливательность — это функция, которая дает первоначальный ответ на вопрос: *Где сейчас находится мое устройство / Какое содержимое я должен наблюдать?* Ответ не точен, но вместо этого в форме: *Вы близки к этим якорям; попробуйте найти один из них*.

Грубая релокализация работает, связывая различные показания датчиков на устройстве как с созданием, так и с запросом якорей. Для наружных сценариев данные датчиков, как правило, GPS (Глобальная система позиционирования) положение устройства. Когда GPS не доступен или ненадежен (например, в помещении), данные датчика состоят из точек доступа Wi-Fi и Bluetooth-маяков в диапазоне. Все собранные данные датчиков способствуют поддержанию пространственного индекса, который используется пространственными якорями Azure для быстрого определения якорей, находящихся в пределах примерно 100 метров от вашего устройства.

Быстрый поиск якорей, включенных в грубую релокализацию, упрощает разработку приложений, поддерживаемых коллекциями мирового масштаба (скажем, миллионами геораспределенных) якорей. Сложность управления якорями все скрыты, что позволяет больше сосредоточиться на вашей удивительной логики приложения. Вся якорная тяжелая работа сделана для вас за кулисами пространственными якорями Azure.

## <a name="collected-sensor-data"></a>Собранные данные датчиков

Данные датчика, которые можно отправить на якорную службу, одним из следующих:

* Положение GPS: широта, долгота, высота.
* Сила сигнала точек доступа Wi-Fi в диапазоне.
* Сила сигнала Bluetooth-маяков в диапазоне.

Как правило, для доступа к данным GPS, Wi-Fi или BLE ваше приложение должно будет получить разрешения на доступ к данным GPS, WiFi или BLE. Кроме того, некоторые из приведенных выше данных датчиков недоступны по дизайну на некоторых платформах. Для учета этих ситуаций сбор данных датчиков является необязательным и выключен по умолчанию.

## <a name="set-up-the-sensor-data-collection"></a>Настройка сбора данных датчиков

Начнем с создания поставщика отпечатков пальцев датчиков и информирования сессии о нем:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[СЕ НДК](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[СЕ Винрт](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Далее необходимо решить, какие датчики вы хотели бы использовать для грубой релокализации. Это решение характерно для разрабатываемого приложения, но рекомендации в следующей таблице должны дать вам хорошую отправную точку:


|             | Помещении | На открытом воздухе |
|-------------|---------|----------|
| Gps         | Выкл. | С |
| Wi-Fi        | С | На (необязательно) |
| Ble маяки | На (необязательно с оговорками, см. ниже) | Выкл. |


### <a name="enabling-gps"></a>Включение GPS

Предполагая, что ваше приложение уже имеет разрешение на доступ к положению GPS устройства, вы можете настроить пространственные якоря Azure, чтобы использовать его:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[СЕ НДК](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[СЕ Винрт](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

При использовании GPS в приложении, имейте в виду, что показания, предоставляемые аппаратным обеспечением, как правило:

* асинхронная и низкочастотная (менее 1 Гц).
* ненадежный / шумный (в среднем 7-м стандартное отклонение).

Как правило, как оС устройства, так и пространственные якоря Azure будут делать некоторую фильтрацию и экстраполяцию на необработанном сигнале GPS в попытке смягчить эти проблемы. Эта дополнительная обработка требует дополнительного времени для конвергенции, поэтому для достижения наилучших результатов следует попытаться:

* создать одного поставщика отпечатков пальцев датчиков как можно раньше в вашем приложении
* держать датчик отпечатков пальцев поставщика жив между несколькими сессиями
* поделиться поставщиком отпечатков пальцев датчика между несколькими сеансами

Если вы планируете использовать поставщика отпечатков пальцев датчика вне якорного сеанса, убедитесь, что вы запустите его, прежде чем запрашивать оценки датчиков. Например, следующий код будет заботиться об обновлении позиции устройства на карте в режиме реального времени:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[СЕ НДК](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[СЕ Винрт](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Включение Wi-Fi

Предполагая, что у приложения уже есть разрешение на доступ к состоянию Wi-Fi устройства, можно настроить пространственные якоря Azure, чтобы использовать его:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[СЕ НДК](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[СЕ Винрт](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

При использовании Wi-Fi в приложении, имейте в виду, что показания, предоставляемые аппаратным обеспечением, как правило:

* асинхронная и низкочастотная (менее 0,1 Гц).
* потенциально задушен на уровне ОС.
* ненадежный / шумный (в среднем 3-дБм стандартного отклонения).

Пространственные якоря Azure попытаются создать фильтрованную карту силы сигнала WiFi во время сеанса в попытке смягчить эти проблемы. Для достижения наилучших результатов следует попытаться:

* создать сеанс задолго до размещения первого якоря.
* сохранить сеанс в живых как можно дольше (т.е. создайте все якоря и запрос в одном сеансе).

### <a name="enabling-bluetooth-beacons"></a>Включение Bluetooth-маяков

Предполагая, что у приложения уже есть разрешение на доступ к состоянию Bluetooth устройства, можно настроить пространственные якоря Azure, чтобы использовать его:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[СЕ НДК](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[СЕ Винрт](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Маяки, как правило, универсальные устройства, где все - в том числе UUIDs и MAC-адреса - могут быть настроены. Эта гибкость может быть проблематичной для пространственных якорей Azure, поскольку она считает, что маяки однозначно идентифицируется их UUID. Неспособность обеспечить эту уникальность, скорее всего, приведет к пространственной червоточины. Для достижения наилучших результатов следует:

* присвоить уникальные UUID к вашим маякам.
* развернуть их - как правило, в обычном шаблоне, например, в сетке.
* передать список уникальных УИД-маяка поставщику отпечатков пальцев:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[СЕ НДК](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[СЕ Винрт](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Пространственные якоря Azure будут отслеживать только маяки Bluetooth, которые находятся в известном списке UUID-интерфейсов, близких к маяку. Вредоносные маяки, запрограммированные иметь включенные в список UUID, могут по-прежнему негативно влиять на качество услуги. По этой причине маяки следует использовать только в курируемых помещениях, где можно контролировать их развертывание.

## <a name="querying-with-sensor-data"></a>Запрос с данными датчиков

После создания якорей с соответствующими данными датчика, вы можете начать их извлечения с помощью показаний датчика, сообщаемых вашим устройством. Вы больше не обязаны предоставлять службу со списком известных якорей, которые вы ожидаете найти - вместо этого вы просто сообщите службе о местонахождении вашего устройства, о чем сообщают его бортовые датчики. Затем пространственные якоря Azure вычислят набор якорей рядом с устройством и попытаются визуально сопоставить их.

Чтобы запросы использовали данные датчика, начните с создания критериев «близкого устройства»:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[СЕ НДК](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[СЕ Винрт](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

Параметр `DistanceInMeters` контролирует, как далеко мы будем исследовать якорь графа для получения содержимого. Предположим, например, что вы заселили некоторое пространство с якорями при постоянной плотности 2 каждый метр. Кроме того, камера на вашем устройстве наблюдает за одним якорем, и служба успешно его обнаружила. Скорее всего, вы заинтересованы в получении всех якорей, которые вы разместили поблизости, а не одного якоря, который вы сейчас наблюдаете. Предполагая, что якоря, которые вы разместили, подключены к графику, служба может получить все близлежащие якоря для вас, следуя краям графика. Количество пройденного графика контролируется `DistanceInMeters`; Вам будут предоставлены все якоря, подключенные к тому, `DistanceInMeters`который вы нашли, которые находятся ближе, чем .

Имейте в виду, `MaxResultCount` что большие значения могут негативно повлиять на производительность. Установите его на разумное значение для вашего приложения.

Наконец, вам нужно сказать сессии использовать датчик на основе смотреть вверх:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[СЕ НДК](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[СЕ Винрт](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Ожидаемые результаты

Устройства GPS потребительского класса, как правило, довольно неточны. Исследование, проведенное [Заненбергеном и Барбо (2011)][6] сообщает, что средняя точность мобильных телефонов с помощью GPS (A-GPS) составляет около 7 метров - довольно большое значение, чтобы быть проигнорированы! Для учета этих ошибок измерения служба рассматривает якоря как распределение вероятности в пространстве GPS. Таким образом, якорь в настоящее время область пространства, что, скорее всего (то есть, с более чем 95% уверенности) содержит свою истинную, неизвестную позицию GPS.

Те же рассуждения применяются при запросе с GPS. Устройство представлено как еще один пространственный регион доверия вокруг своего истинного, неизвестного положения GPS. Обнаружение близлежащих якорей означает просто найти якоря с регионами доверия *достаточно близко к* области доверия устройства, как показано на рисунке ниже:

![Отбор якорных кандидатов с ПОМОЩЬю GPS](media/coarse-reloc-gps-separation-distance.png)

Точность сигнала GPS, как на якорь создания, а также во время запросов, имеет большое влияние на набор вернулся якорей. В отличие от этого, запросы, основанные на WiFi / маяки будут рассматривать все якоря, которые имеют по крайней мере одну точку доступа / маяк в общем с запросом. В этом смысле результат запроса, основанного на WiFi / маяках, в основном определяется физическим диапазоном точек доступа / маяков и экологическими препятствиями.

В приведенной ниже таблице оценивается ожидаемое пространство поиска для каждого типа датчиков:

| Датчик      | Радиус поиска (приблизительно.) | Сведения |
|-------------|:-------:|---------|
| Gps         | 20 м - 30 м | Определяется неопределенностью GPS среди других факторов. Заявленные цифры оцениваются по средней точности GPS мобильных телефонов с A-GPS, то есть 7 метров. |
| Wi-Fi        | 50 м - 100 м | Определяется диапазоном точек беспроводного доступа. Зависит от частоты, силы передатчика, физических препятствий, помех и так далее. |
| Ble маяки |  70 м | Определяется дальностью маяка. Зависит от частоты, силы передачи, физических препятствий, помех и так далее. |

## <a name="per-platform-support"></a>Поддержка на платформе

В следующей таблице кратко излагаются данные датчиков, собранные на каждой из поддерживаемых платформ, а также любые оговорки, связанные с платформой:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| Gps         | Недоступно | Поддерживается с помощью AIS [LocationManager][3] (как GPS, так и NETWORK) | Поддерживается с помощью [APIs CLLocationManager][4] |
| Wi-Fi        | Поддерживается со скоростью примерно одного сканирования каждые 3 секунды | Поддерживается. Начиная с уровня 28 Уровня API, wiFi-сканы задушены до 4 вызовов каждые 2 минуты. С Android 10, регулирование может быть отключено из меню настроек разработчика. Для получения дополнительной информации, см [документация Android][5]. | N/A - нет общедоступного API |
| Ble маяки | Ограничено [Эддистоном][1] и [iBeacon][2] | Ограничено [Эддистоном][1] и [iBeacon][2] | Ограничено [Эддистоном][1] и [iBeacon][2] |

## <a name="next-steps"></a>Дальнейшие действия

Используйте грубую локализацию в приложении.

> [!div class="nextstepaction"]
> [Единство](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++ (NDK)](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++ (WinRT)](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
