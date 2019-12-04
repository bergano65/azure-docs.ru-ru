---
title: Грубая перелокализация
description: Краткое руководство по грубой локализации.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3477bac051346e4b334ff3437085c402090b2c98
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765467"
---
# <a name="coarse-relocalization"></a>Простое уточнение расположения

Грубая Локализация — это функция, которая предоставляет исходный ответ на вопрос: *где мое устройство сейчас и какое содержимое следует наблюдать?* Ответ не является точным, а в форме: *близко к этим привязкам попробуйте найти один из них*.

Грубая локализация работает путем связывания различных считанных датчиков устройств с созданием и запросом привязок. В сценариях для общего использования данные датчика обычно являются позицией GPS (Глобальная система позиционирования) устройства. Если GPS недоступен или ненадежен (например, «крышки»), данные датчиков состоят в точках доступа WiFi и маяках Bluetooth в диапазоне. Все собранные данные датчика вносят вклад в поддержание пространственного индекса. Пространственный индекс используется службой привязки для быстрого определения привязок, которые находятся в пределах приблизительно 100 метров устройства.

Быстрый поиск привязок, включенных с помощью грубой локализации, упрощает разработку приложений, поддерживающих коллекции мирового масштаба (скажем, миллионы геораспределенных географических точек). Сложность управления привязкой скрыта, что позволяет сосредоточиться на более подробной логике приложения. Вся тяжелая привязка выполняется за кулисами службы.

## <a name="collected-sensor-data"></a>Собранные данные датчика

Данные датчика, которые можно отправить в службу привязки, являются одним из следующих:

* Расположение GPS: Широта, Долгота, высота.
* Сила сигнала точек доступа WiFi в диапазоне.
* Сила сигнала сигналов маяков Bluetooth в диапазоне.

В общем случае приложению потребуется получить разрешения для конкретного устройства для доступа к данным GPS, Wi-Fi или BLE. Кроме того, некоторые из приведенных выше данных датчиков недоступны для определенных платформ. Чтобы учитывать эти ситуации, сбор данных датчика является необязательным и по умолчанию отключен.

## <a name="set-up-the-sensor-data-collection"></a>Настройка сбора данных датчика

Начнем с создания поставщика отпечатков пальцев с датчика и создания сеанса, осведомленного о нем:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Далее необходимо решить, какие датчики вы хотели бы использовать для грубой локализации. Это решение в целом зависит от разрабатываемого приложения, но рекомендации, приведенные в следующей таблице, помогут вам получить хорошую отправную точку:


|             | Выдвижки | Туризм |
|-------------|---------|----------|
| GPS         | Выключено | Включено |
| Wi-Fi        | Включено | Включено (необязательно) |
| BLE маяки | Вкл. (необязательно с предупреждениями см. ниже) | Выключено |


### <a name="enabling-gps"></a>Включение GPS

Если у вашего приложения уже есть разрешение на доступ к точке GPS устройства, можно настроить пространственные привязки Azure для его использования:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

При использовании GPS в приложении следует помнить, что в нем используются следующие средства чтения:

* Асинхронная и низкая частота (менее 1 Гц).
* ненадежный/шум (для среднего стандартного отклонения в 7-м).

Как правило, как ОС устройства, так и пространственные привязки Azure будут выполнять некоторую фильтрацию и экстраполяцию на необработанном сигнале GPS, пытаясь устранить эти проблемы. Эта дополнительная обработка требует дополнительного времени для конвергенции, поэтому для получения наилучших результатов выполните следующие действия.

* Создание поставщика отпечатков пальцев с датчика как можно раньше в приложении
* Отслеживание активности поставщика отпечатков пальцев и совместное использование нескольких сеансов

Если вы планируете использовать поставщик отпечатков пальцев с датчика вне сеанса привязки, убедитесь, что вы запускаете его перед запросом оценки датчика. Например, следующий код позаботится об обновлении позиции устройства на карте в режиме реального времени:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

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

# <a name="swifttabswift"></a>[Swift](#tab/swift)

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

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

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

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

### <a name="enabling-wifi"></a>Включение WiFi

Если у вашего приложения уже есть разрешение на доступ к состоянию Wi-Fi устройства, можно настроить пространственные привязки Azure для его использования:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

При использовании Wi-Fi в приложении помните о том, что для оборудования обычно используются следующие данные:

* Асинхронная и низкая частота (менее 0,1 Гц).
* потенциально регулируется на уровне ОС.
* ненадежный/шум (в среднем 3-dBm стандартное отклонение).

Пространственные привязки Azure попытаются создать отфильтрованную карту мощности сигнала Wi-Fi во время сеанса, пытаясь устранить эти проблемы. Для получения лучших результатов следует попытаться:

* Создайте сеанс, прежде чем поместить первую привязку.
* Обеспечьте активность сеанса в течение всего времени (то есть создайте все привязки и запрос в одном сеансе).

### <a name="enabling-bluetooth-beacons"></a>Включение маяков Bluetooth

Если у вашего приложения уже есть разрешение на доступ к состоянию Bluetooth устройства, можно настроить пространственные привязки Azure для его использования:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Маяки обычно являются универсальными устройствами, где все, включая UUID и MAC-адреса, можно настроить. Такая гибкость может быть проблемой для пространственных привязок Azure, которые считают, что маяки уникально идентифицируются по их UUID. Невозможность обеспечить такую уникальность скорее всего будет преобразовываться в пространственные норки. Для получения лучших результатов следует:

