---
title: Краткое руководство. Создание концентратора событий с группой потребителей в Центрах событий Azure
description: Краткое руководство. Создание пространства имен Центров событий c концентратором событий и группой потребителей с помощью шаблонов Azure Resource Manager.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: b262f5f4122256a55c005002c4323205a5b564f0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717660"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Краткое руководство. Создание концентратора событий с помощью шаблона Azure Resource Manager

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом кратком руководстве описано, как создать концентратор событий с помощью [шаблона Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). С помощью шаблона Azure Resource Manager вы создадите пространство имен типа [Центры событий](event-hubs-what-is-event-hubs.md) с одним концентратором событий. Здесь показано, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями. См. сведения о [создании шаблонов Azure Resource Manager][Authoring Azure Resource Manager templates]. Синтаксис и свойства JSON, используемые в шаблоне, см. в статье о [типах ресурсов Microsoft.EventHub](/azure/templates/microsoft.eventhub/allversions).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="create-an-event-hub"></a>Создание концентратора событий

В этом кратком руководстве используется существующий [шаблон быстрого запуска](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json):

[!code-json[create-azure-event-hub-namespace](~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json)]

См. [примеры шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

Чтобы развернуть шаблон, выполните следующую команду:

1. Выберите **Попробовать** в следующем блоке кода и следуйте инструкциям, чтобы войти в Azure Cloud Shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Создание концентратора событий занимает несколько секунд.

1. Нажмите кнопку **Копировать**, чтобы скопировать сценарий PowerShell.
1. Щелкните правой кнопкой в консоли оболочки и выберите **Вставить**.

## <a name="verify-the-deployment"></a>Проверка развертывания

Чтобы проверить развертывание, можно открыть группу ресурсов на [портале Azure](https://portal.azure.com) или использовать следующий скрипт Azure PowerShell.  Если вы не закрывали Cloud Shell, вам не нужно копировать или выполнять первую строку (для чтения и размещения).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов. Если вы не закрывали Cloud Shell, вам не нужно копировать или выполнять первую строку (для чтения и размещения).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Дополнительная информация

В этой статье описано, как создать группу ресурсов, пространство имен Центров событий и концентратор событий в пространстве имен. Пошаговые инструкции по отправке событий в концентратор и получении событий из него см. в следующих руководствах по **отправке и получению событий**:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [GO](event-hubs-go-get-started-send.md)
- [C (только отправка)](event-hubs-c-getstarted-send.md)
- [Apache Storm (только получение)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
