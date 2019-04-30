---
title: Настройка языковой модели с помощью API Индексатора видео в Azure
titlesuffix: Azure Media Services
description: В этой статье описана настройка языковой модели с помощью API Индексатора видео.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: ca1e66d20b19c1a5b85a4f4ff1c433331116bee7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553748"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Настройка языковой модели с помощью API Индексатора видео

Индексатор видео позволяет создавать пользовательские языковые модели для настройки распознавания речи путем передачи текста адаптации, а именно текста из предметной области, к словарю которой вы хотите адаптировать подсистему. Как только вы обучите свою модель, новые слова, появляющиеся в тексте адаптации, будут распознаваться. 

Дополнительные сведения и рекомендации для пользовательских языковых моделей см. в статье [Customize a Language model with the Video Indexer APIs](customize-language-model-overview.md) (Настройка языковой модели с помощью API Индексатора видео).

Вы можете использовать API Индексатора видео для создания и изменения пользовательских языковых моделей в своей учетной записи, как описано в этом разделе. Вы также можете использовать веб-сайт, как описано в статье [Customize a Language model with the Video Indexer APIs](customize-language-model-with-api.md) (Настройка языковой модели с помощью API Индексатора видео).

## <a name="create-a-language-model"></a>Создание языковой модели

Следующая команда создает новую пользовательскую языковую модель в указанной учетной записи. Вы можете передать файлы для языковой модели в этом вызове. Кроме того, можно создать модель языка и передать файлы для модели позже, обновив языковую модель.

> [!NOTE]
> По-прежнему следует провести обучение модели с поддержкой файлов для модели, чтобы освоить содержимое из файлов. Направления по обучению языку находятся в следующем разделе.

### <a name="request-url"></a>URL-адрес запроса

