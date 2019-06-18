---
title: Создание концентратора событий с группой потребителей — Центры событий Azure | Документация Майкрософт
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
ms.date: 06/13/2019
ms.author: shvija
ms.openlocfilehash: c020a7673fe018565a6f1aeb9f7cb2124024a2c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118861"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Краткое руководство. Создание концентратора событий с помощью шаблона Azure Resource Manager

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом кратком руководстве, можно создать концентратор событий с помощью [шаблона Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Развертывание шаблона Azure Resource Manager создать пространство имен типа [концентраторов событий](event-hubs-what-is-event-hubs.md), с одним концентратором событий. Здесь показано, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями. Дополнительные сведения о создании шаблонов см. в статье о [создании шаблонов Azure Resource Manager][Authoring Azure Resource Manager templates]. Синтаксис и свойства JSON, используемые в шаблоне, см. в статье о [типах ресурсов Microsoft.EventHub](/azure/templates/microsoft.eventhub/allversions).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="create-an-event-hub"></a>Создание концентратора событий

В этом кратком руководстве используется [существующий шаблон Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json). Дополнительные примеры шаблонов см. в статье [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

Для развертывания шаблона:

1. Выберите **попробовать** из следующих блок кода и следуйте инструкциям для входа в Azure Cloud shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Занимает несколько секунд, чтобы создать концентратор событий.

1. Нажмите кнопку **Копировать**, чтобы скопировать сценарий PowerShell.
1. Щелкните правой кнопкой мыши в консоли оболочки, а затем выберите **вставить**.

## <a name="verify-the-deployment"></a>Проверка развертывания

Для проверки развертывания, можно либо открыть группу ресурсов из [портала Azure](https://portal.azure.com), или используйте следующий скрипт Azure PowerShell.  Если Cloud shell по-прежнему открыта, не нужно копировать и запуска первой строки (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов. Если Cloud shell по-прежнему открыта, не нужно копировать и запуска первой строки (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы создали пространство имен Центров событий и использовали примеры приложений для отправки событий в созданный концентратор и получения событий из него. Пошаговые инструкции по отправке событий в концентратор и получении событий из него см. в следующих руководствах по **отправке и получению событий**:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
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
