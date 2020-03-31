---
title: Сигнал описательные звуковые дорожки с Azure Media Services v3 Документы Майкрософт
description: Следуйте инструкциям этого руководства, чтобы загрузить файл, закодировать видео, добавить описательные звуковые дорожки и передавать содержимое с помощью Media Services v3.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392193"
---
# <a name="signal-descriptive-audio-tracks"></a>Сигнал описательные звуковые дорожки

Вы можете добавить в видео дорожку речевого сопровождения, чтобы помочь слабовидящим клиентам следить за видеозаписью, слушая повествование. В Media Services v3 вы сигнализируете описательные звуковые дорожки, аннотируя звуковую дорожку в файле манифеста.

В этой статье показано, как кодировать видео, загружать аудио-файл MP4 (AAC codec), содержащий описательный звук в актив вывода, и отображать файл .ism, чтобы включить описательный звук.

## <a name="prerequisites"></a>Предварительные требования

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).
- Выполните действия, описанные в статье [Доступ к API Служб мультимедиа Azure с помощью Azure CLI](access-api-cli-how-to.md), и сохраните учетные данные. Эти данные понадобятся для доступа к API.
- Обзор [динамической упаковки](dynamic-packaging-overview.md).
- Просмотрите учебник по [загрузке, кодированию и потоковому видео.](stream-files-tutorial-with-api.md)

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Создание входного ресурса и отправка в него локального файла 

