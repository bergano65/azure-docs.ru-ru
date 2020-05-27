---
title: Краткое руководство. Создание Брандмауэра Azure с зонами доступности с помощью шаблона Resource Manager
description: 'Развертывание службы "Брандмауэр Azure" с помощью шаблона. Виртуальная сеть является виртуальной сетью с тремя подсетями. В ней развернуты две виртуальные машины Windows Server: инсталляционный сервер и обычный сервер.'
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/30/2020
ms.author: victorh
ms.openlocfilehash: 9b9b7926caa717c1a02988ac7a927bd9bd39d52a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683708"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---resource-manager-template"></a>Краткое руководство. Развертывание Брандмауэра Azure с зонами доступности с помощью шаблона Resource Manager

В этом кратком руководстве шаблон Resource Manager используется для развертывания Брандмауэра Azure в трех зонах доступности. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Этот шаблон создает тестовую сетевую среду с брандмауэром. Сеть имеет одну виртуальную сеть (VNet) с тремя подсетями. *AzureFirewallSubnet*, *ServersSubnet* и *JumpboxSubnet*. В подсетях *ServersSubnet* и *JumpboxSubnet* есть одна двухъядерная виртуальная машина Windows Server.

Брандмауэр находится в подсети *AzureFirewallSubnet* и использует коллекцию правил приложения с одним правилом, которое разрешает доступ к `www.microsoft.com`.

Определенный пользователем маршрут направляет сетевой трафик из подсети *ServersSubnet* через брандмауэр, где применяются его правила.

Дополнительные сведения о брандмауэре Azure см. в разделе [Руководство по развертыванию и настройке службы "Брандмауэр Azure" с помощью портала Azure](tutorial-firewall-deploy-portal.md).

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.

## <a name="create-an-azure-firewall-with-availability-zones"></a>Создание Брандмауэра Azure с зонами доступности

Этот шаблон создает Брандмауэр Azure с зонами доступности, а также необходимые ресурсы для поддержки Брандмауэра Azure.

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json" range="001-444" highlight="369-442":::

В шаблоне определено несколько ресурсов Azure:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups;** ](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks);
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

### <a name="deploy-the-template"></a>Развертывание шаблона

Выполните развертывание шаблона Resource Manager в Azure:

1. Выберите элемент **Развертывание в Azure**, чтобы войти в Azure и открыть шаблон. Шаблон создает Брандмауэр Azure, сетевую инфраструктуру и две виртуальные машины.

   [![Развертывание в Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. На странице портала **Create a sandbox setup of Azure Firewall with Zones** (Создание песочницы Брандмауэра Azure с зонами) введите или выберите следующие значения.
   - **Группа ресурсов.** Выберите **Create new** (Создать новую), введите имя группы ресурсов и выберите **ОК**. 
   - **Имя виртуальной сети**. Введите имя новой виртуальной сети. 
   - **Имя администратора**. Введите имя учетной записи пользователя с правами администратора.
   - **Пароль администратора**. Введите пароль администратора. 

3. Прочтите условия использования и установите флажок **Я принимаю указанные выше условия**, а затем нажмите кнопку **Приобрести**. Для развертывания может потребоваться 10 минут или более.

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

Изучите ресурсы, созданные с помощью брандмауэра.

Дополнительные сведения о синтаксисе JSON и свойствах для брандмауэра в шаблоне см. в разделе [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Очистка ресурсов

При необходимости группу ресурсов, брандмауэр и все связанные ресурсы можно удалить с помощью команды PowerShell `Remove-AzResourceGroup`. Чтобы удалить группу ресурсов с именем *MyResourceGroup*, выполните следующее. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Не удаляйте группу ресурсов и брандмауэр, если планируете работать с учебником по мониторингу брандмауэра. 

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы можете отследить журналы Брандмауэра Azure.

> [!div class="nextstepaction"]
> [Руководство. Мониторинг журналов и метрик Брандмауэра Azure](tutorial-diagnostics.md)