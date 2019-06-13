---
title: Краткое руководство. Создание Load Balancer (цен. категория "Стандартный") с помощью шаблона Azure Resource Manager
titlesuffix: Azure Load Balancer
description: В этом кратком руководстве показано, как создать Load Balancer (цен. категория "Стандартный") с помощью шаблона Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 0e47560ca43b23f4779da701f3e6f11f53a6b1ce
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480402"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Краткое руководство. Создание Load Balancer (цен. категория "Стандартный") с помощью шаблона Azure Resource Manager для распределения нагрузки между виртуальными машинами

Балансировка нагрузки обеспечивает более высокий уровень доступности и масштабирования за счет распределения входящих запросов между несколькими виртуальными машинами. Вы можете использовать шаблон Azure Resource Manager для создания подсистемы балансировки нагрузки, чтобы распределять нагрузку между виртуальными машинами. В этом кратком руководстве показано, как распределять нагрузку между виртуальными машинами с помощью подсистемы балансировки нагрузки уровня "Стандартный".

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-a-standard-load-balancer"></a>Создание подсистемы балансировки нагрузки уровня "Стандартный"

В этом разделе вы создадите службу Load Balancer (цен. категория "Стандартный"), помогающую распределять нагрузку виртуальных машин. Подсистема балансировки нагрузки уровня "Стандартный" поддерживает только стандартные общедоступные IP-адреса. Вместе с подсистемой балансировки нагрузки уровня "Стандартный" необходимо также создать новый стандартный общедоступный IP-адрес, настроенный для него в качестве интерфейсного сервера (с именем *LoadBalancerFrontend* по умолчанию). Существует множество методов для создания Load Balancer (цен. категория "Стандартный"). В этом кратком руководстве описывается, как развернуть [шаблон Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json) с помощью Azure PowerShell. Шаблоны Resource Manager — это JSON-файлы, которые определяют ресурсы, необходимые для развертывания решения. Основные понятия, связанные с развертыванием решений Azure и управлением ими, объясняются в [документации по Azure Resource Manager](/azure/azure-resource-manager/). Чтобы найти дополнительные связанные шаблоны Azure Load Balancer, ознакомьтесь с разделом [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

Для развертывания шаблона выберите **Попробовать**, чтобы открыть Azure Cloud Shell, а затем вставьте следующий сценарий PowerShell в окно оболочки. Чтобы вставить код, щелкните окно оболочки правой кнопкой мыши, а затем выберите **Вставить**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
$adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword
```

Обратите внимание на то, что имя группы ресурсов — это имя проекта с добавлением **rg**. Имя группы ресурсов потребуется в следующем разделе.  Создание ресурсов занимает несколько минут.

## <a name="test-the-load-balancer"></a>Тестирование Load Balancer

1. Войдите на [портале Azure](https://portal.azure.com).
1. В области слева выберите **Группы ресурсов**.
1. Выберите группу ресурсов, созданную в последнем разделе.  Имя группы ресурсов по умолчанию — это имя проекта с добавлением **rg**.
1. Выберите подсистему балансировки нагрузки.  Доступна только одна подсистема балансировки нагрузки. Имя по умолчанию — это имя проекта с добавлением **-lb**.
1. Скопируйте общедоступный IP-адрес (только часть, представляющую собой IP-адрес) и вставьте его в адресную строку браузера. В браузере отобразится страница по умолчанию веб-сервера IIS.

   ![Веб-сервер IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Чтобы увидеть, как Load Balancer распределяет трафик между тремя виртуальными машинами, можно принудительно обновить веб-браузер на клиентском компьютере.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если больше нет необходимости в Load Balancer и всех связанных ресурсах, их можно удалить. Для этого на портале Azure выберите группу ресурсов, содержащую Load Balancer, и выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как создать Load Balancer (цен. категория "Стандартный"), подключить к ней виртуальные машины, настроить правило трафика Load Balancer, пробу работоспособности, а также протестировать Load Balancer. Чтобы узнать больше об Azure Load Balancer, ознакомьтесь с другими руководствами по этой службе.

> [!div class="nextstepaction"]
> [Руководства по Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