Функция **CreateInputAsset** создает входной [ресурс](https://docs.microsoft.com/rest/api/media/assets) и отправляет в него определенный локальный видеофайл. Этот **актив** используется в качестве ввода в кодирующую работу. В Media Services v3 вход в **работу** может быть либо **активом,** либо может быть контентом, который вы предоставляете учетной записи Media Services через URL-адреса HTTPS. 

Если вы хотите узнать, как кодировать с URL HTTPS, смотрите [эту статью](job-input-from-http-how-to.md) .  

В Службах мультимедиа версии 3 для отправки файлов используются API службы хранилища. В следующих фрагментах кода .NET показано, как это сделать.

Следующая функция выполняет такие действия:

* Создает **актив** 
* Получает writable [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) в [контейнер](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) актива в хранилище
* отправляет файл в контейнер в хранилище через URL-адрес SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Если вам нужно передать имя созданного входнаего актива другим `Name` методам, убедитесь, `CreateInputAssetAsync`что использовать свойство на объекте актива, возвращенном, например, inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Создание выходного актива для хранения результата задания кодирования

Выходной [ресурс](https://docs.microsoft.com/rest/api/media/assets) сохраняет результаты задания кодирования. Следующая функция показывает, как создать выходный актив.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Если вам необходимо передать имя созданного ресурса другим методам, убедитесь, что использовать `Name` `CreateIOutputAssetAsync`свойство на объекте актива, возвращенном, например, outputAsset.Name. 

В случае этой статьи, `outputAsset.Name` передать `SubmitJobAsync` значение `UploadAudioIntoOutputAsset` и функции.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Создание преобразования и задания, которое кодирует загруженный файл

При кодировании или обработке содержимого в Службах мультимедиа параметры кодировки часто задают в качестве набора инструкций. Затем необходимо отправить **задание**, чтобы применить этот набор к видео. Отправляя новые задания для каждого нового видео, вы применяете этот набор ко всем видео в своей библиотеке. Набор инструкций в Службах мультимедиа называется **Преобразование**. Для получения дополнительной [информации см.](transform-concept.md) Пример кода, описанный в руководстве, определяет набор инструкций, которые кодируют видео для его потоковой передачи на различные устройства под управлением iOS и Android. 

Следующий пример создает преобразование (если его не существует).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

Следующая функция представляет задание.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Ожидание завершения задания

Выполнение задания занимает некоторое время. По его завершению вы будете уведомлены. Мы рекомендуем использовать Event Grid для ожидания завершения задания.

Задание обычно проходит через следующие состояния: **Запланировано**, **Очередь**, **Обработка**, **Готово** (окончательное состояние). Если в задании обнаружена ошибка, вы получите состояние **Ошибка**. Если задание находится в процессе отмены, вы получите состояние **Выполнение отмены** и **Отменено** по завершении.

Для получения дополнительной информации [см.](reacting-to-media-services-events.md)

## <a name="upload-the-audio-only-mp4-file"></a>Загрузить аудио-файл MP4

Загрузите дополнительный аудио-файл MP4 (кодек AAC), содержащий описательный звук в актив вывода.  

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

Вот пример вызова к функции: `UpoadAudioIntoOutputAsset`

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Отсечение файла .ism

Когда задание кодирования выполняется, выходный актив будет содержать файлы, генерируемые заданиям кодирования. 

1. На портале Azure перейдите к учетной записи хранилища, связанной с учетной записью Медиа Сервисов. 
1. Найдите контейнер с именем вашего выходного актива. 
1. В контейнере найдите файл .ism и нажмите **Edit blob** (в правом окне). 
1. Отодежьте файл .ism, добавив информацию о загруженном аудио-файле MP4 (кодек AAC), содержащем описательный звук и пресс **Сохранить,** когда сделано.

    Чтобы сигнализировать описательные звуковые дорожки, необходимо добавить параметры "доступность" и "роль" в файл .ism. Вы должны правильно задать эти параметры, чтобы звуковая дорожка передавалась в качестве звукового описания. Например, `<param name="accessibility" value="description" />` добавьте и `<param name="role" value="alternate" />` в файл .ism для конкретного звукового трека, как показано в следующем примере.
 
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

## <a name="get-a-streaming-locator"></a>Получить потокового локатора

После выполнения кодирования необходимо сделать видео в выходном ресурсе доступным для воспроизведения клиентами. Это можно сделать в два этапа. Сначала создайте [указатель потоковой передачи](https://docs.microsoft.com/rest/api/media/streaminglocators), а затем URL-адреса потоковой передачи, которые клиенты могут использовать. 

Процесс создания **потокового локатора** называется публикацией. По умолчанию **потоковый локатор** действителен сразу после выполнения вызовов API и длится до тех пор, пока он не будет удален, если только вы не наверстируете дополнительное время начала и окончания. 

При создании [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) необходимо указать желаемое имя **StreamingPolicyName**. В этом примере вы будете передавать потоковую передачу в ясном (или незашифрованном контенте), поэтому используется предопределенная четкая политика потоковой передачи **(PredefinedStreamingPolicy.ClearStreamingOnly).**

> [!IMPORTANT]
> При использовании пользовательской [политики потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingpolicies) следует разработать ограниченный набор таких политик для учетной записи Служб мультимедиа и повторно использовать их для компонентов StreamingLocator каждый раз, когда требуются те же параметры шифрования и протоколы. У вашей учетной записи служб мультимедиа есть квота на количество входов в политику потоковой передачи. Вы не должны создавать новую политику потоковой передачи для каждого указателя потоковой передачи.

В следующем коде предполагается, что вы вызываете функцию с уникальным locatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Хотя пример в этом разделе рассматривает потоковую передачу, этот же вызов можно использовать, чтобы создать указатель потоковой передачи для передачи видео с помощью поэтапной загрузки.

### <a name="get-streaming-urls"></a>Получение URL-адресов потоковой передачи

После создания [указателя потоковой передачи](https://docs.microsoft.com/rest/api/media/streaminglocators) можно получить URL-адреса потоковой передачи, как показано в разделе **GetStreamingURLs**. Чтобы создать URL-адрес, необходимо сцепить имя узла [конечной точки потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingendpoints) и путь **указателя потоковой передачи**. В этом примере используется **конечная точка потоковой передачи** *по умолчанию.* При первом создании учетной записи Media Service эта **конечная точка потоковой передачи** *по умолчанию* будет находиться в остановленном состоянии, поэтому необходимо вызвать **Start.**

> [!NOTE]
> В этом методе вам необходим locatorName, который использовался при создании **указателя потоковой передачи** для выходного ресурса.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Тестирование с помощью Проигрывателя мультимедиа Azure

Для тестирования потоковой передачи в этой статье используется Проигрыватель мультимедиа Azure. 

> [!NOTE]
> Если проигрыватель размещен на сайте HTTPS, обновите URL-адрес до HTTPS.

1. Откройте веб-браузер [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)и перейдите к .
2. В **URL:** поле, вставьте один из значений потокового URL вы получили от вашего приложения. 
 
     URL-адрес можно указать в формате HLS, Dash или Smooth, а Проигрыватель мультимедиа Azure автоматически выберет соответствующий протокол потоковой передачи для воспроизведения на устройстве.
3. Щелкните **Update Player** (Обновить проигрыватель).

Проигрыватель мультимедиа Azure можно использовать для тестирования, но его нельзя применять в рабочей среде. 

## <a name="next-steps"></a>Дальнейшие действия

[Анализ видео](analyze-videos-tutorial-with-api.md)
