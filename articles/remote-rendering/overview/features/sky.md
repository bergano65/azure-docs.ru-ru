---
title: Отражения неба
description: Описывает, как настроить карты среды для отражения неба
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680613"
---
# <a name="sky-reflections"></a>Отражения неба

В удаленном рендере Azure текстура неба используется для реалистичного освещения объектов. Для приложений дополненной реальности эта текстура должна напоминать ваше реальное окружение, чтобы объекты казались убедительными. В этой статье описывается, как изменить текстуру неба.

> [!NOTE]
> Текстура неба также называется *картой окружающей среды.* Эти термины используются взаимозаменяемо.

## <a name="object-lighting"></a>Освещение объектов

Azure Remote Rendering использует *физические визуализации* (PBR) для реалистичных вычислений освещения. Хотя вы можете добавить [источники света](lights.md) к вашей сцене, используя хорошую текстуру неба имеет наибольшее влияние.

На приведенных ниже изображениях показаны результаты освещения различных поверхностей только с текстурой неба:

| Шероховатости  | 0                                        | 0,25                                          | 0,5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Неметаллические  | ![Диэлектрик0](media/dielectric-0.png)   | ![ГринПойнтПарк](media/dielectric-0.25.png)  | ![ГринПойнтПарк](media/dielectric-0.5.png)  | ![ГринПойнтПарк](media/dielectric-0.75.png)  | ![ГринПойнтПарк](media/dielectric-1.png)  |
| Metal      | ![ГринПойнтПарк](media/metallic-0.png)  | ![ГринПойнтПарк](media/metallic-0.25.png)    | ![ГринПойнтПарк](media/metallic-0.5.png)    | ![ГринПойнтПарк](media/metallic-0.75.png)    | ![ГринПойнтПарк](media/metallic-1.png)    |

Для получения дополнительной информации о [materials](../../concepts/materials.md) модели освещения, см.

> [!IMPORTANT]
> Azure Remote Rendering использует текстуру неба только для моделей освещения. Он не делает небо фоном, так как приложения дополненной реальности уже имеют надлежащий фон - реальный мир.

## <a name="changing-the-sky-texture"></a>Изменение текстуры неба

Чтобы изменить карту среды, все, что вам нужно сделать, это [загрузить текстуру](../../concepts/textures.md) и изменить `SkyReflectionSettings`сеанс:

``` cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

Обратите внимание, что `LoadTextureFromSASAsync` вариант используется выше, потому что встроенная текстура загружается. В случае загрузки из [связанных хранилищ капли,](../../how-tos/create-an-account.md#link-storage-accounts)используйте `LoadTextureAsync` вариант.

## <a name="sky-texture-types"></a>Типы текстуры неба

В качестве карт среды можно использовать как *[кубические карты,](https://en.wikipedia.org/wiki/Cube_mapping)* так и *2D текстуры.*

Все текстуры должны быть в [поддерживаемом формате текстуры.](../../concepts/textures.md#supported-texture-formats) Вам не нужно предоставлять mipmaps для текстур неба.

### <a name="cube-environment-maps"></a>Карты среды кубов

Для справки, вот развернутая кубика:

![Необернутый кубик](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` Используйте `TextureType.CubeMap` с для загрузки текстур cubemap.

### <a name="sphere-environment-maps"></a>Карты среды сферы

При использовании 2D-текстуры в качестве карты среды изображение должно находиться в [пространстве сферических координат.](https://en.wikipedia.org/wiki/Spherical_coordinate_system)

![Изображение неба в сферических координатах](media/spheremap-example.png)

`AzureSession.Actions.LoadTextureAsync` Используйте `TextureType.Texture2D` с для загрузки сферических карт среды.

## <a name="built-in-environment-maps"></a>Встроенные карты среды

Azure Remote Rendering предоставляет несколько встроенных карт среды, которые всегда доступны. Все встроенные карты среды являются кубиками.

|Идентификатор                         | Описание                                              | Рисунки                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Разнообразие полосатых огней, яркое крытое базовое освещение    | ![Автомагазин](media/autoshop.png)
|builtin://BoilerRoom               | Яркий крытый свет настройки, несколько оконных фонарей      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | Разнообразие цветные огни в среде света в помещении настройки  | ![Красочнаястудия](media/colorful-studio.png)
|builtin://Hangar                   | Умеренно яркий свет окружающего зала                     | ![МалыйХангар](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Тусклый крытый параметр со светлым контрастом              | ![ПромышленныйпипэндВванв](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Дневной свет окружающей комнаты, яркий свет области окна     | ![Лебомбо](media/lebombo.png)
|builtin://SataraNight              | Темное ночное небо и земля со многими окружающими огнями   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | Яркий солнечный свет и контраст теней                      | ![СанниВондельпарк](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Ясное небо свет с умеренным наземным освещением            | ![Сиферфонтейн](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Умеренно меняющиеся солнце и тень                         | ![TearsofsteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Вечерний закат света приближается сумерки                    | ![ВенецияЗакат](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Яркие, пышные и белые светлые тона, затемненные земли | ![УипплКрикРегиональныйПарк](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Дневное время с ярким окружающим наземным светом                 | ![УинтерРивер](media/winter-river.png)
|builtin://DefaultSky               | То же, что TearsOfSteelBridge                               | ![По умолчаниюСки](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Огни](../../overview/features/lights.md)
* [Материалы](../../concepts/materials.md)
* [Текстуры](../../concepts/textures.md)
* [Инструмент командной строки TexConv](../../resources/tools/tex-conv.md)
