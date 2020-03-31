---
title: Развертывание Брандмауэра Azure с помощью шаблона
description: Развертывание Брандмауэра Azure с помощью шаблона. Созданная сеть имеет один VNet с тремя подсетями. Развернуты два двухъядерных виртуальных компьютера Windows Server.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169203"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Развертывание Брандмауэра Azure с помощью шаблона

[Шаблон "Создание настройки песочницы AzureFirewall"](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) создает тестовую сетевую среду с брандмауэром. Сеть имеет одну виртуальную сеть (VNet) с тремя подсетями: *AzureFirewallSubnet,* *ServersSubnet*и *JumpboxSubnet*. В подсетях *ServersSubnet* и *JumpboxSubnet* есть одна двухъядерная виртуальная машина Windows Server.

Брандмауэр находится в подсети *AzureFirewallSubnet* и имеет набор правил приложения с `www.microsoft.com`одним правилом, которое позволяет получить доступ к .

Определенный пользователем маршрут направляет сетевой трафик из подсети *ServersSubnet* через брандмауэр, где применяются его правила.

Дополнительные сведения о брандмауэре Azure см. в разделе [Руководство по развертыванию и настройке службы "Брандмауэр Azure" с помощью портала Azure](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Использование шаблона для развертывания брандмауэра Azure

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

**Установка и развертывание брандмауэра Azure с помощью шаблона**

1. Доступ к [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)шаблону по адресу .
   
1. Прочитайте вводную информацию, а когда будете готовы развернуть шаблон, выберите **Развертывание в Azure**.
   
1. Войдите на портал Azure, если необходимо. 

1. На странице портала **Create a sandbox setup of AzureFirewall** (Создание настройки песочницы AzureFirewall) введите или выберите следующие значения.
   
   - **Группа ресурсов**: Выберите **Создать новое,** введите имя для группы ресурсов и выберите **OK.** 
   - **Название виртуальной сети**: Введите имя для нового VNet. 
   - **Имя пользователя администратора**: Введите имя пользователя для учетной записи пользователя администратора.
   - **Пароль администратора**: Введите пароль администратора. 
   
1. Прочтите условия использования и установите флажок **Я принимаю указанные выше условия**.
   
1. Нажмите кнопку **Купить**.
   
   Создание ресурсов займет несколько минут. 
   
1. Изучите ресурсы, созданные с помощью брандмауэра. 

Дополнительные сведения о синтаксисе JSON и свойствах для брандмауэра в шаблоне см. в разделе [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда они больше не нужны, можно удалить группу ресурсов, брандмауэр и все связанные с ними ресурсы, запустив команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell. Чтобы удалить группу ресурсов с именем *MyResourceGroup*, выполните следующее. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Не удаляйте группу ресурсов и брандмауэр, если планируете перейти к изучению руководства по отслеживанию брандмауэра. 

## <a name="next-steps"></a>Дальнейшие действия

Также вы можете изучить журналы брандмауэра Azure.

> [!div class="nextstepaction"]
> [Руководство. Журналы мониторинга брандмауэра Azure](./tutorial-diagnostics.md)
