---
title: Управление ресурсами в службах мультимедиа Azure
titleSuffix: Azure Media Services
description: Ресурс, в который вы набираете носитель (например, с помощью передачи или приема в режиме реального времени), выходные данные (из выходных данных задания) и публикуюте мультимедиа из (для потоковой передачи). В этом разделе приводятся общие сведения о создании нового ресурса и отправке файлов.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582198"
---
# <a name="manage-assets"></a>Управление активами

В службах мультимедиа Azure [ресурс-контейнер](https://docs.microsoft.com/rest/api/media/assets) 

* Отправка файлов мультимедиа в ресурс
* прием и архивация динамических потоков в ресурс
* вывод результатов кодирования задания аналитики в ресурс
* Публикация мультимедиа для потоковой передачи 
* скачивание файлов из ресурса.

В этом разделе приводятся общие сведения об отправке файлов в ресурс и выполнении некоторых других распространенных операций. Здесь также приводятся ссылки на примеры кода и связанные разделы.

## <a name="prerequisite"></a>Предварительные требования 

Перед началом разработки ознакомьтесь со следующими материалами:

* [Основные понятия](concepts-overview.md)
* [Разработка с использованием интерфейсов API служб мультимедиа версии 3](media-services-apis-overview.md) (включает в себя сведения о доступе к интерфейсам API, соглашения об именовании и т. д.) 

## <a name="upload-media-files-into-an-asset"></a>Отправка файлов мультимедиа в ресурс

После передачи цифровых файлов в хранилище и их связывания с ресурсом их можно использовать в рабочих процессах кодирования, потоковой передачи и анализа содержимого служб мультимедиа. Один из стандартных рабочих процессов Служб мультимедиа — отправка, кодирование и потоковая передача файла. В этом разделе описываются общие инструкции.

1. Используйте API Служб мультимедиа версии 3, чтобы создать новый входной ресурс. Эта операция создает контейнер в учетной записи хранения, связанной с вашей учетной записью Служб мультимедиа. API возвращает имя контейнера (например, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Если у вас уже есть контейнер больших двоичных объектов, который необходимо связать с ресурсом, можно указать имя контейнера при создании ресурса. Службы мультимедиа в настоящее время поддерживают только большие двоичные объекты в корневом контейнере, а не с путями в имени файла. Таким образом, контейнер с именем файла input.mp4 подойдет. Однако контейнер с именем файла "videos/Inputs/input. MP4" не будет работать.

    Azure CLI можно использовать для передачи непосредственно в любую учетную запись хранения и контейнер, на которые у вас есть права в вашей подписке.

    Имя контейнера должно быть уникальным и соответствовать правилам именования хранилища. Имя не обязательно должно соответствовать формату имени контейнера ресурса Служб мультимедиа (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Получите URL-адрес SAS с разрешениями на чтение и запись, которые будут использоваться для передачи цифровых файлов в контейнер ресурса.

    Можно использовать API Служб мультимедиа для вывода [списка URL-адресов контейнеров ресурса](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).

    **Ассетконтаинерсас. листконтаинерсас** принимает параметр [листконтаинерсасинпут](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) , для которого задано `expiryTime`значение. Время должно быть установлено в < 24 часа.

    [Листконтаинерсасинпут](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) возвращает несколько URL-адресов SAS, так как для каждой учетной записи хранения существует два ключа учетной записи хранения. Учетная запись хранения имеет два ключа, так как она помогает выполнять отработку отказа и легкое вращение ключей учетной записи хранения. Первый URL-адрес SAS представляет первый ключ учетной записи хранения, а второй URL-адрес SAS — второй ключ.
3. Используйте API-интерфейсы или пакеты SDK службы хранилища Azure (например, [REST API](../../storage/common/storage-rest-api-auth.md) или [пакет SDK для .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) для отправки файлов в контейнер ресурсов.
4. Используйте API Служб мультимедиа версии 3, чтобы создать преобразование и задание для обработки входного ресурса. Дополнительные сведения см. в статье [Преобразования и задания](transform-concept.md).
5. Потоковая передача содержимого из выходного ресурса.

### <a name="create-a-new-asset"></a>Создание ресурса

> [!NOTE]
> Свойства ресурса типа DateTime всегда имеют формат UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Пример REST см. в разделе [Создание ресурса с помощью REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples).

В примере показано, как создать **текст запроса** , в котором можно указать описание, имя контейнера, учетную запись хранения и другие полезные сведения.

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

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Прием и архивация динамических потоков в ресурс

В службах мультимедиа выходной объект в [реальном времени](https://docs.microsoft.com/rest/api/media/liveoutputs) подобен цифровому видеозаписывающему, который будет перехватывать и записывать Ваш динамический поток в ресурс в учетной записи служб мультимедиа. Записанное содержимое сохраняется в контейнере [, определенном ресурсом](https://docs.microsoft.com/rest/api/media/assets) ресурса.

Дополнительные сведения можно найти в разделе

* [Использование DVR в облаке](live-event-cloud-dvr.md)
* [Руководство по потоковой передаче Live](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Вывод результатов задания в ресурс-контейнер

В службах мультимедиа при обработке видео (например, кодирование или анализ) необходимо создать выходной [ресурс](assets-concept.md) для хранения результатов [задания](transforms-jobs-concept.md).

Дополнительные сведения можно найти в разделе

* [Кодирование видео](encoding-concept.md)
* [Создание входных данных задания из локального файла](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Публикация ресурса для потоковой передачи

Чтобы опубликовать ресурс для потоковой передачи, необходимо создать [указатель потоковой передачи](streaming-locators-concept.md). Указателю потоковой передачи необходимо сообщить имя ресурса, которое необходимо опубликовать. 

Дополнительные сведения можно найти в разделе

[Руководство по Отправка, кодировка и потоковая передача видео с помощью Служб мультимедиа версии 3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Скачивание результатов задания из выходного ресурса

Затем можно загрузить эти результаты задания в локальную папку с помощью службы мультимедиа и API-интерфейсов хранилища. 

См. пример [загрузки файлов](download-results-howto.md) .

## <a name="filtering-ordering-paging"></a>Фильтрации, упорядочивание, разбиение по страницам

Ознакомьтесь с разделом [Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа](entities-overview.md).

## <a name="next-steps"></a>Дальнейшие шаги

Ознакомьтесь с полными примерами кода, демонстрирующими передачу, кодирование, анализ, потоковую передачу и по запросу: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [Остальные](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
