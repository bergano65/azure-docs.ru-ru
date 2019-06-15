---
title: Известные проблемы в браузерах (библиотека проверки подлинности Майкрософт для JavaScript) | Azure
description: Дополнительные сведения о известные проблемы при использовании библиотеки проверки подлинности Майкрософт для JavaScript (MSAL.js) с помощью браузера Safari.
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
ms.openlocfilehash: cb89b1ef4dbbef234fba3152d7f85bbadfbdc64a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873890"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Известные проблемы в браузере Safari с MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Автоматическое обновление маркеров на Safari 12 и ITP 2.0

Apple iOS 12 и операционных систем MacOS 10.14 включены в выпуске [браузера Safari 12](https://developer.apple.com/safari/whats-new/). В целях безопасности и конфиденциальности включает Safari 12 [Intelligent 2.0 предотвращения отслеживания](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). По сути, это приводит к браузер, чтобы удалить файлы cookie сторонних задаваемое. ITP 2.0 обрабатывает файлы cookie, задайте поставщиками удостоверений, как сторонние файлы cookie.

### <a name="impact-on-msaljs"></a>Влияние на MSAL.js

MSAL.js использует скрытом запросе Iframe для выполнения автоматической получение маркера и его обновление как часть `acquireTokenSilent` вызовов. Автоматическая запросов маркеров зависят от Iframe наличие доступа к сеансу прошедшего проверку подлинности пользователя, представленного файлов cookie, установленных с помощью Azure AD. С помощью ITP 2.0, предотвращая доступ к эти файлы cookie, MSAL.js не сможет автоматически получать и обновлять маркеры и в результате `acquireTokenSilent` сбоев.

Не существует решения этой проблемы на этом этапе, и мы подбираем с сообществом стандартов.

### <a name="work-around"></a>Обойти

По умолчанию параметр ITP включен в браузере Safari. Этот параметр можно отключить, перейдя по адресу **предпочтения** -> **конфиденциальности** и сняв флажок **предотвращения межсайтовых отслеживания** параметр.

![параметр Safari](./media/msal-js-known-issue-safari-browser/safari.png)

Вам потребуется обрабатывать `acquireTokenSilent` сбоев с помощью интерактивного получения маркера вызов, который предлагает пользователю войти в систему.
Чтобы избежать повторные входы в систему, можно реализовать подход — обработка `acquireTokenSilent` сбоя и предоставить пользователю возможность отключить этот параметр ITP в Safari, прежде чем продолжить, с помощью интерактивного вызова. После отключения параметра последующих продлениях автоматической маркера должны завершиться успешно.
