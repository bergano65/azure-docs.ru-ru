---
title: Обзор агента виртуальной машины Azure
description: Обзор агента виртуальной машины Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: 3d9c178201ab0c22ed4eab9cf65f7d48e59e1359
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246125"
---
# <a name="azure-virtual-machine-agent-overview"></a>Обзор агента виртуальной машины Azure
Агент виртуальной машины Microsoft Azure — это защищенный упрощенный процесс, который управляет взаимодействием виртуальной машины с контроллером структуры Azure. Основная роль агента виртуальной машины — это включение и выполнение расширений виртуальной машины Azure. Расширения виртуальной машины позволяют выполнять дополнительные действия по настройке виртуальной машины после развертывания, например устанавливать и настраивать программное обеспечение. Они также предоставляют возможности восстановления, такие как сброс пароля администратора виртуальной машины. Расширения виртуальной машины не могут выполняться без агента виртуальной машины Azure.

В этой статье подробно описана установка и обнаружение виртуального машинного агента Azure.

## <a name="install-the-vm-agent"></a>"Установить агент виртуальной машины"

### <a name="azure-marketplace-image"></a>Образ из Azure Marketplace

Агент виртуальной машины Azure устанавливается по умолчанию на любой виртуальной машине Windows, развернутой с помощью образа из Azure Marketplace. При развертывании образа из Azure Marketplace с помощью портала, PowerShell, интерфейса командной строки или шаблона Azure Resource Manager также устанавливается агент виртуальной машины.

Пакет гостевого агента Windows состоит из двух частей:

- агент подготовки (PA);
- гостевой агент Windows (WinGA).

Для загрузки виртуальной машины на ней должен быть установлен агент подготовки. Устанавливать WinGA не требуется. Во время развертывания виртуальной машины можно выбрать вариант без установки WinGA. В следующем примере показано, как выбрать параметр *provisionVmAgent* с помощью шаблона Azure Resource Manager.

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Если агенты не установлены, вы не сможете использовать некоторые службы Azure, такие как Azure Backup или Azure Security. Для работы этих служб требуется установить расширение. Если виртуальная машина была развернута без WinGA, установить последнюю версию агента можно позднее.

### <a name="manual-installation"></a>Ручная установка
Агент виртуальной машины Windows можно установить вручную с помощью пакета установщика Windows. Установка вручную может потребоваться при создании пользовательского образа виртуальной машины, который будет развернут в Azure. Чтобы вручную установить агент виртуальной машины Windows, [скачайте установщик агента виртуальной машины](https://go.microsoft.com/fwlink/?LinkID=394789). Агент VM поддерживается на Windows Server 2008 R2 и позже.

> [!NOTE]
> Важно обновить опцию AllowExtensionOperations после ручной установки VMAgent на VM, который был развернут из изображения без включения ProvisionVMAgent.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Предварительные требования
- Агенту Windows VM для запуска по крайней мере Windows Server 2008 R2 (64 бита) необходимо работать с системой .Net 4.0. Смотрите [минимальную поддержку версий для виртуальных машинных агентов в Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Убедитесь, что ваш VM имеет доступ к IP-адресу 168.63.129.16. Для получения дополнительной информации смотрите [Что такое IP-адрес 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

## <a name="detect-the-vm-agent"></a>Обнаружение агента виртуальной машины

### <a name="powershell"></a>PowerShell

Для получения сведений о виртуальных машинах Azure можно воспользоваться модулем PowerShell Azure Resource Manager. Чтобы просмотреть сведения о виртуальной машине, например состояние подготовки агента виртуальной машины, выполните командлет [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

В следующем сокращенном примере выходных данных показано свойство *ProvisionVMAgent*, вложенное в *OSProfile*. С помощью этого свойства можно определить, развернут ли на виртуальной машине агент виртуальной машины.

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Следующий скрипт можно использовать для получения краткого списка имен виртуальных машин и состояния агента виртуальной машины.

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Обнаружение вручную

После входа в виртуальную машину Windows для проверки запущенных процессов можно использовать диспетчер задач. Чтобы обнаружить агент виртуальной машины Azure, откройте диспетчер задач, перейдите на вкладку *Подробности* и найдите процесс с именем **WindowsAzureGuestAgent.exe**. Наличие этого процесса означает, что агент виртуальной машины установлен.


## <a name="upgrade-the-vm-agent"></a>Установка агента виртуальной машины
Агент виртуальной машины Azure для Windows обновляется автоматически. Так как новые виртуальные машины развертываются в Azure, во время подготовки виртуальной машины на них устанавливается последняя версия агента виртуальной машины. Чтобы во время создания образа на пользовательских образах виртуальных машин устанавливалась новая версия агента виртуальной машины, их необходимо обновлять вручную.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Windows Гостевой агент Автоматические журналы Коллекция
У гостевого агента Windows есть функция для автоматического сбора некоторых журналов. Эта функция является контроллером процесса CollectGuestLogs.exe. Она существует как для Облачных служб PaaS, так и для виртуальных машин IaaS, и его цель состоит в том, чтобы быстро & автоматически собирать некоторые журналы диагностики из VM - так что они могут быть использованы для автономного анализа. Собранные журналы: журналы событий, журналы ОС, журналы Azure и некоторые ключи реестра. Он производит файл, который передается хост VM. Этот файл может быть рассмотрен инженерными группами и специалистами службы поддержки для расследования проблем по запросу клиента, владеющего VM.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о расширениях виртуальных машин см. в статье c [обзором расширений и компонентов виртуальной машины Azure](overview.md).
