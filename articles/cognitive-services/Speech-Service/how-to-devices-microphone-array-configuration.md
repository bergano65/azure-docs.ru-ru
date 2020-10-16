---
title: Настройка микропрограммы для массива микрофонов — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как настроить массив микрофонов, чтобы пакет SDK для речевых устройств мог его использовать.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: a2652bed6c8e7dec0a6fe8f9471793c3873646bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82781760"
---
# <a name="how-to-configure-a-microphone-array"></a>Настройка набора микрофонов

В этой статье вы узнаете, как настроить [массив микрофонов](https://aka.ms/sdsdk-microphone). Он включает в себя настройку рабочего угла и выбор микрофона, используемого для пакета SDK для речевых устройств.

Пакет SDK для речевых устройств лучше подходит для массива микрофонов, разработанного в соответствии с [нашими рекомендациями](https://aka.ms/sdsdk-microphone). Конфигурация массива микрофонов может быть предоставлена операционной системой или предоставлена одним из следующих методов.

Изначально поддерживаемые микрофона в пакете SDK для речевых устройств выбираются из фиксированного набора конфигураций.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

В Windows конфигурация массива микрофона предоставляется аудио драйвером.

В версии v 1.11.0 пакет SDK для речевых устройств также поддерживает настройку из [JSON-файла](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
В Windows сведения о геометрических массивах микрофона автоматически получаются из аудио драйвера. Таким образом, свойства `DeviceGeometry` ,  `SelectedGeometry` и `MicArrayGeometryConfigFile` являются необязательными. Мы используем [файл JSON](https://aka.ms/sdsdk-micarray-json) , предоставленный с помощью, `MicArrayGeometryConfigFile` для получения диапазона беамформинг.

Если массив микрофонов указан с помощью `AudioConfig::FromMicrophoneInput` , используется указанный микрофон. Если микрофон не указан или `AudioConfig::FromDefaultMicrophoneInput` вызывается, используется микрофон по умолчанию, который указан в параметрах звука в Windows.
Microsoft Audio Stack в пакете SDK для речевых устройств поддерживает выборку скоростей, которые являются неотъемлемо кратными 16 кГц.

## <a name="linux"></a>Linux
В Linux необходимо предоставить сведения о геометрии микрофона. Использование `DeviceGeometry` и `SelectedGeometry` сохраняет поддержку. Его также можно предоставить с помощью файла JSON, используя `MicArrayGeometryConfigFile` свойство. Как и в Windows, диапазон беамформинг может быть предоставлен JSON File.

Если массив микрофонов указан с помощью `AudioConfig::FromMicrophoneInput` , используется указанный микрофон. Если микрофон не указан или `AudioConfig::FromDefaultMicrophoneInput` вызван, мы записываем из устройства алса с именем *Default*. По умолчанию *значение по умолчанию* всегда указывает на нулевое устройство 0, но пользователи могут изменить его в `asound.conf` файле. 

Microsoft Audio Stack в пакете SDK для речевых устройств поддерживает понижение скорости выборки, которые являются неотъемлемо кратными 16 кГц. Кроме того, поддерживаются следующие форматы: 32-разрядный IEEE с прямым порядком байтов, 32-бит с прямым обратным порядком байтов, 16-разрядное целое число со знаком, с прямым обратным порядком и 8-разрядное целое со знаком с обратным порядком байтов

## <a name="android"></a>Android
В настоящее время пакет SDK для речевых устройств поддерживает только [Рубо v1](speech-devices-sdk-android-quickstart.md) . Поведение аналогично предыдущему выпуску, за исключением того, что `MicArrayGeometryConfigFile` свойство Now можно использовать для указания JSON-файла, содержащего диапазон беамформинг.

## <a name="microphone-array-configuration-json"></a>JSON конфигурации массива Microphone

Файл JSON для конфигурации с геометрическим массивом микрофона будет соответствовать [схеме JSON](https://aka.ms/sdsdk-micarray-json). Ниже приведены некоторые примеры, которые следуют за схемой.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


либо


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

либо

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Выберите свое речевое устройство](get-speech-devices-sdk.md)