Это запрос POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Ниже приведен запрос в Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[Просмотрите необходимые параметры и выполните тестирование с помощью портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Параметры запроса

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountId|string|Да|Глобальный уникальный идентификатор для учетной записи|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|
|modelName|string|Да|Имя языковой модели|
|Язык|string|Да|Язык языковой модели <br/>Параметру **язык** необходимо присвоить язык в формате BCP-47 "тег языка – регион" (например, en-US). Поддерживаемые языки: английский (en-US), немецкий (de-DE), испанский (es-SP), арабский (ar-EG), французский (fr-FR), хинди (hi-HI), итальянский (it-IT), японский (ja-JP), португальский (pt-BR), русский (ru-RU) и китайский (zh-CN).  |

### <a name="request-body"></a>Текст запроса

Чтобы передать файлы, которые будут добавлены в языковую модель, необходимо передать файлы в тексте, используя данные формы в дополнение к предоставлению значений для обязательных параметров выше. Это можно осуществить двумя путями: 

1. Ключом будет имя файла, а значением будет TXT-файл.
2. Ключом будет имя файла, а значением будет URL-адрес текстового файла.

### <a name="response"></a>Отклик

В ответе содержатся метаданные для вновь созданной языковой модели вместе с метаданными для каждого файла модели, используя формат в примере выходных данных JSON.

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

Следующая команда обучает пользовательскую языковую модель в указанной учетной записи с содержимым в файлах, которые были переданы и включены в языковой модели. 

> [!NOTE]
> Сначала необходимо создать языковую модель и передать ее файлы. Передавать файлы можно либо при создании языковой модели, либо путем обновления языковой модели. 

### <a name="request-url"></a>URL-адрес запроса

Это запрос PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

Ниже приведен запрос в Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[Просмотрите необходимые параметры и выполните тестирование с помощью портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train).

### <a name="request-parameters"></a>Параметры запроса

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountID|string|Да|Глобальный уникальный идентификатор этой учетной записи|
|modelId|string|Да|Идентификатор языковой модели (создается при создании языковой модели)|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

В ответе содержатся метаданные для только что обученной языковой модели вместе с метаданными для каждого файла модели, используя формат в примере выходных данных JSON.

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

Затем следует использовать значение **идентификатор** языковой модели для параметра **linguisticModelId** при [передачи видео в индекс](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) и для параметра **languageModelId** при [повторном индексировании видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-language-model"></a>Удаление языковой модели

Следующая команда удаляет пользовательскую языковую модель из указанной учетной записи. Любое видео, в котором использовалась удаленная языковая модель, будет хранить тот же индекс, пока вы не выполните повторную индексацию. При повторной индексации вы можете назначить новую языковую модель для видео. В противном случае для повторной индексации видео Индексатор видео будет использовать модель по умолчанию.

### <a name="request-url"></a>URL-адрес запроса

Это запрос DELETE.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Ниже приведен запрос в Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Просмотрите необходимые параметры и выполните тестирование с помощью портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete).

### <a name="request-parameters"></a>Параметры запроса 

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountID|string|Да|Глобальный уникальный идентификатор этой учетной записи|
|modelId|string|Да|Идентификатор языковой модели (создается при создании языковой модели)|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

При успешном удалении языковой модели содержимое не возвращается.

## <a name="update-a-language-model"></a>Обновление языковой модели

Следующая команда обновляет пользовательскую языковую модель в указанной учетной записи.

> [!NOTE]
> Должна быть создана языковая модель. Этот вызов можно использовать, чтобы включить или отключить все файлы в модели, обновить имя языковой модели и передать файлы для добавления в языковую модель.

### <a name="request-url"></a>URL-адрес запроса

Это запрос PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

Ниже приведен запрос в Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[Просмотрите необходимые параметры и выполните тестирование с помощью портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update).

### <a name="request-parameters"></a>Параметры запроса 

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountID|string|Да|Глобальный уникальный идентификатор этой учетной записи|
|modelId|string|Да|Идентификатор языковой модели (создается при создании языковой модели)|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|
|modelName|string|Нет|Новое имя, которое можно предоставить к модели|
|включить|Логическое|Нет|Выберите, все ли файлы этой модели включены (true) или отключены (false)|

### <a name="request-body"></a>Текст запроса

Чтобы передать файлы, которые будут добавлены в языковую модель, необходимо передать файлы в тексте, используя данные формы в дополнение к предоставлению значений для обязательных параметров выше. Это можно осуществить двумя путями: 

1. Ключом будет имя файла, а значением будет TXT-файл.
2. Ключом будет имя файла, а значением будет URL-адрес текстового файла.

### <a name="response"></a>Отклик

В ответе содержатся метаданные для только что обученной языковой модели вместе с метаданными для каждого файла модели, используя формат в примере выходных данных JSON.

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
Можно использовать **идентификатор** файлов, возвращенных здесь, чтобы загрузить содержимое файла.

## <a name="update-a-file-from-a-language-model"></a>Обновление файла языковой модели

Следующая команда позволяет обновить имя и состояние **включения** файла пользовательской языковой модели в указанной учетной записи.

### <a name="request-url"></a>URL-адрес запроса

Это запрос PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

Ниже приведен запрос в Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[Просмотрите необходимые параметры и выполните тестирование с помощью портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update).

### <a name="request-parameters"></a>Параметры запроса 

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountId|string|Да|Глобальный уникальный идентификатор этой учетной записи|
|modelId|string|Да|Идентификатор языковой модели, содержащей файл (создается при создании языковой модели)|
|fileId|string|Да|Идентификатор файла, который обновляется (созданный при передачи файла во время создания или обновления языковой модели)|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|
|fileName|string|Нет|Имя обновляемого файла|
|включить|Логическое|Нет|Обновление, является ли этот файл включен (true) или отключен (false) в языковой модели|

### <a name="request-body"></a>Текст запроса

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

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
Можно использовать **идентификатор** файла, возвращенного здесь, чтобы загрузить содержимое файла.

## <a name="get-a-specific-language-model"></a>Получение конкретной языковой модели

Следующая команда возвращает сведения об указанной языковой модели в указанной учетной записи, например язык и файлы, которые находятся в языковой модели. 

### <a name="request-url"></a>URL-адрес запроса

Это запрос GET.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Ниже приведен запрос в Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Просмотрите необходимые параметры и выполните тестирование с помощью портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get).

