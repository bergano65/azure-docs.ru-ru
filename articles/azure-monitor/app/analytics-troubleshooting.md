---
title: Устранение неполадок аналитики в Azure Application Insights | Документация Майкрософт
description: 'Возникли проблемы с аналитикой Application Insights? Начните отсюда. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: mbullwin
ms.openlocfilehash: ecf0638aa999208331603ac30ccf4eb17b3c4500
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960689"
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Устранение неполадок аналитики в Application Insights
Возникли проблемы с [аналитикой Application Insights](analytics.md)? Начните отсюда. Аналитика — мощный инструмент поиска Azure Application Insights.

## <a name="limits"></a>Ограничения
* В настоящее время результаты запросов ограничены данными за последнюю неделю.
* Тестируемые браузеры: последние выпуски Chrome, Microsoft Edge и Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Известные несовместимые расширения браузеров
* Ghostery

Отключите это расширение или используйте другой браузер.

## <a name="e-a"></a> "Непредвиденная ошибка"
![Экран непредвиденной ошибки](media/analytics-troubleshooting/010.png)

Произошла внутренняя ошибка во время выполнения портала — необработанное исключение.

* Очистите кэш браузера.

## <a name="e-b"></a>403… попробуйте перезагрузить
![403... Попробуйте перезагрузить](media/analytics-troubleshooting/020.png)

Произошла ошибка, связанная с проверкой подлинности (во время проверки подлинности или во время создания маркера доступа). Возможно, для восстановления портала потребуется изменение параметров браузера.

* Убедитесь, что в браузере [включены сторонние файлы cookie](#cookies) . 

## <a name="authentication"></a>403… проверьте зону безопасности
![403... Проверьте зону безопасности](media/analytics-troubleshooting/030.png)

Произошла ошибка, связанная с проверкой подлинности (во время проверки подлинности или во время создания маркера доступа). Возможно, для восстановления портала потребуется изменение параметров браузера.

1. Убедитесь, что в браузере [включены сторонние файлы cookie](#cookies) . 
2. Вы использовали элемент избранного, закладку или сохраненную ссылку для открытия портала аналитики? Вы вошли, используя не те учетные данные, которые использовались при сохранении ссылки?
3. Используйте окно браузера в закрытом или анонимном режиме (после закрытия всех окон). Необходимо будет ввести свои учетные данные. 
4. Откройте другое (стандартное) окно браузера и перейдите к [Azure](https://portal.azure.com). Выполните выход. Затем откройте ссылку и выполните вход, используя правильные учетные данные.
5. У пользователей браузеров Microsoft Edge и Internet Explorer также может возникать эта ошибка, если параметры доверенной зоны не поддерживаются.
   
    Убедитесь, что [портал аналитики](https://portal.azure.com) и [портал Azure Active Directory](https://portal.azure.com) находятся в одной зоне безопасности.
   
   * В Internet Explorer щелкните **Свойства браузера**, **Безопасность**, **Надежные сайты**, **Узлы**.
     
     ![Диалоговое окно "Свойства браузера", добавление сайта в список надежных сайтов](media/analytics-troubleshooting/033.png)
     
     Если в списке веб-сайтов есть любой из следующих URL-адресов, убедитесь, чтобы и остальные адреса также были включены:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Ресурс не найден
![404... Ресурс не найден](media/analytics-troubleshooting/040.png)

Application resource was deleted from Application Insights and isn't available anymore (Ресурс приложения был удален из Application Insights и больше недоступен). Это может произойти, если сохранить URL-адрес на странице аналитики.

## <a name="e-e"></a>403... Нет авторизации
![403... Нет авторизации](media/analytics-troubleshooting/050.png)

У вас нет разрешения на открытие этого приложения в аналитике.

* Вы получили ссылку от другого пользователя? Попросите, чтобы вас внесли в список [читателей или участников этой группы ресурсов](../../azure-monitor/app/resources-roles-access-control.md).
* Вы сохранили ссылку, используя другие учетные данные? Откройте [портал Azure](https://portal.azure.com), выполните выход, а затем повторно щелкните эту ссылку и введите правильные учетные данные.

## <a name="html-storage"></a>403 ... Хранилище HTML5
На нашем портале используются хранилища HTML5 — localStorage и sessionStorage.

* Chrome: Settings (Параметры), Privacy (Конфиденциальность), Content settings (Параметры содержимого).
* Internet Explorer: "Свойства браузера", вкладка "Дополнительно", "Безопасность", "Включить хранилище DOM".

![403... Попробуйте включить хранилище HTML5](media/analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Подписка не найдена
![404... Подписка не найдена](media/analytics-troubleshooting/070.png)

Недопустимый URL-адрес. 

* Откройте ресурс приложения на [портале Application Insights](https://portal.azure.com). Затем используйте кнопку аналитики.

## <a name="e-h"></a>404… страница не существует
![404... Страница не существует](media/analytics-troubleshooting/080.png)

Недопустимый URL-адрес.

* Откройте ресурс приложения на [портале Application Insights](https://portal.azure.com). Затем используйте кнопку аналитики.

## <a name="cookies"></a>Включение сторонних файлов cookie
  Узнайте, [как отключить сторонние файлы cookie](https://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), но обратите внимание, что их необходимо **включить** .


[!INCLUDE [app-insights-analytics-footer](../../../includes/app-insights-analytics-footer.md)]

