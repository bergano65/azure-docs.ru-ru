---
title: Вопросы на Internet Explorer (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Используйте библиотеку аутентификации Майкрософт для JavaScript (MSAL.js) с браузером Internet Explorer.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695863"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Известные проблемы в браузерах Internet Explorer и Microsoft Edge (MSAL.js)

Библиотека аутентификации Microsoft для JavaScript (MSAL.js) создана для [JavaScript ES5,](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) чтобы она была запущена в Internet Explorer. Есть, однако, несколько вещей, чтобы знать.

## <a name="run-an-app-in-internet-explorer"></a>Выполнить приложение в Internet Explorer
Если вы собираетесь использовать MSAL.js в приложениях, которые могут работать в Internet Explorer, вам нужно будет добавить ссылку на полизаполнение обещания, прежде чем ссылаться на сценарий MSAL.js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Это происходит потому, что Internet Explorer не поддерживает обещания JavaScript на родине.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Отладка приложения, работающего в Internet Explorer

### <a name="running-in-production"></a>Запуск в производстве
Развертывание приложения в производство (например, в веб-приложениях Azure) обычно работает нормально, при условии, что конечный пользователь принял всплывающие данные. Мы протестировали его с Internet Explorer 11.

### <a name="running-locally"></a>Запуск локально
Если вы хотите запустить и отладить локально ваше приложение работает в Internet Explorer, вы должны быть осведомлены *http://localhost:1234*о следующих соображениях (предположим, что вы хотите запустить приложение как):

- Internet Explorer имеет механизм безопасности под названием "защищенный режим", который предотвращает MSAL.js от правильной работы. Среди симптомов, после вступления в систему, http://localhost:1234/nullстраница может быть перенаправлена на .

- Чтобы выполнить и отладить приложение локально, необходимо отключить этот "защищенный режим". Для этого:

    1. Нажмите Internet Explorer **Tools** (значок передачи).
    1. Выберите **параметры Интернета,** а затем вкладку **безопасности.**
    1. Нажмите на **интернет-зону** и отключите **защищенный режим включения (требует перезагрузки Internet Explorer).** Internet Explorer предупреждает, что ваш компьютер больше не защищен. Нажмите кнопку **ОК**.
    1. Перезапустите Internet Explorer.
    1. Выполнить и отладить приложение.

После завершения работы восстановите настройки безопасности Internet Explorer.  Выберите **настройки** -> **Internet Options** -> **Security** -> **Сброс всех зон до уровня по умолчанию.**

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о [известных проблемах при использовании MSAL.js в Internet Explorer](msal-js-use-ie-browser.md).