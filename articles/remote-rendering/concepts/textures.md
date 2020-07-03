---
title: Текстуры
description: Рабочий процесс ресурса текстуры
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681666"
---
# <a name="textures"></a>Текстуры

Текстуры — это неизменяемый [общий ресурс](../concepts/lifetime.md). Текстуры могут быть загружены из [хранилища BLOB-объектов](../how-tos/conversion/blob-storage.md) и применены непосредственно к моделям, как показано в [руководстве. изменение среды и материалов](../tutorials/unity/changing-environment-and-materials.md). Однако чаще всего текстуры будут частью [преобразованной модели](../how-tos/conversion/model-conversion.md), на которую ссылаются [материалы](materials.md).

## <a name="texture-types"></a>Типы текстур

Различные типы текстур имеют разные варианты использования.

* **2D-текстуры** в основном используются в [материалах](materials.md).
* **Кубемапс** можно использовать для [перевозки](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Поддерживаемые форматы текстур

Все текстуры, переданные в ARR, должны быть в [формате DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). Желательно с MIP-карты и сжатием текстур. Если вы хотите автоматизировать процесс преобразования, ознакомьтесь [с программой командной строки тексконв](../resources/tools/tex-conv.md) .

## <a name="loading-textures"></a>Загрузка текстур

При загрузке текстуры необходимо указать ожидаемый тип. Если тип не соответствует, происходит сбой загрузки текстуры.
При загрузке текстуры с таким же URI дважды возвращается тот же объект текстуры, что и [общий ресурс](../concepts/lifetime.md).

Аналогично загрузке моделей существует два варианта адресации ресурса текстуры в исходном хранилище BLOB-объектов:

* URI SAS может обращаться к ресурсу текстуры. Соответствующая функция загрузки `LoadTextureFromSASAsync` связана с `LoadTextureFromSASParams`параметром. Используйте этот вариант также при загрузке [встроенных текстур](../overview/features/sky.md#built-in-environment-maps).
* Вы можете напрямую обращаться к текстуре с помощью параметров хранилища больших двоичных объектов, если [хранилище BLOB-объектов связано с учетной записью](../how-tos/create-an-account.md#link-storage-accounts). Соответствующая функция загрузки в этом случае `LoadTextureAsync` — с `LoadTextureParams`параметром.

В следующем примере кода показано, как загрузить текстуру через URI SAS (или встроенную текстуру). Обратите внимание, что в другом случае для другого варианта различаются только функции или параметры загрузки.

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

В зависимости от того, для чего предполагается использовать текстуру, могут существовать ограничения для типа и содержимого текстуры. Например, таблица с приблизительным соответствием для объекта [PBR](../overview/features/pbr-materials.md) должна иметь градации серого.

> [!CAUTION]
> Все *асинхронные* функции в arr возвращают объекты асинхронной операции. Необходимо сохранить ссылку на эти объекты до завершения операции. В противном случае сборщик мусора на C# может удалить операцию на раннем этапе и не сможет завершиться. В примере кода выше переменная-член "_textureLoad" используется для хранения ссылки, пока не поступит *завершенное* событие.

## <a name="next-steps"></a>Дальнейшие шаги

* [Материалы](materials.md)
* [Небес](../overview/features/sky.md)
