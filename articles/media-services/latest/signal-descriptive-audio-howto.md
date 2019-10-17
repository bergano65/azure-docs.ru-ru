---
title: Сигнальные звуковые дорожки с помощью служб мультимедиа Azure v3 | Документация Майкрософт
description: Выполните действия, описанные в этом руководстве, чтобы отправить файл, закодировать видео, добавить описательные звуковые дорожки и выполнить потоковую передачу содержимого с помощью служб мультимедиа v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: 0d8f88e6c2fe273efa969278146de67ba18eaecf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392193"
---
# <a name="signal-descriptive-audio-tracks"></a>Сигнальные звуковые дорожки сигнала

Вы можете добавить в видео дорожку речевого сопровождения, чтобы помочь клиентам, которые могут отслеживать запись видео, прослушивать речевое сопровождение. В службах мультимедиа версии 3 вы сообщаете о звуковых дорожках, записываете звуковую дорожку в файл манифеста.

В этой статье показано, как кодировать видео, отправить звуковой файл MP4 (AAC кодек), содержащий описательный звук в выходной ресурс, и изменить ISM-файл, включив в него описательный звук.

## <a name="prerequisites"></a>Технические условия

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).
- Выполните действия, описанные в статье [Доступ к API Служб мультимедиа Azure с помощью Azure CLI](access-api-cli-how-to.md), и сохраните учетные данные. Эти данные понадобятся для доступа к API.
- Ознакомьтесь с [динамической упаковкой](dynamic-packaging-overview.md).
- Ознакомьтесь с руководством по [передаче, кодированию и потоковой видеороликам](stream-files-tutorial-with-api.md) .

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Создание входного ресурса и отправка в него локального файла 

