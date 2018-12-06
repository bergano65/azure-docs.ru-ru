---
title: Как сбросить сетевой интерфейс для виртуальной машины Azure под управлением Windows | Документация Майкрософт
description: В этой статье показано, как сбросить сетевой интерфейс для виртуальной машины Azure под управлением Windows.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 61001d4926dcce68872a368afb5b28f2d3a8e2c0
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51819006"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Как сбросить сетевой интерфейс для виртуальной машины Azure под управлением Windows 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Вы не можете подключиться к виртуальной машине Microsoft Azure под управлением Windows после того, как отключили стандартный сетевой интерфейс (NIC) или установили статический IP-адрес для NIC вручную. В этой статье показано, как сбросить сетевой интерфейс для виртуальной машины Azure под управлением Windows, что устранит проблему удаленного подключения.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Сброс сетевого интерфейса

### <a name="for-classic-vms"></a>Классические виртуальные машины

Чтобы сбросить сетевой интерфейс, сделайте следующее:

#### <a name="use-azure-portal"></a>Использование портала Azure

1.  Перейдите на [портал Azure]( https://ms.portal.azure.com).
2.  Выберите **Виртуальные машины (классика)**.
3.  Выберите задействованную виртуальную машину.
4.  Выберите **IP-адреса**.
5.  Если для **назначения частного IP-адреса** не установлено значение **Статический**, установите **его**.
6.  Изменение **IP-адреса** на другой, который доступен в подсети.
7.  Щелкните "Сохранить".
8.  Виртуальная машина будет перезапущена, чтобы инициализировать новый NIC в системе.
9.  Попробуйте подключиться к компьютеру по протоколу удаленного рабочего стола. В случае успешного подключения при необходимости можно изменить частный IP-адрес на исходный или использовать прежний. 

#### <a name="use-azure-powershell"></a>Использование Azure PowerShell

1. Убедитесь, что у вас установлена [последняя версия Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Откройте расширенный сеанс Azure PowerShell (Запуск от имени администратора). Выполните следующие команды:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Suscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ServiceName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Попробуйте подключиться к компьютеру по протоколу удаленного рабочего стола. В случае успешного подключения при необходимости можно изменить частный IP-адрес на исходный или использовать прежний. 

### <a name="for-vms-deployed-in-resource-group-model"></a>Виртуальные машины, развернутые в модели группы ресурсов

1.  Перейдите на [портал Azure]( https://ms.portal.azure.com).
2.  Выберите задействованную виртуальную машину.
3.  Выберите **Сетевые интерфейсы**.
4.  Выберите сетевой интерфейс, связанный с компьютером.
5.  Щелкните **IP configurations** (Конфигурации IP).
6.  Выберите IP-адрес. 
7.  Если для **назначения частного IP-адреса** не установлено значение **Статический**, установите **его**.
8.  Изменение **IP-адреса** на другой, который доступен в подсети.
9. Виртуальная машина будет перезапущена, чтобы инициализировать новый NIC в системе.
10. Попробуйте подключиться к компьютеру по протоколу удаленного рабочего стола. В случае успешного подключения при необходимости можно изменить частный IP-адрес на исходный или использовать прежний. 

#### <a name="use-azure-powershell"></a>Использование Azure PowerShell

1. Убедитесь, что у вас установлена [последняя версия Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Откройте расширенный сеанс Azure PowerShell (Запуск от имени администратора). Выполните следующие команды:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Suscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureRMAccount
    Select-AzureRMSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzureRMVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzureRMVM
    ```
3. Попробуйте подключиться к компьютеру по протоколу удаленного рабочего стола.  В случае успешного подключения при необходимости можно изменить частный IP-адрес на исходный или использовать прежний. 

## <a name="delete-the-unavailable-nics"></a>Удаление недоступных сетевых интерфейсов
После подключения к компьютеру с помощью удаленного рабочего стола необходимо удалить устарелые сетевые интерфейсы во избежание возможных проблем.

1.  Откройте диспетчер устройств.
2.  Выберите **Просмотреть** > **Показать скрытые устройства**.
3.  Выберите **Сетевые адаптеры**. 
4.  Найдите адаптеры с именем "Сетевой адаптер Hyper-V (Майкрософт)".
5.  Вы можете увидеть неактивный адаптер. Щелкните его правой кнопкой, а затем выберите "Удалить".

    ![изображение сетевого интерфейса](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Удалите только отключенные адаптеры с именем "Сетевой адаптер Hyper-V (Майкрософт)". Удаление других скрытых адаптеров может привести к дополнительным проблемам.
    >
    >

6.  Теперь все отключенные адаптеры должны быть удалены из системы.
