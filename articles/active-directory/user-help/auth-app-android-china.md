---
title: Доступность и ограничения Microsoft Authenticator для Android в Китае | Документация Майкрософт
description: Узнайте о том, как получить приложение Microsoft Authenticator (доступность в Китае).
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 12/08/2020
ms.author: curtand
ms.openlocfilehash: 7b3af7c5bd55e748b1d1f4cf8f192797187da30a
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96901802"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Microsoft Authenticator для Android в общедоступном облаке в Китае

Приложение Microsoft Authenticator для Android доступно для скачивания в Китае. Магазин Google Play Маркет в Китае недоступен, поэтому приложение нужно скачать из других китайских магазинов с приложениями. Приложение Microsoft Authenticator для Android сейчас предоставляется в следующих китайских магазинах:

- [Baidu](https://shouji.baidu.com/software/26638379.html)
- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy Store](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

Самая последняя сборка приложения предоставляется в Google Play Маркет, но мы стараемся как можно быстрее обновлять приложение в других магазинах. Так как ни один из магазинов не предлагает пользовательские пакеты приложения для Android (APK), приложение можно без проблем обновлять из любого из следующих расположений:

- хранилище, из которого оно было скачано;
- Google Play Маркет, если пользователь пересекает регионы.

## <a name="limitations"></a>Ограничения

Приложение Microsoft Authenticator для Android использует для получения push-уведомлений облачную систему обмена сообщениями Google Firebase и Сервисы Google Play. Так как обе эти службы недоступны в Китае, функциональные возможности приложения будут несколько ограничены:

- зарегистрировать приложение Authenticator в качестве метода многофакторной проверки подлинности (MFA) с использованием push-уведомлений нельзя;

- настроить [вход с помощью телефона](../authentication/howto-authentication-sms-signin.md) нельзя. Для этого требуется настроить приложение в качестве метода MFA с использованием push-уведомлений, что сейчас не поддерживается.

Если пользователь ранее настроил вход с помощью телефона или многофакторную проверку подлинности через приложение, он сможет вручную проверять наличие запросов уведомлений в приложении и использовать приложение для проверки личности.

## <a name="multi-factor-authentication-workaround"></a>Решение для многофакторной проверки подлинности

Вместо использования push-уведомлений для многофакторной проверки подлинности пользователи могут настроить на устройстве [приложение Authenticator для получения кодов проверки](multi-factor-authentication-setup-auth-app.md#set-up-the-microsoft-authenticator-app-to-use-verification-codes) для проверки личности при многофакторной проверке подлинности. Эти коды проверки действительны в течение 30 секунд, и для их использования администратор должен включить для клиента проверку с помощью кодов TOTP (одноразовые пароли с ограниченным сроком действия).

## <a name="availability"></a>Доступность

Функция Microsoft Authenticator | Доступность в Китае
------------------------------- | ---------------------
Регистрация MFA с использованием push-уведомлений | Нет
Проверка личности с помощью push-уведомлений для предварительно созданной учетной записи MFA | Нет
Проверка личности с помощью выполняемой вручную проверки уведомлений для предварительно созданной учетной записи MFA | Да
Регистрация и проверка подлинности MFA с использованием кодов проверки (TOTP) | Да
Регистрация для входа с помощью телефона | Нет
Вход с помощью предварительно зарегистрированного телефона и push-уведомлений | Нет
Вход с помощью предварительно зарегистрированного телефона и выполняемой вручную проверки на наличие запросов на проверку подлинности | Да
Поддержка приложения для проверки подлинности для личных учетных записей Майкрософт | нет

## <a name="next-steps"></a>Дальнейшие действия

- [Скачивание и установка приложения Microsoft Authenticator](user-help-auth-app-download-install.md)
