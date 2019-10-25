---
title: Известные проблемы в браузерах (Библиотека проверки подлинности Microsoft для JavaScript)
titleSuffix: Microsoft identity platform
description: Сведения об известных проблемах при использовании библиотеки проверки подлинности Майкрософт для JavaScript (MSAL. js) в браузере Safari.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51d800ea2fbbc733a6213d7bc4f61f955612aba0
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803066"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Известные проблемы в браузере Safari с MSAL. js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Автоматическое продление токена в Safari 12 и ITP 2,0

В состав операционных систем Apple iOS 12 и MacOS 10,14 входит выпуск [браузера Safari 12](https://developer.apple.com/safari/whats-new/). В целях обеспечения безопасности и конфиденциальности в Safari 12 включен [интеллектуальная защита от отслеживания 2,0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Это по сути заставляет браузер удалять сторонние файлы cookie. ITP 2,0 также обрабатывает файлы cookie, заданные поставщиками удостоверений, как сторонние файлы cookie.

### <a name="impact-on-msaljs"></a>Влияние на MSAL. js

MSAL. js использует скрытый IFRAME для автоматического получения и продления токена в рамках `acquireTokenSilent`ных вызовов. Запросы на автоматический маркер зависят от IFRAME, у которого есть доступ к сеансу пользователя, прошедшему проверку подлинности, представленному файлами cookie, заданными Azure AD. При использовании порта ITP 2,0, препятствующего доступу к этим файлам cookie, MSAL. js не может автоматически получать и обновлять маркеры, а это приводит к сбоям `acquireTokenSilent`.

На этом этапе решения для этой проблемы не существует, и мы оцениваем варианты с помощью сообщества стандартов.

### <a name="work-around"></a>Обойти

По умолчанию параметр ITP включен в браузере Safari. Чтобы отключить этот параметр, перейдите к пункту **настройки** -> **Конфиденциальность** и снимите флажок **запретить межсайтовую трассировку** .

![параметр Safari](./media/msal-js-known-issue-safari-browser/safari.png)

Вам потребуется выполнить обработку ошибок `acquireTokenSilent` с помощью интерактивного вызова маркера получения, который предлагает пользователю выполнить вход.
Чтобы избежать повторяющихся входов, подход, который можно реализовать, заключается в обработке `acquireTokenSilent` сбоя и предоставлении пользователю возможности отключить параметр ITP в Safari перед продолжением интерактивного вызова. Если этот параметр отключен, последующие продления срока действия автоматических токенов должны выполняться.
