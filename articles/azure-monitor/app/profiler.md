---
title: Профилирование динамических приложений службы приложений Azure с помощью Application Insights | Документация Майкрософт
description: Профилирование динамических приложений в службе приложений Azure с помощью Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275780"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Профилирование динамических приложений службы приложений Azure с помощью Application Insights

Вы можете запускать Profiler на ASP.NET и ASP.NET основных приложений, которые работают на Azure App Service с использованием базового уровня обслуживания или выше. Сейчас включить средство Profiler на платформе Linux можно с помощью [этого метода](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Включение Profiler для приложений
Чтобы включить Profiler для приложения, сделайте следующее. Если вы работаете с другим типом службы Azure, вот инструкции для включения Profiler на других поддерживаемых платформах:
* [Облачные службы](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Приложения Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Виртуальные машины](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Приложение Insights Profiler предварительно устанавливается как часть времени выполнения служб ы App. Приведенные ниже приведенные ниже шаги покажут, как включить его для службы приложений. Выполните следующие действия, даже если вы включили App Insights SDK в приложение во время сборки.

1. Включите настройки "Всегда на" для службы приложений. Можно обновить настройки на странице конфигурации Службы app в соответствии с общими настройками.
1. Перейдите к панели **служб приложений** на портале Azure.
1. Выберите **Параметры > Application Insights**.

   ![Включение Application Insights на портале служб приложений](./media/profiler/AppInsights-AppServices.png)

1. Следуйте инструкциям на панели, чтобы создать ресурс Application Insights или выбрать имеющийся ресурс для отслеживания приложения. Также убедитесь, что средство Profiler **включено**. Если ресурс Application Insights находится в другой подписке от Службы приложения, вы не можете использовать эту страницу для настройки приложений. Вы все еще можете сделать это вручную, хотя, создавая необходимые настройки приложения вручную. [Следующий раздел содержит инструкции для ручного включения Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Добавление расширения сайта Application Insights][Enablement UI]

1. Теперь средство Profiler включается с помощью параметра приложения Службы приложений.

    ![Параметр приложения для профилировщика][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Включить Профилер вручную или с менеджером ресурсов Azure
Профиль приложения Insights можно сделать, создав настройки приложений для службы приложений Azure. Страница с параметрами, показанными выше, создает эти настройки приложения для вас. Но вы можете автоматизировать создание этих настроек с помощью шаблона или другими средствами. Эти настройки также будут работать, если ресурс Application Insights находится в другой подписке от службы приложений Azure.
Вот параметры, необходимые для включения профайлера:

|Параметр приложения    | Значение    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey для ресурса Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | No 3 |


Вы можете установить эти значения с помощью [шаблонов управления ресурсами Azure,](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager) [Azure Powershell,](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) [Azure CLI.](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest)

### <a name="enabling-profiler-for-other-clouds-manually"></a>Включение профилера для других облаков вручную

Если вы хотите включить профайлер для других облаков, вы можете использовать настройки ниже приложения.

|Параметр приложения    | Ценности правительства США| Облако Китая |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Отключение профилировщика

Чтобы остановить или перезапустить Profiler для отдельного экземпляра приложения, в области **Веб-задания** перейдите к ресурсу приложения. Чтобы удалить профилировщик, откройте **Расширения**.

![Отключение профилировщика для веб-задания][disable-profiler-webjob]

Мы рекомендуем включить Profiler для всех приложений, чтобы обеспечить максимально быстрое обнаружение проблем производительности.

Файлы протеже могут быть удалены при использовании WebDeploy для развертывания изменений в веб-приложении. Можно предотвратить удаление, исключив App_Data папку от удаления во время развертывания. 


## <a name="next-steps"></a>Дальнейшие действия

* [Работа с Application Insights в Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
