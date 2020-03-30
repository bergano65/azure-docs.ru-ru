---
title: Настройка модели брендов с помощью API video Indexer
titleSuffix: Azure Media Services
description: Узнайте, как настроить модель брендов с помощью API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127995"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Настройка модели торговых марок с помощью API Индексатора видео

Индексатор видео поддерживает распознавание торговых марок в речи и визуальном тексте во время индексирования и повторного индексирования видео- и аудиосодержимого. Функция распознавания торговых марок идентифицирует упоминания о продуктах, службах и компаниях, предлагаемых базой данных торговых марок Bing. Например, если корпорация Майкрософт упоминается в видео или аудиоконтенте или если она отображается в визуальном тексте в видео, Video Indexer обнаруживает ее как бренд в контенте. Специальная модель торговых марок позволяет исключить обнаружение некоторых торговых марок и включить в состав торговые марки, которые должны быть частью вашей модели, но могут не находиться в базе данных торговых марок Bing.

Дополнительные сведения см. в [этой статье](customize-brands-model-overview.md).

Вы можете использовать API Индексатора видео для создания, использования и изменения моделей торговых марок, обнаруженных в видео, как описано в этом разделе. Вы также можете использовать веб-сайт Индексатора видео, как описано в статье [Customize a Brands model with the Video Indexer API](customize-brands-model-with-api.md) (Настройка модели торговых марок с помощью API Индексатора видео).

## <a name="create-a-brand"></a>Создание торговой марки

[Создание бренда](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) API создает новый пользовательский бренд и добавляет его к пользовательской модели брендов для указанной учетной записи.

> [!NOTE]
> Установка `enabled` (в теле) на истину ставит бренд в список *Включить* для видео индексатора для обнаружения. Установка `enabled` на ложные ставит бренд в список *Исключение,* так что Видео Индексер не будет обнаруживать его.

Некоторые другие параметры, которые можно установить в теле:

* Значение `referenceUrl` может быть любой ссылки на веб-сайты для бренда, например, ссылка на его страницу Википедии.
* Значение `tags` представляет собой список тегов для бренда. Этот тег отображается в поле *категории* бренда на веб-сайте Video Indexer. Например, торговая марка Azure может быть отмечена тегом или отнесена к категории "Облако".

### <a name="response"></a>Ответ

В ответе содержится информация о торговой марке, которую вы только что создали в соответствии с форматом примера ниже.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Удаление торговой марки

[Удаление API бренда](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) удаляет бренд из пользовательской модели Брендов для указанной учетной записи. Учетная запись указана в параметре. `accountId` После успешного вызова торговая марка больше не будет находиться в таких списках торговых марок, как *Включить* или *Исключить*.

### <a name="response"></a>Ответ

При успешном удалении бренда нет возвращенного содержимого.

## <a name="get-a-specific-brand"></a>Получение определенной торговой марки

[Получение бренда](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) API позволяет искать детали бренда в пользовательской модели Брендов для указанной учетной записи с помощью идентификатора бренда.

### <a name="response"></a>Ответ

В ответе содержится информация о торговой марке, которую вы искали (с помощью идентификатора торговой марки), в соответствии с форматом примера ниже.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled`установлено, `true` чтобы означает, что бренд находится в списке *Включить* `enabled` для видео индексатора для обнаружения, и быть ложным означает, что бренд находится в списке *Исключение,* так что видео индексер не будет обнаруживать его.

## <a name="update-a-specific-brand"></a>Обновление определенной торговой марки

[Обновление API бренда](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) позволяет искать детали бренда в пользовательской модели Брендов для указанной учетной записи с использованием идентификатора бренда.

### <a name="response"></a>Ответ

В ответе содержится обновленная информация о торговой марке, которую вы только что обновили в соответствии с форматом примера ниже.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Получение всех торговых марок

[Получить все бренды](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) API возвращает все бренды в пользовательской модели брендов для указанной учетной записи, независимо от того, бренд предназначен для включения *или* *исключения* брендов списка.

### <a name="response"></a>Ответ

В ответе содержится список всех торговых марок вашей учетной записи и все сведения о них в соответствии с форматом примера ниже.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Бренд под названием *Пример* находится в списке *Включить* для видео индексатора для обнаружения, и бренд под названием *Example2* находится в списке *Исключение,* так что видео-индексер не будет обнаруживать его.

## <a name="get-brands-model-settings"></a>Параметры модели торговых марок

Настройки [брендов](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) API возвращают настройки модели Brands в указанной учетной записи. Параметры торговой марки отображают, включена или нет база данных торговой марки Bing. Если бренды Bing не включены, Video Indexer будет обнаруживать только бренды из пользовательской модели брендов указанной учетной записи.

### <a name="response"></a>Ответ

Ответ показывает включены ли торговые марки Bing в соответствии с форматом примера ниже.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`установлено на истину представляет то, что бренды Bing включены. Если `useBuiltin` это неверно, бренды Bing отключены. Значение `state` может быть проигнорировано, поскольку оно было уничено.

## <a name="update-brands-model-settings"></a>Обновление параметров модели торговых марок

Обновление [брендов](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) API обновляет настройки модели Брендов в указанной учетной записи. Параметры торговой марки отображают, включена или нет база данных торговой марки Bing. Если бренды Bing не включены, Video Indexer будет обнаруживать только бренды из пользовательской модели брендов указанной учетной записи.

Флаг, `useBuiltIn` установленный на истину, означает, что бренды Bing включены. Если `useBuiltin` это неверно, бренды Bing отключены.

### <a name="response"></a>Ответ

При успешном обновлении модели Brands нет возврата содержимого.

## <a name="next-steps"></a>Дальнейшие действия

[Настройка модели торговых марок с помощью веб-сайта Индексатора видео](customize-brands-model-with-website.md)
