---
title: Настройка учетной записи для автономной потоковой передачи содержимого, защищенного Widevine, в Azure
description: В этой статье показано, как настроить учетную запись служб мультимедиа Azure для автономной потоковой передачи содержимого, защищенного Widevine.
services: media-services
keywords: DASH, DRM, режим автономной работы Widevine, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 5d7dccfecc47b14be62a78600561a8ff0f7ca501
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111959"
---
# <a name="offline-widevine-streaming-for-android"></a>Потоковая передача Widevine для Android (автономный режим)

Помимо защиты содержимого для автономной потоковой передачи подписка содержимого мультимедиа и службы аренды предоставляют содержимое, которое можно загрузить. Это подходит при отсутствии подключения к Интернету. Возможно, потребуется загрузить содержимое на ваш телефон или планшет для воспроизведения в режиме "в самолете", когда во время полета нет подключения к сети. Дополнительные сценарии, в которых может потребоваться загрузить содержимое:

- Некоторые поставщики содержимого могут запретить доставку лицензии DRM за пределы страны. Если пользователь хочет смотреть содержимое во время поездок за границу, необходимо автономное скачивание.
- В некоторых странах доступность Интернета и пропускная способность ограничены. Пользователи могут загрузить содержимое, чтобы иметь возможность смотреть его в достаточно высоком разрешении для удобного просмотра.

В этой статье обсуждается реализация воспроизведения в автономном режиме содержимого DASH, защищенного Widevine на устройствах Android. Автономный DRM позволяет создать подписку, модели аренды и приобретения для содержимого, чтобы позволить клиентам ваших служб легко загружать содержимое без подключения к Интернету.

Вы можете создать приложения для проигрывателя Android такими тремя способами:

> [!div class="checklist"]
> * Создание проигрывателя с использованием API Java пакета SDK ExoPlayer.
> * Создание проигрывателя с использованием привязки Xamarin пакета SDK ExoPlayer.
> * Создание проигрывателя с использованием расширения зашифрованных носителей (EME) и расширения источника мультимедиа (MSE) в браузере Chrome для мобильных устройств версии 62 или более поздней.

В статье также содержатся ответы на некоторые распространенные вопросы, связанные с автономной потоковой передачей содержимого, защищенного Widevine.

## <a name="prerequisites"></a>Технические условия 

Перед реализацией автономного DRM для Widevine на устройствах Android вам потребуется:

- Ознакомиться с концепциями, реализованными для защиты интернет-содержимого с помощью Widevine DRM. Это подробно рассматривается в следующих документах и примерах:
    - [Проектирование системы для защиты содержимого с несколькими подсистемами DRM и управлением доступом](design-multi-drm-system-with-access-control.md)
    - [Использование динамического шифрования DRM и службы доставки лицензий](protect-with-drm.md)
