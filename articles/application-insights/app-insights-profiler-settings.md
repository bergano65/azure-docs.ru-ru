---
title: Колонка "Параметры" для Azure Application Insights Profiler | Документация Майкрософт
description: Просмотр состояния профилировщика и запуск сеансов профилирования
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 81608dd7281ceddce7e0701535ad99e1c9e44315
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142917"
---
# <a name="configure-application-insights-profiler"></a>Настройка Application Insights Profiler

## <a name="profiler-settings-page"></a>Страница параметров Profiler

Страницу параметров профилировщика можно открыть со страницы "Производительность" в Application Insights, нажав кнопку **Profiler**.

![запись области настройки профилировщика][configure-profiler-entry]

Страница настройки Application Insights Profiler содержит четыре компонента. 
1. **Профилировать**. Если нажать эту кнопку, запустится сеанс профилирования для всех приложений, связанных с выбранным экземпляром Application Insights.
1. **Связанные приложения**. Позволяет открыть список приложений, отправляющих данные профилировщика к этому ресурсу Application Insights.
1. **Выполняемые сеансы**. Когда вы нажмете кнопку **Профилировать**, здесь отобразятся данные о состоянии сеанса.
1. **Недавние сеансы профилирования**. Здесь отображаются сведения о прошлых сеансах профилирования.

![Профилировщик по запросу][profiler-on-demand]

## <a name="app-service-environments-ase"></a>Среды службы приложений (ASE)
В зависимости от настроек среды ASE вызов, проверяющий состояние агентов, может быть заблокирован. Тогда на этой странице будет указано, что агент не запущен, но это может не соответствовать действительности. Чтобы узнать наверняка, проверьте состояние веб-задания для приложения. Но если все параметры приложения установлены правильно, и для приложения установлено расширение сайта Application Insights, профилировщик будет работать и вы увидите в списке недавние сеансы профилирования, если для этого приложения получено достаточно трафика.

## <a id="profileondemand"></a> Запуск профилировщика вручную

Профилировщик можно активировать вручную одним нажатием кнопки. Предположим, вы выполняете веб-тест производительности. Чтобы понять, как работает ваше веб-приложение под нагрузкой, вам потребуются трассировки. Очень важно иметь возможность контролировать время записи трассировок, поскольку вам известно, когда будет выполняться нагрузочный тест, однако это время может не совпадать с интервалом случайной выборки.
Далее объясняется, как работает этот сценарий.

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Шаг 1. Создание трафика в веб-приложение путем запуска веб-теста производительности (необязательно)

Если в веб-приложение уже поступает входящий трафик или вы хотите создавать трафик вручную, пропустите этот раздел и перейдите к шагу 2.

Перейдите на портал Application Insights в раздел **Настройка > Тестирование производительности**. Нажмите кнопку "Создать", чтобы запустить новый тест производительности.

![Создание теста производительности][create-performance-test]

В области **Новый тест производительности** настройте целевой URL-адрес теста. Примите все параметры по умолчанию и запустите нагрузочный тест.

![Настройка нагрузочного теста][configure-performance-test]

Вы увидите, что новый тест будет поставлен в очередь первым, а за ним будет следовать состояние "Выполняется".

![нагрузочный тест отправлен и поставлен в очередь][load-test-queued]

![нагрузочный тест выполняется][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Шаг 2. Запуск профилировщика по запросу

После запуска нагрузочного теста можно запустить профилировщик для записи трассировок для веб-приложения в то время, когда оно получает нагрузку.
Перейдите к области "Настройка профилировщика":


### <a name="step-3-view-traces"></a>Шаг 3. Просмотр трассировок

После завершения работы профилировщика следуйте инструкциям в уведомлении, чтобы перейти к странице "Производительность" и просмотреть трассировки.

## <a name="troubleshooting-on-demand-profiler"></a>Устранение неполадок профилировщика по запросу

Иногда после сеанса профилирования по запросу может отображаться сообщение об ошибке времени ожидания профилировщика:

![Ошибка времени ожидания профилировщика][profiler-timeout]

Возможны две причины появления этой ошибки.

1. Сеанс профилирования по запросу прошел успешно, однако для обработки собранных данных Application Insights потребовалось больше времени. Если обработка данных не завершится за 15 минут, на портале отобразится сообщение об истечении времени ожидания. Тем не менее трассировки профилировщика будут показаны через некоторое время. В этом случае просто игнорируйте сообщение об ошибке. Мы работаем над исправлением этой проблемы.

1. Более старая версия агента профилировщика веб-приложения не поддерживает функцию профилирования по запросу. Если вы уже давно включили профиль Application Insights, скорее всего, вам потребуется обновить агент профилировщика для использования функции профилирования по запросу.
  
Выполните следующие действия, чтобы проверить и установить последний профилировщик.

1. Перейдите к параметрам приложений службы приложений и проверьте, заданы ли следующие параметры:
    * **APPINSIGHTS_INSTRUMENTATIONKEY** — замените правильным ключом инструментирования для Application Insights.
    * **APPINSIGHTS_PORTALINFO** — ASP.NET.
    * **APPINSIGHTS_PROFILERFEATURE_VERSION** — 1.0.0. Если любой из этих параметров не задан, перейдите на панель включения Application Insights, чтобы установить последнее расширение сайта.

1. Перейдите к панели Application Insights на портале службы приложений.

    ![Включение Application Insights на портале службы приложений][enable-app-insights]

1. Если на следующей странице отображается кнопка "Обновить", нажмите ее, чтобы обновить расширение сайта Application Insights, которое установит последнюю версию агента профилировщика.

    ![Обновление расширения веб-сайта][update-site-extension]

1. Затем щелкните **изменить**, чтобы включить профилировщик, и выберите **ОК** для сохранения изменений.

    ![Измените и сохраните App Insights][change-and-save-appinsights]

1. Вернитесь на вкладку **Параметры приложений** службы приложений, чтобы еще раз убедиться, что перечисленные ниже параметры приложений заданы правильно.
    * **APPINSIGHTS_INSTRUMENTATIONKEY** — замените правильным ключом инструментирования для Application Insights.
    * **APPINSIGHTS_PORTALINFO** — ASP.NET.
    * **APPINSIGHTS_PROFILERFEATURE_VERSION** — 1.0.0.

    ![параметры приложений для профилировщика][app-settings-for-profiler]

1. При необходимости проверьте версию расширения и убедитесь, что нет новых обновлений.

    ![проверка обновлений для расширения][check-for-extension-update]

## <a name="next-steps"></a>Дальнейшие действия
[Включение Profiler и просмотр трассировок](app-insights-profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png