---
title: Создание пространства имен Azure Service Bus с помощью шаблона
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
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 5febdd63ab6f854ca3244f8449f6f715a75e735f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264481"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Создание пространства имен sass, используя шаблон управления ресурсами Azure

Узнайте, как развернуть шаблон менеджера ресурсов Azure для создания пространства имен Service Bus. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями. Для получения дополнительной информации о создании шаблонов можно ознакомиться с [документацией менеджера ресурсов Azure.](/azure/azure-resource-manager/)

Следующие шаблоны также доступны для создания пространства имен Service Bus:

* [Создание пространства имен служебной шины с очередью](./service-bus-resource-manager-namespace-queue.md)
* [Создание пространства имен служебной шины с разделом и подпиской](./service-bus-resource-manager-namespace-topic.md)
* [Создание пространства имен служебной шины с очередью и правилом авторизации](./service-bus-resource-manager-namespace-auth-rule.md)
* [Создание пространства имен служебной шины с разделом, подпиской и правилом с помощью шаблона Azure Resource Manager](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Если у вас нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/) перед началом.

## <a name="create-a-service-bus-namespace"></a>Создание пространства имен служебной шины

В этом быстром запуске используется [существующий шаблон менеджера ресурсов](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) из [шаблонов Azure quickstart:](https://azure.microsoft.com/resources/templates/)

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

См. [примеры шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Для создания пространства имен шины обслуживания путем развертывания шаблона:

1. Выберите **Попробовать** в следующем блоке кода и следуйте инструкциям, чтобы войти в Azure Cloud Shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Имя группы ресурсов — это имя пространства имен службы с придативаемым **rg.**

2. Нажмите кнопку **Копировать**, чтобы скопировать сценарий PowerShell.
3. Щелкните правой кнопкой в консоли оболочки и выберите **Вставить**.

Создание концентратора событий занимает несколько секунд.

## <a name="verify-the-deployment"></a>Проверка развертывания

Чтобы увидеть развернутое пространство имен шины обслуживания, можно либо открыть группу ресурсов с портала Azure, либо использовать следующий скрипт Azure PowerShell. Если оболочка облака по-прежнему открыта, вам не нужно копировать/запускать первую и вторую строки следующего сценария.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell используется для развертывания шаблона в этом учебнике. Для других методов развертывания шаблонов см.:

* [С помощью портала Azure](../azure-resource-manager/templates/deploy-portal.md).
* [С помощью Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [С помощью REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов. Если оболочка облака по-прежнему открыта, вам не нужно копировать/запускать первую и вторую строки следующего сценария.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы создали пространство имен служебной шины. Ознакомьтесь с другими краткими руководствами, чтобы узнать, как создавать очереди, разделы и подписки и использовать их.

* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Начало работы с разделами служебной шины](service-bus-dotnet-how-to-use-topics-subscriptions.md)
