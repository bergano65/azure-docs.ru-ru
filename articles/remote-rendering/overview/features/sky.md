---
title: Отражения неба
description: Описывает, как настроить карты среды для переотражений.
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680613"
---
# <a name="sky-reflections"></a>Отражения неба

В удаленной отрисовке Azure для реалистичного освещения объектов используется высокоплотная текстура. Для приложений с расширенной реальности эта текстура должна напоминать реальную окружающую функцию, чтобы объекты были убедительными. В этой статье описывается, как изменить геотекстуру.

> [!NOTE]
> Текстура небесной заливки также называется *картой среды*. Эти термины взаимозаменяемы.

## <a name="object-lighting"></a>Освещение объектов

Удаленная визуализация Azure использует *физическую отрисовку* (PBR) для реалистичных вычислений освещения. Несмотря на то, что вы можете добавить источники освещения в сцену, использование хорошей текстуры с высокой [интенсивностью](lights.md) оказывает наибольшее влияние.

На рисунках ниже показаны результаты освещения разных поверхностей только с помощью высокотекстурной текстуры:

| Неровность  | 0                                        | 0,25                                          | 0,5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Без металла  | ![Dielectric0](media/dielectric-0.png)   | ![гринпоинтпарк](media/dielectric-0.25.png)  | ![гринпоинтпарк](media/dielectric-0.5.png)  | ![гринпоинтпарк](media/dielectric-0.75.png)  | ![гринпоинтпарк](media/dielectric-1.png)  |
| Metal      | ![гринпоинтпарк](media/metallic-0.png)  | ![гринпоинтпарк](media/metallic-0.25.png)    | ![гринпоинтпарк](media/metallic-0.5.png)    | ![гринпоинтпарк](media/metallic-0.75.png)    | ![гринпоинтпарк](media/metallic-1.png)    |

Дополнительные сведения о модели освещения см. в главе [материалы](../../concepts/materials.md) .

> [!IMPORTANT]
> Удаленная визуализация Azure использует текстуру небесно только для моделей освещения. Он не отображает перевозки в качестве фона, так как расширенные приложения реально уже имеют правильный фон — реальный мир.

## <a name="changing-the-sky-texture"></a>Изменение текстуры перевозки

Чтобы изменить карту среды, достаточно [Загрузить текстуру](../../concepts/textures.md) и изменить значение сеанса `SkyReflectionSettings`:

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

Обратите внимание `LoadTextureFromSASAsync` , что этот вариант используется выше, так как загружается встроенная текстура. Если загрузка производится из [связанных хранилищ BLOB-объектов](../../how-tos/create-an-account.md#link-storage-accounts), используйте вариант `LoadTextureAsync`.

## <a name="sky-texture-types"></a>Типы небесных текстур

В качестве карт среды можно использовать как *[кубемапс](https://en.wikipedia.org/wiki/Cube_mapping)* , так и *2D-текстуры* .

Все текстуры должны быть в [поддерживаемом формате текстуры](../../concepts/textures.md#supported-texture-formats). Вам не нужно предоставлять MIP-карты для небесных текстур.

### <a name="cube-environment-maps"></a>Карты среды Куба

Для справки ниже приведен неупакованный кубической карты:

![Развернутый кубической карты](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  Используйте `LoadTextureFromSASAsync` с `TextureType.CubeMap` для загрузки текстур кубической карты.

### <a name="sphere-environment-maps"></a>Карты среды Sphere

При использовании двухмерной текстуры в качестве схемы среды изображение должно находиться в [сферической области координат](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Перенебесное изображение в сферных координатах](media/spheremap-example.png)

Используйте `AzureSession.Actions.LoadTextureAsync` с `TextureType.Texture2D` для загрузки карт сферического окружения.

## <a name="built-in-environment-maps"></a>Встроенные карты среды

Удаленная визуализация Azure предоставляет несколько встроенных схем среды, которые всегда доступны. Все встроенные карты среды являются кубемапс.

|Идентификатор                         | Описание                                              | Рисунки                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Разнообразные индикаторы с чередованием, яркий базовый свет освещения    | ![Автомагазин](media/autoshop.png)
|builtin://BoilerRoom               | Яркий параметр освещения, несколько световых окон      | ![боилеррум](media/boiler-room.png)
|builtin://ColorfulStudio           | Разноцветные световые индикаторы в средней освещенности  | ![колорфулстудио](media/colorful-studio.png)
|builtin://Hangar                   | Умеренный яркий световой зал                     | ![смаллхангар](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Настройка «затемнение» с светло-темным контрастом              | ![индустриалпипеандвалве](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Постоянную наружное окружение, ярко-светлая область окна     | ![лебомбо](media/lebombo.png)
|builtin://SataraNight              | Темно-ночный небесно-голубой и земля со многими окружающими индикаторами   | ![сатаранигхт](media/satara-night.png)
|builtin://SunnyVondelpark          | Яркий солнечный свет и контрастность тени                      | ![суннивонделпарк](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Снятие небесного света с умеренным освещением от заземления            | ![сиферфонтеин](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Умеренное изменение Sun и затенения                         | ![теарсофстилбридже](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Вечером-светло-Закатный подход к Сумерки                    | ![веницесунсет](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Яркие, Луш-зеленый и белый световые тона, затемненное заземление | ![вхипплекрикрегионалпарк](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Постоянную с ярко-светлым заземлением                 | ![винтерривер](media/winter-river.png)
|builtin://DefaultSky               | То же, что и Теарсофстилбридже                               | ![дефаултски](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Дальнейшие шаги

* [Освещение](../../overview/features/lights.md)
* [Материалы](../../concepts/materials.md)
* [Текстуры](../../concepts/textures.md)
* [Программа командной строки Тексконв](../../resources/tools/tex-conv.md)
