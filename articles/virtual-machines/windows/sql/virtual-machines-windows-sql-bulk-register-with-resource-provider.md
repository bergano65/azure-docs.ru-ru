---
title: Массовый регистр виртуальных машин в Azure с поставщиком ресурсов S'L VM Документы Майкрософт
description: Массовый регистр спомощьи s'L Server VMs с поставщиком ресурсов S'L VM для повышения управляемости.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353882"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Массовый регистр виртуальных машин в Azure с помощью поставщика ресурсов S'L VM

В этой статье описывается, как навалом зарегистрировать виртуальную машину (VM) в `Register-SqlVMs` Azure с поставщиком ресурсов S'L VM с помощью cmdlet PowerShell.

Cmdlet `Register-SqlVMs` может быть использован для регистрации всех виртуальных машин в данном списке подписок, групп ресурсов или в списке конкретных виртуальных машин. Cmdlet будет регистрировать виртуальные машины в _легком_ режиме управления, а затем генерировать как отчет, так [и файл журнала.](#output-description) 

Процесс регистрации не несет риска, не имеет простоев и не будет перезапускать сервер s или виртуальную машину. 

Для получения более подробной информации о поставщике [ресурсов,](virtual-machines-windows-sql-register-with-resource-provider.md)см. 

## <a name="prerequisites"></a>Предварительные требования

Для регистрации VM сервера S'L у поставщика ресурсов необходимо следующее: 

- [Подписка Azure,](https://azure.microsoft.com/free/) [зарегистрированная у поставщика ресурсов](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) и содержащая незарегистрированные виртуальные машины сервера S'L Server. 
- Учетные данные клиента, используемые для регистрации виртуальных машин, существуют в любой из следующих ролей RBAC: **Virtual Machine contributor,** **Contributor**, или **Owner.** 
- Последняя версия [Az PowerShell](/powershell/azure/new-azureps-module-az). 
- Последняя версия [Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="getting-started"></a>Начало работы

Прежде чем приступить к работе, необходимо сначала создать локальную копию скрипта, импортировать его в качестве модуля PowerShell и подключиться к Azure. 

### <a name="create-script"></a>Создание скрипта

Чтобы создать сценарий, скопируйте [полный сценарий](#full-script) из конца `RegisterSqlVMs.psm1`этой статьи и сохраните его локально как. 

### <a name="import-script"></a>Сценарий импорта

После создания скрипта его можно импортировать в качестве модуля в терминале Powershell. 

Откройте административный терминал PowerShell и `RegisterSqlVMs.psm1` перейдите туда, где вы сохранили файл. Затем запустите следующий смдlet PowerShell, чтобы импортировать скрипт в качестве модуля: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Подключение к Azure

Используйте следующий смдлет PowerShell для подключения к Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Все ВМ в списке подписок 

Используйте следующие cmdlet для регистрации всех виртуальных машин S'L Server в списке подписок:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Выходные данные примера: 

```
Number of Subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Все ВМ в одной подписке

Используйте следующие cmdlet для регистрации всех виртуальных машин S'L Server в одной подписке: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Выходные данные примера:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Все ВМ в нескольких группах ресурсов

Используйте следующие cmdlet для регистрации всех виртуальных машин S'L Server в нескольких группах ресурсов в рамках одной подписки:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Выходные данные примера:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Все ВМ в группе ресурсов

Используйте следующие cmdlet для регистрации всех виртуальных машин S'L Server в одной группе ресурсов: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Выходные данные примера:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-single-resource-group"></a>Конкретные ВМ в одной группе ресурсов

Используйте следующие cmdlet для регистрации определенных виртуальных машин s'L Server в одной группе ресурсов:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Выходные данные примера:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vm"></a>Конкретные VM

Используйте следующие cmdlet для регистрации определенной виртуальной машины S'L Server: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Выходные данные примера:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Описание вывода

Файл отчета и журнал генерируется `Register-SqlVMs` каждый раз при использовании cmdlet. 

### <a name="report"></a>Report

Отчет генерируется как `.txt` файл `RegisterSqlVMScriptReport<Timestamp>.txt` с именем, где метка времени — это время запуска cmdlet. В докладе перечислены следующие детали:

| **Выходное значение** | **Описание** |
| :--------------  | :-------------- | 
| Количество регистрации подписок не удалось, потому что у вас нет доступа или учетные данные неверны | Это приводит номер и список подписок, у которых были проблемы с предоставленной аутентификацией. Подробную ошибку можно найти в журнале, испоискив идентификатор подписки. | 
| Количество подписок, которые не могут быть опробованы, поскольку они не зарегистрированы в RP | Этот раздел содержит количество и список подписок, которые не были зарегистрированы на поставщика ресурсов S'L VM. |
| Найдено всего VM | Подсчет виртуальных машин, найденных в сфере параметров, перешел на cmdlet. | 
| VMs уже зарегистрированы | Количество виртуальных машин, которые были пропущены, поскольку они уже были зарегистрированы в поставщике ресурсов. |
| Количество зарегистрированных ВМ успешно | Количество виртуальных машин, которые были успешно зарегистрированы после запуска cmdlet. Перечисляет зарегистрированные виртуальные `SubscriptionID, Resource Group, Virtual Machine`машины в формате . | 
| Количество ВМ не удалось зарегистрировать из-за ошибки | Граф виртуальных машин, которые не зарегистрировались из-за какой-то ошибки. Подробную информацию об ошибке можно найти в файле журнала. | 
| Количество VMs, пропущенных в качестве VM или агента порывов на VM, не работает | Подсчет и список виртуальных машин, которые не могут быть зарегистрированы ни как виртуальная машина или гостевой агент на виртуальной машине не работает. Они могут быть повторены после запуска виртуальной машины или гостевого агента. Подробности можно найти в файле журнала. |
| Количество пропущенных VMs-компьютеров, поскольку они не работают под управлением сервера S'L на Windows | Количество виртуальных машин, которые были пропущены, поскольку они не работают с сервером S'L или не являются виртуальной машиной Windows. Виртуальные машины перечислены `SubscriptionID, Resource Group, Virtual Machine`в формате . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Журнал 

Ошибки регистрируются в `VMsNotRegisteredDueToError<Timestamp>.log` файле журнала, названном местом, где метка времени — это время начала скрипта. Если ошибка находится на уровне подписки, журнал содержит запятую подписку и сообщение об ошибке. Если ошибка связана с регистрацией виртуальной машины, журнал содержит идентификатор подписки, название группы ресурсов, имя виртуальной машины, код ошибки и сообщение, разделенное запятыми. 

## <a name="remarks"></a>Remarks

При регистрации VMs-сервера s'L Server с помощью предоставленного скрипта следует следующее:

- Регистрация у поставщика ресурсов требует, чтобы гостевой агент работал на VM сервера S'L. Windows Server 2008 изображения не имеют гостевой агент, так что эти виртуальные машины не удастся и должны быть зарегистрированы вручную с помощью [режима управления NoAgent](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes).
- Существует логика повторная попытка, встроенная в преодоление прозрачных ошибок. Если виртуальная машина успешно зарегистрирована, то это быстрая работа. Однако, если регистрация не удается, то каждая виртуальная машина будет повторно.  Таким образом, вы должны предоставить значительное время для завершения процесса регистрации - хотя фактическое требование времени зависит от типа и количества ошибок. 

## <a name="full-script"></a>Полный сценарий

Полный сценарий на GitHub можно [оформить](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1)на GitHub, см. 

Копировать полный сценарий `RegisterSqLVMs.psm1`и сохранить его как .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Часто задаваемые вопросы для сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Руководство по ценообразованию для сервера S'L на Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Выпуск примечаний для сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)
