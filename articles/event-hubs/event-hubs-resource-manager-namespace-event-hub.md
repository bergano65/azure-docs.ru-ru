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
ms.openlocfilehash: 48d41ef4df986f959dfabe04e07552e287fdfcd0
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457105"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Создание пространства имен Центров событий с концентратором событий и группой потребителей с помощью шаблона Azure Resource Manager
Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом кратком руководстве вы создадите концентратор событий с помощью шаблона Azure Resource Manager. С помощью шаблона Azure Resource Manager вы создадите пространство имен типа [Центры событий](event-hubs-what-is-event-hubs.md) с одним концентратором событий и одной группой потребителей. Здесь показано, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями. Дополнительные сведения о создании шаблонов см. в статье о [создании шаблонов Azure Resource Manager][Authoring Azure Resource Manager templates].

Полный шаблон приведен в разделе [Event Hub and consumer group template][Event Hub and consumer group template] (Шаблон концентратора событий и группы потребителей) на сайте GitHub.

> [!NOTE]
> Чтобы узнать о новых шаблонах, изучите коллекцию [Шаблоны быстрого запуска Azure][Azure Quickstart Templates] и выполните в ней поиск по запросу "Центры событий".

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим кратким руководством вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную][] запись, прежде чем начать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы используете **Azure PowerShell**, чтобы развернуть шаблон Resource Manager локально, то для работы с этим кратким руководством необходимо запустить последнюю версию PowerShell. Если вам нужно выполнить установку или обновление, см. руководство по [установке и настройке Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

Если вы решили установить и использовать **Azure CLI**, чтобы развернуть шаблон Resource Manager локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.4 или более поздней версии. Выполните команду `az --version`, чтобы узнать номер версии. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="what-will-you-deploy"></a>Что вы развернете?

С помощью этого шаблона вы развернете пространство имен Центров событий с концентратором событий и группой потребителей.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="define-parameters"></a>Определение параметров
С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел `Parameters` , содержащий все значения параметров. Для изменяющихся значений нужно определить параметры с учетом развертываемого проекта либо окружения, где выполняется развертывание. Не определяйте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Ниже описаны параметры, которые определяет шаблон.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Имя создаваемого пространства имен Центров событий.

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Имя концентратора событий, создаваемого в пространстве имен Центров событий.

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

Имя группы потребителей, создаваемой для концентратора событий.

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>версия_API

Версия API шаблона.

```json
"apiVersion": {
"type": "string"
}
```

## <a name="define-resources-to-deploy"></a>Определить ресурсы для развертывания

Создает пространство имен типа **EventHubs** с концентратором событий и группой потребителей.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="azure-powershell"></a>Azure PowerShell
Чтобы развернуть ресурсы с помощью Azure PowerShell, выполните следующие команды.

```azurepowershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure
Чтобы развернуть ресурсы с помощью Azure PowerShell, выполните следующие команды.

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
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
