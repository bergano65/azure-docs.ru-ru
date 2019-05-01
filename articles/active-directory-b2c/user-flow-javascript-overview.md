---
title: JavaScript и странице контракта версии — Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как включить JavaScript и использовать версии контракта страницы в Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 91b4b621fc3dcedb52f88372fbfac222a744dbd1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570626"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>JavaScript и странице версий контракта в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C предоставляет набор упакованного содержимого, содержащий HTML, CSS и JavaScript для элементов пользовательского интерфейса в потоки пользователей и пользовательских политик. Чтобы включить JavaScript для приложений, необходимо добавить элемент к вашей [настраиваемой политики](active-directory-b2c-overview-custom.md) или включить ее для маршруты пользователей на портале, выберите контракт страницы и использовать [b2clogin.com](b2clogin.md) в запросах.

Если вы планируете включить [JavaScript](javascript-samples.md) код на стороне клиента, имеет смысл чтобы JavaScript основан на элементы являются неизменяемыми. В противном случае любые изменения могут вызвать непредвиденное поведение на страницах пользователя. Чтобы избежать таких проблем, можно принудительно задать использование контракта страницы и указать версию контракта страницы. Это гарантирует, что все определения содержимого, которые вы на основе JavaScript являются неизменяемыми. Даже если вы не собираетесь включить JavaScript, можно указать версию контракта страницы для страниц.

## <a name="user-flows"></a>Маршруты пользователей

В свойствах потока пользователя вы можете включить JavaScript, который также применит использование контракта на странице. Затем вы можете задать страницу "Версии контракта", как описано в следующем разделе.

![Включение параметра JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

Даже если вы не включили JavaScript в свойствах потока пользователя, вы можете указать страницу "Версии контракта" для страниц потока пользователя. Откройте поток пользователя и выберите **Макеты страниц**. В разделе **Имя макета** выберите страницу потока пользователя и **страницу "Версии контракта"**.

![Включение параметра JavaScript](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>Пользовательские политики

Чтобы включить JavaScript в пользовательские политики, добавьте **ScriptExecution** элемент **RelyingParty** элемент в файле настраиваемой политики. Дополнительные сведения см. в разделе [примеры JavaScript для использования в Azure Active Directory B2C](javascript-samples.md).

Независимо от того, имеется ли в пользовательских политиках включить JavaScript, можно указать версию контракта страницы для страниц. Дополнительные сведения об указании контракт страницы см. в разделе [выберите контракт страницы в Azure Active Directory B2C с помощью пользовательских политик](page-contract.md).

## <a name="next-steps"></a>Дальнейшие действия

См. статью [Примеры JavaScript для Azure Active Directory B2C](javascript-samples.md).
