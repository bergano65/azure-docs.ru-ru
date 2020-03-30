---
title: Создание ресурса Azure Application Insights | Документация Майкрософт
description: Вручную настройте мониторинг Application Insights для нового работающего приложения.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c1b3a6920723ad59b714cce4bd69e1b95fe1995f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132420"
---
# <a name="create-an-application-insights-resource"></a>Создание ресурса Application Insights

В Azure Application Insights данные о приложении отображаются в *ресурсе* Microsoft Azure. Таким образом, создание ресурса является частью [настройки Application Insights для мониторинга нового приложения][start]. После создания нового ресурса вы можете получить его ключ приборов и использовать его для настройки SDK Application Insights. Ключ приборов связывает телеметрию с ресурсом.

## <a name="sign-in-to-microsoft-azure"></a>Войти на связь в Microsoft Azure

Если у вас нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись перед началом.

## <a name="create-an-application-insights-resource"></a>Создание ресурса Application Insights

Вопийте на [портале Azure](https://portal.azure.com)и создайте ресурс Application Insights:

![Нажмите на знак «К» в левом верхнем углу. Выберите инструменты разработчика, за которыми следуют исследования приложений](./media/create-new-resource/new-app-insights.png)

   | Параметры        |  Значение           | Описание  |
   | ------------- |:-------------|:-----|
   | **Название**      | Уникальное значение | Имя, идентифицирует приложение, которое вы контролируете. |
   | **Ресурсная группа**     | myResourceGroup      | Название новой или существующей группы ресурсов для размещения данных App Insights. |
   | **Расположение** | Восточная часть США | Выберите место рядом с вами или рядом с местом размещения приложения. |

> [!NOTE]
> Хотя одно и то же имя ресурса можно использовать в различных группах ресурсов, может быть полезно использовать глобально уникальное имя. Это может быть полезно, если вы планируете [выполнять перекрестные ресурсные запросы,](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application) поскольку это упрощает требуемый синтаксис.

Введите соответствующие значения в требуемые поля, а затем выберите **Обзор и создайте**.

![Введите значения в требуемые поля, а затем выберите "обзор и создание".](./media/create-new-resource/review-create.png)

Когда приложение создано, открывается новая панель. В этом стеле вы видите данные о производительности и использовании в ашего контролируемом приложении. 

## <a name="copy-the-instrumentation-key"></a>Копирование ключа инструментирования

Ключ приборов определяет ресурс, с которым вы хотите связать данные телеметрии. Вам нужно будет скопировать ключ приборов и добавить его в код приложения.

![Нажмите и скопируйте клавишу приборов](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Установка пакета SDK в приложении

Установите пакет SDK Application Insights в приложении. Выполнение этого шага зависит от типа приложения.

Используйте ключ инструментирования для настройки [пакета SDK, который можно установить в приложении][start].

SDK включает в себя стандартные модули, которые отправляют телеметрию без необходимости писать какой-либо дополнительный код. Для более подробного отслеживания действий пользователей или диагностики неполадок отправляйте собственные данные телеметрии, [используя API][api].

## <a name="creating-a-resource-automatically"></a>Автоматическое создание ресурса

### <a name="powershell"></a>PowerShell

Создание нового ресурса Application Insights

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Пример

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Результаты

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Для получения полной документации PowerShell для этого cmdlet, а также узнать, как получить ключ приборов, обратитесь к [документации Azure PowerShell.](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0)

### <a name="azure-cli-preview"></a>Azure CLI (предварительный просмотр)

Чтобы получить доступ к предварительным командам Application Insights Azure CLI, сначала необходимо выполнить:

```azurecli
 az extension add -n application-insights
```

Если вы не запустите `az extension add` команду, вы увидите сообщение об ошибке, в которое говорится:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Теперь вы можете запустить следующее для создания ресурса Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Пример

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Результаты

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Для получения полной документации Azure CLI для этой команды и узнать, как получить ключ приборов, обратитесь к [документации Azure CLI.](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)

## <a name="next-steps"></a>Дальнейшие действия
* [Диагностический поиск](../../azure-monitor/app/diagnostic-search.md)
* [Изучение метрик](../../azure-monitor/app/metrics-explorer.md)
* [Написание запросов аналитики](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md
