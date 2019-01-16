---
title: Способы добавления больших двоичных объектов к объектам в Azure Digital Twins | Документация Майкрософт
description: Сведения о добавлении больших двоичных объектов к объектам в Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 36f4caac38f2f4891af6f61b78b55c7eff15eae4
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116744"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Добавление больших двоичных объектов к объектам в Azure Digital Twins

Большие двоичные объекты представляют собой неструктурированные представления распространенных типов файлов, например изображения и журналы. Большие двоичные объекты отслеживают, какие данные они представляют с помощью типа MIME (например: image/jpeg) и метаданных (имя, описание, тип и т. д.).

Azure Digital Twins поддерживает присоединение больших двоичных объектов к устройствам, пространствам и пользователям. Большие двоичные объекты могут представлять фотографию профиля пользователя, фото устройства, видео, карты, индекс встроенного ПО, данные JSON или журналы.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-an-overview"></a>Отправка больших двоичных объектов: обзор

Вы можете использовать составные запросы для отправки больших двоичных объектов в определенные конечные точки и их соответствующие компоненты.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Метаданные больших двоичных объектов

В дополнение к **Content-Type** и **Content-Disposition** составные запросы больших двоичных объектов Azure Digital Twins должны также указывать правильный текст JSON. Текст JSON для отправки зависит от типа выполняемой операции HTTP-запроса.

Ниже приведены четыре основные схемы JSON:

![Схемы JSON][1]

Эти схемы модели подробно описаны в документации Swagger.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Сведения об использовании справочной документации см. в статье [Использование Swagger с Digital Twins](./how-to-use-swagger.md).

### <a name="examples"></a>Примеры

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Для отправки текстового файла в виде большого двоичного объекта и связывания его с пространством создайте аутентифицированный запрос HTTP POST:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Текст должен быть следующим:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Значение | Заменить на |
| --- | --- |
| USER_DEFINED_BOUNDARY | Имя границы составного содержимого |

Приведенный ниже код представляет собой реализацию .NET для передачи одного и того же большого двоичного объекта с помощью класса [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

В обоих примерах:

1. Убедитесь, что заголовки включают `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Убедитесь, что текст состоит из нескольких частей:

   - Первая часть содержит необходимые метаданные больших двоичных объектов.
   - Вторая часть содержит текстовый файл.

1. Убедитесь, что текстовый файл указан как `Content-Type: text/plain`.

## <a name="api-endpoints"></a>Конечные точки API

В следующих разделах описываются основные конечные точки API, связанные с большими двоичными объектами, и их функциональность.

### <a name="devices"></a>Устройства

Большие двоичные объекты можно подключать к устройствам. На следующем изображении представлена справочная документация по Swagger для ваших API управления. На нем указаны конечные точки API, связанные с устройством, для использования больших двоичных объектов и любые требуемые параметры пути, которые можно передать в них.

![Большие двоичные объекты устройств][2]

Например, для обновления или создания большого двоичного объекта, а затем подключения объекта к устройству выполняется аутентифицированный запрос HTTP PATCH к:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Параметр | Заменить на |
| --- | --- |
| *YOUR_BLOB_ID* | Идентификатор требуемого большого двоичного объекта |

Успешный запрос вернет объект **DeviceBlob** JSON в ответе. Объект **DeviceBlobs** соответствует следующей схеме JSON:

| Атрибут | type | ОПИСАНИЕ | Примеры |
| --- | --- | --- | --- |
| **DeviceBlobType** | Строка | Категория большого двоичного объекта, который можно подключить к устройству | `Model` и `Specification` |
| **DeviceBlobSubtype** | Строка | Подкатегория больших двоичных объектов, более точных, чем **DeviceBlobType** | `PhysicalModel`, `LogicalModel`, `KitSpecification` и `FunctionalSpecification` |

> [!TIP]
> Используйте приведенную выше таблицу для успешной обработки возвращаемых данных запроса.

### <a name="spaces"></a>Пробелы

Большие двоичные объекты можно также подключать к пространствам. На представленном ниже рисунке перечислены все конечные точки API пространства, отвечающие за обработку больших двоичных объектов. Кроме того, указаны разные параметры пути, которые можно передать в эти конечные точки.

![Большие двоичные объекты пространства][3]

Например, для возврата больших двоичных объектов, присоединенных к пространству, выполняется аутентифицированный запрос HTTP GET к:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Параметр | Заменить на |
| --- | --- |
| *YOUR_BLOB_ID* | Идентификатор требуемого большого двоичного объекта |

Запрос PATCH к той же конечной точке обновляет описание метаданных и создает новые версии большого двоичного объекта. HTTP-запрос выполняется с использованием метода PATCH вместе с любыми необходимыми метаданными и составными данными.

Успешные операции возвращают объект **SpaceBlob**, который соответствует следующей схеме. Вы можете использовать его для применения возвращенных данных.

| Атрибут | type | ОПИСАНИЕ | Примеры |
| --- | --- | --- | --- |
| **SpaceBlobType** | Строка | Категория большого двоичного объекта, который можно подключить к пространству | `Map` и `Image` |
| **SpaceBlobSubtype** | Строка | Подкатегория больших двоичных объектов, более точных, чем **SpaceBlobType** | `GenericMap`, `ElectricalMap`, `SatelliteMap` и `WayfindingMap` |

### <a name="users"></a>Пользователи

Большие двоичные объекты можно подключить к моделям пользователя (например, чтобы связать изображение профиля). На представленном ниже рисунке показаны соответствующие конечные точки API для пользователей и любые необходимые параметры пути, такие как `id`:

![Большие двоичные объекты пользователей][4]

Например, для получения большого двоичного объекта, подключенного к пользователю, выполняется аутентифицированный запрос HTTP GET (с необходимыми данными формы) к:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Параметр | Заменить на |
| --- | --- |
| *YOUR_BLOB_ID* | Идентификатор требуемого большого двоичного объекта |

Возвращенный JSON (объекты **UserBlobs**) соответствует следующим моделям JSON:

| Атрибут | type | ОПИСАНИЕ | Примеры |
| --- | --- | --- | --- |
| **UserBlobType** | Строка | Категория большого двоичного объекта, который можно подключить к пользователю | `Image` и `Video` |
| **UserBlobSubtype** |  Строка | Подкатегория больших двоичных объектов, более точных, чем **UserBlobType** | `ProfessionalImage`, `VacationImage` и `CommercialVideo`. |

## <a name="common-errors"></a>Распространенные ошибки

Распространенной ошибкой является отсутствие правильной информации заголовка.

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о справочной документации Swagger для Azure Digital Twins см. в статье [Использование Swagger с Digital Twins](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