Функция **CreateInputAsset** создает входной [ресурс](https://docs.microsoft.com/rest/api/media/assets) и отправляет в него определенный локальный видеофайл. Этот **ресурс** используется в качестве входных данных для задания кодирования. В Службах мультимедиа версии 3 входные данные для **задания** могут быть либо **ресурсом**, либо содержимым, доступным в учетной записи Служб мультимедиа через URL-адрес HTTPS. 

Если вы хотите узнать, как кодировать URL-адрес HTTPS, ознакомьтесь с [этой статьей](job-input-from-http-how-to.md) .  

В Службах мультимедиа версии 3 для отправки файлов используются API службы хранилища. В следующих фрагментах кода .NET показано, как это сделать.

Следующая функция выполняет такие действия:

* создает **ресурс**; 
* получает записываемый [URL-адрес SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [контейнера ресурса в хранилище](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container);
* отправляет файл в контейнер в хранилище с использованием URL-адреса SAS;

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Если необходимо передать имя созданного входного ресурса другим методам, обязательно используйте свойство `Name` объекта Asset, возвращенного из `CreateInputAssetAsync`, например inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Создание выходного ресурса для сохранения результата задания кодирования

Выходной [ресурс](https://docs.microsoft.com/rest/api/media/assets) сохраняет результаты задания кодирования. Следующая функция показывает, как создать выходной ресурс.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Если необходимо передать имя созданного выходного ресурса другим методам, убедитесь, что используется свойство `Name` объекта Asset, возвращенного из `CreateIOutputAssetAsync`, например outputAsset.Name. 

В случае использования этой статьи передайте значение `outputAsset.Name` в функции `SubmitJobAsync` и `UploadAudioIntoOutputAsset`.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Создание преобразования и задания, которое кодирует отправленный файл

При кодировании или обработке содержимого в Службах мультимедиа параметры кодировки часто задают в качестве набора инструкций. Затем необходимо отправить **задание**, чтобы применить этот набор к видео. Отправляя новые задания для каждого нового видео, вы применяете этот набор ко всем видео в своей библиотеке. Набор инструкций в Службах мультимедиа называется **Преобразование**. Дополнительные сведения см. в статье [Преобразования и задания](transform-concept.md). Пример кода, описанный в руководстве, определяет набор инструкций, которые кодируют видео для его потоковой передачи на различные устройства под управлением iOS и Android. 

В следующем примере создается преобразование (если оно не существует).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

Следующая функция отправляет задание.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Ожидание завершения задания

Выполнение задания занимает некоторое время. По его завершению вы будете уведомлены. Для ожидания завершения задания рекомендуется использовать сетку событий.

Задание обычно проходит через следующие состояния: **запланировано**, **поставлено в очередь**, **Обработка**, **завершено** (конечное состояние). Если в задании обнаружена ошибка, вы получите состояние **Ошибка**. Если задание находится в процессе отмены, вы получите состояние **Выполнение отмены** и **Отменено** по завершении.

Дополнительные сведения см. в разделе [Обработка событий сетки событий](reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Отправить звуковой файл MP4

Отправьте дополнительный звуковой файл MP4 (AAC кодек), содержащий описательный звук в выходной ресурс.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Ниже приведен пример вызова функции `UpoadAudioIntoOutputAsset`:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Изменение файла ISM

По завершении задания кодирования выходной ресурс будет содержать файлы, созданные заданием кодирования. 

1. В портал Azure перейдите к учетной записи хранения, связанной с учетной записью служб мультимедиа. 
1. Найдите контейнер с именем выходного ресурса. 
1. Найдите файл ISM в контейнере и щелкните **изменить BLOB-объект** (в правом окне). 
1. Измените ISM-файл, добавив сведения о отправленном звуковом файле MP4 (AAC кодека), содержащем описательный звук, и нажмите кнопку **сохранить** после завершения.

    Чтобы передать описательные звуковые дорожки, необходимо добавить параметры "Accessibility" и "role" в файл ISM. Вы должны правильно задать эти параметры, чтобы сообщить звуковой дорожку в качестве звукового описания. Например, добавьте `<param name="accessibility" value="description" />` и `<param name="role" value="alternate" />` в файл ISM для конкретной звуковой дорожки, как показано в следующем примере.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Получение указателя потоковой передачи

После выполнения кодирования необходимо сделать видео в выходном ресурсе доступным для воспроизведения клиентами. Это можно сделать в два этапа. Сначала создайте [указатель потоковой передачи](https://docs.microsoft.com/rest/api/media/streaminglocators), а затем URL-адреса потоковой передачи, которые клиенты могут использовать. 

Процесс создания **указателя потоковой передачи** называется публикацией. По умолчанию **указатель потоковой передачи** допустим сразу после выполнения вызова API и действует, пока не будет удален, если не настроить дополнительное начальное и конечное время. 

При создании [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) необходимо указать желаемое имя **StreamingPolicyName**. В этом примере выполняется потоковая передача незашифрованного содержимого, поэтому используется предварительно определенная политика прозрачной потоковой передачи (**PredefinedStreamingPolicy.ClearStreamingOnly**).

> [!IMPORTANT]
> При использовании пользовательской [политики потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingpolicies) следует разработать ограниченный набор таких политик для учетной записи Служб мультимедиа и повторно использовать их для компонентов StreamingLocator каждый раз, когда требуются те же параметры шифрования и протоколы. У вашей учетной записи служб мультимедиа есть квота на количество входов в политику потоковой передачи. Вы не должны создавать новую политику потоковой передачи для каждого указателя потоковой передачи.

В следующем коде предполагается, что вы вызываете функцию с уникальным locatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Хотя пример в этом разделе рассматривает потоковую передачу, этот же вызов можно использовать, чтобы создать указатель потоковой передачи для передачи видео с помощью поэтапной загрузки.

### <a name="get-streaming-urls"></a>Получение URL-адресов потоковой передачи

После создания [указателя потоковой передачи](https://docs.microsoft.com/rest/api/media/streaminglocators) можно получить URL-адреса потоковой передачи, как показано в разделе **GetStreamingURLs**. Чтобы создать URL-адрес, необходимо сцепить имя узла [конечной точки потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingendpoints) и путь **указателя потоковой передачи**. В этом примере используется **конечная точка потоковой передачи** *по умолчанию*. При первом создании учетной записи Служб мультимедиа эта **конечная точка потоковой передачи** *по умолчанию* будет находиться в остановленном состоянии, поэтому вам необходимо вызвать функцию **Start**.

> [!NOTE]
> В этом методе вам необходим locatorName, который использовался при создании **указателя потоковой передачи** для выходного ресурса.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Тестирование с помощью Проигрывателя мультимедиа Azure

Для тестирования потоковой передачи в этой статье используется решение "Проигрыватель мультимедиа Azure". 

> [!NOTE]
> Если проигрыватель размещен на сайте HTTPS, обновите URL-адрес до HTTPS.

1. Откройте браузер и перейдите по ссылке [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. В поле **URL-адрес** вставьте один из значений URL-адреса потоковой передачи, полученный из приложения. 
 
     URL-адрес можно указать в формате HLS, Dash или Smooth, а Проигрыватель мультимедиа Azure автоматически выберет соответствующий протокол потоковой передачи для воспроизведения на устройстве.
3. Щелкните **Update Player** (Обновить проигрыватель).

Проигрыватель мультимедиа Azure можно использовать для тестирования, но он не должен использоваться в рабочей среде. 

## <a name="next-steps"></a>Дальнейшие действия

[Анализ видео](analyze-videos-tutorial-with-api.md)
