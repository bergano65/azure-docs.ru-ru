---
title: Краткое руководство. Создание Брандмауэра Azure с несколькими общедоступными IP-адресами, используя шаблон Resource Manager
description: Сведения об использовании шаблона Resource Manager для создания Брандмауэра Azure с несколькими общедоступными IP-адресами.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 3d58173d239e7a9249b588ff038ea46cfedb27a3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605205"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Краткое руководство. Создание Брандмауэра Azure с несколькими общедоступными IP-адресами, используя шаблон Resource Manager

В этом кратком руководстве шаблон Resource Manager используется для развертывания брандмауэра Azure с несколькими общедоступными IP-адресами.

Развернутый брандмауэр содержит правила преобразования сетевых адресов (NAT), разрешающие подключения по протоколу удаленного рабочего стола к двум виртуальным машинам Windows Server 2019.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Дополнительные сведения о Брандмауэре Azure с несколькими общедоступными IP-адресами см. в статье [Развертывание Брандмауэра Azure с использованием нескольких общедоступных IP-адресов с помощью Azure PowerShell](deploy-multi-public-ip-powershell.md).

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.

## <a name="create-an-azure-firewall"></a>Создание Брандмауэра Azure

Этот шаблон создает Брандмауэр Azure с двумя общедоступными IP-адресами, а также необходимые ресурсы для поддержки Брандмауэра Azure.

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/fw-docs-qs/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

В шаблоне определено несколько ресурсов Azure:

- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups;** ](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks);
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)


### <a name="deploy-the-template"></a>Развертывание шаблона

Развертывание шаблона Resource Manager в Azure:

1. Выберите элемент **Развертывание в Azure**, чтобы войти в Azure и открыть шаблон. Шаблон создает Брандмауэр Azure, сетевую инфраструктуру и две виртуальные машины.

   [![Развертывание в Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. На портале на странице **Создание Брандмауэра Azure с несколькими общедоступными IP-адресами** введите или выберите следующие значения.
   - Подписка: выберите из существующих подписок. 
   - Группа ресурсов:  выберите из существующих групп ресурсов или выберите **Создать** и нажмите кнопку **ОК**.
   - Расположение. Выберите расположение
   - Имя пользователя администратора: введите имя учетной записи пользователя с правами администратора. 
   - Пароль администратора: введите пароль администратора или ключ.

3. Установите флажок **Я принимаю указанные выше условия** и нажмите кнопку **Приобрести**. Развертывание может занять более 10 минут.

## <a name="validate-the-deployment"></a>Проверка развертывания

Ознакомьтесь с развернутыми ресурсами на портале Azure. Обратите внимание на общедоступные IP-адреса брандмауэра.  

Используйте подключение к удаленному рабочему столу для подключения к общедоступным IP-адресам брандмауэра. Успешные подключения демонстрируют правила преобразования сетевых адресов (NAT) брандмауэра, разрешающие подключение к внутренним серверам.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам уже не нужны ресурсы, созданные с помощью брандмауэра, удалите группу ресурсов. При этом будут удалены брандмауэры и все связанные с ним ресурсы.

Чтобы удалить группу ресурсов, вызовите командлет `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Развертывание и настройка Брандмауэра Azure в гибридной сети с помощью портала Azure](tutorial-hybrid-portal.md)