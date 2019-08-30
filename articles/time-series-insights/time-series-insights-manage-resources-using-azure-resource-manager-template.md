---
title: Как управлять средой "Аналитика временных рядов Azure" с помощью шаблонов Azure Resource Manager | Документация Майкрософт
description: В этой статье описано, как программно управлять средой "Аналитика временных рядов Azure" с помощью Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: d3e22df76124185c1e23f04f59145e12a1fec023
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164259"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Создание ресурсов службы "Аналитика временных рядов" с помощью шаблонов Resource Manager

В этой статье описывается, как создать и развернуть ресурсы службы "Аналитика временных рядов" с помощью шаблонов Azure Resource Manager, PowerShell и поставщика ресурсов аналитики временных рядов.

"Аналитика временных рядов" поддерживает такие ресурсы:

   | Resource | Описание |
   | --- | --- |
   | Среда | Среда "аналитика временных рядов" — это логическая группа событий, которые считываются из брокеров событий, хранятся и становятся доступными для запроса. Дополнительные сведения см. в статье [Планирование среды службы "аналитика временных рядов Azure](time-series-insights-environment-planning.md) ". |
   | Источник события | Источник событий — это подключение к брокеру событий, из которого среда "Аналитика временных рядов" читает и принимает события. Источники событий, поддерживаемые в настоящее время: Центр Интернета вещей и концентратор событий. |
   | Набор эталонных данных | Наборы эталонных данных предоставляют метаданные о событиях в среде. Метаданные в наборах эталонных данных во время приема будут соединены с событиями. Наборы эталонных данных определяются своими свойствами ключей событий как ресурсы. Фактические метаданные, составляющие набор эталонных данных, отправляются или изменяются через API плоскости данных. |
   | Политика доступа | Политики доступа предоставляют разрешения для отправки запросов данных, обработки ссылочных данных в среде, а также предоставляют другим пользователям среды доступ к сохраненным запросам и перспективам. Дополнительные сведения см. в статье [предоставление доступа к данным в среде "аналитика временных рядов" с помощью портал Azure](time-series-insights-data-access.md) |

Шаблоны Resource Manager являются файлами в формате JSON, определяющими инфраструктуру и конфигурацию ресурсов в группе ресурсов. В следующих документах более подробно описаны файлы шаблонов:

- [Развертывание шаблона Azure Resource Manager](../azure-resource-manager/template-deployment-overview.md)
- [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Microsoft.TimeSeriesInsights resource types](/azure/templates/microsoft.timeseriesinsights/allversions) (Типы ресурсов Microsoft.TimeSeriesInsights)

Шаблон быстрого запуска [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) опубликован на GitHub. Этот шаблон создает среду "Аналитика временных рядов", дочерний источник событий, который должен использовать события из концентратора событий, и политики доступа, которые предоставляют доступ к данным среды. Если не указан имеющийся концентратор событий, он будет создан при развертывании.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Укажите шаблон развертывания и параметры

Следующая процедура описывает использование PowerShell для развертывания шаблона Azure Resource Manager, который создает среду "Аналитика временных рядов", дочерний источник событий, который должен использовать события из концентратора событий и политики доступа, которые предоставляют доступ к данным среды. Если не указан имеющийся концентратор событий, он будет создан при развертывании.

1. Установите Azure PowerShell, следуя указаниям в разделе [Getting started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) (Приступая к работе с Azure PowerShell).

1. Клонируйте или скопируйте шаблон [201-timeseriesinsights-environment-with-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) из GitHub.

   * Создание файла параметров

     Чтобы создать файл параметров, скопируйте файл [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json).

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Необходимые параметры

     | Параметр | Описание |
     | --- | --- |
     | eventHubNamespaceName | Пространство имен концентратора событий источника. |
     | имяконцентраторасобытий | Имя концентратора событий источника. |
     | consumerGroupName | Имя группы потребителей, которую будет использовать служба "Аналитика временных рядов" для чтения данных из концентратора событий. **Примечание.** Во избежание конфликта ресурсов группа потребителей должна быть выделена для службы "Аналитика временных рядов" и не предоставлять доступ другим читателям. |
     | EnvironmentName | Имя среды. Имя не может включать: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, илюбыеуправляющиесимволы.`/` Все остальные символы разрешены.|
     | eventSourceName | Имя дочернего ресурса источника событий. Имя не может включать: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, илюбыеуправляющиесимволы.`/` Все остальные символы разрешены. |

    <div id="optional-parameters"></div>

   * Необязательные параметры

     | Параметр | Описание |
     | --- | --- |
     | existingEventHubResourceId | Дополнительный идентификатор ресурса имеющегося концентратора событий будет подключен к среде "Аналитика временных рядов" через источник событий. **Примечание.** Пользователь, развертывающий шаблоны, должен иметь привилегии, чтобы выполнять операцию listkeys в концентраторе событий. Если значение не передается, новый концентратор событий будет создан с помощью шаблона. |
     | environmentDisplayName | Дополнительное понятное имя среды, отображаемое в инструменте или пользовательских интерфейсах вместо имени среды. |
     | environmentSkuName | Имя номера SKU. Дополнительные сведения см. на странице [цен на службу "Аналитика временных рядов"](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | Емкость единиц номера SKU. Дополнительные сведения см. на странице [цен на службу "Аналитика временных рядов"](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | Минимальный период, в течение которого события среды будут доступны для запроса. Значение должно быть указано в формате ISO 8601, `P30D` например для политики хранения в течение 30 дней. |
     | eventSourceDisplayName | Дополнительное понятное имя среды, отображаемое в инструменте или пользовательских интерфейсах вместо имени источника события. |
     | eventSourceTimestampPropertyName | Свойство события, которое будет использоваться в качестве метки времени источника события. Если для timestampPropertyName значение не указано, задано значение NULL или указана пустая строка, будет использоваться время создания события. |
     | eventSourceKeyName | Имя общего ключа доступа, которое будет использовать служба "Аналитика временных рядов" для подключения к концентратору событий. |
     | accessPolicyReaderObjectIds | Список идентификаторов объектов пользователей или приложений в Azure AD, которые должны иметь доступ на чтение к среде. ObjectId субъекта-службы можно получить, вызвав командлет **Get азадусер** или **Get-азадсервицепринЦипал** . Создание политики доступа для группы Azure AD еще не поддерживается. |
     | accessPolicyContributorObjectIds | Список идентификаторов объектов пользователей или приложений в Azure AD, которые должны иметь доступ участника к среде. ObjectId субъекта-службы можно получить, вызвав командлет **Get азадусер** или **Get-азадсервицепринЦипал** . Создание политики доступа для группы Azure AD еще не поддерживается. |

   * Например, следующий файл параметров будет использоваться для создания среды и источника событий, который считывает события из имеющегося концентратора событий. Он также создает две политики доступа, которые предоставляют доступ участника к среде.

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }    
         }
     }
     ```
  
    * Дополнительные сведения см. в разделе [Параметры](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Развертывание шаблона быстрого запуска локально с помощью PowerShell

> [!IMPORTANT]
> Операции командной строки, показанные ниже, описывают [модуль AZ PowerShell](https://docs.microsoft.com/powershell/azure/overview).

1. В PowerShell войдите в свою учетную запись Azure.

    * В командной строке PowerShell выполните следующую команду:

      ```powershell
      Connect-AzAccount
      ```

    * Вам будет предложено войти в учетную запись Azure. Войдя в систему, выполните следующую команду, чтобы просмотреть доступные подписки:

      ```powershell
      Get-AzSubscription
      ```

    * Эта команда возвращает список доступных подписок Azure. Выберите подписку для текущего сеанса, выполнив приведенную ниже команду. Замените `<YourSubscriptionId>` идентификатором GUID подписки Azure, которую вы хотите использовать:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Создайте группу ресурсов, если ее еще нет.

   * Если у вас нет группы ресурсов, создайте новую группу ресурсов с помощью команды **New-азресаурцеграуп** . Введите имя группы ресурсов и нужное расположение. Пример:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * После успешного выполнения операции появится сводка по новой группе ресурсов.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Протестируйте развертывание.

   * Проверьте развертывание, выполнив командлет `Test-AzResourceGroupDeployment`. При тестировании развернутой службы укажите точно такие же параметры, как и при ее выполнении.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Создание развертывания

    * Чтобы создать развертывание, выполните командлет `New-AzResourceGroupDeployment` и укажите необходимые параметры при появлении запроса. Параметры включают в себя имя развертывания, имя группы ресурсов и путь к файлу шаблона или его URL-адрес. Если параметр **Режим** не указан, используется стандартное значение **Добавочный**. Дополнительные сведения см. в статье [Добавочные и полные развертывания](../azure-resource-manager/deployment-modes.md).

    * Следующая команда запрашивает пять обязательных параметров в окне PowerShell:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
      ```

    * Чтобы использовать вместо этого файл параметров, выполните приведенную ниже команду:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * При выполнении командлета развертывания также можно использовать встроенные параметры. Команда выглядит следующим образом:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Чтобы выполнить [полное](../azure-resource-manager/deployment-modes.md) развертывание, установите для параметра **Режим** значение **Полный**.

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Проверка развертывания

    * В случае успешного развертывания ресурсов в окне PowerShell будет приведена сводка по развертыванию.

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 5/8/2019 10:28:34 PM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Развертывание шаблона быстрого запуска через портал Azure

   * Домашняя страница шаблона быстрого запуска на GitHub также содержит кнопку **Развертывание в Azure**. При нажатии на нее открывается страница "Настраиваемое развертывание" на портале Azure. На этой странице можно ввести или выбрать значения для каждого параметра из таблиц [обязательных](#required-parameters) или [необязательных параметров](#optional-parameters). Если нажать кнопку **Приобрести** после заполнения параметров, начнется развертывание шаблона.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Следующие шаги

- Сведения о программном управлении ресурсами службы "Аналитика временных рядов" с помощью REST API см. в статье [Time Series Insights Management](https://docs.microsoft.com/rest/api/time-series-insights-management/) (Управление службой "Аналитика временных рядов").
