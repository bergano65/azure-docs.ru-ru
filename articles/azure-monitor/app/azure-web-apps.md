---
title: Мониторинг производительности служб приложений Azure | Документация Майкрософт
description: Мониторинг производительности приложений для служб приложений Azure. Диаграмма нагрузки и времени отклика, информация о зависимости и набор оповещений о производительности.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: d2134e059a446c18108e8dd16bcc74504b42b15a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437206"
---
# <a name="monitor-azure-app-service-performance"></a>Мониторинг производительности Службы приложений Azure

Включение мониторинга ваших веб-приложений на основе ASP.NET и ASP.NET Core, работающих в [службах приложений Azure,](https://docs.microsoft.com/azure/app-service/) теперь стало проще, чем когда-либо. Если раньше вам нужно было вручную установить расширение сайта, то последнее расширение/агент теперь встроено в изображение службы приложения по умолчанию. В этой статье вы сможете проследить за приложениями, а также предоставитпредварительные рекомендации по автоматизации процесса крупномасштабных развертываний.

> [!NOTE]
> Ручнодобавляя расширение сайта Application Insights через расширения **инструментов** > **разработки** унижается. Этот метод установки расширения зависел от ручных обновлений для каждой новой версии. Последний стабильный выпуск расширения теперь [предустановлен](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) как часть изображения Службы Приложения. Файлы находятся `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` в и автоматически обновляются с каждым стабильным релизом. Если вы будете следовать инструкциям на основе агента, чтобы включить мониторинг ниже, он автоматически удалит удаляемый расширение для вас.

## <a name="enable-application-insights"></a>Включение Application Insights

Существует два способа обеспечения мониторинга приложений для размещенных приложений Azure App Services:

* **Мониторинг приложений на основе агента** (ApplicationInsightsAgent).  
    * Этот метод является самым простым для включения, и не требуется продвинутая конфигурация. Его часто называют мониторингом "времени выполнения". Для служб приложений Azure мы рекомендуем как минимум включить этот уровень мониторинга, а затем на основе конкретного сценария можно оценить, нужен ли более продвинутый мониторинг с помощью ручных приборов.

* **Ручное нанесение приложения через код,** установив SDK Application Insights.

    * Этот подход гораздо более настраиваемый, но он требует [добавления зависимости от пакетов Application Insights SDK NuGet.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) Этот метод также означает, что вы должны управлять обновлениями последней версии пакетов самостоятельно.

    * Если вам нужно сделать пользовательские вызовы API для отслеживания событий/зависимостей, не захваченных по умолчанию, с помощью мониторинга на основе агента, необходимо использовать этот метод. Ознакомьтесь с [API для пользовательских событий и метрик статьи,](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) чтобы узнать больше. Это также в настоящее время является единственным поддерживаемым вариантом для рабочих нагрузок на основе Linux.

> [!NOTE]
> При обнаружении как агента на основе мониторинга, так и ручного приборного аппарата на основе SDK будут выполнены только настройки ручного прибора. Это необходимо для предотвращения отправки дубликатов данных. Чтобы узнать больше об этом, ознакомьтесь с [разделом устранения неполадок](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) ниже.

## <a name="enable-agent-based-monitoring"></a>Включить мониторинг на основе агента

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Сочетание APPINSIGHTS_JAVASCRIPT_ENABLED и urlCompression не поддерживается. Для получения дополнительной информации смотрите объяснение в [разделе устранения неполадок](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. На панели управления Azure **выберите Application Insights** для своей службы приложений.

    ![В разделе "Параметры" выберите пункт Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Нажмите переключатель "Создать новый ресурс", если вы еще не настроили ресурс Application Insights для этого приложения. 

     > [!NOTE]
     > Нажав кнопку **OK** для создания нового ресурса, вам будет предложено выбрать параметр **Применение параметров мониторинга**. Нажав кнопку **Продолжить**, вы свяжете новый ресурс Application Insights со службой приложений, а также **активируете перезапуск самой службы приложений**. 

     ![Инструментирование веб-приложения](./media/azure-web-apps/create-resource-01.png)

2. Указав ресурс для использования, вы можете выбрать способ сбора данных в Application Insights для каждой платформы для приложения. ASP.NET мониторинг приложения по умолчанию с двумя различными уровнями сбора.

    ![Выбор параметров для каждой платформы](./media/azure-web-apps/choose-options-new.png)

   * Уровень **коллекции** .NET Basic предлагает основные возможности APM с одним экземпляром.

   * Уровень **рекомендуемой коллекции** .NET:
       * Добавляет тенденции использования ЦП, памяти и операций ввода-вывода.
       * Сопоставляет микрослужбы в пределах границ запросов или зависимостей.
       * Собирает тенденции использования и обеспечивает сопоставление от результатов доступности до транзакций.
       * Собирает исключения, необработанные хост-процессом.
       * Улучшает точность метрик APM под нагрузкой при использовании выборки.

3. Для настройки таких параметров, как выборка, которую вы могли бы ранее контролировать с помощью файла applicationinsights.config, теперь можно взаимодействовать с теми же настройками через настройки приложения с соответствующей префиксом. 

    * Например, чтобы изменить исходный процент выборки, `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` можно создать `100`настройку приложения: и значение .

    * Для списка поддерживаемых адаптивных настроек телеметрии выборки, вы можете проконсультироваться с [кодом](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) и [связанной документации.](https://docs.microsoft.com/azure/azure-monitor/app/sampling)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Поддерживаются следующие версии .NET Core: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2, ASP.NET Core 3.0

Ориентация на полную платформу от .NET Core, автономного развертывания и приложений на основе Linux в настоящее время **не поддерживается** мониторингом на основе агента/расширения. ([Ручные приборы](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) с помощью кода будут работать во всех предыдущих сценариях.)

1. На панели управления Azure **выберите Application Insights** для своей службы приложений.

    ![В разделе "Параметры" выберите пункт Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Нажмите переключатель "Создать новый ресурс", если вы еще не настроили ресурс Application Insights для этого приложения. 

     > [!NOTE]
     > Нажав кнопку **OK** для создания нового ресурса, вам будет предложено выбрать параметр **Применение параметров мониторинга**. Нажав кнопку **Продолжить**, вы свяжете новый ресурс Application Insights со службой приложений, а также **активируете перезапуск самой службы приложений**. 

     ![Инструментирование веб-приложения](./media/azure-web-apps/create-resource-01.png)

2. Указав, какой ресурс использовать, вы можете выбрать, как вы хотите, чтобы Application Insights собирал данные на платформе для вашего приложения. .NET Core предлагает **Рекомендуемую коллекцию** или **отключенную** для .NET Core 2.0, 2.1, 2.2 и 3.0.

    ![Выбор параметров для каждой платформы](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Из веб-приложения Службы Приложения под **настройками** > **выберите Application Insights** > **Enable.** Мониторинг на основе агента Node.js в настоящее время находится в предварительном просмотре.

# <a name="java"></a>[Java](#tab/java)

Веб-приложения на базе Java App Service в настоящее время не поддерживают автоматический мониторинг на основе агента/расширения. Для обеспечения мониторинга java-приложения необходимо [вручную сделать инструмент новое приложение.](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started)

# <a name="python"></a>[Python](#tab/python)

Веб-приложения Python App Service в настоящее время не поддерживают автоматический мониторинг на основе агента/расширения. Для обеспечения мониторинга для приложения Python необходимо [вручную сделать инструмент.](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

---

## <a name="enable-client-side-monitoring"></a>Включение наблюдения на стороне клиента

# <a name="net"></a>[.NET](#tab/net)

Мониторинг на стороне клиента является отказом в ASP.NET. Для обеспечения мониторинга на стороне клиента:

* Выберите **настройки** >» » » Настройки приложения»
   * В настройках приложения добавьте новое **имя настройки приложения** и **значение:**

     Имя: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Значение: `true`

   * **Сохраните** параметры и **перезапустите** приложение.

![Скриншот uI настроек приложений](./media/azure-web-apps/appinsights-javascript-enabled.png)

Чтобы отключить мониторинг на стороне клиента, либо удалите связанную пару значений ключа из настроек приложения, либо установите значение к ложному.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Мониторинг на стороне клиента **по умолчанию включен** для приложений .NET Core с **Рекомендуемой коллекцией,** независимо от наличия настройки приложения «APPINSIGHTS_JAVASCRIPT_ENABLED».

Если по какой-то причине вы хотите отключить мониторинг на стороне клиента:

* Выберите **настройки** > **настроек приложения**
   * В настройках приложения добавьте новое **имя настройки приложения** и **значение:**

     Имя:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Значение: `false`

   * **Сохраните** параметры и **перезапустите** приложение.

![Скриншот uI настроек приложений](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Для обеспечения мониторинга клиентской стороны для приложения Node.js необходимо [вручную добавить в приложение сторону клиента JavaScript SDK.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

# <a name="java"></a>[Java](#tab/java)

Для обеспечения мониторинга клиентской стороны для вашего Java-приложения необходимо [вручную добавить в приложение JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript)стороны клиента.

# <a name="python"></a>[Python](#tab/python)

Для обеспечения мониторинга клиентской стороны для приложения Python необходимо [вручную добавить в приложение сторону клиента JavaScript SDK.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

---

## <a name="automate-monitoring"></a>Автоматизация мониторинга

Для включения телеметрии с помощью Application Insights необходимо установить только настройки приложения:

   ![Настройки приложения службы приложений с доступными настройками Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Определения параметров приложения

|Имя параметра приложения |  Определение | Значение |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Основное расширение, которое контролирует мониторинг времени выполнения. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Только в режиме по умолчанию для обеспечения оптимальной производительности включены важные функции. | `default` или `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Управление, если бинарный `InstrumentationEngine` перезапись двигателя будет включен. Эта настройка имеет последствия для производительности и влияет на время запуска/запуска. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Элементы управления, если текст таблицы & Azure будет захвачен вместе с вызовами зависимости. Предупреждение о производительности: время запуска приложения холодное будет затронуто. Эта настройка `InstrumentationEngine`требует . | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Настройки приложения для приложений с менеджером ресурсов Azure

Настройки приложений для служб приложений можно управлять и настраивать с [помощью шаблонов Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) Этот метод можно использовать при развертывании новых ресурсов службы приложений с автоматизацией управления ресурсами Azure или для изменения настроек существующих ресурсов.

Базовая структура настроек приложения JSON для службы приложений приведена ниже:

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

Для примера шаблона менеджера ресурсов Azure с настройками приложений, настроенными для Application Insights, этот [шаблон](https://github.com/Andrew-MSFT/BasicImageGallery) может быть полезен, в частности, раздел, начинающееся на [строке 238.](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Автоматизировать создание ресурса Application Insights и ссылку на недавно созданную службу приложений.

Чтобы создать шаблон менеджера ресурсов Azure со всеми настройками Application Insights по умолчанию, начните процесс так, как если бы вы собирались создать новое Web-приложение с включенным application Insights.

Выберите **варианты автоматизации**

   ![Меню создания веб-приложений Службы Приложения](./media/azure-web-apps/create-web-app.png)

Эта опция генерирует новейший шаблон Управления ресурсами Azure со всеми необходимыми настройками.

  ![Шаблон веб-приложения Службы App Service](./media/azure-web-apps/arm-template.png)

Ниже приведен пример, заменить `AppMonitoredSite` все экземпляры с вашим названием сайта:

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
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
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
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>Включение через PowerShell

Для того, чтобы обеспечить мониторинг приложения через PowerShell, необходимо изменить только базовые настройки приложения. Ниже приведен пример, который позволяет мониторинг приложений для веб-сайта под названием "AppMonitoredSite" в ресурсной группе "AppMonitoredRG", и настраивает данные, которые будут отправлены на "012345678-abcd-ef01-2345-6789abcd" инструментation ключ.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Расширение мониторинга обновления/агент

### <a name="upgrading-from-versions-289-and-up"></a>Обновление из версий 2.8.9 и вверх

Обновление с версии 2.8.9 происходит автоматически, без каких-либо дополнительных действий. Новые биты мониторинга поставляются в фоновом режиме для целевого сервиса приложений, и при перезагрузке приложения они будут подобраны.

Чтобы проверить, какую версию расширения вы работаете посетить`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Скриншот url-путиhttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Обновление от версий 1.0.0 - 2.6.5

Начиная с версии 2.8.9 используется предустановленное расширение сайта. Если вы более ранняя версия, вы можете обновить одним из двух способов:

* [Обновление, включив через портал](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Даже если у вас установлено расширение Application Insights для службы приложений Azure, uI отображает только кнопку **Enable.** За кулисами, старый частный сайт расширение будет удален.)

* [Обновление через PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Установите настройки приложения, чтобы включить предустановленное расширение сайта ApplicationInsightsAgent. Смотрите [Включение через powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Вручную удалите расширение частного сайта под названием Application Insights для службы приложений Azure.

Если обновление выполнено из версии до 2.5.1, убедитесь, что dlls ApplicationInsigths удаляются из папки ячейки приложения, [увидеть шаги устранения неполадок.](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)

## <a name="troubleshooting"></a>Устранение неполадок

Ниже приведено наше пошаговоц-руководство по устранению неполадок для мониторинга на основе расширения/агента для приложений на основе .NET и .NET Core, работающих в службах приложений Azure.

> [!NOTE]
> Java-приложения поддерживаются только в службах приложений Azure с помощью ручных приборов на основе SDK, и поэтому приведенные ниже шаги не применяются к этим сценариям.

1. Убедитесь, что приложение `ApplicationInsightsAgent`контролируется через .
    * Проверьте, что `ApplicationInsightsAgent_EXTENSION_VERSION` настройка приложения установлена на значение "No2".
2. Убедитесь, что приложение отвечает требованиям, которые должны контролироваться.
    * Перейдите по адресу `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.

    ![Скриншот страницы результатов https://yoursitename.scm.azurewebsites/applicationinsights](./media/azure-web-apps/app-insights-sdk-status.png)

    * Подтвердите, что `Application Insights Extension Status``Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Если он не работает, следуйте [инструкциям мониторинга application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Подтвердите, что источник статуса существует и выглядит следующим:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Если аналогичное значение не присутствует, это означает, что приложение в настоящее время не работает или не поддерживается. Чтобы убедиться, что приложение работает, попробуйте вручную посетить url-адреса приложения/приложения, что позволит получить информацию о времени выполнения.

    * Подтвердите, что `IKeyExists``true`
        * Если это `false`так, добавляйте `APPINSIGHTS_INSTRUMENTATIONKEY` и `APPLICATIONINSIGHTS_CONNECTION_STRING` с помощью ikey guid в настройках приложения.

    * Подтвердите, что `AppAlreadyInstrumented`нет `AppContainsDiagnosticSourceAssembly`записей для , и `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Если какая-либо из этих записей существует, удалите следующие пакеты из приложения: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`и `Microsoft.AspNet.TelemetryCorrelation`.

В приведенной ниже таблице приводится более подробное объяснение того, что означают эти значения, их основные причины и рекомендуемые исправления:

|Значение проблемы|Объяснение|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | Это значение указывает на то, что расширение обнаружило, что некоторые аспекты SDK уже присутствуют в Приложении, и будет отступить. Это может быть связано `System.Diagnostics.DiagnosticSource` `Microsoft.AspNet.TelemetryCorrelation`со ссылкой на, или`Microsoft.ApplicationInsights`  | Удалите ссылки. Некоторые из этих ссылок добавляются по умолчанию из некоторых шаблонов Visual `Microsoft.ApplicationInsights`Studio, а старые версии Visual Studio могут добавлять ссылки на .
|`AppAlreadyInstrumented:true` | Если приложение ориентировано на .NET Core 2.1 или 2.2 и относится к [Пакету Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-package, то оно приносит в Application Insights, и расширение будет отыскать. | Клиентам на .NET Core 2.1,2.2 [рекомендуется](https://github.com/aspnet/Announcements/issues/287) использовать мета-пакет Microsoft.AspNetCore.App.|
|`AppAlreadyInstrumented:true` | Это значение также может быть вызвано наличием вышеуказанных dlls в папке приложения от предыдущего развертывания. | Очистите папку приложения, чтобы убедиться, что эти dlls удаляются. Проверьте как каталог ячеек локального приложения, так и каталог wwwroot в Службе приложений. (Чтобы проверить каталог wwwroot вашего веб-приложения Службы App: Advanced Tools (Kudu) > консоль Debug > CMD > home'site.wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Это значение указывает на то, `Microsoft.AspNet.TelemetryCorrelation` что расширение обнаружило ссылки в приложении и будет отстойным. | Удаление ссылки.
|`AppContainsDiagnosticSourceAssembly**:true`|Это значение указывает на то, `System.Diagnostics.DiagnosticSource` что расширение обнаружило ссылки в приложении и будет отстойным.| Удаление ссылки.
|`IKeyExists:false`|Это значение указывает на то, что ключ приборов не присутствует в AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Возможные причины: значения, возможно, были случайно удалены, забыли установить значения в сценарии автоматизации и т.д. | Убедитесь, что настройка присутствует в настройках приложения Службы приложения App.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED и urlCompression не поддерживается

Если вы используете APPINSIGHTS_JAVASCRIPT_ENABLED в тех случаях, когда содержимое кодируется, вы можете получить ошибки, такие как: 

- Ошибка переписывания URL 500 URL
- 500.53 ОШИБКА модуля URL-модуля с правилами перезаписи сообщений не может быть применена при кодировании содержимого ответа HTTP ('gzip'). 

Это связано с тем, что APPINSIGHTS_JAVASCRIPT_ENABLED настройки приложения устанавливаются на истинное и кодирование контента, присутствующие в то же время. Этот сценарий пока не поддерживается. Решение заключается в удалении APPINSIGHTS_JAVASCRIPT_ENABLED из настроек приложения. К сожалению, это означает, что если клиент / браузер стороне JavaScript приборов по-прежнему требуется, ручные ссылки SDK необходимы для ваших веб-страниц. Пожалуйста, следуйте [инструкциям](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) по ручному приборам с помощью JavaScript SDK.

Для получения последней информации об агенте /расширении Application Insights, ознакомьтесь с [примечаниями к выпуску.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)

### <a name="php-and-wordpress-are-not-supported"></a>PHP и WordPress не поддерживаются

Сайты PHP и WordPress не поддерживаются. В настоящее время официально не поддерживается SDK/агент для мониторинга этих рабочих нагрузок на серверной стороне. Тем не менее, вручную инструментирование клиент-стороны сделок на сайте PHP или WordPress, добавив клиента стороне JavaScript на веб-страницы могут быть выполнены с помощью [JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript). 

## <a name="next-steps"></a>Следующие шаги
* [Запуск профилировщика в живом приложении](../app/profiler.md).
* [Функции Azure.](https://github.com/christopheranderson/azure-functions-app-insights-sample) Отслеживайте функции Azure с помощью Application Insights.
* [Включите отправку данных диагностики Azure](../platform/diagnostics-extension-to-application-insights.md) в Application Insights.
* [Отслеживайте метрики состояния службы](../platform/data-platform.md), чтобы убедиться, что служба доступна и отвечает на запросы.
* [Получайте уведомления](../platform/alerts-overview.md) при возникновении операционных событий или превышении пороговых значений метрик.
* Используйте [расширение Application Insights для приложений JavaScript и веб-страниц](javascript.md), чтобы получать данные телеметрии клиентов из браузеров, которые используются для открытия веб-страницы.
* [Настройте веб-тесты доступности](monitor-web-app-availability.md), чтобы получать уведомления о сбоях в работе сайта.
