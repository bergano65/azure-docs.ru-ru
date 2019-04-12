---
title: Мониторинг производительности служб приложений Azure | Документация Майкрософт
description: Мониторинг производительности приложений для служб приложений Azure. Диаграммы нагрузки и время отклика, сведения о зависимостях и настройка оповещений о производительности.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: mbullwin
ms.openlocfilehash: 25f620cb36c2bfb548ecf08c33dc04b37118a256
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489628"
---
# <a name="monitor-azure-app-service-performance"></a>Мониторинг производительности Службы приложений Azure

Включение мониторинга на .NET и .NET Core на основе приложений, работающих [службы приложений](https://docs.microsoft.com/azure/app-service/) стала проще, чем когда-либо. Тогда как ранее было необходимо вручную установить расширение сайта, последнюю версию расширения/агента теперь встроена в изображение приложения службы по умолчанию. В этой статье будут пошагово Включение мониторинга Application Insights, а также предварительные рекомендации для автоматизации процесса для крупномасштабного развертывания.

> [!NOTE]
> Вручную добавлять расширение сайта Application Insights с помощью **средства разработки** > **расширения** является устаревшим. Этот метод установки расширения зависела от обновления вручную для каждой новой версии. Последняя стабильная версия расширения — это теперь [предварительно](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) как часть образа службы приложений. Файлы размещаются в `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` и автоматически обновляются с каждым выпуском стабильной. Если вы следуете инструкциям на основе агента, чтобы включить мониторинг ниже, он автоматически удалит устаревшие расширения для вас.

## <a name="enable-application-insights"></a>Включение Application Insights

Чтобы включить мониторинг приложений для приложений, размещенных служб приложений Azure двумя способами:

* **Мониторинг приложений на основе агента** (ApplicationInsightsAgent).  
    * Этот метод является самым простым для включения и никаких дополнительных настроек не требуется. Оно часто называется «runtime» мониторинг. Для служб приложений Azure рекомендуется по меньшей мере, включение этот уровень мониторинга и затем зависимости от конкретного сценария, вы можете оценить ли необходим более расширенный мониторинг через инструментирование вручную.

* **Вручную инструментирование приложения с помощью кода** , установив пакет SDK Application Insights.

    * Этот подход гораздо более настраивается, но он требует [Добавление зависимости для пакетов NuGet пакета SDK для Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Этот метод также означает, что вам нужно самостоятельно управлять обновления до последней версии пакетов.

    * Если вы хотите внести вызовы пользовательских API для отслеживания событий и зависимости, не фиксируются по умолчанию с помощью агентов мониторинга, необходимо использовать этот метод. Ознакомьтесь с [API для пользовательских событий и метрик статьи](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) для получения дополнительных сведений.

> [!NOTE]
> Если на основе агента мониторинга и вручную пакет SDK на основе инструментирования обнаруживается, что будет учитываться только параметры инструментирование вручную. Это позволяет предотвратить дублирование данных с отправлено. Дополнительные сведения об этом извлечение [разделе об устранении неполадок](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) ниже.

## <a name="enable-agent-based-monitoring-net"></a>Включение мониторинга .NET на основе агента

1. На панели управления Azure **выберите Application Insights** для своей службы приложений.

    ![В разделе "Параметры" выберите пункт Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Нажмите переключатель "Создать новый ресурс", если вы еще не настроили ресурс Application Insights для этого приложения. 

     > [!NOTE]
     > Нажав кнопку **OK** для создания нового ресурса, вам будет предложено выбрать параметр **Применение параметров мониторинга**. Нажав кнопку **Продолжить**, вы свяжете новый ресурс Application Insights со службой приложений, а также **активируете перезапуск самой службы приложений**. 

     ![Инструментирование веб-приложения](./media/azure-web-apps/create-resource-01.png)

2. Указав ресурс для использования, вы можете выбрать способ сбора данных в Application Insights для каждой платформы для приложения. Мониторинг приложений ASP.NET включен по умолчанию с двумя разными уровнями из коллекции.

    ![Выбор параметров для каждой платформы](./media/azure-web-apps/choose-options-new.png)

   * Уровень **базовой коллекции** .NET предоставляет основные возможности APM с одним экземпляром.

   * Уровень **рекомендуемой коллекции** .NET:
       * Добавляет тенденции использования ЦП, памяти и операций ввода-вывода.
       * Сопоставляет микрослужбы в пределах границ запросов или зависимостей.
       * Собирает тенденции использования и обеспечивает сопоставление от результатов доступности до транзакций.
       * Собирает исключения, необработанные хост-процессом.
       * Улучшает точность метрик APM под нагрузкой при использовании выборки.

3. Чтобы настроить такие параметры, как выборка, которой можно управлять с помощью файла applicationinsights.config ранее теперь вы можете работать с те же параметры, с помощью параметров приложения с помощью соответствующего префикса. 

    * Например, чтобы изменить процент начальной выборки, можно создать параметр приложения: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` и значение `100`.

    * Список поддерживаемых адаптивной выборки телеметрии процессора параметров, можно найти [кода](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) и [соответствующую документацию](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-net-core"></a>Включение агента мониторинга .NET Core

Поддерживаются следующие версии .NET Core: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

Целевой платформой framework из .NET Core, автономное развертывание и ASP.NET Core 3.0 в настоящий момент **не поддерживается** с мониторингом агента или расширения на основе. ([Инструментирование вручную](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) через код будет работать во всех предыдущих сценариях.)

1. На панели управления Azure **выберите Application Insights** для своей службы приложений.

    ![В разделе "Параметры" выберите пункт Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Нажмите переключатель "Создать новый ресурс", если вы еще не настроили ресурс Application Insights для этого приложения. 

     > [!NOTE]
     > Нажав кнопку **OK** для создания нового ресурса, вам будет предложено выбрать параметр **Применение параметров мониторинга**. Нажав кнопку **Продолжить**, вы свяжете новый ресурс Application Insights со службой приложений, а также **активируете перезапуск самой службы приложений**. 

     ![Инструментирование веб-приложения](./media/azure-web-apps/create-resource-01.png)

2. После указания, какой ресурс следует использовать, можно выбрать способ Application Insights для сбора данных каждой платформы для приложения. .NET core предоставляет **рекомендуется коллекции** или **отключено** для .NET Core 2.0, 2.1 и 2.2.

    ![Выбор параметров для каждой платформы](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>Включение мониторинга .NET на стороне клиента

Мониторинг на стороне клиента явно согласиться на ASP.NET. Включение наблюдения на стороне клиента.

* Выберите **параметры** > ** ** приложения параметры ***
   * В разделе "Параметры приложения" добавьте новую **имя параметра приложения** и **значение**:

     Имя: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Значение: `true`

   * **Сохраните** параметры и **перезапустите** приложение.

![Снимок экрана приложения параметры пользовательского интерфейса](./media/azure-web-apps/appinsights-javascript-enabled.png)

Чтобы отключить мониторинг либо удалите пару связанное значение ключа в настройках приложения на стороне клиента или значение равно false.

## <a name="enable-client-side-monitoring-net-core"></a>Включение клиентского мониторинга .NET Core

Мониторинг на стороне клиента является **включен по умолчанию** для приложений .NET Core с помощью **рекомендуется коллекции**, независимо от того, присутствует ли параметр «APPINSIGHTS_JAVASCRIPT_ENABLED» приложения.

Если для какой-то причине вы хотите отключить мониторинг на стороне клиента:

* Выберите **параметры** > **параметры приложения**
   * В разделе "Параметры приложения" добавьте новую **имя параметра приложения** и **значение**:

     name. `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Значение: `false`

   * **Сохраните** параметры и **перезапустите** приложение.

![Снимок экрана приложения параметры пользовательского интерфейса](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Автоматизация наблюдения

Чтобы включить сбор данных телеметрии с Application Insights, должны быть заданы только параметры приложения:

   ![Параметры приложения службы приложений с доступными параметрами Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Определения параметров приложения

|Имя параметра приложения |  Определение | Значение |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Основным расширением, управляющий мониторинг среды выполнения. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  По умолчанию режим только, основные возможности для обеспечения оптимальной производительности. | `default` или `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Управляет, если ядро перезаписи двоичный файл `InstrumentationEngine` будет включена. Этот параметр влияет на производительность и влияет на время холодного запуска или запуска. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Элементы управления, если SQL и Azure таблицы текст будет захватываться вместе с вызовами зависимостей. Предупреждение о производительности: этот параметр требует `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Параметры приложений службы приложений с помощью Azure Resource Manager

Параметры приложения для службы приложений можно управлять и настроены [шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Этот метод может использоваться при развертывании новых ресурсов службы приложений с помощью службы автоматизации Azure Resource Manager, или для изменения параметров из существующих ресурсов.

Базовая структура JSON параметров приложения службы приложений приведен ниже:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Пример шаблона Azure Resource Manager с помощью параметров приложения, настроенного для Application Insights это [шаблона](https://github.com/Andrew-MSFT/BasicImageGallery) может быть полезным, в частности раздел будет начинаться на [строка 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Автоматизируйте создание ресурса Application Insights и ссылку на только что созданный службы приложений.

Чтобы создать шаблон диспетчера ресурсов Azure с все настройки, установленные по умолчанию Application Insights, начните процесс, как, если нужно создать новое веб-приложение с помощью Application Insights включена.

Выберите **параметры автоматизации**

   ![Меню создания веб-приложения службы приложений](./media/azure-web-apps/create-web-app.png)

Этот параметр создает последний шаблон Azure Resource Manager с настроены все необходимые параметры.

  ![Шаблон веб-приложения службы приложений](./media/azure-web-apps/arm-template.png)

Ниже приведен пример, замените все вхождения `AppMonitoredSite` с имени веб-сайта:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> Шаблон создаст параметры приложения в режиме «default». Этот режим используется производительности оптимизированных для операций, хотя можно изменить шаблон, чтобы активировать любой функции, вы предпочитаете.

### <a name="enabling-through-powershell"></a>Включить его при помощи PowerShell

Для включения отслеживания с помощью PowerShell только базовые параметры приложения должны быть изменены. Ниже приведен пример, который позволяет мониторинг приложений для веб-сайта, называется «AppMonitoredSite» в группе ресурсов «AppMonitoredRG», и настраивает данные для отправки для ключа инструментирования «012345678-abcd-ef01-2345-6789abcd».

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Обновите агент и расширения мониторинга

### <a name="upgrading-from-versions-289-and-up"></a>Обновление с версий 2.8.9 и выше

Обновление с версии 2.8.9 происходит автоматически, без каких-либо дополнительных действий. Биты новый мониторинга отправляются в фоновом режиме для целевой службы приложений, и при перезапуске приложения они будут учтены.

Проверка версии расширения выполняется посещение `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Снимок экрана: URL-адрес http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Обновление с версии 1.0.0 — 2.6.5

Начиная с версии 2.8.9 используется расширение предварительно установленном сайте. Если вы являетесь более ранней версии, можно обновить с помощью одного из двух способов:

* [Обновления, включив через портал](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Даже если у вас есть расширение Application Insights для установки службы приложений Azure, интерфейс пользователя показывает только **включить** кнопки. На самом деле старым расширением частного сайта будут удалены.)

* [Обновления с помощью PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Задайте параметры приложения, чтобы включить расширение предварительно установленный сайт ApplicationInsightsAgent. См. в разделе [включить его при помощи powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Вручную удалите расширение частного сайта, с именем расширение Application Insights для службы приложений Azure.

Если обновление выполняется с версии до 2.5.1, проверьте, что библиотеки DLL ApplicationInsigths удаляются из папки bin приложения [см. в разделе действия по устранению неполадок](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Устранение неполадок

Ниже приведен нашим пошаговым руководством по устранению неполадок для мониторинга на основе агент и расширения для .NET и .NET Core на основе приложений, работающих в службах приложений Azure.

> [!NOTE]
> Приложений Java и Node.js. поддерживаются только в службах приложений Azure с помощью инструментирование вручную на основе пакета SDK и поэтому не применяются указанные ниже действия для этих сценариев.

1. Убедитесь, что выполняется мониторинг приложения с помощью `ApplicationInsightsAgent`.
    * Убедитесь, что `ApplicationInsightsAgent_EXTENSION_VERSION` приложение установлено в значение «~ 2».
2. Убедитесь, что приложение соответствует требованиям для отслеживания.
    * Перейдите к `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Снимок экрана https://yoursitename.scm.azurewebsites/applicationinsights страницы результатов](./media/azure-web-apps/app-insights-sdk-status.png)

    * Убедитесь, что `Application Insights Extension Status` — `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Если она не запущена, выполните [включите Application Insights, мониторинг инструкции](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Убедитесь, что источник состояния существует и выглядит следующим образом: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Если отсутствует примерно такое же значение, это означает, что приложение не запущен или не поддерживается. Чтобы убедиться, что приложение запущено, попробуйте вручную зайти на конечные точки URL-адрес и приложение приложения, предоставляющими сведения среды выполнения, станут доступны.

    * Убедитесь, что `IKeyExists` — `true`
        * Если нет, добавьте "APPINSIGHTS_INSTRUMENTATIONKEY с помощью идентификатора guid ключа инструментирования для настройки приложения.

    * Убедитесь, что нет записей для `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, и `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Если любая из этих записей существует, удалите следующие пакеты из приложения: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, и `Microsoft.AspNet.TelemetryCorrelation`.

В таблице ниже приведен более подробное описание что означают эти значения, их базовые вызывает, а также рекомендованные способы устранения проблемы:

|Значение проблемы|Пояснение|Исправление
|---- |----|---|
| `AppAlreadyInstrumented:true` | Это значение указывает, что расширение обнаружила, что некоторые аспекты пакета SDK уже присутствует в приложении и будет переключения в пассивный режим. Она может быть вызвано ссылку `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`, или `Microsoft.ApplicationInsights`  | Удалите ссылки. Некоторые из этих ссылок добавляются по умолчанию на основе определенных шаблонов Visual Studio и более ранних версиях Visual Studio может добавлять ссылки на `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Если приложение предназначено для .NET Core 2.1 или 2.2 и ссылается на [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) сам пакет метаданных, затем он переводит в Application Insights и расширение будет переключения в пассивный режим. | Для клиентов .NET Core 2.1,2.2, [рекомендуется](https://github.com/aspnet/Announcements/issues/287) вместо этого использовать Microsoft.AspNetCore.App мета package.|
|`AppAlreadyInstrumented:true` | Это значение также может быть вызвана наличие выше библиотеки DLL в папке приложения из предыдущего развертывания. | Очистите папку приложения, чтобы убедиться, что эти библиотеки будут удалены.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Это значение указывает, что расширение обнаружила ссылки на `Microsoft.AspNet.TelemetryCorrelation` в приложении и будет переключения в пассивный режим. | Удалите ссылку.
|`AppContainsDiagnosticSourceAssembly**:true`|Это значение указывает, что расширение обнаружила ссылки на `System.Diagnostics.DiagnosticSource` в приложении и будет переключения в пассивный режим.| Удалите ссылку.
|`IKeyExists:false`|Это значение указывает, что ключ инструментирования отсутствует в AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY`. Возможные причины: Значения может быть случайно удален, забыли задайте значения в сценарий автоматизации и т. д. | Убедитесь, что параметр присутствует в параметрах приложения службы приложений.

Последние сведения о агент/расширение Application Insights, извлечь [заметки о выпуске](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Дальнейшие действия
* [Запуск профилировщика в живом приложении](../app/profiler.md).
* [Функции Azure.](https://github.com/christopheranderson/azure-functions-app-insights-sample) Отслеживайте функции Azure с помощью Application Insights.
* [Включите отправку данных диагностики Azure](../platform/diagnostics-extension-to-application-insights.md) в Application Insights.
* [Отслеживайте метрики состояния службы](../platform/data-platform.md), чтобы убедиться, что служба доступна и отвечает на запросы.
* [Получайте уведомления](../platform/alerts-overview.md) при возникновении операционных событий или превышении пороговых значений метрик.
* Используйте [расширение Application Insights для приложений JavaScript и веб-страниц](javascript.md), чтобы получать данные телеметрии клиентов из браузеров, которые используются для открытия веб-страницы.
* [Настройте веб-тесты доступности](monitor-web-app-availability.md), чтобы получать уведомления о сбоях в работе сайта.
