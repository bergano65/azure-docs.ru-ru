---
title: Развертывание Брандмауэра Azure с помощью шаблона
description: Развертывание брандмауэра Azure с помощью шаблона. В созданной сети есть одна виртуальная сеть с тремя подсетями. Две, две-ядерные виртуальные машины Windows Server развернуты.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169203"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Развертывание Брандмауэра Azure с помощью шаблона

[Шаблон "Создание настройки песочницы AzureFirewall"](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) создает тестовую сетевую среду с брандмауэром. Сеть имеет одну виртуальную сеть (VNet) с тремя подсетями: *азурефиреваллсубнет*, *серверссубнет*и *жумпбокссубнет*. В подсетях *ServersSubnet* и *JumpboxSubnet* есть одна двухъядерная виртуальная машина Windows Server.

Брандмауэр находится в подсети *азурефиреваллсубнет* и содержит набор правил приложений с одним правилом, обеспечивающим доступ к `www.microsoft.com`.

Определенный пользователем маршрут направляет сетевой трафик из подсети *ServersSubnet* через брандмауэр, где применяются его правила.

Дополнительные сведения о брандмауэре Azure см. в разделе [Руководство по развертыванию и настройке службы "Брандмауэр Azure" с помощью портала Azure](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Использование шаблона для развертывания брандмауэра Azure

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

**Установка и развертывание брандмауэра Azure с помощью шаблона**

1. Перейдите к шаблону по ссылке [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox).
   
1. Прочитайте вводную информацию, а когда будете готовы развернуть шаблон, выберите **Развертывание в Azure**.
   
1. Войдите на портал Azure, если необходимо. 

1. На странице портала **Create a sandbox setup of AzureFirewall** (Создание настройки песочницы AzureFirewall) введите или выберите следующие значения.
   
   - **Группа ресурсов**: выберите **создать**, введите имя группы ресурсов и нажмите кнопку **ОК**. 
   - **Имя виртуальной сети**. Введите имя для новой сети. 
   - **Имя пользователя администратора**: введите имя пользователя для учетной записи администратора.
   - **Пароль**администратора. Введите пароль администратора. 
   
1. Прочтите условия использования и установите флажок **Я принимаю указанные выше условия**.
   
1. Щелкните **Приобрести**.
   
   Создание ресурсов займет несколько минут. 
   
1. Изучите ресурсы, созданные с помощью брандмауэра. 

Дополнительные сведения о синтаксисе JSON и свойствах для брандмауэра в шаблоне см. в разделе [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если они больше не нужны, можно удалить группу ресурсов, брандмауэр и все связанные с ней ресурсы, выполнив команду PowerShell [Remove-азресаурцеграуп](/powershell/module/az.resources/remove-azresourcegroup) . Чтобы удалить группу ресурсов с именем *MyResourceGroup*, выполните следующее. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Не удаляйте группу ресурсов и брандмауэр, если планируете перейти к изучению руководства по отслеживанию брандмауэра. 

## <a name="next-steps"></a>Дополнительная информация

Также вы можете изучить журналы брандмауэра Azure.

> [!div class="nextstepaction"]
> [Руководство по мониторингу журналов Брандмауэра Azure](./tutorial-diagnostics.md)
