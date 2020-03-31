---
title: Добавление сетевых интерфейсов или удаление из Пазур VMs
description: Узнайте, как добавлять и удалять сетевые интерфейсы виртуальных машин.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 4169bfb5da5b1ad13bab0eb01397f7c1fb20b11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060327"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Добавление и удаление сетевых интерфейсов виртуальных машин

Узнайте, как добавить существующий сетевой интерфейс при создании виртуальной машины Azure (VM). Также научитесь добавлять или удалять сетевые интерфейсы из существующего VM в остановленном (размещенном) состоянии. Сетевой интерфейс позволяет VM Azure общаться с интернетом, Azure и натерритории ресурсов. VM имеет один или несколько сетевых интерфейсов. 

При необходимости см. инструкции по [добавлению, изменению и удалению IP-адресов для сетевого интерфейса Azure](virtual-network-network-interface-addresses.md). Для создания, изменения или удаления сетевых интерфейсов [см.](virtual-network-network-interface.md)

## <a name="before-you-begin"></a>Перед началом

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Если у вас его нет, навяжните учетную запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно. Выполните одну из этих задач перед началом оставшейся части этой статьи:

- **Пользователи портала**: Войти на [портал Azure](https://portal.azure.com) с вашей учетной записью Azure.

- **Пользователи PowerShell:** либо запускать команды в [облачной оболочке Azure,](https://shell.azure.com/powershell)либо запускать PowerShell с компьютера. Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Во вкладке браузера Azure Cloud Shell найдите список выпадающих **средств для очистки среды Select,** а затем выберите **PowerShell,** если он еще не выбран.

    Если вы работаете локально PowerShell, используйте версию модуля Azure PowerShell 1.0.0 или позже. Выполните командлет `Get-Module -ListAvailable Az.Network`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure.

- **Пользователи интерфейса Командной строки Azure (CLI)**: либо запускают команды в [оболочке Azure Cloud Shell,](https://shell.azure.com/bash)либо запускают CLI с компьютера. Используйте версию Azure CLI 2.0.26 или позже, если вы используете Azure CLI локально. Выполните командлет `az --version`, чтобы узнать установленную версию. Если вам нужно установить или обновить, [см.](/cli/azure/install-azure-cli) Выполните команду `az login`, чтобы создать подключение к Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Добавление имеющихся сетевых интерфейсов к новой виртуальной машине

При создании виртуальной машины через портал портал создает сетевой интерфейс с настройками по умолчанию и прикрепляет для вас сетевой интерфейс к VM. Вы не можете использовать портал для добавления существующих сетевых интерфейсов в новый VM или для создания VM с несколькими сетевыми интерфейсами. Вы можете сделать оба с помощью CLI или PowerShell. Обязательно ознакомьтесь с [ограничениями.](#constraints) При создании виртуальной машины с несколькими сетевыми интерфейсами, необходимо также настроить ОС, чтобы использовать их соответствующим образом. Дополнительные сведения см. в статьях о настройке виртуальных машин [Windows](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) или [Linux](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics).

### <a name="commands"></a>Команды

Перед созданием VM [создайте сетевой интерфейс.](virtual-network-network-interface.md#create-a-network-interface)

|Инструмент|Команда|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Добавление сетевого интерфейса к имеющейся виртуальной машине

Чтобы добавить сетевой интерфейс в виртуальную машину:

1. Перейдите на [портал Azure,](https://portal.azure.com) чтобы найти существующую виртуальную машину. Поиск и выбор **виртуальных машин.**

2. Выберите имя вашего VM. Она должна поддерживать количество сетевых интерфейсов, которое вы хотите добавить. Чтобы узнать, сколько сетевых интерфейсов поддерживает каждый размер [Linux VMs](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM, [Windows VMs](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)см.

3. В панели команд VM выберите **Stop,** а затем **OK** в поле диалога подтверждения. Затем подождите, пока **статус** VM изменяется в **Остановленный (сделки)**.

4. Из панели меню VM выберите**сетевой интерфейс** **Networking** > Attach. Затем в **Прикрепите существующий сетевой интерфейс,** выберите сетевой интерфейс, который вы хотели бы прикрепить, и выберите **OK.**

    >[!NOTE]
    >Выбранный вами сетевой интерфейс не может иметь ускоренную сеть, не может иметь адрес IPv6, назначенный ему, и должен существовать в той же виртуальной сети с сетевым интерфейсом, который в настоящее время подключен к VM.

    Если у вас нет сетевого интерфейса, сначала создайте его. Для этого выберите **Создать сетевой интерфейс**. Дополнительные сведения см. в разделе [Создание сетевого интерфейса](virtual-network-network-interface.md#create-a-network-interface). Сведения о дополнительных ограничениях при добавлении сетевых интерфейсов к виртуальным машинам см. в разделе [Ограничения](#constraints).

5. Из меню VM бар, выберите **Обзор** > **Начало** перезагрузки виртуальной машины.

Теперь вы можете настроить операционную систему VM, чтобы использовать несколько сетевых интерфейсов должным образом. Дополнительные сведения см. в статьях о настройке виртуальных машин [Windows](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) или [Linux](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics).

### <a name="commands"></a>Команды

|Инструмент|Команда|
|---|---|
|CLI|[az vm nic добавить](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (ссылка); [подробные шаги](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Адд-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (справка); [подробные шаги](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Просмотр сетевых интерфейсов для виртуальной машины

Вы можете просмотреть сетевые интерфейсы, подключенные к виртуальной машине, чтобы изучить конфигурацию каждого из них, а также назначенные им IP-адреса. 

1. Перейдите на [портал Azure,](https://portal.azure.com) чтобы найти существующую виртуальную машину. Поиск и выбор **виртуальных машин.**

    >[!NOTE]
    >Восстайте в использовании учетной записи, которая назначена роль владельца, вкладчика или сетевого вкладчика для вашей подписки. Дополнительные сведения о назначении ролей учетным записям см. в разделе [Участник сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Выберите имя VM, для которого вы хотите просмотреть прилагаемые сетевые интерфейсы.

3. В баре меню VM выберите **Сеть**.

Дополнительную информацию о параметрах сетевого интерфейса и сведения об их изменении см. в статье [Создание, изменение или удаление сетевых интерфейсов](virtual-network-network-interface.md). Сведения о добавлении, изменении и удалении IP-адресов для сетевых интерфейсов Azure см. в [этой статье](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Команды

|Инструмент|Команда|
|---|---|
|CLI|[az vm nic список](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Удаление сетевого интерфейса из виртуальной машины

1. Перейдите на [портал Azure,](https://portal.azure.com) чтобы найти существующую виртуальную машину. Поиск и выбор **виртуальных машин.**

2. Выберите имя VM, для которого вы хотите просмотреть прилагаемые сетевые интерфейсы.

3. В панели инструментов VM выберите **Stop**.

4. Подождите, пока **состояние** виртуальной машины не изменится на **Остановлено (освобождено)**.

5. Из панели меню VM выберите сетевой интерфейс **Networking** > **Detach.**

6. В диалоговом окне интерфейса **сети Detach** выберите сетевой интерфейс, который вы хотели бы отсоединить. Затем выберите **OK**.

    >[!NOTE]
    >Если в списке только один сетевой интерфейс, его нельзя отсоединить, потому что виртуальная машина всегда должна иметь присоединенное к нему хотя бы один сетевой интерфейс.

### <a name="commands"></a>Команды

|Инструмент|Команда|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (ссылка); [подробные шаги](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Удалить-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (ссылка); [подробные шаги](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Ограничения

- К виртуальной машине должен быть подключен по крайней мере один сетевой интерфейс.

- К виртуальной машине можно подключить столько сетевых интерфейсов, сколько поддерживает ее размер. Чтобы узнать больше о том, сколько сетевых интерфейсов поддерживает [Linux VMs](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) каждый размер [Windows VMs](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)VM, см. Все размеры поддерживают как минимум два сетевых интерфейса.

- Сетевые интерфейсы, добавленные к VM, в настоящее время не могут быть прикреплены к другому VM. Дополнительные сведения о создании сетевого интерфейса см. в [этом разделе](virtual-network-network-interface.md#create-a-network-interface).

- В прошлом можно было добавлять сетевые интерфейсы только к вм:, которые поддерживали несколько сетевых интерфейсов и создавались по крайней мере с двумя сетевыми интерфейсами. Вы не можете добавить сетевой интерфейс в VM, который был создан с одним сетевым интерфейсом, даже если размер VM поддерживал более одного сетевого интерфейса. И наоборот, сетевые интерфейсы можно было удалять только из виртуальной машины по крайней мере с тремя сетевыми интерфейсами, так как виртуальные машины, созданные по крайней мере с двумя сетевыми интерфейсами, всегда должны были иметь по крайней мере два сетевых интерфейса. Эти ограничения больше не применяются. Теперь вы можете создать виртуальную машину с любым количеством сетевых интерфейсов (в пределах количества, поддерживаемого размером виртуальной машины).

- По умолчанию первый сетевой интерфейс, прикрепленный к VM, является *основным* сетевым интерфейсом. Все остальные сетевые интерфейсы в виртуальной машине считаются *дополнительными*.

- Вы можете контролировать, какой сетевой интерфейс вы отправляете исходящий трафик. Однако VM по умолчанию отправляет весь исходящий трафик на IP-адрес, назначенный основной конфигурации IP-интерфейса основного сетевого интерфейса.

- В прошлом все виртуальные машины в одной группе доступности должны были иметь один или несколько сетевых интерфейсов. Теперь в одной группе доступности могут находиться виртуальные машины с любым количеством сетевых интерфейсов (в пределах количества, поддерживаемого размером виртуальной машины). Виртуальную машину можно добавить в группу доступности только при ее создании. Дополнительные сведения о группах доступности см. в разделе [Настройка нескольких виртуальных машин в группе доступности для обеспечения избыточности](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

- Вы можете подключить сетевые интерфейсы в одном и том же VM к различным подсетям в виртуальной сети. Однако все сетевые интерфейсы должны быть подключены к одной и той же виртуальной сети.

- Вы можете добавить любой IP-адрес для любой IP-конфигурации любого основного или дополнительного сетевого интерфейса во внутренний Azure Load Balancer. Раньше во внутренний пул можно было добавить только основной IP-адрес для основного сетевого интерфейса. Дополнительные сведения об IP-адресах и IP-конфигурациях см. в статье [Добавление, изменение и удаление IP-адресов для сетевых интерфейсов Azure](virtual-network-network-interface-addresses.md).

- Удаление VM не удаляет прилагаемые к нему сетевые интерфейсы. Если вы удалите виртуальную машину, сетевые интерфейсы отключатся от нее. Вы можете добавить эти сетевые интерфейсы в различные vMs или удалить их.

- Как и в IPv6, вы не можете прикрепить сетевой интерфейс с ускоренной сети, включенной в VM после его создания. Кроме того, чтобы воспользоваться преимуществами ускоренной сети, вы должны выполнить шаги в ОС виртуальной машины. Дополнительные сведения об ускоренной сети и ограничениях см. в разделах об ускорении работы в сети для виртуальных машин [Windows](create-vm-accelerated-networking-powershell.md) или [Linux](create-vm-accelerated-networking-cli.md).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы создать VM с несколькими сетевыми интерфейсами или IP-адресами, см.:

|Задача|Инструмент|
|---|---|
|Создание виртуальной машины с несколькими сетевыми адаптерами|[Интерфейс командной строки](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Создание виртуальной машины с одним сетевым адаптером, которому назначено несколько IPv4-адресов|[Интерфейс командной строки](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Создание виртуальной машины с одним сетевым адаптером, которому назначен частный IPv6-адрес (обслуживаемый Azure Load Balancer).|[Интерфейс командной строки](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [шаблон Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
