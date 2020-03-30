---
title: Версии JavaScript и макет страницы
titleSuffix: Azure AD B2C
description: Узнайте, как включить JavaScript и использовать версии макета страницы в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185848"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript и версии макета страниц в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C предоставляет набор упакованного контента, содержащего HTML, CSS и JavaScript для элементов пользовательского интерфейса в пользовательских потоках и пользовательских политиках.

Для включения JavaScript для ваших приложений:

* Включите его в поток пользователя с помощью портала Azure
* Выберите [макет страницы](page-layout.md)
* Используйте [b2clogin.com](b2clogin.md) в запросах

Если вы намереваетесь включить клиентский код [JavaScript,](javascript-samples.md) элементы, на которые вы основываете JavaScript, должны быть неизменяемыми. Если они не являются неизменяемыми, любые изменения могут привести к неожиданному поведению на ваших страницах пользователей. Чтобы предотвратить эти проблемы, принудите использование макета страницы и укажите версию макета страницы, чтобы гарантировать, что определения содержимого, на которых вы основывали JavaScript, неизменяемы. Даже если вы не собираетесь включать JavaScript, вы можете указать версию макета страницы для ваших страниц.

## <a name="enable-javascript"></a>Включение JavaScript

В пользовательском потоке **Свойства,** вы можете включить JavaScript. Включение JavaScript также обеспечивает использование макета страницы. Затем можно настроить версию макета страницы для потока пользователей, описанную в следующем разделе.

![Страница свойств потока пользователей с выделенной настройкой Включить JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Выберите версию макета страницы

Независимо от того, включаемы ли вы JavaScript в свойствах потока пользователя, можно указать версию макета страницы для страниц потока пользователей. Откройте поток пользователей и выберите **макеты Страницы.** Под **LAYOUT NAME**выберите страницу потока пользователя и выберите **версию макета страницы.**

Для получения информации о различных [Page layout version change log](page-layout.md)версиях макета страницы см.

![Настройки макета страницы на портале, показывающие выпадение версии макета страницы](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Дальнейшие действия

Вы можете найти примеры использования JavaScript в [образцах JavaScript для использования в Azure Active Directory B2C.](javascript-samples.md)
