---
title: Версии JavaScript и макета страницы
titleSuffix: Azure AD B2C
description: Узнайте, как включить JavaScript и использовать версии макета страницы в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f6d4849b02f320c7479469b4ee56be50e4f8dee
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840100"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Версии JavaScript и макета страницы в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C предоставляет набор упакованного содержимого, содержащего HTML, CSS и JavaScript для элементов пользовательского интерфейса в потоках пользователя и пользовательских политик. Чтобы включить JavaScript для приложений, необходимо добавить элемент в [настраиваемую политику](custom-policy-overview.md) или включить его на портале для потоков пользователей, выбрать макет страницы и использовать [b2clogin.com](b2clogin.md) в запросах.

Если вы планируете включить код на стороне клиента [JavaScript](javascript-samples.md) , необходимо убедиться, что элементы, на которых основан JavaScript, являются неизменяемыми. В противном случае любые изменения могут привести к непредвиденному поведению пользовательских страниц. Чтобы избежать этих проблем, можно принудительно применить макет страницы и указать версию макета страницы. Это гарантирует, что все определения содержимого, основанные на JavaScript, будут неизменными. Даже если вы не планируете включать JavaScript, можно указать версию макета страницы для страниц.

## <a name="user-flows"></a>Маршруты пользователей

В **свойствах**потока пользователя можно включить JavaScript, который также обеспечивает применение макета страницы. Затем можно задать версию макета страницы для пользовательского потока, как описано в следующем разделе.

![Страница свойств потока пользователей с выделенным параметром "включить JavaScript"](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>Выберите версию макета страницы

Независимо от того, включен ли JavaScript в свойствах потока пользователя, можно указать версию макета страницы для страниц потока пользователя. Откройте поток пользователя и выберите **макеты страниц**. В разделе **имя макета**выберите страницу потока пользователя и выберите **версию макета страницы**.

Сведения о различных версиях макета страниц см. в [журнале изменений версий](page-layout.md#version-change-log).

![Параметры макета страницы на портале, в котором отображается раскрывающийся список версий макета страницы](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>Пользовательские политики

Чтобы включить JavaScript в пользовательских политиках, добавьте элемент **скриптексекутион** в элемент **релингпарти** в файле настраиваемой политики. Дополнительные сведения см. [в разделе примеры JavaScript для использования в Azure Active Directory B2C](javascript-samples.md).

Независимо от того, включен ли JavaScript в пользовательских политиках, можно указать версию макета страницы для страниц. Дополнительные сведения об указании макета страницы см. [в разделе Выбор макета страницы в Azure Active Directory B2C с помощью пользовательских политик](page-layout.md).

## <a name="next-steps"></a>Дальнейшие действия

Сведения о различных версиях макета страниц см. в разделе **Журнал изменений версии** статьи [Выбор макета страницы в Azure Active Directory B2C с помощью пользовательских политик](page-layout.md#version-change-log).

Примеры использования JavaScript можно найти в [примерах JavaScript для использования в Azure Active Directory B2C](javascript-samples.md).
