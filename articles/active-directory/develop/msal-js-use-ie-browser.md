---
title: Проблемы в Internet Explorer (MSAL. js) | Службы
titleSuffix: Microsoft identity platform
description: Используйте библиотеку проверки подлинности Майкрософт для JavaScript (MSAL. js) с браузером Internet Explorer.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de918d87abca521f3b573f831f8bfd6a7e17728
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424290"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Известные проблемы в Internet Explorer и браузерах Microsoft ребр (MSAL. js)

Библиотека проверки подлинности Microsoft для JavaScript (MSAL. js) создается для [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) , чтобы его можно было запускать в Internet Explorer. Однако есть несколько моментов, которые следует иметь в курсе.

## <a name="run-an-app-in-internet-explorer"></a>Запуск приложения в Internet Explorer
Если вы планируете использовать MSAL. js в приложениях, которые могут запускаться в Internet Explorer, перед обращением к скрипту MSAL. js необходимо добавить ссылку на заполняющий обещание.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Это обусловлено тем, что Internet Explorer не поддерживает JavaScript в собственном коде.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Отладка приложения, выполняющегося в Internet Explorer

### <a name="running-in-production"></a>Работа в рабочей среде
Развертывание приложения в рабочей среде (например, в веб-приложениях Azure) обычно работает нормально, если конечные пользователи приняли всплывающие окна. Мы тестировали его с помощью Internet Explorer 11.

### <a name="running-locally"></a>Локальное выполнение
Если вы хотите выполнять и отлаживать локально приложение, работающее в Internet Explorer, необходимо учитывать следующие моменты (предположим, что вы хотите запустить приложение как *http://localhost:1234* ):

- Internet Explorer имеет механизм безопасности с именем "защищенный режим", который предотвращает неправильную работу MSAL. js. После входа в систему страницу можно перенаправить на http://localhost:1234/null.

- Чтобы запустить и выполнить отладку приложения локально, необходимо отключить этот "защищенный режим". Для этого:

    1. Щелкните **инструменты** Internet Explorer (значок шестеренки).
    1. Выберите **Свойства браузера** и перейдите на вкладку **Безопасность** .
    1. Щелкните зону **Интернета** и снимите флажок **Включить защищенный режим (требуется перезапуск Internet Explorer)** . Internet Explorer предупреждает о том, что компьютер больше не защищен. Нажмите кнопку **ОК**.
    1. Перезапустите Internet Explorer.
    1. Запуск и отладка приложения.

По завершении восстановите параметры безопасности Internet Explorer.  Выберите **параметры** -> **Свойства обозревателя** -> **Безопасность** -> **Сбросить все зоны до уровня по умолчанию**.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об [известных проблемах при использовании MSAL. js в Internet Explorer](msal-js-use-ie-browser.md).