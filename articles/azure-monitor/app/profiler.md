---
title: Профилирование динамических приложений службы приложений Azure с помощью Application Insights | Документация Майкрософт
description: Профилирование динамических приложений в службе приложений Azure с помощью Application Insights Profiler.
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
ms.openlocfilehash: f3ec10a970406cbb1bb6a1a52ffa8508e37fc516
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414173"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Профилирование динамических приложений службы приложений Azure с помощью Application Insights

Сейчас Profiler работает для приложений ASP.NET и ASP.NET Core, запущенных в службе приложений Azure. Он требуется, начиная от уровня службы "Базовый". Сейчас включить средство Profiler на платформе Linux можно с помощью [этого метода](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Включение Profiler для приложений
Чтобы включить Profiler для приложения, сделайте следующее. Далее приведены сведения по включению Profiler на других поддерживаемых платформах при выполнении службы Azure другого типа.
* [Облачные службы](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Приложения Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Виртуальные машины](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Средство Application Insights Profiler предварительно установлено в среде выполнения службы приложений. Но это средство нужно включить, чтобы получать профили для приложений службы приложений Azure. Чтобы активировать Profiler после развертывания приложения, даже если в исходный код был включен пакет SDK для App Insights, выполните следующие.

1. Перейдите к панели **служб приложений** на портале Azure.
2. Выберите **Параметры > Application Insights**.

   ![Включение Application Insights на портале служб приложений](./media/profiler/AppInsights-AppServices.png)

3. Следуйте инструкциям на панели, чтобы создать ресурс Application Insights или выбрать имеющийся ресурс для отслеживания приложения. Также убедитесь, что средство Profiler **включено**.

   ![Добавление расширения сайта Application Insights][Enablement UI]

4. Теперь средство Profiler включается с помощью параметра приложения Службы приложений.

    ![Параметр приложения для профилировщика][profiler-app-setting]

## <a name="disable-profiler"></a>Отключение профилировщика

Чтобы остановить или перезапустить Profiler для отдельного экземпляра приложения, в области **Веб-задания** перейдите к ресурсу приложения. Чтобы удалить профилировщик, откройте **Расширения**.

![Отключение профилировщика для веб-задания][disable-profiler-webjob]

Мы рекомендуем включить Profiler для всех приложений, чтобы обеспечить максимально быстрое обнаружение проблем производительности.

Если вы используете WebDeploy для развертывания изменений в веб-приложении, убедитесь, что папка App_Data не удалилась во время развертывания. В противном случае файлы с расширением профилировщика будут удалены при следующем развертывании веб-приложения в Azure.



## <a name="next-steps"></a>Дополнительная информация

* [Работа с Azure Application Insights в Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
