---
title: Создание пространства имен для обмена сообщениями служебной шины с помощью шаблона Azure Resource Manager | Документация Майкрософт
description: Используйте шаблон Azure Resource Manager для создания пространства имен для обмена сообщениями служебной шины
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: e5c4eca772cf17f04ea10f4d5ae166ea41eaa830
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496927"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Создание пространства имен служебной шины с помощью шаблона диспетчера ресурсов Azure
Из этого краткого руководства вы узнаете, как создать шаблон Azure Resource Manager, создающий пространство имен служебной шины типа **Messaging** с номером SKU уровня **Стандартный**. В этой статье также определяются параметры, которые задаются во время развертывания. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями. Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager][Authoring Azure Resource Manager templates]. Полный шаблон приведен в разделе [Service Bus namespace template][Service Bus namespace template] (Шаблон пространства имен служебной шины) на сайте GitHub.

> [!NOTE]
> Для скачивания и развертывания можно использовать указанные ниже шаблоны диспетчера ресурсов Azure. 
> 
> * [Создание пространства имен служебной шины с очередью](service-bus-resource-manager-namespace-queue.md)
> * [Создание пространства имен служебной шины с разделом и подпиской](service-bus-resource-manager-namespace-topic.md)
> * [Создание пространства имен служебной шины с очередью и правилом авторизации](service-bus-resource-manager-namespace-auth-rule.md)
> * [Создание пространства имен служебной шины с разделом, подпиской и правилом с помощью шаблона Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Чтобы узнать о новых шаблонах, в коллекции [шаблонов быстрого запуска Azure][Azure Quickstart Templates] выполните поиск "Service Bus".

## <a name="quick-deployment"></a>Быстрое развертывание
Чтобы выполнить пример без написания какого-либо JSON или выполнения команды PowerShell или CLI, нажмите показанную ниже кнопку.

[![Развертывание в Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

Чтобы создать и развернуть шаблон вручную, выполните действия, приведенные в разделах этой статьи.

## <a name="prerequisites"></a>Технические условия
Для работы с этим кратким руководством вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.

Если вы намерены использовать **Azure PowerShell** для развертывания шаблона Resource Manager, [установите Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).

Если вы намерены использовать **Azure CLI** для развертывания шаблона Resource Manager, [установите Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Создание JSON-шаблона Resource Manager 
Создайте файл JSON с именем **MyServiceBusNamespace.json** и приведенным ниже содержимым. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

Этот шаблон создает стандартное пространство имен служебной шины. Сведения о синтаксисе и свойствах JSON см. в справочнике по шаблонам о [пространствах имен](/azure/templates/microsoft.servicebus/namespaces).

## <a name="create-the-parameters-json"></a>Создание JSON-кода для параметров
В шаблоне, созданном на предыдущем шаге, есть раздел `Parameters`. Можно определить параметры для тех значений, которые отличаются, в зависимости от разворачиваемого проекта или целевой среды. В этом шаблоне определены следующие параметры: **serviceBusNamespaceName**, **serviceBusSku** и **location**. Для получения дополнительных сведений ознакомьтесь с [номерами SKU служебной шины](https://azure.microsoft.com/pricing/details/service-bus/), которые нужно создать.

Создайте файл JSON с именем **MyServiceBusNamespace-Parameters.json** и приведенным ниже содержимым. 

> [!NOTE] 
> Укажите имя для пространства имен служебной шины. 


```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
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

### <a name="deploy-resources"></a>Развертывание ресурсов
Чтобы развернуть ресурсы с помощью Azure PowerShell, перейдите в папку, где сохранены файлы JSON, и выполните следующие команды.

> [!IMPORTANT]
> Укажите имя группы ресурсов Azure в параметре $resourceGroupName, прежде чем выполнять следующие команды. 

1. Объявите переменную для имени группы ресурсов и укажите ее значение. 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Создание группы ресурсов Azure.

    ```azurepowershell
    New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ```
3. Разверните шаблон Resource Manager. Укажите имена самого развертывания, группы ресурсов, файла JSON для шаблона и файла JSON для параметров.

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>Использование Azure CLI для развертывания шаблона

### <a name="sign-in-to-azure"></a>Вход в Azure

1. Выполните следующую команду, чтобы войти в Azure:

    ```azurecli
    az login
    ```
2. Задайте контекст текущей подписки. Замените `MyAzureSub` идентификатором именем подписки Azure, которую вы хотите использовать:

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>Развертывание ресурсов
Чтобы развернуть ресурсы с помощью Azure CLI, перейдите в папку с файлами JSON и выполните следующие команды.

> [!IMPORTANT]
> Укажите имя для группы ресурсов Azure в команде az group create. .

1. Создание группы ресурсов Azure. 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. Разверните шаблон Resource Manager. Укажите имена группы ресурсов, развертывания, файла JSON для шаблона и файла JSON для параметров.

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы создали пространство имен служебной шины. Ознакомьтесь с другими краткими руководствами, чтобы узнать, как создавать очереди, разделы и подписки и использовать их. 

- [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
- [Начало работы с разделами служебной шины](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: https://azure.microsoft.com/pricing/details/service-bus/
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
