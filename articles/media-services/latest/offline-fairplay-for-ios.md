---
title: Защита содержимого HLS в Azure с помощью Apple FairPlay в автономном режиме | Документация Майкрософт
description: В этом разделе приводятся общие сведения и показывается, как использовать службы мультимедиа Azure для динамического шифрования содержимого HTTP Live Streaming (HLS) с использованием Apple FairPlay в автономном режиме.
services: media-services
keywords: HLS, DRM, потоковая передача FairPlay (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: f2514fff2a3bb292a86c9f4c0e92c37ed2709097
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341044"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Потоковая передача FairPlay в автономном режиме для iOS 

 Службы мультимедиа Azure предоставляют набор хорошо спроектированных [служб системы защиты содержимого](https://azure.microsoft.com/services/media-services/content-protection/), которые охватывают:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Шифрование AES-128

Шифрование содержимого "Управление цифровыми правами" (DRM) или AES выполняется динамически по запросу для различных протоколов потоковой передачи. Службы доставки ключей расшифровки AES и лицензии DRM также предоставляются службами мультимедиа.

Помимо защиты содержимого для интерактивной потоковой передачи по различным протоколам потоковой передачи также часто запрашивается автономный режим для защищенного содержимого. Поддержка автономного режима необходима в следующих случаях:

* Воспроизведение, когда подключение к Интернету недоступно, например во время путешествий.
* Некоторые поставщики содержимого могут запретить доставку лицензии DRM за пределы границы страны/региона. Если пользователи хотят просматривать содержимое во время поездок за пределы страны или региона, необходимо автономное скачивание.
* В некоторых странах и регионах доступность Интернета и пропускная способность по-прежнему ограничено. Пользователи могут сначала скачать содержимое, чтобы иметь возможность смотреть его в достаточно высоком разрешении для удобного просмотра. В этом случае проблемой является не доступность сети, а ее ограниченная пропускная способность. Для поставщиков ОТТ и платформы интернет-видео (OVP) необходима поддержка автономного режима.

В этой статье рассматривается поддержка автономного режима потоковой передачи FairPlay, предназначенная для устройств под управлением iOS 10 или более поздней версии. Эта функция не поддерживается для других платформ Apple, таких как watchOS, tvOS или Safari на macOS.

> [!NOTE]
> Автономный DRM взимается только за лицензию для одного запроса при загрузке содержимого. Все ошибки, не тарифицируются.

## <a name="prerequisites"></a>Технические условия

Перед реализацией автономного DRM для FairPlay на устройстве iOS 10+ вам нужно:

* Ознакомиться с интерактивной защитой содержимого FairPlay. 

    - [Требования и конфигурация лицензии Apple FairPlay](fairplay-license-overview.md)
    - [Использование динамического шифрования DRM и службы доставки лицензий](protect-with-drm.md)
    - Пример .NET с конфигурацией интерактивной потоковой передачи FPS: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Получить пакет SDK FPS из сети разработчиков Apple. Пакет SDK FPS состоит из двух компонентов:

    - Пакет SDK сервера FPS, который содержит модуль безопасности ключа (KSM), образцы клиентов, спецификацию и набор тестовых векторов.
    - Пакет развертывания FPS, который содержит D function, спецификации вместе с инструкциями о том, как создать сертификат FPS, пользовательский закрытый ключ и секретный код приложения. Apple выдает пакет развертывания FPS только лицензированным поставщикам содержимого.
* Клонировать https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Вам потребуется изменить код в репозитории сценариев для [шифрования с помощью DRM при использовании .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) для добавления конфигураций FairPlay.  

## <a name="configure-content-protection-in-azure-media-services"></a>Настройка защиты содержимого в Службах мультимедиа Azure

В методе [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) выполните следующие действия.

Раскомментируйте код, который настраивает параметр политики FairPlay.

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Кроме того, раскомментируйте код, который добавляет CBCS ContentKeyPolicyOption в список ContentKeyPolicyOptions.

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Включение автономного режима

Для включения автономного режима создайте пользовательскую политику StreamingPolicy и используйте ее имя при создании StreamingLocator в [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

Теперь ваша учетная запись мультимедийных служб настроена на предоставление автономных лицензий FairPlay.

## <a name="sample-ios-player"></a>Пример проигрывателя iOS

Поддержка режима автономной работы FPS доступна только в iOS 10 и более поздних версиях. Пакет SDK сервера FPS (версии 3.0 или более поздней) содержит документ и пример автономного режима FPS. В частности, пакет SDK сервера FPS (версии 3.0 или более поздней версии) содержит следующие два элемента, связанные с автономным режимом:

* Документ, посвященный автономному воспроизведению с помощью потоковой передачи FairPlay и HTTP Live Streaming. Apple, 14 сентября 2016 г. В версии 4.0 пакета SDK сервера FPS этот документ объединяется с основным документом FPS.
* Пример кода: образец HLSCatalog (часть пакета SDK сервера FPS от Apple) для автономного режима FPS в \FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. В примере приложения HLSCatalog следующие файлы кода, которые используются для реализации функций автономного режима:

    - Файл кода AssetPersistenceManager.swift: AssetPersistenceManager — это основной класс в этом примере, демонстрирующий:

        - Как управлять загрузкой потоков HLS, таких как API, используемые для запуска и отмены загрузки, а также для удаления имеющихся ресурсов с устройств.
        - Как отслеживать ход выполнения скачивания.
    - Файлы кода AssetListTableViewController.swift и AssetListTableViewCell.swift: Assetlisttableviewcontroller — это основной интерфейс этого примера. Он предоставляет список ресурсов, которые пример может использовать для воспроизведения, скачивания, удаления и отмены загрузки. 

С помощью этих шагов можно настроить работающий проигрыватель iOS. Предположим, что вы начинаете с образца HLSCatalog в версии 4.0.1 пакета SDK сервера FPS. Выполните следующие изменения кода:

В HLSCatalog\Shared\Managers\ContentKeyDelegate.swift реализуйте метод `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` с помощью следующего кода. Пусть drmUr будет переменной, назначенной URL-адресу HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

В HLSCatalog\Shared\Managers\ContentKeyDelegate.swift реализуйте метод `requestApplicationCertificate()`. Эта реализация зависит от того, внедряете ли вы сертификат (только открытый ключ) в устройство или размещаете сертификат в Интернете. Следующая реализация использует сертификат размещенного приложения, используемого в наших тестовых примерах. Пусть certUrl будет переменной, которая содержит URL-адрес сертификата приложения.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Для окончательного интегрированного теста URL-адреса видео и сертификата приложения будут указаны в разделе "Интегрированный тест."

В HLSCatalog\Shared\Resources\Streams.plist добавьте URL-адрес тестового видео. В качестве идентификатора ключа содержимого используйте URL-адрес для приобретения лицензии FairPlay, а протокол skd как уникальное значение.

![Автономные потоки FairPlay приложения iOS](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Используйте собственные URL-адреса тестового видео, приобретения лицензии FairPlay, сертификата приложения (если они настроены). Или же вы можете перейти к следующему разделу, содержащему тестовые образцы.

## <a name="integrated-test"></a>Интегрированный тест

Три тестовых образца в службах мультимедиа охватывают следующие три сценария:

* Защита FPS с видео, аудио и альтернативной звуковой дорожкой.
* Защита FPS с видео и аудио, но без альтернативной звуковой дорожки.
* Защита FPS с видео, но без аудио.

Вы можете найти образцы на этом [демо-сайте](https://aka.ms/poc#22) вместе с соответствующим сертификатом приложения, размещенным в веб-приложении Azure.
При использовании 3-й или 4-й версии примера пакета SDK сервера FPS, если главный плейлист содержит альтернативный аудио-поток, в автономном режиме воспроизводится только аудио. Таким образом, вам нужно убрать альтернативный аудиопоток. Другими словами, второй и третий образцы, перечисленные ранее, работают в интерактивном и автономном режимах. В первом примере звук будет воспроизводиться только в автономном режиме, в то время как интерактивная потоковая передача работает правильно.

## <a name="faq"></a>Часто задаваемые вопросы

Следующие ответы на часто задаваемые вопросы помогут устранить неполадки:

- **Почему в автономном режиме воспроизводится только звук, но нет видео?** Такое поведение, по-видимому, связано с дизайном примера приложения. Когда присутствует альтернативная звуковая дорожка (что относится к HLS), в автономном режиме, как в iOS 10, так и в iOS 11 по умолчанию воспроизводится альтернативная звуковая дорожка. Чтобы компенсировать это поведение в автономном режиме FPS, удалите альтернативную звуковую дорожку из потока. Чтобы сделать это для служб мультимедиа, добавьте динамический фильтр манифеста audio-only=false. Другими словами URL-адрес HLS оканчивается на .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Почему после добавления audio-only=false в автономном режиме по-прежнему воспроизводится аудио без видео?** Содержимое может быть кэшировано в зависимости от структуры ключа кэша сети доставки содержимого (CDN). Очистите кэш.
- **Режим автономной работы FPS также поддерживается на iOS 11 в дополнение к iOS 10?** Да. Автономный режим FPS поддерживается в iOS 10 и в iOS 11.
- **Почему я не могу найти документ автономного воспроизведения с помощью потоковой передачи FairPlay и HTTP Live Streaming в пакете SDK сервера FPS?** Начиная с 4-й версии пакета SDK сервера FPS, этот документ был объединен с руководством по потоковому программированию FairPlay.
- **Какова структура скачанного или автономного файла на устройствах iOS?** Структура скачанного файла на устройстве iOS выглядит как на следующем снимке экрана. В папке `_keys` хранятся скачанные лицензии FPS, один файл хранилища для каждого узла службы лицензий. В папке `.movpkg` хранится аудио- и видеосодержимое. Первая папка, имя которой заканчивается дефисом с числами, содержит видео. Числовое значение равно значению PeakBandwidth для представлений видео. Вторая папка с именем, заканчивающимся тире, за которым следует 0, содержит аудио. Третья папка с именем "Данные" содержит главный список воспроизведения содержимого FPS. Наконец, boot.xml предоставляет полное описание содержимого папки `.movpkg`. 

![Структура файла примера приложения автономного FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Пример файла boot.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о возможностях [использования динамического шифрования AES-128 и службы доставки ключей](protect-with-aes128.md).
