---
title: Версии JavaScript и макета страницы
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185848"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Версии JavaScript и макета страницы в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C предоставляет набор упакованного содержимого, содержащего HTML, CSS и JavaScript для элементов пользовательского интерфейса в потоках пользователя и пользовательских политик.

Чтобы включить JavaScript для приложений, выполните следующие действия.

* Включите его в потоке пользователей с помощью портал Azure
* Выберите [Макет страницы](page-layout.md)
* Использование [b2clogin.com](b2clogin.md) в запросах

Если вы планируете включить код на стороне клиента [JavaScript](javascript-samples.md) , элементы, которые основаны на коде JavaScript, должны быть неизменными. Если они не являются неизменяемыми, то любые изменения могут привести к непредвиденному поведению пользовательских страниц. Чтобы избежать этих проблем, принудительно Используйте макет страницы и укажите версию макета страницы, чтобы определения содержимого, основанные на JavaScript, были неизменяемыми. Даже если вы не планируете включать JavaScript, можно указать версию макета страницы для страниц.

## <a name="enable-javascript"></a>Включение JavaScript

В **свойствах**потока пользователя можно включить JavaScript. Включение JavaScript также обеспечивает принудительное использование макета страницы. Затем можно задать версию макета страницы для пользовательского потока, как описано в следующем разделе.

![Страница свойств потока пользователей с выделенным параметром "включить JavaScript"](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Выберите версию макета страницы

Независимо от того, включен ли JavaScript в свойствах потока пользователя, можно указать версию макета страницы для страниц потока пользователя. Откройте поток пользователя и выберите **макеты страниц**. В разделе **имя макета**выберите страницу потока пользователя и выберите **версию макета страницы**.

Сведения о различных версиях макета страницы см. в [журнале изменений версии макета страницы](page-layout.md).

![Параметры макета страницы на портале, в котором отображается раскрывающийся список версий макета страницы](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Дальнейшие действия

Примеры использования JavaScript можно найти в [примерах JavaScript для использования в Azure Active Directory B2C](javascript-samples.md).
