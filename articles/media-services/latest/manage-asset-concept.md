---
title: Управление активами в медиасервисах Azure
titleSuffix: Azure Media Services
description: Актив, в котором вы введаете мультимедиа (например, через загрузку или пропуск в реальном времени), выходные медиа (из вывода задания) и публикуете мультимедиа из (для потоковой передачи). В этой теме дается обзор того, как создать новый актив и загрузить файлы.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9136fd702fad5c12a8ec97a68ff8a592a203d7d2
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582198"
---
# <a name="manage-assets"></a>Управление активами

В службах мультимедиа Azure [актив](https://docs.microsoft.com/rest/api/media/assets) — это то место, где вы 

* загрузить медиафайлы в актив,
* глотание и архив жить потоки в актив,
* вывод результатов кодирования работы аналитики в актив,
* публиковать медиа для потоковой передачи, 
* загрузить файлы из актива.

Эта тема дает обзор того, как загрузить файлы в актив и выполнить некоторые другие общие операции. Он также предоставляет ссылки на образцы кода и смежные темы.

## <a name="prerequisite"></a>Предварительные требования 

Перед началом разработки ознакомьтесь со следующими материалами:

* [Основные понятия](concepts-overview.md)
* [Разработка с использованием интерфейсов API служб мультимедиа версии 3](media-services-apis-overview.md) (включает в себя сведения о доступе к интерфейсам API, соглашения об именовании и т. д.) 

## <a name="upload-media-files-into-an-asset"></a>Загрузка медиафайлов в актив

После того, как цифровые файлы загружаются в хранилище и связаны с активом, они могут быть использованы в кодировании, потоковом и анализе рабочих процессов содержимого службы Media Services. Один из стандартных рабочих процессов Служб мультимедиа — отправка, кодирование и потоковая передача файла. В этом разделе описываются общие инструкции.

1. Используйте API Служб мультимедиа версии 3, чтобы создать новый входной ресурс. Эта операция создает контейнер в учетной записи хранения, связанной с вашей учетной записью Служб мультимедиа. API возвращает имя контейнера (например, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Если у вас уже есть контейнер с blob, который вы хотите связать с активом, вы можете указать имя контейнера при создании актива. Службы мультимедиа в настоящее время поддерживают только большие двоичные объекты в корневом контейнере, а не с путями в имени файла. Таким образом, контейнер с именем файла input.mp4 подойдет. Однако контейнер с именем файла "видео/входы/ввод.mp4" не работает.

    Azure CLI можно использовать для передачи непосредственно в любую учетную запись хранения и контейнер, на которые у вас есть права в вашей подписке.

    Имя контейнера должно быть уникальным и соответствовать правилам именования хранилища. Имя не обязательно должно соответствовать формату имени контейнера ресурса Служб мультимедиа (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Получите URL-адрес SAS с разрешениями на чтение и запись, которые будут использоваться для передачи цифровых файлов в контейнер ресурса.

    Можно использовать API Служб мультимедиа для вывода [списка URL-адресов контейнеров ресурса](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).

    **AssetContainerSas.listContainerSas** принимает параметр [ListContainerSasInput,](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) на который вы устанавливаете. `expiryTime` Время должно быть установлено до < 24 часов.

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) возвращает несколько URL-адресов SAS, так как для каждой учетной записи хранилища имеются два ключа учетной записи хранения. Учетная запись хранилища имеет два ключа, поскольку она помогает при сбой и бесшовное вращение ключей учетной записи хранилища. Первый URL SAS представляет первый ключ учетной записи хранилища, а второй URL SAS — второй ключ.
3. Для загрузки файлов в контейнер активов используйте apIs-данные хранения Azure или SDK (например, [API REST хранилища или](../../storage/common/storage-rest-api-auth.md) [.NET SDK).](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
4. Используйте API Служб мультимедиа версии 3, чтобы создать преобразование и задание для обработки входного ресурса. Для получения дополнительной [информации см.](transform-concept.md)
5. Потоковая передача содержимого из выходного ресурса.

### <a name="create-a-new-asset"></a>Создание ресурса

> [!NOTE]
> Свойства актива типа Datetime всегда находятся в формате UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Пример REST см. в разделе [Создание ресурса с помощью REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples).

На примере показано, как создать **орган запроса,** где можно указать описание, имя контейнера, учетную запись хранения и другую полезную информацию.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

### <a name="see-also"></a>См. также

* [Создание входных данных задания из локального файла](job-input-from-local-file-how-to.md)
* [Создание входных данных задания из URL-адреса HTTPS](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Глоток и архив жить потоки в актив

В Media Services объект [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs) подосевается цифровым видеорегистратором, который будет ловить и записывать ваш живой поток в актив в вашей учетной записи Media Services. Записанное содержимое сохраняется в контейнере, определенном ресурсом [актива.](https://docs.microsoft.com/rest/api/media/assets)

Дополнительные сведения можно найти в разделе

* [Использование DVR в облаке](live-event-cloud-dvr.md)
* [Потоковое живое обучение](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Вывод результатов задания в актив

В Службах медиа при обработке видео (например, кодировании или анализе) необходимо создать выходный [актив](assets-concept.md) для хранения результата [вашей работы.](transforms-jobs-concept.md)

Дополнительные сведения можно найти в разделе

* [Кодирование видео](encoding-concept.md)
* [Создание входных данных задания из локального файла](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Публикация актива для потоковой передачи

Чтобы опубликовать актив для потоковой передачи, необходимо создать [потоковое Locator.](streaming-locators-concept.md) Потоковое локатор анавидное может знать имя актива, которое вы хотите опубликовать. 

Дополнительные сведения можно найти в разделе

[Учебник: Загрузка, кодирование и потоковое видео с медиа-сервисами v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Скачать результаты задания из выходного актива

Затем вы можете загрузить эти результаты своей работы в локальную папку с помощью AIS Media Service и Storage. 

Смотрите пример [файлов загрузки.](download-results-howto.md)

## <a name="filtering-ordering-paging"></a>Фильтрации, упорядочивание, разбиение по страницам

Ознакомьтесь с разделом [Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа](entities-overview.md).

## <a name="next-steps"></a>Следующие шаги

Смотрите полный пример кода, которые демонстрируют, как загружать, кодировать, анализировать, транслировать в прямом эфире и по требованию: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [ОТДЫХ](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
