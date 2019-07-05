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
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 4162775153a48dc8ea28e06f7c99f9927b9c602a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444772"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Создание пространства имен служебной шины с помощью шаблона Azure Resource Manager

Узнайте, как для развертывания шаблона Azure Resource Manager создать пространство имен служебной шины. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями. Дополнительные сведения о создании шаблонов см. в разделе [документация по Azure Resource Manager](/azure/azure-resource-manager/).

Следующие шаблоны также доступны для создания пространства имен служебной шины:

* [Создание пространства имен служебной шины с очередью](./service-bus-resource-manager-namespace-queue.md)
* [Создание пространства имен служебной шины с разделом и подпиской](./service-bus-resource-manager-namespace-topic.md)
* [Создание пространства имен служебной шины с очередью и правилом авторизации](./service-bus-resource-manager-namespace-auth-rule.md)
* [Создание пространства имен служебной шины с разделом, подпиской и правилом с помощью шаблона Azure Resource Manager](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="create-a-service-bus-namespace"></a>Создание пространства имен служебной шины

В этом кратком руководстве используется [существующий шаблон Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Дополнительные примеры шаблонов см. в статье [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Чтобы создать пространство имен служебной шины, развернув шаблон:

1. Выберите **попробовать** из следующих блок кода и следуйте инструкциям для входа в Azure Cloud shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Имя группы ресурсов — это имя пространства имен служебной шины с **rg** добавляется.

2. Нажмите кнопку **Копировать**, чтобы скопировать сценарий PowerShell.
3. Щелкните правой кнопкой мыши в консоли оболочки, а затем выберите **вставить**.

Занимает несколько секунд, чтобы создать концентратор событий.

## <a name="verify-the-deployment"></a>Проверка развертывания

Для просмотра пространства имен шины развернутой службы, можно откройте группу ресурсов на портале Azure или используйте следующий скрипт Azure PowerShell. Если Cloud shell по-прежнему открыта, не требуется копировать и запуск первой и второй строками из следующих скриптов.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Чтобы развернуть шаблон в этом руководстве используется Azure PowerShell. Другие методы развертывания для шаблонов см. в разделе:

* [С помощью портала Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [С помощью Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [С помощью REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов. Если Cloud shell по-прежнему открыта, не требуется копировать и запуск первой и второй строками из следующих скриптов.

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
