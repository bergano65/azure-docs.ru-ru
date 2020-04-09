---
title: Проблемное устранение tLS/SSL вопросов (MSAL iOS/macOS) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, что делать с различными проблемами, используя сертификаты TLS/SSL с MSAL. Библиотека "Объектив-С".
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881083"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Как: Устранение проблем MSAL для iOS и macOS TLS/SSL вопросов

В этой статье содержится информация, которая поможет вам устранить проблемы, с которыми вы можете столкнуться при использовании [библиотеки подлинности Майкрософт (MSAL) для iOS и macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Проблемы сети

**Ошибка -1200**: "Произошла ошибка SSL и безопасное подключение к серверу не может быть сделано".

Эта ошибка означает, что соединение не является безопасным. Это происходит, когда сертификат недействителен. Для получения дополнительной информации, в ключая, какой сервер не проходит проверку TLS, обратитесь `NSURLErrorFailingURLErrorKey` в `userInfo` словарь объекта ошибки.

Эта ошибка из сетевой библиотеки Apple. Полный список кодов ошибок NSURL находится в NSURLError.h в macOS и iOS SDKs. Для получения более подробной [URL Loading System Error Codes](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)информации об этой ошибке см.

## <a name="certificate-issues"></a>Проблемы с сертификатом

Если URL-адрес, предоставляющий недействительный сертификат, подключается к серверу, который вы собираетесь использовать в качестве части потока аутентификации, хорошим началом диагностики проблемы является тестирование URL-адреса с помощью службы проверки SSL, такой как [SSL Server Test.](https://www.ssllabs.com/ssltest/analyze.html) Он тестирует сервер на широком спектре сценариев и браузеров и проверяет многие известные уязвимости.

По умолчанию новая функция [Apple App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) применяет более строгие политики безопасности к приложениям, которые используют сертификаты TLS/SSL. Некоторые операционные системы и веб-браузеры начали применять некоторые из этих политик по умолчанию. По соображениям безопасности мы рекомендуем вам не отстранять от АТС.

Сертификаты с использованием sha-1 хэши известны уязвимости. Большинство современных веб-браузеров не позволяют сертификаты с SHA-1 хэши.

## <a name="captive-portals"></a>Пленные порталы

В плену портал представляет веб-страницу для пользователя, когда они впервые получить доступ к сети Wi-Fi и еще не получили доступ к этой сети. Он перехватывает их интернет-трафик до тех пор, пока пользователь не удовлетворит требования портала. Ошибки в сети, поскольку пользователь не может подключиться к сетевым ресурсам, ожидаются до тех пор, пока пользователь не подключится через портал.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [кэптивных порталах](https://en.wikipedia.org/wiki/Captive_portal) и новой [функции Apple App Transport Security (ATS).](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)
