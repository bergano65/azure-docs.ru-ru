---
title: Руководство. Установка приложений на виртуальной машине Windows в Azure | Документация Майкрософт
description: В этом руководстве описано, как использовать расширение пользовательских скриптов для выполнения скриптов и развертывания приложений на виртуальных машинах Windows в Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 354625accb39344d07a22f2d3935cf4cf022d491
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977667"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Руководство. Развертывание приложений на виртуальной машине Windows в Azure с помощью расширения пользовательских скриптов

Для быстрой и согласованной настройки виртуальных машин можно использовать [расширение пользовательских скриптов для Windows](extensions-customscript.md). Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Использование расширения пользовательских скриптов на установки IIS
> * Создание виртуальной машины, которая использует расширение пользовательских скриптов
> * Просмотр выполнения сайта IIS после применения расширения

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

## <a name="custom-script-extension-overview"></a>Общие сведения о расширении настраиваемых сценариев
Расширение настраиваемых сценариев скачивает и выполняет сценарии на виртуальных машинах Azure. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения.

Расширение пользовательских сценариев интегрируется с шаблонами Azure Resource Manager, а также его можно запустить с помощью интерфейса командной строки Azure, PowerShell, портала Azure или API REST виртуальной машины Azure.

Расширение настраиваемых сценариев можно использовать для виртуальных машин как в Windows, так и в Linux.


## <a name="create-virtual-machine"></a>Создание виртуальной машины
Укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Теперь вы можете создать виртуальную машину с помощью командлета [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). В следующем примере создается виртуальная машина с именем *myVM* в расположении *EastUS*. Если они еще не существуют, создаются поддерживающие сетевые ресурсы и группа доступности *myResourceGroupAutomate*. Чтобы разрешить веб-трафик, командлет также открывает порт *80*.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Создание этих ресурсов и виртуальной машины может занять несколько минут.


## <a name="automate-iis-install"></a>Автоматизация установки IIS
Воспользуйтесь командлетом [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension), чтобы установить расширение пользовательских скриптов. Это расширение запускает `powershell Add-WindowsFeature Web-Server` для установки веб-сервера IIS, а затем обновляет страницу *Default.htm* для отображения имени узла виртуальной машины.

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Проверка веб-сайта
Получите общедоступный IP-адрес подсистемы балансировки нагрузки с помощью командлета [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). Следующий пример позволяет получить IP-адрес для созданного ранее *myPublicIPAddress*.

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

После этого можно ввести общедоступный IP-адрес в веб-браузер. Отображается веб-сайт, а также имя узла виртуальной машины, на которую балансировщик нагрузки направил трафик, как показано в следующем примере:

![Выполнение веб-сайта IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Дополнительная информация

В этом учебнике вы автоматизировали установку IIS на виртуальной машине. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Использование расширения пользовательских скриптов на установки IIS
> * Создание виртуальной машины, которая использует расширение пользовательских скриптов
> * Просмотр выполнения сайта IIS после применения расширения

Перейдите к следующему руководству, чтобы научиться создавать пользовательские образы виртуальных машин.

> [!div class="nextstepaction"]
> [Создание образа настраиваемой виртуальной машины](./tutorial-custom-images.md)
