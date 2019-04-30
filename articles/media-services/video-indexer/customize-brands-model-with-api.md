---
title: Использование Индексатора видео Azure для настройки модели торговых марок
titlesuffix: Azure Media Services
description: В этой статье описана настройка модели торговых марок с помощью Индексатора видео Azure.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: df77a745ef6508b15b5a8bcde5eede0e06eb1afc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583729"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Настройка модели торговых марок с помощью API Индексатора видео

Индексатор видео поддерживает распознавание торговых марок в речи и визуальном тексте во время индексирования и повторного индексирования видео- и аудиосодержимого. Функция распознавания торговых марок идентифицирует упоминания о продуктах, службах и компаниях, предлагаемых базой данных торговых марок Bing. Например, если корпорацию Майкрософт упоминают в видео- или аудиосодержимом или она отображается в визуальном тексте видео, Индексатор видео распознает ее как торговую марку в содержимом. Специальная модель торговых марок позволяет исключить обнаружение некоторых торговых марок и включить в состав торговые марки, которые должны быть частью вашей модели, но могут не находиться в базе данных торговых марок Bing.

Дополнительные сведения см. в [этой статье](customize-brands-model-overview.md).

Вы можете использовать API Индексатора видео для создания, использования и изменения моделей торговых марок, обнаруженных в видео, как описано в этом разделе. Вы также можете использовать веб-сайт Индексатора видео, как описано в статье [Customize a Brands model with the Video Indexer API](customize-brands-model-with-api.md) (Настройка модели торговых марок с помощью API Индексатора видео).

## <a name="create-a-brand"></a>Создание торговой марки

Вы сможете создать новую собственную торговую марку и добавить ее к специальной модели торговых марок для указанной учетной записи.

### <a name="request-url"></a>URL-адрес запроса

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Просмотрите необходимые параметры и выполните тестирование с помощью Портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Параметры запроса

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountId|string|Да|Глобальный уникальный идентификатор для учетной записи|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

В дополнение к этим параметрам необходимо указать текст запроса объекта JSON, который предоставляет информацию о новой торговой марке в соответствии с форматом примера ниже.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Параметр **enabled** со значением true помещает торговую марку в список *Включить*, чтобы Индексатор видео ее обнаружил. Параметр **enabled** со значением false помещает торговую марку в список *Исключить*, чтобы Индексатор видео ее не обнаружил.

Значение **referenceUrl** может быть ссылкой на любой веб-сайт для торговой марки, например ссылка на страницу в Википедии.

Значение **tags** представляет собой список тегов для торговой марки. Эго можно увидеть в поле для торговых марок *Категория* на веб-сайте Индексатора видео. Например, торговая марка Azure может быть отмечена тегом или отнесена к категории "Облако".

### <a name="response"></a>Отклик

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

Вы можете удалить торговую марку из специальной модели торговых марок для указанной учетной записи. Учетная запись указана в параметре **accountId**. После успешного вызова торговая марка больше не будет находиться в таких списках торговых марок, как *Включить* или *Исключить*.

### <a name="request-url"></a>URL-адрес запроса

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Просмотрите необходимые параметры и выполните тестирование с помощью Портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Параметры запроса

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountId|string|Да|Глобальный уникальный идентификатор для учетной записи|
|идентификатор|integer|Да|Идентификатор торговой марки (сгенерированный во время создания торговой марки).|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

При успешном удалении торговой марки содержимое не возвращается.

## <a name="get-a-specific-brand"></a>Получение определенной торговой марки

Вы можете искать информацию о торговой марке в специальной модели торговых марок для указанной учетной записи, используя идентификатор торговой марки.

### <a name="request-url"></a>URL-адрес запроса

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Просмотрите необходимые параметры и выполните тестирование с помощью Портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Параметры запроса

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountId|string|Да|Глобальный уникальный идентификатор для учетной записи|
|идентификатор|integer|Да|Идентификатор торговой марки (сгенерированный во время создания торговой марки).|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

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
> Параметр **enabled** со значением **true** означает, что торговая марка находиться в списке *Включить* для обнаружения Индексатором видео. Параметр **enabled** со значением false означает, что торговая марка находиться в списке *Исключить*, чтобы Индексатор видео ее не обнаружил.

## <a name="update-a-specific-brand"></a>Обновление определенной торговой марки

