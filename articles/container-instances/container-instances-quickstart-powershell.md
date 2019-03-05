---
title: Краткое руководство. Запуск приложения в службе "Экземпляры контейнеров Azure" с помощью PowerShell
description: В этом кратком руководстве вы развернете приложение, выполняющееся в контейнере Docker, в службе "Экземпляры контейнеров Azure" с помощью Azure PowerShell.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b8cb84523288f45dfb719d69e4f7d227039598a9
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806918"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-with-azure-powershell"></a>Краткое руководство. Запуск контейнерного приложения в службе "Экземпляры контейнеров Azure" с помощью Azure PowerShell

Служба "Экземпляры контейнеров Azure" позволяет легко и быстро запускать контейнеры Docker в Azure. В этом случае не нужно развертывать виртуальные машины или использовать единую платформу оркестрации контейнеров, такую как Kubernetes. В этом кратком руководстве вы с помощью портала Azure создадите в Azure контейнер Windows и сделаете его приложение доступным по полному доменному имени (FQDN). Через несколько секунд после выполнения одной команды развертывания можно просмотреть выполняющееся приложение:

![Приложение, развернутое в службе "Экземпляры контейнеров Azure" (просмотр в браузере)][qs-powershell-01]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Экземпляры контейнеров Azure, как и все ресурсы Azure, должны быть развернуты в группе ресурсов. Группы ресурсов позволяют организовать соответствующие ресурсы Azure и управлять ими.

Для начала создайте группу ресурсов с именем *myResourceGroup* в регионе *eastus* с помощью следующей команды [New-AzResourceGroup][New-AzResourceGroup]:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Создание контейнера

Теперь, когда вы создали группу ресурсов, можно запустить контейнер в Azure. Чтобы создать экземпляр контейнера с помощью Azure PowerShell, укажите имя группы ресурсов, имя экземпляра контейнера и образ контейнера Docker для командлета [New-AzContainerGroup][New-AzContainerGroup]. При работе с этим кратким руководством используется образ Windows `microsoft/iis:nanoserver` из общедоступного реестра Docker Hub. Этот образ содержит Internet Information Services (IIS) для запуска на сервере Nano Server.

Вы можете предоставить контейнеры в Интернете, указав один или несколько портов для открытия, метку имени DNS или и то и другое. При работы с этим руководством вы развернете контейнер с меткой имени DNS, чтобы сделать службы IIS общедоступными.

Выполните следующую команду, чтобы запустить экземпляр контейнера. Значение `-DnsNameLabel` должно быть уникальным в пределах региона Azure, в котором создается экземпляр. Если появится сообщение об ошибке "Метка имени DNS недоступна", попробуйте другую метку имени DNS.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

В течение нескольких секунд вы должны получить ответ от Azure. Состояние `ProvisioningState` контейнера изначально **Создается**, но через минуту или две оно должно измениться на **Успешно**. Вы можете проверить состояние развертывания с помощью командлета [Get-AzContainerGroup][Get-AzContainerGroup]:

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Состояние подготовки, полное доменное имя (FQDN) и IP-адрес контейнера отображаются в выходных данных командлета:

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Когда состояние `ProvisioningState` контейнера **Успешно**, перейдите в `Fqdn` в браузере. Если появится примерно такая веб-страница, поздравляем! Вы успешно развернули приложение, выполняющееся в контейнере Docker в Azure.

![Службы IIS, развернутые с помощью службы "Экземпляры контейнеров Azure" (просмотр в браузере)][qs-powershell-01]

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с контейнером его можно удалить с помощью командлета [Remove-AzContainerGroup][Remove-AzContainerGroup]:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы создали экземпляр контейнера Azure из образа, размещенного в общедоступном реестре Docker Hub. Если вы хотите создать образ контейнера и развернуть его через частный реестр контейнеров Azure, перейдите к руководству по использованию службы "Экземпляры контейнеров Azure".

> [!div class="nextstepaction"]
> [Руководство по использованию службы "Экземпляры контейнеров Azure"](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
