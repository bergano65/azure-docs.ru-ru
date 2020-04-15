---
title: Краткое руководство. Создание Брандмауэра Azure и групп IP-адресов с помощью шаблона Resource Manager
description: Сведения об использовании шаблона Resource Manager для создания Брандмауэра Azure и групп IP-адресов.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 767e91af7fb5ff3de517a79ac4df55af5057c76f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677759"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>Краткое руководство. Создание Брандмауэра Azure и групп IP-адресов с помощью шаблона Resource Manager

В этом кратком руководстве шаблон Resource Manager используется для развертывания Брандмауэра Azure с примерами групп IP-адресов, используемых в правиле сети и приложения.

Группа IP-адресов — это ресурс верхнего уровня, позволяющий определять и группировать IP-адреса, диапазоны и подсети в один объект. Это полезно для управления IP-адресами в правилах Брандмауэра Azure. Вы можете либо вручную ввести IP-адреса, либо импортировать их из файла.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.

## <a name="create-an-azure-firewall-and-ip-groups"></a>Создание Брандмауэра Azure и групп IP-адресов

Этот шаблон создает Брандмауэр Azure и группы IP-адресов, а также необходимые ресурсы для поддержки Брандмауэра Azure.

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

В шаблоне определено несколько ресурсов Azure:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
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

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json"><img src="./media/quick-create-ipgroup-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. На портале на странице **Создание Брандмауэра Azure с группами IP-адресов** введите или выберите следующие значения:
   - Подписка: выберите из существующих подписок. 
   - Группа ресурсов:  выберите из существующих групп ресурсов или выберите **Создать** и нажмите кнопку **ОК**.
   - Расположение. Выберите расположение
   - Имя виртуальной сети: введите имя для новой виртуальной сети (VNet). 
   - Имя группы IP-адресов 1: введите имя для группы IP-адресов 1. 
   - Имя группы IP-адресов 2: введите имя для группы IP-адресов 2. 
   - Имя пользователя администратора: введите имя учетной записи пользователя с правами администратора. 
   - Аутентификация: выберите sshPublicKey или пароль. 
   - Пароль администратора: введите пароль администратора или ключ.

3. Установите флажок **Я принимаю указанные выше условия** и нажмите кнопку **Приобрести**. Для развертывания может потребоваться 10 минут или более.

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

На портале Azure ознакомьтесь с развернутыми ресурсами, особенно правилами брандмауэра, использующими группы IP-адресов.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="Группы IP-адресов.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Правила сети.":::

Дополнительные сведения о синтаксисе JSON и свойствах для брандмауэра в шаблоне см. в статье [Справочник по шаблонам Microsoft.Network azureFirewalls](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам уже не нужны ресурсы, созданные с помощью брандмауэра, удалите группу ресурсов. При этом будут удалены брандмауэры и все связанные с ним ресурсы.

Чтобы удалить группу ресурсов, вызовите командлет `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Развертывание и настройка Брандмауэра Azure в гибридной сети с помощью портала Azure](tutorial-hybrid-portal.md)