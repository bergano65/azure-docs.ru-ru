---
title: Краткое руководство. Создание Load Balancer с помощью шаблона Azure
titleSuffix: Azure Load Balancer
description: В этом кратком руководстве показано, как создать Load Balancer с помощью шаблона Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 175c5a36c873d16d50d5192a489133a01018e335
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80474601"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Краткое руководство. Создание Load Balancer с помощью шаблона Azure Resource Manager для распределения нагрузки между виртуальными машинами

Балансировка нагрузки обеспечивает более высокий уровень доступности и масштабирования за счет распределения входящих запросов между несколькими виртуальными машинами. В этом кратком руководстве показано, как развернуть шаблон Azure Resource Manager, с помощью которого создается Load Balancer (цен. категория "Стандартный") для распределения нагрузки между виртуальными машинами. При использовании шаблона Resource Manager выполняется меньшее количество действий по сравнению с другими методами развертывания.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-a-load-balancer"></a>Создание Load Balancer

Load Balancer и общедоступные IP-адреса SKU должны совпадать. Вместе с Load Balancer (цен. категория "Стандартный") необходимо также создать стандартный общедоступный IP-адрес, настроенный в качестве интерфейсного для Load Balancer (цен. категория "Стандартный"). Для создания Load Balancer (цен. категория "Базовый"), используйте [этот шаблон](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). Для производственных рабочих нагрузок компания Майкрософт рекомендует использовать номера SKU цен. категории "Стандартный".

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-324" highlight="58-122":::

В шаблоне определено несколько ресурсов Azure:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers);
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) (для подсистемы балансировки нагрузки и каждой из трех виртуальных машин);
- [**Microsoft.Network/networkSecurityGroups;** ](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks);
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (три из них)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (три из них);
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (три из них; используется для настройки IIS и веб-страниц).

Чтобы найти дополнительные связанные шаблоны Azure Load Balancer, перейдите на страницу [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Развертывание шаблона

1. Выберите **Попробовать** в следующем блоке кода, чтобы открыть Azure Cloud Shell, и следуйте отображающимся инструкциям, чтобы войти в Azure.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Подождите, пока появится запрос из консоли.

1. Выберите **Копировать** из предыдущего блока кода, чтобы скопировать сценарий PowerShell.

1. Щелкните правой кнопкой в области консоли оболочки и выберите **Вставить**.

1. Введите значения.

   При развертывании шаблона создается три зоны доступности. Зоны доступности поддерживаются только в [определенных регионах](../availability-zones/az-overview.md). Используйте один из поддерживаемых регионов. Если у вас есть сомнения, введите **centralus**.

   Имя группы ресурсов — это имя проекта с добавлением **rg**. Имя группы ресурсов потребуется в следующем разделе.

Развертывание шаблона занимает около 10 минут. По завершении выходные данные должны быть следующего вида:

![Выходные данные развертывания PowerShell для шаблона Azure Resource Manager в Load Balancer (цен. категория "Стандартный")](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Для развертывания шаблона используется Azure PowerShell. В дополнение к Azure PowerShell можно также использовать портал Azure, Azure CLI и REST API. Дополнительные сведения о других методах развертывания см. в статье о [развертывании с использованием шаблонов](../azure-resource-manager/templates/deploy-portal.md).

## <a name="test-the-load-balancer"></a>Тестирование подсистемы балансировки нагрузки

1. Войдите на [портал Azure](https://portal.azure.com).

1. В области слева выберите **Группы ресурсов**.

1. Выберите группу ресурсов, созданную при работе с предыдущим разделом. Имя группы ресурсов по умолчанию — это имя проекта с добавлением **rg**.

1. Выберите подсистему балансировки нагрузки. Имя по умолчанию — это имя проекта с добавлением **-lb**.

1. Скопируйте только часть общедоступного IP-адреса и вставьте его в адресную строку браузера.

   ![Общедоступный IP-адрес шаблона Azure Resource Manager в Azure Load Balancer (цен. категория "Стандартный")](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    В браузере отобразится страница по умолчанию веб-сервера службы IIS.

   ![Веб-сервер IIS](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Чтобы увидеть, как Load Balancer распределяет трафик между тремя виртуальными машинами, можно принудительно обновить веб-браузер на клиентском компьютере.

## <a name="clean-up-resources"></a>Очистка ресурсов

Ставшие ненужными группу ресурсов, подсистему балансировки нагрузки и все связанные ресурсы можно удалить. Для этого на портале Azure выберите группу ресурсов, содержащую Load Balancer, и щелкните **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как создать систему Load Balancer (цен. категория "Стандартный"), подключить к ней виртуальные машины, настроить правило трафика Load Balancer, выполнить пробу работоспособности, а также протестировать Load Balancer.

Чтобы узнать больше об Azure Load Balancer, ознакомьтесь с соответствующими учебниками.

> [!div class="nextstepaction"]
> [Руководства по Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
