---
title: Коды ошибок мультиплеера Azure
description: Ссылка на код ошибки для медиаплеера Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727259"
---
# <a name="error-codes"></a>Коды ошибок #

Когда воспроизведение не может начаться или остановлено, `error()` событие ошибки будет уволено, и функция вернет код и дополнительное сообщение, чтобы помочь разработчику приложения получить более подробную информацию. `error().message`не является сообщением, отображаемым пользователю.  Сообщение, отображаемое `error().code` пользователю, основано на битах 27-20, см.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Коды ошибок, биты (31-28) (4 бита) ##

Опишите область ошибки.

- 0 — неизвестно
- 1 - УСИЛИТЕЛЬ
- 2 - AzureHtml5JS
- 3 - Вспышки
- 4 - Серебряные фонари
- 5 - Html5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Коды ошибок, биты (27-0) (28 бит) ##

Опишите детали ошибки, биты 27-20 обеспечивают высокий уровень, биты 19-0 обеспечивают более подробную информацию, если они доступны.


| amp.errorCode. (имя) | Коды, биты (27-0) (28 бит) | Описание |
|---|---:|---|
| **диапазон ошибок MEDIA_ERR_ABORTED (0x0100000 - 0x01FFFFF)** | | |
| прерванныйErrUnknown | 0x0100000 | Общие ошибки прерывания |
| прерванныйErrNotImplemented | 0x0100001 | Ошибка прерывания, не реализованная |
| прерванныйErrHttpMixedContentBlocked | 0x0100002 | Ошибка прерывания, смешанное содержимое заблокировано `http://` - `https://` обычно происходит при загрузке потока со страницы |
| **MEDIA_ERR_NETWORK ошибки начинают значение (0x0200000 - 0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Общая ошибка сети |
| networkErrHttpBadUrlФорматформат | 0x0200190 | Ответ на ошибки http 400 |
| networkErrHttpUserAthRequired | 0x0200191 | Ответ на ошибку http 401 |
| networkErrHttpUserForbidden | 0x0200193 | Ответ на ошибки http 403 |
| networkErrHttpUrlNotFound | 0x0200194 | Ответ на ошибки http 404 |
| networkErrHttpNotРазрешено | 0x0200195 | Ответ на ошибки http 405 |
| networkErrHttpgone | 0x020019A | Ответ на ошибки http 410 |
| networkErrHttpPreconditionFailed | 0x020019C | Ответ на ошибки http 412 |
| networkErrHttpInternalServerСбой | 0x02001F4 | Ответ на ошибки http 500
| networkErrHttpBadGateway | 0x02001F6 | Ответ на ошибку http 502 |
| networkErrhttpserviceнее | 0x02001F7 | Ответ на ошибки http 503 |
| networkErrHttpGatewayTimeout | 0x02001F8 | Ответ на ошибки http 504 |
| networkErrTimeout | 0x0200258 | Ошибка тайм-аута сети
| networkErrErr | 0x0200259 | Ответ на ошибку сетевого подключения |
| **MEDIA_ERR_DECODE ошибки (0x0300000 - 0x03FFFFF)** | | |
| декодироватьErrUnknown | 0x0300000 | Общая ошибка декодирования |
| **MEDIA_ERR_SRC_NOT_SUPPORTED ошибок (0x0400000 - 0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | Общий источник не поддерживается ошибка |
| srcErrParseПрезентация | 0x0400001 | Ошибка разбора презентации |
| srcErrParseСегмент | 0x0400002 | Ошибка разбора сегмента |
| srcErrUnsupportedПрезентация | 0x0400003 | Презентация не поддерживается |
| srcErrInvalidСегмент | 0x0400004 | Недействительный сегмент |
| **MEDIA_ERR_ENCRYPTED ошибки начинают значение (0x0500000 - 0x05FFFFF)** | | |
| зашифрованныйErrUnknown | 0x0500000 | Общие зашифрованные ошибки | 
| encryptErrDecrypterNotFound | 0x0500001 | Дешифровщик не найден |
| encryptErrDecrypterInit | 0x0500002 | Ошибка инициализации расшифровки |
| encryptErrDecrypterNotSupported | 0x0500003 | Дешифровка не поддерживается |
| encryptErrKeyAcquire | 0x0500004 | Ключевое приобретение не удалось |
| encryptErrDecryption | 0x0500005 | Расшифровка сегмента провалилась |
| encryptErrLicenseAcquire | 0x0500006 | Лицензия приобретать не удалось |
| **SRC_PLAYER_MISMATCH ошибки начинают значение (0x0600000 - 0x06FFFFF)** | | |
| srcPlayerMismatchНеизвестный | 0x0600000 | Общие не соответствующие тек игрок, чтобы играть источник |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |Flash плагин не установлен, если установлен источник может играть. *ИЛИ* Flash 30 устанавливается и воскывает содержимое AES.  Если это так, пожалуйста, попробуйте другой браузер. Flash 30 не поддерживается сегодня по состоянию на 7 июня. Более подробную информацию можно узнать из [известных проблем.](azure-media-player-known-issues.md) Примечание: Если 0x0060003, как Flash и Silverlight не установлены, если указано в techOrder.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Плагин Silverlight не устанавливается, если установлен источник может играть. Примечание: Если 0x0060003, как Flash и Silverlight не установлены, если указано в techOrder. |
| | 0x00600003 | И Flash, и Silverlight не устанавливаются, если указано в techOrder. |
| **Неизвестные ошибки (0x0FF000000)** | | |
| errUnknown | 0xFF00000 | Неизвестные ошибки |

## <a name="user-error-messages"></a>Сообщения об ошибках пользователя ##

Отображается сообщение пользователя на основе битов кода ошибки 27-20.

- MEDIA_ERR_ABORTED (1) -"Вы прервали воспроизведение видео"
- MEDIA_ERR_NETWORK (2) - "Сетевая ошибка вызвала загрузку видео на провал частично".
- MEDIA_ERR_DECODE (3) - "Воспроизведение видео было прервано из-за проблемы с коррупцией или из-за того, что используемые в видео функции, которые ваш браузер не поддерживал".
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"Видео не может быть загружено, либо потому, что сервер или сеть не удалось или потому, что формат не поддерживается".
- MEDIA_ERR_ENCRYPTED (5) - "Видео зашифровано, и у нас нет ключей для его расшифровки".
- SRC_PLAYER_MISMATCH (6) - "Не был найден совместимый источник для этого видео".
- MEDIA_ERR_UNKNOWN (0xFF) - "Произошла неизвестная ошибка".

## <a name="examples"></a>Примеры ##

### <a name="0x10600001"></a>0x10600001 ##

"Для этого видео не было найдено ни одного совместимого источника". отображается конечному пользователю.

Существует не тек плеер, который может играть в запрашиваемых источников, но если Flash плагин установлен, вполне вероятно, что источник может быть воспроизведен.  

### <a name="0x20200194"></a>0x20200194 ###

"Ошибка сети привела к тому, что загрузка видео провалилась на полпути". отображается конечному пользователю.

AzureHtml5JS не удалось воспроизвести из ответа http 404.

### <a name="categorizing-errors"></a>Категоризация ошибок ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Поймать конкретную ошибку ###

Следующий код ловит только 404 ошибки:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Следующие шаги ##

- [Быстрый запуск медиаплеера Azure](azure-media-player-quickstart.md)