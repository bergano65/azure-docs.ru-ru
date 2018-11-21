---
title: Способы добавления больших двоичных объектов к объектам в Azure Digital Twins | Документация Майкрософт
description: Общие сведения о добавлении больших двоичных объектов в объекты в Azure Digital Twins
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 0929a4a63eee35d21723c980887d6b4217898682
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688336"
---
# <a name="how-to-add-blobs-to-objects-in-azure-digital-twins"></a>Добавление больших двоичных объектов к объектам в Azure Digital Twins

Большие двоичные объекты представляют собой неструктурированные представления распространенных типов файлов (например, изображения и журналы). Большие двоичные объекты отслеживают, какие данные они представляют с помощью типа MIME (например: "image/JPEG") и метаданных (имя, описание, тип и т. д.).

Azure Digital Twins поддерживают присоединение больших двоичных объектов к устройствам, пространствам и пользователям. Большие двоичные объекты могут представлять фотографию профиля пользователя, фото устройства, видео, карты или журналы.

> [!NOTE]
> В этой статье предполагается, что:
> * Ваш экземпляр правильно настроен для получения запросов API управления.
> * Вы правильно прошли проверку подлинности с помощью выбранного клиента REST.

## <a name="uploading-blobs-an-overview"></a>Отправка больших двоичных объектов: обзор

Составные запросы используются для отправки больших двоичных объектов в определенные конечные точки и их соответствующие компоненты.

> [!IMPORTANT]
> Для составных запросов требуются три важных компонента. Для Azure Digital Twins:
> * Заголовок **Content-Type**:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
> * Объект **Content-Disposition**: `form-data; name="metadata"`.
> * Содержимое файла для отправки.
>
> Применяемые **Content-Type** и **Content-Disposition** могут различаться в зависимости от сценария использования.

Каждый составной запрос разбивается на несколько частей. Составные запросы к API управления Azure Digital Twins разбиваются на **две** (**2**) такие части:

1. Первая часть является обязательной и содержит метаданные больших двоичных объектов, такие как связанный тип MIME в **Content-Type** и **Content-Disposition**, указанных выше.

1. Вторая часть содержит фактическое содержимое большого двоичного объекта (неструктурированное содержимое файла).  

Ни одна из двух частей не требуется для запросов **PATCH**. Они необходимы для **POST** или операций создания.

### <a name="blob-metadata"></a>Метаданные больших двоичных объектов

В дополнение к **Content-Type** и **Content-Disposition** составные запросы должны также указывать правильный текст JSON. Текст JSON для отправки зависит от типа выполняемой операции запроса HTTP.

Ниже приведены четыре основные используемые схемы JSON.

![Большие двоичные объекты пространства][1]

Эти схемы модели подробно описаны в предоставленной документации Swagger.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Узнайте, как использовать прилагаемую справочную документацию, прочитав статью [Использование Swagger с Digital Twins](./how-to-use-swagger.md).

### <a name="examples"></a>Примеры

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Чтобы выполнить запрос POST, который отправляет текстовый файл как большой двоичный объект и связывает его с пространством, используйте следующий код:

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

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

| Значение параметра | Заменить на |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Имя границы составного содержимого |

Реализация .NET для передачи одного и того же большого двоичного объекта приведена ниже с помощью класса [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metaData in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>Конечные точки API

Ниже предоставляется пошаговое руководство по ключевым конечным точкам и их определенным функциональным возможностям.

### <a name="devices"></a>Устройства

Большие двоичные объекты можно прикреплять к устройствам. На указанном ниже изображении (со справочной документацией по Swagger для ваших API управления) представлены конечные точки API, связанные с устройством, для потребления больших двоичных объектов и любые требуемые параметры пути, которые можно передать в них:

![Большие двоичные объекты устройств][2]

Например, для обновления или создания большого двоичного объекта, а затем подключения объекта к устройству выполняется запрос PATCH:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Параметр | Заменить на |
| --- | --- |
| *YOUR_BLOB_ID* | Идентификатор требуемого большого двоичного объекта |

Успешный запрос вернет объект DeviceBlob JSON в ответе. DeviceBlobs соответствует следующей схеме JSON:

| Атрибут | type | ОПИСАНИЕ | Примеры |
| --- | --- | --- | --- |
| **DeviceBlobType** | Строка | Категория большого двоичного объекта, который можно подключить к устройству | `Model` и `Specification` |
| **DeviceBlobSubtype*** | Строка | Подкатегория больших двоичных объектов, более детализированных, чем DeviceBlobType | `PhysicalModel`, `LogicalModel`, `KitSpecification` и `FunctionalSpecification` |

> [!TIP]
> Используйте приведенную выше таблицу для успешной обработки возвращаемых данных запроса.

### <a name="spaces"></a>Пробелы

Большие двоичные объекты можно также подключать к пространствам. На рисунке ниже перечислены все конечные точки API пространства, отвечающие за обработку больших двоичных объектов, вместе с любыми параметрами пути, которые можно в них передать:

![Большие двоичные объекты пространства][3]

Например, для возврата больших двоичных объектов, присоединенных к пространству, выполняется запрос GET:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Параметр | Заменить на |
| --- | --- |
| *YOUR_BLOB_ID* | Идентификатор требуемого большого двоичного объекта |

Отправление запроса PATCH к той же конечной точке позволит вам обновить описание метаданных и создать версию большого двоичного объекта. HTTP-запрос выполняется с использованием метода PATCH вместе с любыми необходимыми метаданными и составными данными.

Успешные операции возвращают SpaceBlob, который соответствует следующей схеме и может использоваться для применения возвращенных данных:

| Атрибут | type | ОПИСАНИЕ | Примеры |
| --- | --- | --- | --- |
| **SpaceBlobType** | Строка | Категория большого двоичного объекта, который можно подключить к пространству | `Map` и `Image` |
| **SpaceBlobSubtype** | Строка | Подкатегория больших двоичных объектов, более детализированных, чем SpaceBlobType | `GenericMap`, `ElectricalMap`, `SatelliteMap` и `WayfindingMap` |

### <a name="users"></a>Пользователи

Большие двоичные объекты можно подключить к модели пользователя (например, чтобы связать изображение профиля). На следующем рисунке показаны соответствующие конечные точки API для пользователей и любые необходимые параметры пути, такие как `id`:

![Большие двоичные объекты пользователей][4]

Например, для получения большого двоичного объекта, подключенного к пользователю, выполняется запрос GET с необходимыми данными формы:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Параметр | Заменить на |
| --- | --- |
| *YOUR_BLOB_ID* | Идентификатор требуемого большого двоичного объекта |

Возвращенный JSON (UserBlobs) будет соответствовать следующим моделям JSON:

| Атрибут | type | ОПИСАНИЕ | Примеры |
| --- | --- | --- | --- |
| **UserBlobType** | Строка | Категория большого двоичного объекта, который можно подключить к пользователю | `Image` и `Video` |
| **UserBlobSubtype** |  Строка | Подкатегория больших двоичных объектов, более детализированных, чем UserBlobType | `ProfessionalImage`, `VacationImage` и `CommercialVideo`. |

## <a name="common-errors"></a>Распространенные ошибки

Отсутствие правильной информации заголовка:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о предоставляемой справочной документации по Azure Digital Twins Swagger см. в статье [Использование Swagger с Digital Twins](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
