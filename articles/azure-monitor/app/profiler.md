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
ms.openlocfilehash: b8f6a2d12e1a9920421e6491432b516520ae110b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60730196"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Профилирование динамических приложений службы приложений Azure с помощью Application Insights

Можно запустить Profiler на ASP.NET и ASP.NET Core приложений, запущенных в службе приложений Azure с помощью уровень обслуживания Basic или более поздней версии. Сейчас включить средство Profiler на платформе Linux можно с помощью [этого метода](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Включение Profiler для приложений
Чтобы включить Profiler для приложения, сделайте следующее. Если вы используете другой тип службы Azure, ниже приведены инструкции по включению Profiler на других поддерживаемых платформах.
* [Облачные службы](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Приложения Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Виртуальные машины](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler предварительно устанавливается как часть среды выполнения службы приложений. Приведенные ниже будет показано, как включить, то для службы приложений. Выполните следующие действия, даже если включен пакет SDK для App Insights в приложении во время сборки.

1. Включите параметр «Всегда включено» для службы приложений. Этот параметр на странице настройки службы приложений в разделе Общие параметры, можно обновить.
1. Перейдите к панели **служб приложений** на портале Azure.
1. Выберите **Параметры > Application Insights**.

   ![Включение Application Insights на портале служб приложений](./media/profiler/AppInsights-AppServices.png)

1. Следуйте инструкциям на панели, чтобы создать ресурс Application Insights или выбрать имеющийся ресурс для отслеживания приложения. Также убедитесь, что средство Profiler **включено**. Если ресурс Application Insights находится в другой подписке из службы приложений, нельзя использовать эту страницу для настройки Application Insights. По-прежнему выполнять ее вручную на то, что, создав приложения, необходимый параметры вручную. [Следующий раздел содержит инструкции для вручную включения Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Добавление расширения сайта Application Insights][Enablement UI]

1. Теперь средство Profiler включается с помощью параметра приложения Службы приложений.

    ![Параметр приложения для профилировщика][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Включить Profiler, вручную или с помощью Azure Resource Manager
Application Insights Profiler можно включить, создав параметры приложения для службы приложений Azure. Страница с параметрами, показанный выше создает эти параметры приложения для вас. Но вы можете автоматизировать создание эти параметры, с помощью шаблона или другими способами. Эти параметры также будут работать, если ресурс Application Insights находится в другой подписке из службы приложений Azure.
Ниже приведены параметры, необходимые для включения профилировщика.

|Параметр приложения    | Значение    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | ключ инструментирования ресурса Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Можно задать эти значения, используя [шаблонов Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).


## <a name="disable-profiler"></a>Отключение профилировщика

Чтобы остановить или перезапустить Profiler для отдельного экземпляра приложения, в области **Веб-задания** перейдите к ресурсу приложения. Чтобы удалить профилировщик, откройте **Расширения**.

![Отключение профилировщика для веб-задания][disable-profiler-webjob]

Мы рекомендуем включить Profiler для всех приложений, чтобы обеспечить максимально быстрое обнаружение проблем производительности.

Profiler файлы можно удалить, при использовании веб-развертывание для развертывания изменений в веб-приложение. Удаление можно предотвратить, исключив папка App_Data удалилась во время развертывания. 


## <a name="next-steps"></a>Дальнейшие действия

* [Работа с Azure Application Insights в Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
