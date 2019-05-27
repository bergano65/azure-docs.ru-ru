---
title: Используйте Internet Explorer (библиотека аутентификации Майкрософт для JavaScript) | Azure
description: Дополнительные сведения об использовании библиотеки проверки подлинности Майкрософт для JavaScript (MSAL.js) с помощью обозревателя Internet Explorer.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf8c84120f4c90d3943cfc31ffbf9aafcec0ba3
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873920"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Известные проблемы в браузерах Internet Explorer и Microsoft Edge с MSAL.js

Библиотека проверки подлинности Майкрософт для JavaScript (MSAL.js) создается для [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) , чтобы оно могло выполняться в Internet Explorer. Тем не менее, существуют некоторые важные замечания.

## <a name="run-an-app-in-internet-explorer"></a>Запуск приложения в Internet Explorer
Если вы планируете использовать MSAL.js в приложениях, которые могут выполняться в Internet Explorer, необходимо добавить ссылку на polyfill обещание перед ссылкой MSAL.js скрипт.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Это обусловлено Internet Explorer не поддерживает JavaScript обещания.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Отладка приложения, работающего в Internet Explorer

### <a name="running-in-production"></a>В рабочей среде
Развертывание приложения в рабочей среде (например, в веб-приложениях Azure) обычно работает нормально, если конечный пользователь принял всплывающие окна. Мы протестировали его с помощью Internet Explorer 11.

### <a name="running-locally"></a>Выполняется локально
Если вы хотите выполнять и отлаживать локально в приложении, в обозревателе Internet Explorer, необходимо учитывать следующие факторы (предполагается, что вы хотите запустить приложение в качестве *http://localhost:1234*):

- Internet Explorer имеет механизм безопасности с именем «защищенный режим», который не позволяет MSAL.js работать правильно. Среди симптомов, после входа, страницы могут быть перенаправлены http://localhost:1234/null.

- Для запуска и отладки приложения локально, необходимо отключить этот «защищенный режим». Для этого:

    1. Internet Explorer щелкните **средства** (значок шестеренки).
    1. Выберите **обозревателя** и затем **безопасности** вкладки.
    1. Щелкните **Internet** зоны и снимите флажок **Включить защищенный режим (потребуется перезапуск Internet Explorer)**. Internet Explorer о том, что компьютер больше не будет защищен. Последовательно выберите **ОК**.
    1. Перезапустите Internet Explorer.
    1. Выполнять и отлаживать приложения.

Когда вы закончите, восстановите параметры безопасности Internet Explorer.  Выберите **параметры** -> **обозревателя** -> **безопасности** -> **сбросить все зоны по умолчанию уровень**.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о [известные проблемы при использовании MSAL.js в Internet Explorer](msal-js-use-ie-browser.md).