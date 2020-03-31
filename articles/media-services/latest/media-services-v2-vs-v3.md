---
title: Миграция из Azure Media Services v2 в v3
description: В этой статье описаны изменения, которые впервые появились в версии 3 Служб мультимедиа Azure, и приведены различия между двумя версиями.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087821"
---
# <a name="media-services-v2-vs-v3"></a>Медиа Сервисы v2 vs. v3

В этой статье описаны изменения, которые впервые появились в версии 3 Служб мультимедиа Azure, и приведены различия между двумя версиями.

## <a name="general-changes-from-v2"></a>Общие изменения от v2

* Для активов, созданных с помощью v3, Media Services поддерживает только [шифрование хранилища данных на стороне сервера Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)
    * Вы можете использовать API версии 3 с ресурсами, созданными с помощью API версии 2, в которых есть [шифрование хранилища](../previous/media-services-rest-storage-encryption.md) (AES 256), предоставляемое Службами мультимедиа.
    * Вы не можете создать ресурсы с устаревшим [шифрованием хранилища](../previous/media-services-rest-storage-encryption.md) AES 256 с помощью API версии 3.
* Свойства [актива](assets-concept.md)в v3 отличаются от v2, [посмотрите, как карта свойств](#map-v3-asset-properties-to-v2).
* Пакеты SDK версии 3 теперь лишаются хранилища SDK, что обеспечивает больший контроль над используемой версией хранилища SDK и позволяет избежать проблем с управлением версиями. 
* В API версии 3 вся скорость кодировки указана в битах за секунду. Это отличается от предустановок Media Encoder Standard версии 2. Например, скорость в версии 2 указывается как 128 (кбит/с), а в версии 3 она имела бы значение 128 000 (бит/с). 
* Сущности AssetFiles, AccessPolicies и IngestManifests не существуют в версии 3.
* Свойство IAsset.ParentAssets в версии 3 отсутствует.
* ContentKeys — это больше не сущность, а свойство указателя потоковой передачи.
* Служба "Сетка событий" заменяет NotificationEndpoints.
* Следующие сущности были переименованы:
    * Выходные данные задания заменяют задачу и теперь являются частью задания.
    * Указатель потоковой передачи заменяет Locator.
    * Событие потоковой трансляции заменяет Channel.<br/>Выставление счетов за события потоковой трансляции основано на метриках динамического канала. Для получения дополнительных сведений ознакомьтесь с [выставлением счетов](live-event-states-billing.md) и [ценами](https://azure.microsoft.com/pricing/details/media-services/).
    * Выходные данные потоковой трансляции заменяют Program.
* Выходные данные потоковой трансляции запускаются при создании и останавливаются при удалении. Программы работали иначе в API версии 2, они должны были запускаться после создания.
* Чтобы получить информацию о работе, необходимо знать имя Transform, под которым было создано задание. 
* В v2 файлы [ввода](../previous/media-services-input-metadata-schema.md) и [выходных](../previous/media-services-output-metadata-schema.md) метаданных XML генерируются в результате выполнения задания кодирования. В v3 формат метаданных изменен с XML на JSON. 
* В Media Services v2 можно указать вектор инициализации (IV). В Media Services v3, FairPlay IV не может быть указана. Хотя это не влияет на клиентов, использующих Media Services как для упаковки, так и для доставки лицензий, это может быть проблемой при использовании сторонней DRM системы для доставки лицензий FairPlay (гибридный режим). В этом случае важно знать, что FairPlay IV получен из идентификатора ключа cbcs и может быть извлечен с помощью этой формулы:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    на

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Для получения дополнительной информации смотрите [код Azure Functions C для медиа-служб v3 в гибридном режиме как для операций Live, так и для VOD.](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3)
 
> [!NOTE]
> Просмотрите конвенции о наименовании, которые применяются к [ресурсам Media Services v3.](media-services-apis-overview.md#naming-conventions) Также обзор [именования капли](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Недочеты функций по отношению к API версии 2

API версии 3 содержит следующие недочеты функций относительно API версии 2. Устранение недочетов не завершено.

* [Кодировщик (цен. категория "Премиум")](../previous/media-services-premium-workflow-encoder-formats.md) и [устаревшие обработчики аналитики мультимедиа](../previous/media-services-analytics-overview.md) (Предварительная версия индексатора Служб мультимедиа Azure версии 2, скрытие лиц и т. д.) недоступны в версии 3.<br/>Пользователи, желающие выполнить миграцию из предварительной версии индексатора мультимедийных данных версии 1 или 2, могут немедленно использовать предустановку AudioAnalyzer в API версии 3.  Эта новая предустановка содержит больше возможностей, чем старые индексаторы мультимедийных данных версии 1 или 2. 
* Многие из [передовых функций Media Encoder Standard в aIS v2 в](../previous/media-services-advanced-encoding-with-mes.md) настоящее время недоступны в v3, такие как:
  
    * Совмещение ресурсов
    * Наложения
    * Обрезка
    * Спрайты эскизов
    * Вставка бесшумной звуковой дорожки при вхотвочительном вхотре не имеет звука
    * Вставка видеотрека при входном вводе не имеет видео
* Сейчас события потоковой трансляции с перекодированием не поддерживают вставку баннера в поток и вставку рекламного маркера через вызов API. 
 
## <a name="asset-specific-changes"></a>Конкретные изменения в активе

### <a name="map-v3-asset-properties-to-v2"></a>Карта v3 свойства активов v2

В следующей таблице показано, как свойства [актива](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)на карте v3 к свойствам актива в v2.

|v3 свойства|v2 свойства|
|---|---|
|`id`- (уникальный) полный путь менеджера ресурсов Azure, [см.](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`- (уникальный) [см. Именования конвенций](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`- (уникальное) значение `nb:cid:UUID:` начинается с префикса.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`(варианты создания)|
|`type`||

### <a name="storage-side-encryption"></a>Шифрование на стороне хранилища

Чтобы защитить неактивные ресурсы, их нужно зашифровать на стороне хранилища. В следующей таблице показано, как происходит шифрование на стороне хранилища в Службах мультимедиа.

|Вариант шифрования|Описание|Службы мультимедиа версии 2|Службы мультимедиа версии 3|
|---|---|---|---|
|Шифрование хранилища Служб мультимедиа|Шифрование AES-256, ключ, управляемый Службой сми.|Поддерживается<sup>(1)</sup>|Не поддерживается<sup>(2)</sup>|
|[Шифрование службы хранилища для неактивных данных](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Шифрование на стороне сервера, предлагаемое Azure Storage, ключом, управляемым Azure или клиентом.|Поддерживается|Поддерживается|
|[Шифрование хранилища на стороне клиента](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Шифрование на стороне клиента, предлагаемое хранилищем Azure, ключом, управляемым клиентом в Key Vault.|Не поддерживается|Не поддерживается|

<sup>1</sup> В то время как Media Services поддерживает обработку содержимого в ясной/без какой-либо формы шифрования, делать это не рекомендуется.

<sup>2</sup>В Службах мультимедиа версии 3 для обратной совместимости поддерживается шифрование хранилища (шифрование AES-256), только если ресурсы созданы с помощью Служб мультимедиа версии 2. Значение v3 работает с существующими хранилищами зашифрованных активов, но не позволит создавать новые.

## <a name="code-differences"></a>Отличия в коде

Следующая таблица показывает разницу в коде между версией 2 и 3 в распространенных сценариях.

|Сценарий|API версии 2|API версии 3|
|---|---|---|
|Создайте ресурс и отправьте файл. |[Пример .NET версии 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Пример .NET версии 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Отправка задания|[Пример .NET версии 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Пример .NET версии 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Показано, как сначала создать преобразование, а затем отправить задание.|
|Опубликуйте ресурс с шифрованием AES. |1. Создание ContentKeyАвториацияПолитикаОпция<br/>2. Создание ContentKeyАвториацияПолитика<br/>3. Создание AssetDeliveryPolicy<br/>4. Создание активов и загрузка контента или отправка задания и использование выходных активов<br/>5. Ассоциированные AssetDeliveryPolicy с активами<br/>6. Создание ContentKey<br/>7. Прикрепите ContentKey к активам<br/>8. Создание AccessPolicy<br/>9. Создать Локатор<br/><br/>[Пример .NET версии 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Создание политики ключков контента<br/>2. Создание актива<br/>3. Загрузить содержимое или использовать актив в качестве JobOutput<br/>4. Создание потокового локатора<br/><br/>[Пример .NET версии 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Получить сведения о работе и управлять заданиями |[Управление заданиями с v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Управление заданиями с v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Добавьте эту статью в закладки и проверяйте ее на наличие обновлений.

## <a name="ask-questions-give-feedback-get-updates"></a>Получение справки, отправка отзывов, получение обновлений

Прочитайте статью [сообщества Служб мультимедиа Azure](media-services-community.md), чтобы узнать, как задавать вопросы, оставлять отзывы и получать новости о Службах мультимедиа.

## <a name="next-steps"></a>Дальнейшие действия

[Руководство по миграции из версии 2 в версию 3 Служб мультимедиа](migrate-from-v2-to-v3.md)
