---
title: Полное регистрация виртуальных машин SQL в Azure с помощью поставщика ресурсов виртуальной машины SQL | Документация Майкрософт
description: Чтобы улучшить управляемость, можно выполнить полное регистрация SQL Server виртуальных машин с помощью поставщика ресурсов виртуальной машины SQL.
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
ms.openlocfilehash: 8498346c5623913cb68c63e95a787f31c7d90cc0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901954"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Полное регистрация виртуальных машин SQL в Azure с помощью поставщика ресурсов виртуальной машины SQL

В этой статье описывается, как выполнить массовый регистрацию SQL Server виртуальной машины в Azure с помощью поставщика ресурсов виртуальной машины SQL, используя командлет PowerShell Register-Склвмс.

Командлет Register-Склвмс можно использовать для регистрации всех виртуальных машин в заданном списке подписок, групп ресурсов или списка конкретных виртуальных машин. Командлет зарегистрирует виртуальные машины в режиме _упрощенного_ управления, а затем создаст [отчет и файл журнала](#output-description). 

Процесс регистрации не несет риска, не имеет времени простоя и не перезапускает SQL Server или виртуальную машину. 

Дополнительные сведения о поставщике ресурсов см. в разделе [поставщик ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 

## <a name="prerequisites"></a>Технические условия

Чтобы зарегистрировать виртуальную машину SQL Server с помощью поставщика ресурсов, вам потребуется следующее: 

- [Подписка Azure](https://azure.microsoft.com/free/) , [зарегистрированная в поставщике ресурсов](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) и содержащая незарегистрированные SQL Server виртуальные машины. 
- Учетные данные клиента, используемые для регистрации виртуальных машин, существуют в любой из следующих ролей RBAC: **участник виртуальной машины**, **участник**или **владелец**. 
- Последняя версия [AZ PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="getting-started"></a>Начало работы

Прежде чем продолжать, необходимо создать локальную копию скрипта, импортировать ее в качестве модуля PowerShell и подключиться к Azure. 

### <a name="create-script"></a>Создать скрипт

Чтобы создать скрипт, скопируйте [полный скрипт](#full-script) из конца этой статьи и сохраните его локально как `RegisterSqlVMs.psm1`. 

### <a name="import-script"></a>Импортировать скрипт

После создания скрипта его можно импортировать как модуль в терминале PowerShell. 

Откройте административный терминал PowerShell и перейдите к месту, где был сохранен файл `RegisterSqlVMs.psm1`. Затем выполните следующий командлет PowerShell, чтобы импортировать скрипт как модуль: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Подключение к Azure

Используйте следующий командлет PowerShell для подключения к Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Все виртуальные машины в списке подписок 

Используйте следующий командлет, чтобы зарегистрировать все SQL Server виртуальные машины в списке подписок:

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

## <a name="all-vms-in-a-single-subscription"></a>Все виртуальные машины в одной подписке

Используйте следующий командлет для регистрации всех SQL Server виртуальных машин в одной подписке: 

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

## <a name="all-vms-in-multiple-resource-groups"></a>Все виртуальные машины в нескольких группах ресурсов

Используйте следующий командлет для регистрации всех SQL Server виртуальных машин в нескольких группах ресурсов в одной подписке:

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

## <a name="all-vms-in-a-resource-group"></a>Все виртуальные машины в группе ресурсов

Используйте следующий командлет для регистрации всех SQL Server виртуальных машин в одной группе ресурсов: 

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

## <a name="specific-vms-in-single-resource-group"></a>Конкретные виртуальные машины в одной группе ресурсов

Используйте следующий командлет для регистрации конкретных SQL Server виртуальных машин в одной группе ресурсов:

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

## <a name="specific-vm"></a>Конкретная виртуальная машина

Используйте следующий командлет для регистрации конкретной SQL Server виртуальной машины: 

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

При каждом использовании командлета `Register-SqlVMs` создается и отчет, и файл журнала. 

### <a name="report"></a>Отчет

Отчет создается как файл `.txt` с именем `RegisterSqlVMScriptReport<Timestamp>.txt` где метка времени начала выполнения командлета. В отчете перечислены следующие сведения.

| **Выходное значение** | **Описание** |
| :--------------  | :-------------- | 
| Не удалось зарегистрировать число подписок, так как у вас нет доступа или учетные данные неверны | Это позволяет указать число и список подписок, для которых возникли проблемы с указанной проверкой подлинности. Подробные сведения об ошибке можно найти в журнале, выполнив поиск по ИДЕНТИФИКАТОРу подписки. | 
| Число подписок, которые не удалось попытаться выполнить, так как они не зарегистрированы в RP | В этом разделе содержится количество и список подписок, которые не были зарегистрированы в поставщике ресурсов виртуальной машины SQL. |
| Всего найдено виртуальных машин | Число виртуальных машин, обнаруженных в области параметров, переданных в командлет. | 
| Виртуальные машины уже зарегистрированы | Число виртуальных машин, которые были пропущены, так как они уже зарегистрированы в поставщике ресурсов. |
| Число успешно зарегистрированных виртуальных машин | Число виртуальных машин, успешно зарегистрированных после выполнения командлета. Список зарегистрированных виртуальных машин в формате `SubscriptionID, Resource Group, Virtual Machine`. | 
| Число виртуальных машин, которые не удалось зарегистрировать из-за ошибки | Число виртуальных машин, которые не удалось зарегистрировать из-за ошибки. Подробные сведения об ошибке можно найти в файле журнала. | 
| Число виртуальных машин, пропущенных, так как виртуальная машина или агент ветра порывах на виртуальной машине не работает | Количество и список виртуальных машин, которые не удалось зарегистрировать, так как виртуальная машина или гостевой агент на виртуальной машине не запущен. Их можно повторить после запуска виртуальной машины или гостевого агента. Подробные сведения можно найти в файле журнала. |
| Число пропущенных виртуальных машин, так как они не работают SQL Server в Windows | Число виртуальных машин, которые были пропущены, так как они не работают SQL Server или не являются виртуальными машинами Windows. Виртуальные машины перечислены в формате `SubscriptionID, Resource Group, Virtual Machine`. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Журнал 

Ошибки записываются в файл журнала с именем `VMsNotRegisteredDueToError<Timestamp>.log` где timestamp — время запуска скрипта. Если ошибка связана с уровнем подписки, то в журнале содержится идентификатор подписки, разделенный запятыми, и сообщение об ошибке. Если ошибка связана с регистрацией виртуальной машины, в журнале содержится идентификатор подписки, имя группы ресурсов, имя виртуальной машины, код ошибки и сообщение, разделенные запятыми. 

## <a name="remarks"></a>Примечания

При регистрации SQL Server виртуальных машин с поставщиком ресурсов с помощью предоставленного скрипта учитывайте следующее.

- Для регистрации в поставщике ресурсов требуется Гостевой агент, работающий на виртуальной машине SQL Server. В образах Windows Server 2008 Нет гостевого агента, поэтому эти виртуальные машины завершаются сбоем и должны быть зарегистрированы вручную с помощью [режима управления](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms)"без агента".
- Существует встроенная логика повторных попыток для преодоления прозрачных ошибок. Если виртуальная машина успешно зарегистрирована, эта операция выполняется быстро. Однако в случае сбоя регистрации каждая виртуальная машина будет повторно выполнена.  Таким образом, для завершения процесса регистрации необходимо значительное время, хотя фактические требования к времени зависят от типа и количества ошибок. 

## <a name="full-script"></a>Полный сценарий

Скопируйте полный скрипт и сохраните его как `RegisterSqLVMs.psm1`.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

Полный сценарий на сайте GitHub см. в статье [Пакетная регистрация виртуальных машин SQL с помощью команды AZ PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Часто задаваемые вопросы о SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Руководство по ценам для SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Заметки о выпуске для SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
