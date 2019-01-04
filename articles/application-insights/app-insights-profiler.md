---
title: Профилирование динамических веб-приложений Azure с помощью Application Insights | Документация Майкрософт
description: Профилирование динамических веб-приложений в Azure с помощью Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 4d957c26bd4e4ae278c0909c9df1476b02954b86
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138013"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Профилирование динамических веб-приложений Azure с помощью Application Insights

Сейчас профилировщик работает для веб-приложений ASP.NET и ASP.NET Core, запущенных в компоненте "Веб-приложения". Он требуется, начиная от уровня службы "Базовый". Сейчас включить средство Profiler на платформе Linux можно с помощью [этого метода](app-insights-profiler-aspnetcore-linux.md).

## <a id="installation"></a> Включение профилировщика для веб-приложений
Чтобы включить Profiler для веб-приложения, сделайте следующее. Далее приведены сведения по включению Profiler на других поддерживаемых платформах при выполнении службы Azure другого типа.
* [Облачные службы](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Приложения Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Виртуальные машины](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Средство Application Insights Profiler предварительно установлено в среде выполнения Службы приложений. Но это средство нужно включить, чтобы получать профили для веб-приложений Службы приложений Azure. Чтобы активировать после развертывания веб-приложения, даже если в исходный код был включено пакета SDK для App Insights, выполните следующие.

1. Перейдите к панели **служб приложений** на портале Azure.
1. Выберите **Параметры > Application Insights**.

   ![Включение Application Insights на портале служб приложений](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Следуйте инструкциям на панели, чтобы создать новый ресурс Application Insights или выбрать имеющийся ресурс для отслеживания веб-приложения. Также убедитесь, что средство Profiler **включено**.

   ![Добавление расширения сайта Application Insights][Enablement UI]

1. Теперь средство Profiler включается с помощью параметра приложения Службы приложений.

    ![Параметр приложения для профилировщика][profiler-app-setting]

## <a name="disable-profiler"></a>Отключение профилировщика

Чтобы остановить или перезапустить профилировщик для отдельного экземпляра веб-приложения, в области **Веб-задания** перейдите к ресурсу веб-приложений. Чтобы удалить профилировщик, откройте **Расширения**.

![Отключение профилировщика для веб-задания][disable-profiler-webjob]

Мы рекомендуем включить профилировщик для всех веб-приложений, чтобы обеспечить максимально быстрое обнаружение проблем производительности.

Если вы используете WebDeploy для развертывания изменений в веб-приложении, убедитесь, что папка App_Data не удалилась во время развертывания. В противном случае файлы с расширением профилировщика будут удалены при следующем развертывании веб-приложения в Azure.



## <a name="next-steps"></a>Дополнительная информация

* [Работа с Azure Application Insights в Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

