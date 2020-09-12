---
title: Отражения неба
description: Узнайте, как настроить карту среды для отражения неба
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 39e3b41d49ad06e5dbe5164809a6743da8dedae5
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613755"
---
# <a name="sky-reflections"></a>Отражения неба

В Удаленной отрисовке Azure для реалистичного освещения объектов применяется текстура неба. Для приложений дополненной реальности эта текстура должна убедительно имитировать обстановку реального мира. В этой статье описано, как изменить текстуру неба.

> [!NOTE]
> Текстура неба также называется *картой среды*. Эти термины являются взаимозаменяемыми.

## <a name="object-lighting"></a>Освещение объекта

Удаленная отрисовка Azure использует *физически корректную отрисовку* для вычисления реалистичного освещения. Вы можете добавлять в сцену разные [источники света](lights.md), но хорошая текстура неба дает самый лучший результат.

На рисунках ниже показаны результаты освещения разных поверхностей только текстурой неба:

| Неровность  | 0                                        | 0,25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Неметаллические  | ![Неэлектрический, грубость = 0](media/dielectric-0.png)   | ![Неэлектрический, грубость = 0,25](media/dielectric-0.25.png)  | ![Неэлектрический, грубость = 0,5](media/dielectric-0.5.png)  | ![Неэлектрический, грубость = 0,75](media/dielectric-0.75.png)  | ![Неэлектрический, грубость = 1](media/dielectric-1.png)  |
| Металлические      | ![Металл, грубость = 0](media/metallic-0.png)  | ![Металлическое, грубое = 0,25](media/metallic-0.25.png)    | ![Металл, грубость = 0,5](media/metallic-0.5.png)    | ![Металл, грубое значение = 0,75](media/metallic-0.75.png)    | ![Металл, грубость = 1](media/metallic-1.png)    |

Дополнительные сведения о модели освещения см. в главе о [материалах](../../concepts/materials.md).

> [!IMPORTANT]
> Удаленная отрисовка Azure использует текстуру неба для моделей освещения. Она не отрисовывает цвет неба в качестве фона, так как приложения дополненной реальности уже имеют надлежащий фон, то есть реальный мир.

## <a name="changing-the-sky-texture"></a>Изменение текстуры неба

Чтобы изменить карту среды, достаточно [загрузить текстуру](../../concepts/textures.md) и изменить значение `SkyReflectionSettings` для сеанса.

```cs
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

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
        {
            if (res->GetIsRanToCompletion())
            {
                ApiHandle<SkyReflectionSettings> settings = session->Actions()->GetSkyReflectionSettings();
                settings->SetSkyReflectionTexture(res->GetResult());
            }
            else
            {
                printf("Texture loading failed!\n");
            }
        });
}

```

Обратите внимание, что выше используется вариант `LoadTextureFromSASAsync`, так как загружаются встроенные текстуры. Если загрузка производится из [связанных хранилищ BLOB-объектов](../../how-tos/create-an-account.md#link-storage-accounts), используйте вариант `LoadTextureAsync`.

## <a name="sky-texture-types"></a>Типы текстур неба

Вы можете использовать в качестве карты среды *[cubemap](https://en.wikipedia.org/wiki/Cube_mapping)* или *двухмерную текстуру*.

Все текстуры должны иметь [поддерживаемый формат текстуры](../../concepts/textures.md#supported-texture-formats). Для текстур неба не нужно предоставлять MIP-карты.

### <a name="cube-environment-maps"></a>Кубические карты среды

Для информации ниже приведен пример развернутого объекта cubemap.

![Развернутый объект cubemap](media/Cubemap-example.png)

Используйте `AzureSession.Actions.LoadTextureAsync`/ `LoadTextureFromSASAsync` с `TextureType.CubeMap` для загрузки текстур кубической карты (cubemap).

### <a name="sphere-environment-maps"></a>Сферические карты среды

При использовании двухмерной текстуры в качестве карты среды изображение должно находиться в [ пространстве сферических координат](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Изображение неба вычисляется в сферических координатах](media/spheremap-example.png)

Используйте `AzureSession.Actions.LoadTextureAsync` с `TextureType.Texture2D` для загрузки сферических карт среды.

## <a name="built-in-environment-maps"></a>Встроенные карты среды

Удаленная отрисовка Azure предоставляет несколько встроенных карт среды, которые всегда доступны. Все встроенные карты среды являются кубическими (cubemap).

|Идентификатор                         | Описание                                              | Иллюстрация                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Разнообразные полосы света, яркое базовое освещение для закрытых помещений    | ![Скибокса автомагазина, используемая для освещения объекта](media/autoshop.png)
|builtin://BoilerRoom               | Яркое освещение для сцен в закрытых помещениях, свет от нескольких окон      | ![Боилеррум скибокс, используемый для освещения объекта](media/boiler-room.png)
|builtin://ColorfulStudio           | Окрашенный свет в закрытом помещении средней освещенности  | ![Колорфулстудио скибокс, используемый для освещения объекта](media/colorful-studio.png)
|builtin://Hangar                   | Равномерное неинтенсивное освещение в помещении большого размера                     | ![Смаллхангар скибокс, используемый для освещения объекта](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Тускло освещенное закрытое помещение с контрастными участками              | ![Индустриалпипеандвалве скибокс, используемый для освещения объекта](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Равномерное дневное освещение в комнате, ярко освещенная область окна     | ![Лебомбо скибокс, используемый для освещения объекта](media/lebombo.png)
|builtin://SataraNight              | Темное ночное небо и земля со множеством источников света   | ![Сатаранигхт скибокс, используемый для освещения объекта](media/satara-night.png)
|builtin://SunnyVondelpark          | Яркий солнечный свет и контрастные тени                      | ![Суннивонделпарк скибокс, используемый для освещения объекта](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Чистое небо, умеренное освещение от земли            | ![Сиферфонтеин скибокс, используемый для освещения объекта](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Умеренное освещение с неравномерными светлыми и затененными участками                         | ![Теарсофстилбридже скибокс, используемый для освещения объекта](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Вечернее закатное освещение перед сумерками                    | ![Веницесунсет скибокс, используемый для освещения объекта](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Освещение ярких тонов зеленого и белого цвета, затененная земля | ![Вхипплекрикрегионалпарк скибокс, используемый для освещения объекта](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Дневное освещение с ярко и равномерно освещенной землей                 | ![Винтерривер скибокс, используемый для освещения объекта](media/winter-river.png)
|builtin://DefaultSky               | То же, что и TearsOfSteelBridge                               | ![Дефаултски скибокс, используемый для освещения объекта](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>Документирование API

* [C# Ремотеманажер. Скирефлектионсеттингс, свойство](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.skyreflectionsettings)
* [C++ Ремотеманажер:: Скирефлектионсеттингс ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#skyreflectionsettings)

## <a name="next-steps"></a>Дальнейшие действия

* [Освещение](../../overview/features/lights.md)
* [Материалы](../../concepts/materials.md)
* [Текстуры](../../concepts/textures.md)
* [Инструмент командной строки TexConv](../../resources/tools/tex-conv.md)
