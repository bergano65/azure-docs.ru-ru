---
title: Профилирование динамических приложений службы приложений Azure с помощью Application Insights | Документация Майкрософт
description: Профилирование динамических приложений в службе приложений Azure с помощью Application Insights Profiler.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d463732fc8e8f488851a57fe520f138b101eb6cf
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899945"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Профилирование динамических приложений службы приложений Azure с помощью Application Insights

Вы можете запустить профилировщик на ASP.NET и ASP.NET Core приложениях, работающих в службе приложений Azure, с использованием уровня служб "базовый" или выше. Сейчас включить средство Profiler на платформе Linux можно с помощью [этого метода](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Включение Profiler для приложений
Чтобы включить Profiler для приложения, сделайте следующее. Если вы используете другой тип службы Azure, ниже приведены инструкции по включению профайлера на других поддерживаемых платформах.
* [облачные службы](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Приложения Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Виртуальные машины](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler предварительно устанавливается в составе среды выполнения служб приложений. Ниже показано, как включить его для службы приложений. Выполните эти действия, даже если вы включили пакет SDK для App Insights в приложение во время сборки.

1. Включите параметр "Always On" для службы приложений. Этот параметр можно изменить на странице Конфигурация службы приложений в разделе Общие параметры.
1. Перейдите к панели **служб приложений** на портале Azure.
1. Выберите **Параметры > Application Insights**.

   ![Включение Application Insights на портале служб приложений](./media/profiler/AppInsights-AppServices.png)

1. Следуйте инструкциям на панели, чтобы создать ресурс Application Insights или выбрать имеющийся ресурс для отслеживания приложения. Также убедитесь, что средство Profiler **включено**. Если ресурс Application Insights находится в другой подписке из службы приложений, вы не сможете настроить Application Insights с помощью этой страницы. Вы по-прежнему можете сделать это вручную, создав необходимые параметры приложения вручную. [В следующем разделе приведены инструкции по включению профилировщика вручную.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Добавление расширения сайта Application Insights][Enablement UI]

1. Теперь средство Profiler включается с помощью параметра приложения Службы приложений.

    ![Параметр приложения для профилировщика][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Включение профайлера вручную или с помощью Azure Resource Manager
Application Insights Profiler можно включить, создав параметры приложения для службы приложений Azure. Страница с приведенными выше параметрами создает эти параметры приложения. Но вы можете автоматизировать создание этих параметров с помощью шаблона или других средств. Эти параметры также будут работать, если ресурс Application Insights находится в другой подписке из службы приложений Azure.
Ниже приведены параметры, необходимые для включения профилировщика.

|Параметр приложения    | Value    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey для ресурса Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


Эти значения можно задать с помощью [шаблонов Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Включение профилировщика для других облаков вручную

Если вы хотите включить профилировщик для других облаков, можно использовать приведенные ниже параметры приложения.

|Параметр приложения    | Значения правительства США| облако для Китая. |   
|---------------|---------------------|-------------|
|аппликатионинсигхтспрофилерендпоинт         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|аппликатионинсигхтсендпоинт | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Отключение профилировщика

Чтобы остановить или перезапустить Profiler для отдельного экземпляра приложения, в области **Веб-задания** перейдите к ресурсу приложения. Чтобы удалить профилировщик, откройте **Расширения**.

![Отключение профилировщика для веб-задания][disable-profiler-webjob]

Мы рекомендуем включить Profiler для всех приложений, чтобы обеспечить максимально быстрое обнаружение проблем производительности.

Файлы профилировщика можно удалить при использовании WebDeploy для развертывания изменений в веб-приложении. Вы можете запретить удаление, исключив папку App_Data во время развертывания. 


## <a name="next-steps"></a>Дальнейшие действия

* [Работа с Azure Application Insights в Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
