---
title: Текстуры
description: Рабочий процесс ресурса текстуры
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 27395fe377972f51c849f8a61f51a628612ed54d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202673"
---
# <a name="textures"></a>Текстуры

Текстуры являются неизменяемым [общим ресурсом](../concepts/lifetime.md). Текстуры можно загружать из [хранилища BLOB-объектов](../how-tos/conversion/blob-storage.md) и применять непосредственно к моделям, как показано в [Учебник. Изменение среды и материалов](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). Однако наиболее часто текстуры будут частью [преобразованной модели](../how-tos/conversion/model-conversion.md), где на них ссылаются ее [материалы](materials.md).

## <a name="texture-types"></a>Типы текстур

Различные типы текстур используются в разных случаях.

* **2D-текстуры** в основном используются в [ материалах](materials.md).
* **Кубические карты** обычно используются для [неба](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Поддерживаемые форматы текстур

Все текстуры, переданные в ARR, должны быть в [формате DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). Желательно с MIP-картами и сжатием текстур. Чтобы автоматизировать процесс преобразования, см. [средство командной строки TexConv](../resources/tools/tex-conv.md).

## <a name="loading-textures"></a>Загрузка текстур

При загрузке текстуры необходимо указать ожидаемый тип. Если тип не соответствует, происходит сбой загрузки текстуры.
При загрузке текстуры с одним и тем же URI дважды, возвращается один и тот же объект текстуры, так как это [общий ресурс](../concepts/lifetime.md).

Аналогично загрузке моделей, существует два варианта обращения к ресурсу текстуры в исходном хранилище BLOB-объектов.

* К ресурсу текстуры можно обращаться по подписанному URL-адресу. Для этого используется функция `LoadTextureFromSASAsync` с параметром `LoadTextureFromSASParams`. Также используйте этот вариант для загрузки [встроенных текстур](../overview/features/sky.md#built-in-environment-maps).
* К текстуре также можно обращаться напрямую по параметрам хранилища BLOB-объектов, если [хранилище BLOB-объектов связано с учетной записью](../how-tos/create-an-account.md#link-storage-accounts). В этом случае используется функция загрузки `LoadTextureAsync` с параметром `LoadTextureParams`.

В следующем примере кода показано, как загрузить текстуру через URI SAS (или встроенную текстуру). Обратите внимание, что во втором случае отличается только функция/параметр загрузки.

```cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

```cpp
void LoadMyTexture(ApiHandle<AzureSession> session, std::string textureUri)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::Texture2D;
    params.TextureUrl = std::move(textureUri);
    ApiHandle<LoadTextureAsync> textureLoad = *session->Actions()->LoadTextureFromSASAsync(params);
    textureLoad->Completed([](ApiHandle<LoadTextureAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result()
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}
```

В зависимости от того, для чего предполагается использовать текстуру, могут существовать ограничения по типу и содержимому текстуры. Например, карты неровностей [материала PBR](../overview/features/pbr-materials.md) должны быть в оттенках серого.

> [!CAUTION]
> Все *асинхронные функции* в ARR возвращают объекты асинхронной операции. Ссылку на эти объекты необходимо хранить до завершения операции. В противном случае сборщик мусора C# может удалить операцию раньше, чем она завершится. В примере кода выше переменная-член «_textureLoad» используется, чтобы хранить ссылку до наступления события *Completed*.

## <a name="api-documentation"></a>Документирование API

* [Класс текстуры C#](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# Ремотеманажер. Лоадтекстуреасинк ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtextureasync)
* [C# Ремотеманажер. Лоадтекстурефромсасасинк ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtexturefromsasasync)
* [Класс текстуры C++](/cpp/api/remote-rendering/texture)
* [C++ Ремотеманажер:: Лоадтекстуреасинк ()](/cpp/api/remote-rendering/remotemanager#loadtextureasync)
* [C++ Ремотеманажер:: Лоадтекстурефромсасасинк ()](/cpp/api/remote-rendering/remotemanager#loadtexturefromsasasync)

## <a name="next-steps"></a>Дальнейшие действия

* [Материалы](materials.md)
* [Небо](../overview/features/sky.md)