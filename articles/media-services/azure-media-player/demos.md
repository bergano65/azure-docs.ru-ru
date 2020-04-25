---
title: Демонстрации Проигрыватель мультимедиа Azure
description: На этой странице содержится список ссылок на демонстрационные версии Проигрыватель мультимедиа Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: 584748b23f526e6f03b543b8298927e3f202f743
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82139307"
---
# <a name="azure-media-player-demos"></a>Демонстрации Проигрыватель мультимедиа Azure

Ниже приведен список ссылок на демонстрационные версии Проигрыватель мультимедиа Azure. Вы можете скачать все [проигрыватель мультимедиа Azure примеры](https://github.com/Azure-Samples/azure-media-player-samples) с сайта GitHub.

## <a name="demo-listing"></a>Демонстрационный листинг

| Имя примера | Программное с помощью JavaScript | Статический с помощью элемента видео HTML5 | Описание |
| ------------|----------------------------|-------------------------------------|--------------|
| Basic |
| Задать источник | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_setsource.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_setsource.html) |Воспроизведение незащищенного содержимого.|
| Компоненты |
| VOD AD, вставка | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_vast_ads_vod.html) | Н/Д | Вставьте в ресурс-контейнер VOD предварительные и промежуточные баннеры. |
| Скорость воспроизведения | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_playback_speed.html)| Н/Д | Позволяет читателям управлять скоростью просмотра видео. |
| Обложка записи AMP | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_flush_skin.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_flush_skin.html) | Включает новую обложку AMP. **Примечание.** Запись AMP поддерживается только в версиях AMP 2.1.0 + |
| Субтитры и субтитры | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_webvtt.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_webvtt.html) | Воспроизведение с субтитрами WebVTT.
| Субтитры Live CEA 708 | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_live_captions.html) | Н/Д | Воспроизведение с помощью субтитров Live CEA 708 с заголовками по левому краю. |
| Потоковая передача с прогрессивным возвратом | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveFallback.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveFallback.html) | Базовая настройка адаптивного воспроизведения с применением отката для прогрессивной работы, если потоковая передача не поддерживается на платформе. |
| Прогрессивный видео MP4 | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveVideo.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveVideo.html) | Воспроизведение последовательного аудио MP4. |
| Прогрессивный аудио MP3 | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveAudio.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveAudio.html) | Воспроизведение последовательного аудио MP3. |
| ДД + | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_dolbyDigitalPlus.html) | Н/Д | Воспроизведение содержимого с помощью DD + Audio. |
| Параметры |
| Эвристический профиль | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_heuristicsProfile.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_heuristicsProfile.html) | Изменение профиля эвристики |
| Локализация | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_localization.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_localization.html) |
Настройка локализации |
| Меню "звуковые дорожки" | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiAudio.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_multiAudio.html) |
Параметры для отображения меню "звуковые дорожки" в обложке по умолчанию. |
| Сочетания клавиш | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_hotKeys.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_hotKeys.html) | В этом примере показано, как настроить, какие горячие клавиши включены в проигрывателе. |
| События, ведение журналов и диагностика |
| Регистрация событий | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_registerEvents.html) | Н/Д | Воспроизведение с помощью прослушивателей событий. |
| Logging | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_logging.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_logging.html) | Включение подробного ведения журнала в консоли. |
| Диагностика | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_diagnostics.html) | Н/Д | Получение диагностических данных. Этот пример работает только на некоторых технических технологиях. |
| AES |
| AES без токена | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_notoken.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_notoken.html) | Воспроизведение содержимого AES без токена. |
| Маркер AES | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token.html) | Воспроизведение содержимого AES с токеном. |
| Моделирование прокси-сервера HLS AES | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_withHLSProxy.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_withHLSProxy.html) | Воспроизведение содержимого AES с маркером, показывающее прокси для HLS, чтобы маркер можно было использовать с устройствами iOS. |
| Вспышка маркера AES | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_forceFlash.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_forceFlash.html) | Воспроизведение содержимого AES с маркером, принудительная техническая поддержка Flash. |
| DRM |
| Multi-DRM с PlayReady, Widevine и FairPlay | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | Воспроизведение содержимого DRM без маркера с заголовками PlayReady, Widevine и FairPlay. |
| PlayReady без токена | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken.html) | Воспроизведение содержимого PlayReady без токена. |
| PlayReady — нет принудительного использования Silverlight маркера | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken_forceSilverlight.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken_forceSilverlight.html) | Воспроизведение содержимого PlayReady без маркера, принудительная техническая поддержка Silverlight. |
| Токен PlayReady | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token.html) | Воспроизведение содержимого PlayReady с токеном. |
| Silverlight токена PlayReady | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token_forceSilverlight.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token_forceSilverlight.html) | Воспроизведение содержимого PlayReady с маркером, принудительная техническая поддержка Silverlight. |
| Протокол и техническая |
| Изменить Течордер | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_techOrder.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_techOrder.html) | Изменение технического заказа |
| Принудительно использовать MPEG-ТИРЕ только в Урлревритер | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceDash.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_forceDash.html) | Воспроизведение незащищенного содержимого только по протоколу ТИРЕ. |
| Исключить MPEG-ТИРЕ в Урлревритер | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceNoDash.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_forceNoDash.html) | Воспроизведение незащищенного содержимого только с помощью протоколов Smooth и HLS. |
| Несколько политик доставки | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_multipleDeliveryPolicy.html) | [Статически](https://amp.azure.net/libs/amp/latest/samples/videotag_multipleDeliveryPolicy.html) | Установка в качестве источника содержимого с несколькими политиками доставки из служб мультимедиа Azure |
| Программный выбор |
| Выбор текстовой трека | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectTextTrack.html) | Н/Д | Выбор дорожкы WebVTT из списка "Track". |
| Выбрать скорость | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectBitrate.html) | Н/Д | Выбор скорости из списка скорость. Этот пример работает только на некоторых технических технологиях. |
| Выбор аудиопотока | [Платформе](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectAudioStream.html) | Н/Д | Выбор звукового потока из списка доступных звуковых потоков. Этот пример работает только на некоторых технических технологиях. |

## <a name="next-steps"></a>Следующие шаги

<!---Some context for the following links goes here--->
- [Краткое руководство по Проигрыватель мультимедиа Azure](azure-media-player-quickstart.md)