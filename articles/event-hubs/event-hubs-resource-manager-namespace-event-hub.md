---
title: Создание пространства имен и группы потребителей Центров событий Azure с помощью шаблона | Документация Майкрософт
description: Создание пространства имен Центров событий c концентратором событий и группой потребителей с помощью шаблонов Azure Resource Manager.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: 584696303bfbaed07f416fb0b3febbcf59d05b35
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085751"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>Краткое руководство. Создание концентратора событий с помощью шаблона Azure Resource Manager
Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом кратком руководстве вы создадите концентратор событий с помощью шаблона Azure Resource Manager. С помощью шаблона Azure Resource Manager вы создадите пространство имен типа [Центры событий](event-hubs-what-is-event-hubs.md) с одним концентратором событий и одной группой потребителей. Здесь показано, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями. Дополнительные сведения о создании шаблонов см. в статье о [создании шаблонов Azure Resource Manager][Authoring Azure Resource Manager templates].


> [!NOTE]
> Полный шаблон приведен в разделе [Event Hub and consumer group template][Event Hub and consumer group template] (Шаблон концентратора событий и группы потребителей) на сайте GitHub. Этот шаблон создал группу потребителей, а также пространство имен концентратора событий и сам концентратор событий. Чтобы узнать о новых шаблонах, изучите коллекцию [Шаблоны быстрого запуска Azure][Azure Quickstart Templates] и выполните в ней поиск по запросу "Центры событий".

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим кратким руководством вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.

Если вы намерены использовать **Azure PowerShell** для развертывания шаблона Resource Manager, [установите Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

Если вы намерены использовать **Azure CLI** для развертывания шаблона Resource Manager, [установите Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Создание JSON-шаблона Resource Manager
Создайте JSON-файл с именем MyEventHub.json и следующим содержимым, затем сохраните его в любую папку (например, C:\EventHubsQuickstarts\ResourceManagerTemplate).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>Создание JSON-кода для параметров
Создайте JSON-файл с именем ADFTutorialARM Parameters.json, который содержит параметры для шаблона Azure Resource Manager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
        }
  }
}
```



## <a name="use-azure-powershell-to-deploy-the-template"></a>Использование Azure PowerShell для развертывания шаблона

### <a name="sign-in-to-azure"></a>Вход в Azure
1. Запуск Azure PowerShell

2. Выполните следующую команду, чтобы войти в Azure:

   ```azurepowershell
   Login-AzureRmAccount
   ```
3. Введите следующие команды, чтобы указать текущий контекст подписки, если она у вас есть:

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="provision-resources"></a>Подготовка ресурсов
Чтобы развернуть и (или) подготовить ресурсы с помощью Azure PowerShell, перейдите в папку C:\EventHubsQuickStart\ARM\ и выполните следующие команды:

> [!IMPORTANT]
> Укажите имя группы ресурсов Azure в параметре $resourceGroupName, прежде чем выполнять следующие команды. 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzureRmResourceGroup $resourceGroupName -location 'East US'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>Использование Azure CLI для развертывания шаблона

## <a name="sign-in-to-azure"></a>Вход в Azure

Следующие действия не требуются, если вы выполняете команды в Cloud Shell. Если вы используете CLI локально, выполните следующие действия, чтобы войти в учетную запись Azure и выбрать текущую подписку:

Выполните следующую команду, чтобы войти в Azure:

```azurecli
az login
```

Задайте контекст текущей подписки. Замените `MyAzureSub` идентификатором именем подписки Azure, которую вы хотите использовать:

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>Подготовка ресурсов
Чтобы развернуть ресурсы с помощью Azure CLI, перейдите в папку C:\EventHubsQuickStart\ARM\ и выполните следующие команды:

> [!IMPORTANT]
> Укажите имя для группы ресурсов Azure в команде az group create. .

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location eastus

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

Поздравляем! С помощью шаблона Resource Manager вы создали в этом пространстве имен концентратор событий и пространство имен Центров событий.

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы создали пространство имен Центров событий и использовали примеры приложений для отправки событий в созданный концентратор и получения событий из него. Пошаговые инструкции по отправке событий в концентратор и получении событий из него см. в следующих руководствах: 

- **Отправка событий в концентратор**: [Приступая к отправке событий в Центры событий Azure на платформе .NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [Отправка событий в концентраторы событий Azure с помощью платформы .NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Отправка событий в Центры событий Azure с помощью Java](event-hubs-java-get-started-send.md), [Отправка событий в Центры событий с помощью Python](event-hubs-python-get-started-send.md), [Отправка событий в Центры событий Azure с помощью Node.js](event-hubs-node-get-started-send.md), [Отправка событий в Центры событий с помощью Go](event-hubs-go-get-started-send.md), [Отправка событий в Центры событий Azure с помощью C](event-hubs-c-getstarted-send.md)
- **Получение событий из концентратора**: [Основные сведения о получении сообщений с помощью узла EventProcessorHost в .NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [Получение событий от Центров событий Azure с помощью платформы .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Получение событий от Центров событий Azure с помощью Java](event-hubs-java-get-started-receive-eph.md), [Получение событий из концентраторов событий с помощью Python](event-hubs-python-get-started-receive.md), [Получение событий от Центров событий Azure с помощью Node.js](event-hubs-node-get-started-receive.md), [Получение событий из концентраторов событий с помощью Go](event-hubs-go-get-started-receive-eph.md), [Получение событий из Центров событий с помощью Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
