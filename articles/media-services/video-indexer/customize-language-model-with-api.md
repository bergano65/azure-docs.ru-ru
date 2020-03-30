---
title: Настройка модели языка с помощью API video Indexer
titlesuffix: Azure Media Services
description: Узнайте, как настроить модель языка с помощью API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127983"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Настройка модели языка с помощью API video Indexer

Индексатор видео позволяет создавать пользовательские языковые модели для настройки распознавания речи путем передачи текста адаптации, а именно текста из предметной области, к словарю которой вы хотите адаптировать подсистему. Как только вы обучите свою модель, новые слова, появляющиеся в тексте адаптации, будут распознаваться.

Дополнительные сведения и рекомендации для пользовательских языковых моделей см. в статье [Customize a Language model with the Video Indexer APIs](customize-language-model-overview.md) (Настройка языковой модели с помощью API Индексатора видео).

Вы можете использовать API Индексатора видео для создания и изменения пользовательских языковых моделей в своей учетной записи, как описано в этом разделе. Вы также можете использовать веб-сайт, как описано в статье [Customize a Language model with the Video Indexer APIs](customize-language-model-with-api.md) (Настройка языковой модели с помощью API Индексатора видео).

## <a name="create-a-language-model"></a>Создание языковой модели

[Создание языковой модели](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) API создает новую пользовательскую модель языка в указанной учетной записи. Вы можете передать файлы для языковой модели в этом вызове. Кроме того, можно создать модель языка и передать файлы для модели позже, обновив языковую модель.

> [!NOTE]
> По-прежнему следует провести обучение модели с поддержкой файлов для модели, чтобы освоить содержимое из файлов. Направления по обучению языку находятся в следующем разделе.

Чтобы загрузить файлы, которые будут добавлены в модель Language, необходимо загрузить файлы в теле с помощью FormData в дополнение к предоставлению значений для требуемых выше параметров. Есть два способа сделать эту задачу:

* Ключевым будет имя файла и значение будет txt файл.
* Ключевым будет имя файла и значение будет URL для файла txt.

### <a name="response"></a>Ответ

Ответ содержит метаданные о недавно созданной модели Языка вместе с метаданными по каждому из файлов модели в соответствии с форматом этого примера вывода JSON:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Обучение языковой модели

[Поезд языковой модели](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) API обучает пользовательскую модель языка в указанной учетной записи с содержимым в файлах, которые были загружены и включены в языковой модели.

> [!NOTE]
> Сначала необходимо создать языковую модель и передать ее файлы. Вы можете загружать файлы при создании модели Language или путем обновления модели Language.

### <a name="response"></a>Ответ

Ответ содержит метаданные по недавно подготовленной модели языка вместе с метаданными по каждому из файлов модели в соответствии с форматом этого примера вывода JSON:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

`id` Возврат представляет собой уникальный идентификатор, используемый для разграничения языковых моделей, в то время `languageModelId` как используется как для [загрузки видео для индексирования](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) и [переиндексации video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) AIS (также известный как `linguisticModelId` в Video Indexer загрузить / переиндексировать AIS).

## <a name="delete-a-language-model"></a>Удаление языковой модели

[Удаление языковой модели](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) API удаляет пользовательскую модель языка из указанной учетной записи. Любое видео, используюееееееееееееееееетумодельную модель Language, будет сохранять тот же индекс до тех пор, пока вы не проиндексировать видео. При переиндексации видео можно назначить новую модель языка для видео. В противном случае Video Indexer будет использовать свою модель по умолчанию для переиндексации видео.

### <a name="response"></a>Ответ

При успешном удалении модели Language нет возврата содержимого.

## <a name="update-a-language-model"></a>Обновление языковой модели

[Обновление модели языка](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) API обновляет пользовательскую модель Language person в указанной учетной записи.

> [!NOTE]
> Должна быть создана языковая модель. Этот вызов можно использовать, чтобы включить или отключить все файлы в модели, обновить имя языковой модели и передать файлы для добавления в языковую модель.

Чтобы загрузить файлы, которые будут добавлены в модель Language, необходимо загрузить файлы в теле с помощью FormData в дополнение к предоставлению значений для требуемых выше параметров. Есть два способа сделать эту задачу:

* Ключевым будет имя файла и значение будет txt файл.
* Ключевым будет имя файла и значение будет URL для файла txt.

### <a name="response"></a>Ответ

Ответ содержит метаданные по недавно подготовленной модели языка вместе с метаданными по каждому из файлов модели в соответствии с форматом этого примера вывода JSON:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Используйте `id` файлы, возвращенные в ответ, чтобы загрузить содержимое файла.

## <a name="update-a-file-from-a-language-model"></a>Обновление файла языковой модели

[Обновление файла](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) позволяет обновить имя `enable` и состояние файла в пользовательской модели языка в указанной учетной записи.

### <a name="response"></a>Ответ

В ответе содержатся метаданные файла, который был обновлен в соответствии с форматом выходных данных JSON, приведенных в примере ниже.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Используйте `id` файл, возвращенный в ответ, чтобы загрузить содержимое файла.

## <a name="get-a-specific-language-model"></a>Получение конкретной языковой модели

[API возвращает](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) информацию об указанной модели языка в указанной учетной записи, например, на языке и файлах, которые находятся в модели Language.

### <a name="response"></a>Ответ

Ответ предоставляет метаданные по указанной модели языка вместе с метаданными по каждому из файлов модели в соответствии с форматом этого примера вывода JSON:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Используйте `id` файл, возвращенный в ответ, чтобы загрузить содержимое файла.

## <a name="get-all-the-language-models"></a>Получение всех языковых моделей

[Получить все](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) API возвращает все пользовательские модели языка в указанной учетной записи в списке.

### <a name="response"></a>Ответ

Ответ содержит список всех моделей языка в вашей учетной записи и каждого из их метаданных и файлов в соответствии с форматом этого примера выходом JSON:

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Удаление файла из языковой модели

API [удаления](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) указанного файла из указанной модели языка в указанной учетной записи.

### <a name="response"></a>Ответ

При успешном удалении файла из модели Language нет возвращенного содержимого.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Получение метаданных файла из языковой модели

[Полученные метаданные](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) API файла возвращают содержимое и метаданные в указанном файле из выбранной модели Языка в вашей учетной записи.

### <a name="response"></a>Ответ

Ответ предоставляет содержимое и метаданные файла в формате JSON, аналогичные этому примеру:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Содержимое этого файла примера – это слова "hello" и "world" в двух отдельных строках.

## <a name="download-a-file-from-a-language-model"></a>Скачать файл из модели Языка

[Загрузка файла](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) API загружает текстовый файл, содержащий содержимое указанного файла из указанной модели языка в указанной учетной записи. Этот текстовый файл должен соответствовать содержимому текстового файла, который был изначально передан.

### <a name="response"></a>Ответ

Ответом будет загрузка текстового файла с содержимым файла в формате JSON.

## <a name="next-steps"></a>Дальнейшие действия

[Customize a Language model with the Video Indexer website](customize-language-model-with-website.md) (Настройка языковой модели с помощью веб-сайта Индексатора видео)
