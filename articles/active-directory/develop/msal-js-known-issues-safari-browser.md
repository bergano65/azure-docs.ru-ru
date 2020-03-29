---
title: Известные проблемы с браузером Safari (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Узнайте о проблемах, связанных с использованием библиотеки аутентификации Майкрософт для JavaScript (MSAL.js) в браузере Safari.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696118"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Известные проблемы на браузере Safari с MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Обновление тихого токена на Safari 12 и ITP 2.0

Операционные системы Apple iOS 12 и MacOS 10.14 включали выпуск [браузера Safari 12.](https://developer.apple.com/safari/whats-new/) Для целей безопасности и конфиденциальности, Safari 12 включает в себя [интеллектуальную профилактику отслеживания 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Это по существу приводит к тому, что браузер отбрасывает настройки сторонних файлов cookie. ITP 2.0 также рассматривает файлы cookie, установленные поставщиками идентификационных данных, как сторонние файлы cookie.

### <a name="impact-on-msaljs"></a>Влияние на MSAL.js

MSAL.js использует скрытый Iframe для выполнения бесшумного `acquireTokenSilent` приобретения и обновления токенов в рамках вызовов. Запросы на тихие токены полагаются на Iframe, имеющий доступ к сеансу подлинности пользователя, представленному файлами cookie, установленными Azure AD. С ПОМОЩЬю МТП 2.0, препятствующего доступу к этим файлам cookie, MSAL.js не может бесшумно приобретать и обновлять токены, что приводит к `acquireTokenSilent` сбоям.

На данный момент нет решения этой проблемы, и мы оцениваем варианты с сообществом стандартов.

### <a name="work-around"></a>Работа вокруг

По умолчанию настройка ITP включена в браузере Safari. Вы можете отключить эту настройку, перенаправившись в **систему Конфиденциальность предпочтений** -> **Privacy** и отодвинив опцию **отслеживания кросс-сайтов Prevent.**

![настройки сафари](./media/msal-js-known-issue-safari-browser/safari.png)

Вам нужно будет `acquireTokenSilent` справиться с сбоями с помощью интерактивного вызова токенов, который побуждает пользователя войти в систему.
Чтобы избежать повторных всходов, подход, который `acquireTokenSilent` можно реализовать, заключается в обработке сбоя и предоставлении пользователю возможности отключить настройки ITP в Safari, прежде чем приступить к интерактивному вызову. После того, как параметр отключен, последующие обновления бесшумных маркеров должны быть успешными.