Вы можете искать информацию о торговой марке в специальной модели торговых марок для указанной учетной записи, используя идентификатор торговой марки.

### <a name="request-url"></a>URL-адрес запроса

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Просмотрите необходимые параметры и выполните тестирование с помощью Портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Параметры запроса

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountId|string|Да|Глобальный уникальный идентификатор для учетной записи|
|идентификатор|integer|Да|Идентификатор торговой марки (сгенерированный во время создания торговой марки).|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

В дополнение к этим параметрам необходимо указать текст запроса объекта JSON, который предоставляет обновленную информацию о торговой марке, которую необходимо обновить, в соответствии с форматом примера ниже.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> В этом примере торговая марка, которая была создана в примере текста запроса в разделе**Create a Brand** (Создание торговой марки), обновиться с новым тегом и новым описанием. **Включенное** значение было изменено на false для помещения в список *Исключить*.

### <a name="response"></a>Отклик

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

Этот запрос возвращает все торговые марки в пользовательскую модель торговых марок для указанной учетной записи независимо от того, в каком списке должна находиться торговая марка — *Включить* или *Исключить*.

### <a name="request-url"></a>URL-адрес запроса

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Просмотрите необходимые параметры и выполните тестирование с помощью Портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Параметры запроса

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountId|string|Да|Глобальный уникальный идентификатор для учетной записи|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

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
> Торговая марка с именем *Example* в списке *Включить* Индексатора видео для обнаружения и торговая марка с именем *Example2* в списке *Исключить* для избежания обнаружения Индексатором видео.

## <a name="get-brands-model-settings"></a>Параметры модели торговых марок

С помощью этой возможности можно возвратить параметры модели торговых марок в указанной учетной записи. Параметры торговой марки отображают, включена или нет база данных торговой марки Bing. Если торговые марки Bing не включены, Индексатор видео обнаружит только торговые марки из специальной модели торговых марок указанной учетной записи.

### <a name="request-url"></a>URL-адрес запроса

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Просмотрите необходимые параметры и выполните тестирование с помощью Портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Параметры запроса

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountId|string|Да|Глобальный уникальный идентификатор для учетной записи|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

Для этого вызова больше не требуется текст запроса.

### <a name="response"></a>Отклик

Ответ показывает включены ли торговые марки Bing в соответствии с форматом примера ниже.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** со значением true означает, что торговые марки Bing включены. Если *useBuiltin* имеет значение false, торговые марки Bing отключены. Значение **state** можно проигнорировать, поскольку оно уже устарело.

## <a name="update-brands-model-settings"></a>Обновление параметров модели торговых марок

Благодаря этому параметры модели торговых марок в указанной учетной записи обновляются. Параметры торговой марки отображают, включена или нет база данных торговой марки Bing. Если торговые марки Bing не включены, Индексатор видео обнаружит только торговые марки из специальной модели торговых марок указанной учетной записи.

### <a name="request-url"></a>URL-адрес запроса
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Просмотрите необходимые параметры и выполните тестирование с помощью портала разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Параметры запроса

|**Имя**|**Тип**|**Обязательный**|**Описание**|
|---|---|---|---|
|location|string|Да|Регион Azure, в который должен быть направлен вызов. Дополнительные сведения см. в статье [Azure regions in which Video Indexer exists](regions.md) (Регионы Azure, в которых существует Индексатор видео).|
|accountId|string|Да|Глобальный уникальный идентификатор для учетной записи|
|accessToken|string|Да|Маркер доступа (должен быть в области [маркера доступа к учетной записи](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) для проверки подлинности вызова. Срок действия маркеров доступа истекает в течение 1 часа.|

### <a name="request-body"></a>Текст запроса

В дополнение к этим параметрам необходимо указать текст запроса объекта JSON, который предоставляет информацию о новой торговой марке в соответствии с форматом примера ниже.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** со значением true означает, что торговые марки Bing включены. Если *useBuiltin* имеет значение false, торговые марки Bing отключены.

### <a name="response"></a>Отклик

При успешном обновлении параметров модели торговых марок содержимое не возвращается.

## <a name="next-steps"></a>Дальнейшие действия

[Настройка модели торговых марок с помощью веб-сайта Индексатора видео](customize-brands-model-with-website.md)
