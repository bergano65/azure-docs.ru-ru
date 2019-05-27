---
title: Развертывание Брандмауэра Azure с помощью шаблона
description: Развертывание Брандмауэра Azure с помощью шаблона
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 12/01/2018
ms.author: victorh
ms.openlocfilehash: e0fbec8b22993345114d8d6642e42095191d0b37
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66115692"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Развертывание Брандмауэра Azure с помощью шаблона

[Шаблон "Создание настройки песочницы AzureFirewall"](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox) создает тестовую сетевую среду с брандмауэром. Сеть имеет одну виртуальную сеть (VNet) с тремя подсетями. *AzureFirewallSubnet*, *ServersSubnet* и *JumpboxSubnet*. В подсетях *ServersSubnet* и *JumpboxSubnet* есть одна двухъядерная виртуальная машина Windows Server.

Брандмауэр находится в подсети *AzureFirewallSubnet* и имеет коллекцию правил приложения с одним правилом, которое разрешает доступ к *www.microsoft.com*.

Определенный пользователем маршрут направляет сетевой трафик из подсети *ServersSubnet* через брандмауэр, где применяются его правила.

Дополнительные сведения о брандмауэре Azure см. в разделе [Руководство по развертыванию и настройке службы "Брандмауэр Azure" с помощью портала Azure](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Использование шаблона для развертывания брандмауэра Azure

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

**Установка и развертывание брандмауэра Azure с помощью шаблона**

1. Перейдите к шаблону по ссылке [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox).
   
1. Прочитайте вводную информацию, а когда будете готовы развернуть шаблон, выберите **Развертывание в Azure**.
   
1. Войдите на портал Azure, если необходимо. 

1. На странице портала **Create a sandbox setup of AzureFirewall** (Создание настройки песочницы AzureFirewall) введите или выберите следующие значения.
   
   - **Группа ресурсов**. Выберите **Create new** (Создать новую), введите имя группы ресурсов и выберите **ОК**. 
   - **Имя виртуальной сети**. Введите имя новой виртуальной сети. 
   - **Имя администратора**. Введите имя учетной записи пользователя с правами администратора.
   - **Пароль администратора**. Введите пароль администратора. 
   
1. Прочтите условия использования и установите флажок **Я принимаю указанные выше условия**.
   
1. Щелкните **Приобрести**.
   
   Создание ресурсов займет несколько минут. 
   
1. Изучите ресурсы, созданные с помощью брандмауэра. 

Дополнительные сведения о синтаксисе JSON и свойствах для брандмауэра в шаблоне см. в разделе [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда они больше не нужны, можно удалить группу ресурсов, брандмауэра и все связанные ресурсы, выполнив [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) команду PowerShell. Чтобы удалить группу ресурсов с именем *MyResourceGroup*, выполните следующее. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Не удаляйте группу ресурсов и брандмауэр, если планируете перейти к изучению руководства по отслеживанию брандмауэра. 

## <a name="next-steps"></a>Дальнейшие действия

Также вы можете изучить журналы брандмауэра Azure.

> [!div class="nextstepaction"]
> [Руководство. Мониторинг журналов и метрик Брандмауэра Azure](./tutorial-diagnostics.md)
