---
title: Текстуры
description: Рабочий процесс текстурных ресурсов
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681666"
---
# <a name="textures"></a>Текстуры

Текстуры являются неизменяемым [общим ресурсом.](../concepts/lifetime.md) Текстуры могут быть загружены из [кабеля хранения](../how-tos/conversion/blob-storage.md) и применяться к моделям непосредственно, как показано в [учебнике: Изменение окружающей среды и материалов.](../tutorials/unity/changing-environment-and-materials.md) Чаще всего, однако, текстуры будут частью [преобразованной модели,](../how-tos/conversion/model-conversion.md)где они ссылаются на его [материалы.](materials.md)

## <a name="texture-types"></a>Типы текстур

Различные типы текстур имеют различные случаи использования:

* **2D Текстуры** в основном используются в [материалах.](materials.md)
* **Cubemaps** могут быть использованы для [неба](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Поддерживаемые форматы текстур

Все текстуры, отданные ARR, должны быть в [формате DDS.](https://en.wikipedia.org/wiki/DirectDraw_Surface) Предпочтительно с mipmaps и сжатием текстуры. Если вы хотите автоматизировать процесс преобразования, обратитесь к [инструменту командной строки TexConv.](../resources/tools/tex-conv.md)

## <a name="loading-textures"></a>Загрузка текстур

При загрузке текстуры необходимо указать ее ожидаемый тип. Если тип несоответствует, текстурная нагрузка завершается неудачей.
Загрузка текстуры с тем же URI дважды вернет тот же объект текстуры, так как это [общий ресурс.](../concepts/lifetime.md)

Как и в погрузочно-загрузка моделей, есть два варианта решения текстуры актива в исходной капли хранения:

* Текстурный актив может быть решен с помощью SAS URI. Соответствующая функция `LoadTextureFromSASAsync` загрузки с параметром `LoadTextureFromSASParams`. Используйте этот вариант также при загрузке [встроенных текстур.](../overview/features/sky.md#built-in-environment-maps)
* Текстура может быть решена параметрами хранения капли непосредственно, в случае, если [хранилище капли связано с учетной записью.](../how-tos/create-an-account.md#link-storage-accounts) Соответствующая функция загрузки `LoadTextureAsync` в `LoadTextureParams`этом случае с параметром .

Следующий пример кода показывает, как загрузить текстуру через SAS URI (или встроенную текстуру) - обратите внимание, что только функция загрузки / параметр отличается для другого случая:

``` cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureAsync(new LoadTextureParams(textureUri, TextureType.Texture2D));
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

В зависимости от того, для чего должна использоваться текстура, могут быть ограничения для типа текстуры и содержимого. Например, карта шероховатостей [материала PBR](../overview/features/pbr-materials.md) должна быть серой шкалой.

> [!CAUTION]
> Все функции *Async* в ARR возвращают асинхронные объекты эксплуатации. Ссылка на эти объекты должна храниться до завершения операции. В противном случае сборщик мусора может удалить операцию досрочно, и она никогда не может закончиться. В примере кода выше переменная "_textureLoad" используется для удержания ссылки до прибытия *события Завершено.*

## <a name="next-steps"></a>Дальнейшие действия

* [Материалы](materials.md)
* [Небо](../overview/features/sky.md)
