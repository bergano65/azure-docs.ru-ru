---
title: Использование панели параметров Azure Application Insights Profiler | Документация Майкрософт
description: Просмотр состояния средства Profiler и запуск сеансов профилирования
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 9603c45443c6339a127f977600eeff2ba57a283f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884165"
---
# <a name="configure-application-insights-profiler"></a>Настройка Application Insights Profiler

## <a name="profiler-settings-pane"></a>Панель параметров Profiler

Чтобы открыть панель параметров Azure Application Insights Profiler, перейдите на панель "Производительность" в Application Insights и нажмите кнопку **Profiler**.

![Панель настройки Profiler][configure-profiler-entry]

Панель **Configure Application Insights Profiler** (Настройка Application Insights Profiler) содержит четыре компонента. 
* **Профилировать**. Запускает сеансы профилирования для всех приложений, связанных с этим экземпляром Application Insights.
* **Связанные приложения**. Позволяет создать список приложений, отправляющих данные профилирования на этот ресурс Application Insights.
* **Выполняемые сеансы**. Отображает состояние сеанса при выборе **Профилировать**. 
* **Недавние сеансы профилирования**. Отображает сведения о прошлых сеансах профилирования.

![Профилировщик по запросу][profiler-on-demand]

## <a name="app-service-environment"></a>Среда службы приложений
В зависимости от настроек Среды службы приложений Azure, вызов, проверяющий состояние агентов, может быть заблокирован. На этой панели может отображаться сообщение о том, что агент не работает, даже если он запущен. Чтобы удостовериться в этом, проверьте состояние веб-задания для приложения. Если верны все значения параметров приложения и в приложении установлено расширение сайта Application Insights, средство Profiler работает. Если приложение получает достаточный объем трафика, в списке должны отобразиться последние сеансы профилирования.

## <a id="profileondemand"></a> Запуск профилировщика вручную

### <a name="minimum-requirements"></a>Минимальные требования 
Пользователь может вручную запустить сеанс профилировщика им требуется как минимум «запись» на своей роли для компонента Application Insights. В большинстве случаев вы получаете доступ автоматически и дополнительных действий не требуется. Если у вас возникают проблемы, роль области подписку для добавления бы роль «Участник компонента Application Insights». [Узнайте больше об управлении доступом роли со службой мониторинга Azure](https://docs.microsoft.com/en-us/azure/azure-monitor/app/resources-roles-access-control).

Вы можете запустить средство Profiler вручную одним щелчком. Предположим, вы выполняете веб-тест производительности. Чтобы понять, как работает ваше веб-приложение под нагрузкой, вам потребуются трассировки. Очень важна возможность контролировать время записи трассировок, так как вам известно, когда будет выполняться нагрузочный тест. Однако это время может не совпадать с интервалом случайной выборки.

В следующем разделе объясняется, как работает этот сценарий.

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Шаг 1. Создание входящего трафика для веб-приложения путем запуска веб-теста производительности (необязательно)

Если в веб-приложение уже поступает входящий трафик или вы хотите создавать трафик вручную, пропустите этот раздел и перейдите к шагу 2.

1. На портале Application Insights выберите **Настройка** > **Тестирование производительности**. 

1. Нажмите кнопку **Создать**, чтобы запустить новый тест производительности.

   ![Создание теста производительности][create-performance-test]

1. В области **Новый тест производительности** настройте целевой URL-адрес теста. Примите все параметры по умолчанию, а затем выберите **Запустить тест**, чтобы запустить нагрузочный тест.

    ![Настройка нагрузочного теста][configure-performance-test]

    Вы увидите, что новый тест будет поставлен в очередь первым, а за ним будет следовать состояние *Выполняется*.

    ![Нагрузочный тест отправлен и поставлен в очередь][load-test-queued]

    ![Нагрузочный тест выполняется][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Шаг 2. Запуск сеанса средства Profiler по запросу

1. После запуска нагрузочного теста можно запустить средство Profiler для записи трассировок для веб-приложения в то время, когда оно получает нагрузку.

1. Перейдите на панель **настройки средства Profiler**.


### <a name="step-3-view-traces"></a>Шаг 3. Просмотр трассировок

После завершения работы средства Profiler следуйте инструкциям в уведомлении, чтобы перейти на панель "Производительность" и просмотреть трассировки.

## <a name="troubleshoot-the-profiler-on-demand-session"></a>Устранение неполадок для сеанса Profiler по запросу

После сеанса по запросу может отображаться сообщение об ошибке времени ожидания средства Profiler:

![Ошибка времени ожидания профилировщика][profiler-timeout]

Эта ошибка может произойти по одной из следующих причин:

* Сеанс средства Profiler по запросу прошел успешно, однако для обработки собранных данных Application Insights потребовалось больше времени, чем ожидалось.  

  Если данные не обрабатываются в течение 15 минут, на портале отобразится сообщение о времени ожидания. Тем не менее трассировки средства Profiler будут показаны через некоторое время. Если появится сообщение об ошибке, на этот раз игнорируйте его. Мы работаем над исправлением этой проблемы.

* Более старая версия агента Profiler веб-приложения не поддерживает функцию профилирования по запросу.  

  Если вы уже давно включили Application Insights Profiler, вам потребуется обновить агент Profiler для использования функции профилирования по запросу.
  
Перейдите на панель **Параметры приложений** служб приложений и проверьте, заданы ли следующие параметры:
* **APPINSIGHTS_INSTRUMENTATIONKEY** — замените правильным ключом инструментирования для Application Insights.
* **APPINSIGHTS_PORTALINFO** — ASP.NET:
* **APPINSIGHTS_PROFILERFEATURE_VERSION** — 1.0.0. 1.0.0

Если установлены не все указанные выше параметры, установите последнее расширение сайта следующим образом:

1. Перейдите на панель **Application Insights** на портале служб приложений.

    ![Включение Application Insights с помощью портала служб приложений][enable-app-insights]

1. Если на панели **Application Insights** отображается кнопка **Обновить**, нажмите ее, чтобы обновить расширение сайта Application Insights, которое установит последнюю версию агента Profiler.

    ![Обновление расширения веб-сайта][update-site-extension]

1. Чтобы убедиться, что Profiler включен, выберите **Изменить**, а затем щелкните **ОК**, чтобы сохранить изменения.

    ![Измените и сохраните App Insights][change-and-save-appinsights]

1. Вернитесь на панель **Параметры приложений** Службы приложений, чтобы убедиться, что заданы следующие значения:
   * **APPINSIGHTS_INSTRUMENTATIONKEY** — замените правильным ключом инструментирования для Application Insights.
   * **APPINSIGHTS_PORTALINFO** — ASP.NET: 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION** — 1.0.0. 1.0.0

     ![Параметры приложений для Profiler][app-settings-for-profiler]

1. При необходимости выберите **Расширения**, а затем проверьте версию расширения и определите, доступно ли обновление.

    ![Проверка обновлений для расширения][check-for-extension-update]

## <a name="next-steps"></a>Дальнейшие действия
[Включить Profiler и просмотреть трассировку](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