* Присвойте вашим маякам уникальные идентификаторы UUID.
* разверните их, как правило, в обычном шаблоне, например в виде сетки.
* Передайте список уникальных идентификаторов UUID маяка поставщику отпечатков пальцев:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Пространственные привязки Azure будут отслеживанием только маяков Bluetooth, которые находятся в списке. Вредоносные маяки, запрограммированные для предоставления идентификаторов UUID, могут негативно повлиять на качество службы. По этой причине вы должны использовать маяки только в проверенных пространствах, где можно управлять развертыванием.

## <a name="querying-with-sensor-data"></a>Запросы с данными датчика

После создания привязок со связанными данными датчика их можно начать с помощью считывания датчиков, сообщаемых вашим устройством. Вам больше не требуется предоставлять службе список известных привязок, которые вы ожидаете найти. Вы просто предоставляете службе сведения о расположении устройства, о которых сообщили встроенные датчики. Затем служба пространственных привязок будет вычислить набор привязок, близких к устройству, и попытается визуально сопоставить их.

Чтобы запросы использовали данные датчика, начните с создания критериев поиска:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

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

Параметр `DistanceInMeters` определяет, как далеко будет рассмотрен граф привязки для получения содержимого. Предположим, что для экземпляра заполнено пространство с привязками с постоянной плотностью 2 каждого счетчика. Более того, Камера на устройстве выполняет наблюдение за одной привязкой, и служба успешно размещает ее. Скорее всего, вы захотите получить все привязки, которые вы поместили поблизости, а не одну привязку, которую вы сейчас просматриваете. При условии, что размещенные привязки подключены к графу, служба может получить все соседние привязки, следуя краям графа. Объем выполненного обхода графа управляется `DistanceInMeters`; Вам будут предоставлены все привязки, подключенные к расположению, которое вы находили, но ближе к `DistanceInMeters`.

Помните, что большие значения для `MaxResultCount` могут негативно сказаться на производительности. Попробуйте задать для него разумное значение, которое имеет смысл для вашего приложения.

Наконец, необходимо сообщить сеансу, что будет использоваться Поиск на основе датчика:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Ожидаемые результаты

Устройства GPS класса "потребитель" обычно довольно неточны. Исследование, [занденбержен и барбеау (2011)][6] , сообщает медианную точность мобильных телефонов с помощью GPS (A-GPS), которая составляет около 7 метров. это довольно большое значение. Чтобы учитывать эти ошибки измерения, служба рассматривает привязки как распределения вероятностей в пространстве GPS. Таким образом, привязка теперь является областью пространства, которая, вероятнее всего, (то есть более 95% достоверности) содержит свою истинную, неизвестную точку GPS.

Та же причина применяется при выполнении запросов с помощью GPS. Устройство представляется в виде другой геопространственной области достоверности в отношении истинной, неизвестной координаты GPS. Обнаружение ближайших привязок преобразуется в простой поиск привязок с областями достоверности, *достаточно близкой* к региону достоверности устройства, как показано на рисунке ниже:

![Выбор кандидатов привязки с помощью GPS](media/coarse-reloc-gps-separation-distance.png)

Точность сигнала GPS, как при создании привязки, так и во время запросов, сильно влияет на набор возвращаемых привязок. В отличие от них, запросы, основанные на WiFi/маяках, будут рассматривать все привязки, имеющие по крайней мере одну точку доступа или Маяк с запросом. В этом смысле результат запроса, основанного на Wi-Fi/маяках, в основном определяется физическим диапазоном точек доступа, маркеров маяка и препятствий на окружающей среде.

В таблице ниже вычисляется ожидаемое пространство поиска для каждого типа датчика:

| Датчик      | Область поиска радиус пространства (примерно) | Сведения |
|-------------|:-------:|---------|
| GPS         | 20 м – 30 м | Определяется неуверенностью GPS среди других факторов. Сообщаемые числа оцениваются по срединной точности GPS для мобильных телефонов с помощью A-GPS, т. е. 7 метров. |
| Wi-Fi        | 50 м-100 м | Определяется диапазоном точек беспроводного доступа. Зависит от частоты, силы передатчика, физических препятствий, помех и т. д. |
| BLE маяки |  70 м | Определяется диапазоном сигналов маяка. Зависит от частоты, силы передачи, физических препятствий, помех и т. д. |

## <a name="per-platform-support"></a>Поддержка на уровне платформы

В следующей таблице перечислены данные датчика, собранные на каждой из поддерживаемых платформ, а также предупреждения, относящиеся к конкретной платформе.


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| GPS         | Н/Д | Поддерживается через API-интерфейсы [локатионманажер][3] (как GPS, так и Network) | Поддерживается через API-интерфейсы [кллокатионманажер][4] |
| Wi-Fi        | Поддерживается с частотой приблизительно одного сканирования каждые 3 секунды. | Поддерживается. Начиная с уровня API 28, сканирование Wi-Fi регулируется до 4 вызовов каждые 2 минуты. С Android 10 регулирование можно отключить в меню "Параметры разработчика". Дополнительные сведения см. в [документации по Android][5]. | Н/д — нет открытого API |
| BLE маяки | Ограничено [еддистоне][1] и [ибеакон][2] | Ограничено [еддистоне][1] и [ибеакон][2] | Ограничено [еддистоне][1] и [ибеакон][2] |

## <a name="next-steps"></a>Дальнейшие действия

Используйте грубую перелокализацию в приложении.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

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