### <a name="request-parameters-and-request-body"></a>Параметры и текст запроса

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountID|string|Да|Глобальный уникальный идентификатор этой учетной записи|
|modelId|string|Да|Идентификатор языковой модели (создается при создании языковой модели)|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

В ответе содержатся метаданные для указанной языковой модели вместе с метаданными для каждого файла модели, используя формат выходных данных JSON, приведенных в примере ниже.

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

Можно использовать **идентификатор** файла, возвращенного здесь, чтобы загрузить содержимое файла.

## <a name="get-all-the-language-models"></a>Получение всех языковых моделей

Следующая команда возвращает все пользовательские языковые модели в указанной учетной записи в списке.

### <a name="request-url"></a>URL-адрес запроса

Это запрос GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

Ниже приведен запрос в Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[Просмотрите необходимые параметры и выполните тестирование с помощью портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get).

### <a name="request-parameters"></a>Параметры запроса

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountID|string|Да|Глобальный уникальный идентификатор для учетной записи|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

В ответе содержатся метаданные для конкретной языковой модели вместе с метаданными для каждого файла модели, используя формат выходных данных JSON, приведенных в примере ниже.

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

Следующая команда удаляет указанный файл из указанной языковой модели в указанной учетной записи. 

### <a name="request-url"></a>URL-адрес запроса

Это запрос DELETE.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

Ниже приведен запрос в Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Просмотрите необходимые параметры и выполните тестирование с помощью портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete).

### <a name="request-parameters"></a>Параметры запроса 

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountID|string|Да|Глобальный уникальный идентификатор этой учетной записи|
|modelId|string|Да|Идентификатор языковой модели, содержащей файл (создается при создании языковой модели)|
|fileId|string|Да|Идентификатор файла, который обновляется (созданный при передачи файла во время создания или обновления языковой модели)|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

При успешном удалении файла из языковой модели содержимое не возвращается.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Получение метаданных файла из языковой модели

Это возвращает содержимое и метаданные указанного файла из выбранной языковой модели в вашей учетной записи.

### <a name="request-url"></a>URL-адрес запроса

Это запрос GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Ниже приведен запрос в Curl.
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Просмотрите необходимые параметры и выполните тестирование с помощью портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model).

### <a name="request-parameters"></a>Параметры запроса 

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountID|string|Да|Глобальный уникальный идентификатор этой учетной записи|
|modelId|string|Да|Идентификатор языковой модели, содержащей файл (создается при создании языковой модели)|
|fileId|string|Да|Идентификатор файла, который обновляется (созданный при передачи файла во время создания или обновления языковой модели)|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

Ответ предоставляет содержимое и метаданные файла в формате JSON следующего вида.

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

## <a name="download-a-file-from-a-language-model"></a>Загрузка файла из языковой модели

Следующая команда загружает текстовый файл, содержащий содержимое указанного файла, из указанной языковой модели в указанной учетной записи. Этот текстовый файл должен соответствовать содержимому текстового файла, который был изначально передан.

### <a name="request-url"></a>URL-адрес запроса
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

Ниже приведен запрос в Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[Просмотрите необходимые параметры и выполните тестирование с помощью портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?).

### <a name="request-parameters"></a>Параметры запроса 

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountID|string|Да|Глобальный уникальный идентификатор этой учетной записи|
|modelId|string|Да|Идентификатор языковой модели, содержащей файл (создается при создании языковой модели)|
|fileId|string|Да|Идентификатор файла, который обновляется (созданный при передачи файла во время создания или обновления языковой модели)|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса 

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

Ответом будет загрузка текстового файла с содержимым файла в формате JSON. 

## <a name="next-steps"></a>Дальнейшие действия

[Customize a Language model with the Video Indexer website](customize-language-model-with-website.md) (Настройка языковой модели с помощью веб-сайта Индексатора видео)