- Клонировать https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Вам потребуется изменить код в [Шифровать с помощью DRM, используя .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM), чтобы добавить конфигурации Widevine.  
- Ознакомиться с пакетом SDK ExoPlayer Google для Android, пакетом SDK видеопроигрывателя с открытым исходным кодом, поддерживающего автономное воспроизведение Widevine DRM. 
    - [Пакет SDK ExoPlayer](https://github.com/google/ExoPlayer)
    - [Руководство для разработчиков ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Блог для разработчиков ExoPlayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Конфигурация защиты содержимого в Службах мультимедиа Azure

В методе [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) выполните следующие действия:

1. Укажите, как выполняется авторизация в службе доставки лицензий доставки ключей содержимого. 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Настройте шаблон лицензии Widevine.  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Создайте ContentKeyPolicyOptions.

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Включите автономный режим.

Чтобы включить **автономный** режим лицензий Widevine, необходимо настроить [шаблон лицензии Widevine](widevine-license-template-overview.md). В объекте **policy_overrides** для свойства **can_persist** задайте значение **true** (по умолчанию false), как показано в [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Настройка проигрывателя Android для воспроизведения в автономном режиме

Для разработки приложений со встроенным проигрывателем для устройств Android проще всего использовать [пакет SDK Google ExoPlayer](https://github.com/google/ExoPlayer), пакет SDK видеопроигрывателя с открытым кодом. ExoPlayer поддерживает функции, не поддерживаемые встроенным API MediaPlayer для Android, к которым относятся протоколы доставки MPEG-DASH и Microsoft Smooth Streaming.

ExoPlayer версии 2.6 и более поздних версий включает в себя множество классов, поддерживающих воспроизведение Widevine DRM в автономном режиме. В частности класс OfflineLicenseHelper предоставляет служебные функции, упрощающие загрузку, обновление и выдачу автономных лицензий с помощью DefaultDrmSessionManager. Классы, предоставленные в папке library/core/src/main/java/com/google/android/exoplayer2/offline/ пакета SDK, поддерживают загрузку видеосодержимого в автономном режиме.

Указанный ниже перечень классов упрощает автономный режим в пакете SDK ExoPlayer для Android.

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java;  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java;
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java;
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java;
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java;
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java;
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java;
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java; 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java;
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java. 

Во время разработки приложения разработчикам следует использовать [руководство разработчика ExoPlayer](https://google.github.io/ExoPlayer/guide.html) и соответствующий [блог разработчиков](https://medium.com/google-exoplayer). Сейчас компания Google еще не выпустила полностью документированный пример реализации или пример кода для приложения ExoPlayer, поддерживающего автономный режим Widevine, поэтому информация ограничена блогом и руководством для разработчиков. 

### <a name="working-with-older-android-devices"></a>Работа со старыми устройствами Android

Для некоторых старых устройств Android необходимо задать значения для следующих свойств **policy_overrides** (определенных в [шаблоне лицензии Widevine](widevine-license-template-overview.md)): **rental_duration_seconds**, **playback_duration_seconds** и **license_duration_seconds**. Кроме того, для них можно задать значение "ноль", что будет означать неограниченную длительность.  

Значения необходимо задать, чтобы избежать ошибки переполнения целочисленного значения. Дополнительные сведения об этой проблеме приведены на страницах https://github.com/google/ExoPlayer/issues/3150 и https://github.com/google/ExoPlayer/issues/3112. <br/>Если не задать значения явно, для свойств **PlaybackDurationRemaining** и **LicenseDurationRemaining** будут назначены очень большие значения (например, 9223372036854775807, которое является максимально положительным значением для 64-разрядного целого числа). В результате этого срок действия лицензии Widevine истечет и расшифровка не произойдет. 

Эта проблема не возникает в Android 5.0 Lollipop или более поздней версии, так как Android 5.0 является первой версией Android, которая полностью поддерживает ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) и 64-разрядные платформы, тогда как Android 4.4 KitKat изначально поддерживает ARMv7 и 32-разрядные платформы, как и другие ранние версии Android.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Создание приложения для воспроизведения Android с помощью Xamarin

Привязки Xamarin для ExoPlayer доступны по следующим ссылкам:

- [Библиотека привязок Xamarin для библиотеки Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin).
- [Привязки Xamarin для ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/).

Также ознакомьтесь с обсуждением привязки Xamarin[здесь](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Приложения проигрывателя Chrome для Android

Начиная с выпуска [Chrome для Android версии 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), в EME поддерживаются постоянные лицензии. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) теперь поддерживается в браузере Chrome для Android. Это позволяет создавать приложения для автономного воспроизведения в браузере Chrome, если у пользователей эта версия Chrome (или более поздняя). 

Кроме того, компания Google выпустила Progressive Web App (PWA) и создала открытый код для него: 

- [Исходный код](https://github.com/GoogleChromeLabs/sample-media-pwa).
- [Версия, размещенная в Google](https://biograf-155113.appspot.com/ttt/episode-2/) (работает только в Chrome версии 62 и более поздних версий на устройствах Android).

Если обновить браузер Chrome версии 62 (или более поздней) для мобильных устройств на телефоне Android и протестировать размещенный выше пример приложения, будет работать как потоковая передача в оперативном режиме, так и воспроизведение в автономном.

Приведенное выше приложение PWA с открытым исходным кодом создается на языке Node.js. Если вы хотите разместить собственную версию на сервере Ubuntu, необходимо учитывать следующие распространенные обнаруженные проблемы, которые могут помешать воспроизведению.

1. Проблема CORS: пример видео в примере приложения размещен в https://storage.googleapis.com/biograf-video-files/videos/. Компания Google настроила CORS для всех своих тестовых примеров, размещенных в контейнере Google Cloud Storage. Они обслуживаются с заголовками CORS, где явно указана запись CORS https://biograf-155113.appspot.com (домен, в котором компания Google разместила свой пример), препятствующая доступу с других сайтов. При попытке доступа появится следующее сообщение об ошибке HTTP: Не удалось загрузить https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd:: заголовок "Access-Control-Allow-Origin" отсутствует в запрашиваемом ресурсе. Источник "https:\//13.85.80.81:8080" не разрешил доступ. Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.
2. Проблема с сертификатом: начиная с Chrome версии 58, EME для Widevine требует протокол HTTPS. Таким образом, необходимо разместить пример приложения по протоколу HTTPS с сертификатом X509. Обычный тестовый сертификат не подходит из-за следующих требований: Вам необходимо получить сертификат, который удовлетворяет следующие минимальные требования:
    - Для Chrome и Firefox требуется, чтобы в сертификате был параметр "Альтернативное имя субъекта" (SAN).
    - Сертификат должен быть подписан доверенным центром сертификации, самозаверяющий сертификат разработки не подходит.
    - Сертификат должен иметь имя CN, соответствующее DNS-имени веб-сервера или шлюза.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="question"></a>Вопрос

Как можно предоставить постоянные лицензии (с поддержкой автономного режима) для некоторых клиентов или пользователей и непостоянные лицензии (без поддержки автономного режима) для остальных пользователей? Нужно ли дублировать содержимое и использовать отдельный ключ содержимого?

### <a name="answer"></a>Ответ
Поскольку версия 3 Службы мультимедиа позволяет активу иметь несколько указателей потоковой передачи. Вы можете иметь:

1.  Один ContentKeyPolicy с license_type = "persistent", ContentKeyPolicyRestriction с утверждением "persistent" и его StreamingLocator.
2.  Другой ContentKeyPolicy с license_type="nonpersistent", ContentKeyPolicyRestriction с утверждением "nonpersistent" и его StreamingLocator.
3.  Оба эти указатели имеют различные ContentKey.

В зависимости от бизнес-логики пользовательской службы маркеров безопасности публикуются разные заявки в маркере JWT. С маркером возможно получить только соответствующие лицензии и воспроизводить только соответствующие URL-адреса.

### <a name="question"></a>Вопрос

Для уровней безопасности Widevine в документации с [общими сведениями об архитектуре DRM Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) Google определены три различные уровня безопасности. Однако в [документации по службам мультимедиа Azure в шаблоне лицензии Widevine](widevine-license-template-overview.md) описаны пять различных уровней безопасности. Что такое связь или сопоставление между двумя различными наборами уровней безопасности?

### <a name="answer"></a>Ответ

В [общих сведениях об архитектуре DRM Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) от Google определены следующие три уровня безопасности:

1.  Уровень безопасности 1. Вся обработка, шифрование содержимого и управление им выполняется в доверенной среде выполнения (TEE). В некоторых моделях реализации обработка безопасности может выполняться в разных микросхемах.
2.  Уровень безопасности 2. Шифрование выполняется (но не обработка видео) в TEE: расшифрованные буферы возвращаются в домен приложения и обрабатываются с помощью отдельного аппаратного или программного обеспечения видео. На уровне 2 данные шифрования по-прежнему обрабатываются только в пределах TEE.
3.  Уровень безопасности 3. На устройстве нет TEE. Для защиты сведений шифрования и расшифрованного содержимого в операционной системе узла можно предпринять соответствующие меры. Реализация уровня 3 может также включать механизм шифрования оборудования, но при этом повышается только уровень производительности, а не безопасность.

В то же время в [документации по службам мультимедиа Azure в шаблоне лицензии Widevine](widevine-license-template-overview.md) свойство security_level объекта content_key_specs может иметь пять различных значений (требования к надежности клиента для воспроизведения):

1.  Требуется программное шифрование методом белого ящика.
2.  Требуется шифрование ПО и скрытый декодер.
3.  Материал ключа и операции шифрования должны быть выполнены в резервной TEE оборудования.
4.  Операции шифрования и расшифровки содержимого должны быть выполнены в резервной TEE оборудования.
5.  Шифрование, расшифровка и обработка всех носителей (сжатых и несжатых) должны быть выполнены в резервной TEE оборудования.

Оба уровня безопасности определяются с помощью Google Widevine. Разница заключается в уровнях использования: уровень архитектуры или уровень API. В API Widevine используется пять уровней безопасности. Объект content_key_specs, который содержит свойство security_level, десериализуется и передается службе глобальной доставки Widevine с помощью службы лицензий Widevine служб мультимедиа Azure. В следующей таблице показано сопоставление между двумя наборами уровней безопасности.

| **Уровни безопасности, определенные в архитектуре Widevine** |**Уровни безопасности, используемые в API Widevine**|
|---|---| 
| **Уровень безопасности 1**. Вся обработка, шифрование содержимого и управление им выполняется в доверенной среде выполнения (TEE). В некоторых моделях реализации обработка безопасности может выполняться в разных микросхемах.|**security_level=5**. Шифрование, расшифровка и обработка всех носителей (сжатых и несжатых) должны быть выполнены в резервной TEE оборудования.<br/><br/>**security_level=4**. Операции шифрования и расшифровки содержимого должны быть выполнены в резервной TEE оборудования.|
**Уровень безопасности 2**. Шифрование выполняется (но не обработка видео) в TEE: расшифрованные буферы возвращаются в домен приложения и обрабатываются с помощью отдельного аппаратного или программного обеспечения видео. На уровне 2 данные шифрования по-прежнему обрабатываются только в пределах TEE.| **security_level=3**. Материал ключа и операции шифрования должны быть выполнены в резервной TEE оборудования. |
| **Уровень безопасности 3**. На устройстве нет TEE. Для защиты сведений шифрования и расшифрованного содержимого в операционной системе узла можно предпринять соответствующие меры. Реализация уровня 3 может также включать механизм шифрования оборудования, но при этом повышается только уровень производительности, а не безопасность. | **security_level=2**. Требуется шифрование ПО и скрытый декодер.<br/><br/>**security_level=1**. Требуется программное шифрование методом белого ящика.|

### <a name="question"></a>Вопрос

Почему для загрузки содержимого требуется так много времени?

### <a name="answer"></a>Ответ

Увеличить скорости загрузки можно двумя способами.

1.  Включите CDN, чтобы пользователи с большей долей вероятности нажимали CDN вместо источника или конечной точки потоковой передачи для загрузки содержимого. Если пользователь нажимает конечную точку потоковой передачи, каждый сегмент HLS или фрагмент DASH динамически упаковывается и зашифровывается. Хотя эта задержка измеряется в миллисекундах для каждого сегмента или фрагмента, если длительность видео составляет один час, суммарная задержка может быть большой, из-за чего увеличивается время загрузки.
2.  Предоставьте пользователям возможность загружать отдельные уровни качества видео и звуковые дорожки, а не все содержимое. Для автономного режима нет смысла загружать все уровни качества. Это достигается двумя способами.
    1.  Управляется клиентом: пользователь выбирает уровень качества видео и звуковые дорожки для загрузки либо в проигрывателе приложения это происходит автоматически.
    2.  Управляется службой: в службах мультимедиа Azure можно использовать функцию динамического манифеста, чтобы создать (глобальный) фильтр, который ограничивает список воспроизведения HLS или DASH MPD до одного уровня качества видео и выбранных звуковых дорожек. URL-адрес загрузки, предоставленный пользователям, будет содержать этот фильтр.

## <a name="summary"></a>Сводка

В этой статье рассматриваются способы реализации воспроизведения в автономном режиме содержимого DASH, защищенного Widevine на устройствах Android.  В ней также содержатся ответы на распространенные вопросы, связанные с автономной потоковой передачей содержимого, защищенного Widevine.
