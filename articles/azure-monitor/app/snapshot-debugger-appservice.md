---
title: Включить моментальный снимок Debugger для приложений .NET в Azure App Service (ru) Документы Майкрософт
description: Включить моментальный снимок для приложений .NET в службе приложений Azure
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8af688e38003e0613a06d7d8622ce279a3838589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298277"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Включить моментальный снимок для приложений .NET в службе приложений Azure

В настоящее время Snapshot Debugger работает для ASP.NET и ASP.NET приложений Core, которые работают в планах службы приложений Azure в планах служб windows.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>Включить моментальный снимок Debugger
Чтобы включить Snapshot Debugger для приложения, следуйте инструкциям ниже. Если вы работаете с другим типом службы Azure, вот инструкции для включения Snapshot Debugger на других поддерживаемых платформах:
* [Облачные службы Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Услуги Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Профилирование веб-приложений, работающих на виртуальной машине Azure или в масштабируемом наборе виртуальных машин, с помощью Application Insights Profiler](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [На территории виртуальных или физических машин](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Если вы используете предварительную версию ядра .NET, пожалуйста, следуйте инструкциям для [Enable Snapshot Debugger для других сред,](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) сначала включите пакет [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet с приложением, а затем заполните остальные инструкции ниже. 

Приложение Insights Snapshot Debugger предварительно устанавливается как часть времени выполнения Службы app, но вам нужно включить его, чтобы получить снимки для приложения Службы приложения. После развертывания приложения, даже если вы включили SDK Application Insights в исходный код, выполните ниже приведенные ниже шаги, чтобы включить отладку моментального снимка.

1. Перейдите к панели **служб приложений** на портале Azure.
2. Выберите **Параметры > Application Insights**.

   ![Включение Application Insights на портале служб приложений](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Следуйте инструкциям на панели, чтобы создать ресурс Application Insights или выбрать имеющийся ресурс для отслеживания приложения. Также убедитесь, что оба переключателя **On**для Snapshot Debugger включены.

   ![Добавление расширения сайта Application Insights][Enablement UI]

4. Snapshot Debugger теперь включен с помощью настройки приложения Службы Приложения.

    ![Настройка приложения для snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Отключить снимок Отладка

Следуйте тем же шагам, что и для **Включенного снимка Debugger,** но переключите оба переключателя для Snapshot Debugger на **Off.**
Мы рекомендуем, чтобы во всех приложениях была включена snapshot Debugger, чтобы облегчить диагностику исключений приложений.

## <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager

Для службы приложений Azure можно настроить настройки приложений в шаблоне управления ресурсами Azure для включения snapshot Debugger и Profiler. Вы добавляете ресурс конфигурации, содержащий настройки приложения в качестве детского ресурса веб-сайта:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Дальнейшие действия

- Создание трафика в приложение, которое может вызвать исключение. Затем подождите от 10 до 15 минут, чтобы снимки были отправлены в экземпляр Application Insights.
- [Снимки](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) можно увидеть на портале Azure.
- Для получения помощи в устранении [Snapshot Debugger troubleshooting](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)проблем с устранением неполадок Snapshot Debugger см.

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

