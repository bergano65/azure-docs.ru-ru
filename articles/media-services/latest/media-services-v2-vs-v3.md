---
title: Миграция из служб мультимедиа Azure v2 в v3
description: В этой статье описаны изменения, которые впервые появились в версии 3 Служб мультимедиа Azure, и приведены различия между двумя версиями.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/01/2020
ms.author: inhenkel
ms.openlocfilehash: 515379a4207a582b441d132b1c28ff11bc83c714
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651758"
---
# <a name="media-services-v2-vs-v3"></a>Службы мультимедиа версии 2 и v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

В этой статье описаны изменения, которые впервые появились в версии 3 Служб мультимедиа Azure, и приведены различия между двумя версиями.

## <a name="general-changes-from-v2"></a>Общие изменения из v2

* Сведения об изменениях, связанных с активом, см. в разделе об [изменениях в ресурсах](#asset-specific-changes) , приведенном ниже.
* Пакеты SDK версии 3 теперь лишаются хранилища SDK, что обеспечивает больший контроль над используемой версией хранилища SDK и позволяет избежать проблем с управлением версиями. 
* В API версии 3 вся скорость кодировки указана в битах за секунду. Это отличается от предустановок Media Encoder Standard версии 2. Например, скорость в версии 2 указывается как 128 (кбит/с), а в версии 3 она имела бы значение 128 000 (бит/с). 
* Сущности AssetFiles, AccessPolicies и IngestManifests не существуют в версии 3.
* ContentKeys — это больше не сущность, а свойство указателя потоковой передачи.
* Служба "Сетка событий" заменяет NotificationEndpoints.
* Следующие сущности были переименованы:

   * v3 Жобаутпут заменяет задачу v2 и теперь является частью задания. Входные и выходные данные теперь находятся на уровне задания. Дополнительные сведения см. в разделе [Создание входных данных задания из локального файла](job-input-from-local-file-how-to.md). 

       Чтобы получить историю хода выполнения задания, прослушайте события EventGrid. Дополнительные сведения см. в разделе [Обработка событий сетки событий](reacting-to-media-services-events.md).
    * Указатель потоковой передачи заменяет Locator.
    * Событие потоковой трансляции заменяет Channel.<br/>Выставление счетов за события потоковой трансляции основано на метриках динамического канала. Для получения дополнительных сведений ознакомьтесь с [выставлением счетов](live-event-states-billing.md) и [ценами](https://azure.microsoft.com/pricing/details/media-services/).
    * Выходные данные потоковой трансляции заменяют Program.
* Выходные данные потоковой трансляции запускаются при создании и останавливаются при удалении. Программы работали иначе в API версии 2, они должны были запускаться после создания.
* Чтобы получить сведения о задании, необходимо знать имя преобразования, под которым было создано задание. 
* В версии 2 [входные](../previous/media-services-input-metadata-schema.md) и [выходные](../previous/media-services-output-metadata-schema.md) XML-файлы метаданных создаются в результате задания кодирования. В версии 3 формат метаданных изменился с XML на JSON. 
* В службах мультимедиа версии 2 можно указать вектор инициализации (IV). В службах мультимедиа v3 не может быть указан FairPlay IV. Хотя он не влияет на клиентов, использующих службы мультимедиа как для упаковки, так и для доставки лицензий, это может быть проблемой при использовании сторонней системы DRM для доставки лицензий FairPlay (гибридный режим). В этом случае важно помнить, что FairPlay IV является производным от идентификатора ключа CBCS и может быть получен с помощью следующей формулы:

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

    Дополнительные сведения см. в статье [код C# функций Azure для служб мультимедиа v3 в гибридном режиме для операций в режиме реального времени и VOD](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Ознакомьтесь с соглашениями об именовании, которые применяются к [ресурсам служб мультимедиа v3](media-services-apis-overview.md#naming-conventions). Также проверьте [имена больших двоичных объектов](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Недочеты функций по отношению к API версии 2

API версии 3 содержит следующие недочеты функций относительно API версии 2. Устранение недочетов не завершено.

* [Кодировщик (цен. категория "Премиум")](../previous/media-services-premium-workflow-encoder-formats.md) и [устаревшие обработчики аналитики мультимедиа](../previous/media-services-analytics-overview.md) (Предварительная версия индексатора Служб мультимедиа Azure версии 2, скрытие лиц и т. д.) недоступны в версии 3.<br/>Пользователи, желающие выполнить миграцию из предварительной версии индексатора мультимедийных данных версии 1 или 2, могут немедленно использовать предустановку AudioAnalyzer в API версии 3.  Эта новая предустановка содержит больше возможностей, чем старые индексаторы мультимедийных данных версии 1 или 2. 
* Многие [Расширенные функции Media Encoder Standard в API v2](../previous/media-services-advanced-encoding-with-mes.md) в настоящее время недоступны в версии 3, например:
  
    * Совмещение ресурсов
    * Наложения
    * Обрезка
    * Спрайты эскизов
    * Вставка автоматической звуковой дорожки, если входные данные не имеют звука
    * Вставка видеодорожки, если у ввода нет видео
* Сейчас события потоковой трансляции с перекодированием не поддерживают вставку баннера в поток и вставку рекламного маркера через вызов API. 
* Рекомендации `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git` и шаблоны, касающиеся использования версии 2 REST API on, см. в разделе пример кода. Пакет SDK для NETCore.

## <a name="asset-specific-changes"></a>Изменения, относящиеся к ресурсу

* Для ресурсов, созданных с помощью v3, службы мультимедиа поддерживают только [Шифрование хранилища на стороне сервера хранилища Azure](../../storage/common/storage-service-encryption.md).
    * Вы можете использовать API версии 3 с ресурсами, созданными с помощью API версии 2, в которых есть [шифрование хранилища](../previous/media-services-rest-storage-encryption.md) (AES 256), предоставляемое Службами мультимедиа.
    * Вы не можете создать ресурсы с устаревшим [шифрованием хранилища](../previous/media-services-rest-storage-encryption.md) AES 256 с помощью API версии 3.
* Свойства [ресурса](assets-concept.md)в v3 отличаются от версии 2. см. [сведения о сопоставлении свойств](#map-v3-asset-properties-to-v2).
* Свойство IAsset.ParentAssets в версии 3 отсутствует.

### <a name="map-v3-asset-properties-to-v2"></a>Свойства ресурса V3 на карте версии 2

В следующей таблице показано, как свойства [ресурса](/rest/api/media/assets/createorupdate#asset)в v3 сопоставляются со свойствами ресурса в версии 2.

|v3, свойства|свойства v2|
|---|---|
|`id`-(уникальный) полный путь Azure Resource Manager см. в разделе примеры в [активе](/rest/api/media/assets/createorupdate) .||
|`name` -(Unique) см. [соглашения об именовании](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` -(уникальное) значение начинается с `nb:cid:UUID:` префикса.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (параметры создания)|
|`type`||

### <a name="storage-side-encryption"></a>Шифрование на стороне хранилища

Чтобы защитить неактивные ресурсы, их нужно зашифровать на стороне хранилища. В следующей таблице показано, как происходит шифрование на стороне хранилища в Службах мультимедиа.

|Вариант шифрования|Описание|Службы мультимедиа версии 2|Службы мультимедиа версии 3|
|---|---|---|---|
|Шифрование хранилища Служб мультимедиа|Шифрование AES-256, ключ, управляемый службами мультимедиа.|Поддерживается<sup>(1)</sup>|Не поддерживается<sup>(2)</sup>|
|[Шифрование службы хранилища для неактивных данных](../../storage/common/storage-service-encryption.md)|Шифрование на стороне сервера, предоставляемое службой хранилища Azure, ключом, управляемым Azure или клиентом.|Поддерживается|Поддерживается|
|[Шифрование хранилища на стороне клиента](../../storage/common/storage-client-side-encryption.md)|Шифрование на стороне клиента, предлагаемое службой хранилища Azure, ключ, управляемый клиентом, в Key Vault.|Не поддерживается|Не поддерживается|

<sup>1</sup> хотя службы мультимедиа поддерживают обработку содержимого в Clear/без какой-либо формы шифрования, делать это не рекомендуется.

<sup>2</sup>В Службах мультимедиа версии 3 для обратной совместимости поддерживается шифрование хранилища (шифрование AES-256), только если ресурсы созданы с помощью Служб мультимедиа версии 2. Это означает, что v3 работает с существующими зашифрованными ресурсами хранилища, но не позволяет создавать новые.

## <a name="code-differences"></a>Отличия в коде

Следующая таблица показывает разницу в коде между версией 2 и 3 в распространенных сценариях.

|Сценарий|API v2|API V3|
|---|---|---|
|Создайте ресурс и отправьте файл. |[Пример .NET версии 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Пример .NET версии 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Отправка задания|[Пример .NET версии 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Пример .NET версии 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Показано, как сначала создать преобразование, а затем отправить задание.|
|Опубликуйте ресурс с шифрованием AES. |1. Создание Контенткэйаусоризатионполициоптион<br/>2. Создание ContentKeyAuthorizationPolicy<br/>3. Создание AssetDeliveryPolicy<br/>4. Создание ресурса и отправка содержимого или отправка задания и использование выходного ресурса<br/>5. Связывание AssetDeliveryPolicy с ресурсом<br/>6. Создание ContentKey<br/>7. Подключение ContentKey к активу<br/>8. Создание AccessPolicy<br/>9. Создание локатора<br/><br/>[Пример .NET версии 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Создание политики ключа содержимого<br/>2. Создание ресурса<br/>3. Отправка содержимого или использование ресурса в качестве Жобаутпут<br/>4. Создание указателя потоковой передачи<br/><br/>[Пример .NET версии 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Получение сведений о заданиях и управление заданиями |[Управление заданиями с помощью версии 2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Управление заданиями с помощью v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Добавьте эту статью в закладки и проверяйте ее на наличие обновлений.

## <a name="ask-questions-give-feedback-get-updates"></a>Получение справки, отправка отзывов, получение обновлений

Прочитайте статью [сообщества Служб мультимедиа Azure](media-services-community.md), чтобы узнать, как задавать вопросы, оставлять отзывы и получать новости о Службах мультимедиа.

## <a name="next-steps"></a>Дальнейшие действия

[Руководство по миграции из версии 2 в версию 3 Служб мультимедиа](migrate-from-v2-to-v3.md)
