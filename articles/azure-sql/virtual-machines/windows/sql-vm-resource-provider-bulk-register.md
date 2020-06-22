---
title: Массовая регистрация виртуальных машин SQL в Azure с помощью поставщика ресурсов виртуальных машин SQL | Документация Майкрософт
description: Массовая регистрация виртуальных машин SQL Server с помощью поставщика ресурсов виртуальной машины SQL для улучшения управляемости.
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
ms.openlocfilehash: 49b669627507af0e3e7386f31e344082cc4686df
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219306"
---
# <a name="register-multiple-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Регистрация нескольких виртуальных машин SQL в Azure с помощью поставщика ресурсов виртуальных машин SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)][!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Эта статья содержит сведения о массовой регистрации виртуальных машин SQL Server в Azure с помощью поставщика ресурсов виртуальной машины SQL, используя командлет PowerShell `Register-SqlVMs`.

Командлет `Register-SqlVMs` можно использовать для регистрации всех виртуальных машин в заданном списке подписок, групп ресурсов или в списке конкретных виртуальных машин. Командлет зарегистрирует виртуальные машины в режиме _упрощенного_ управления, а затем создаст [отчет и файл журнала](#output-description). 

Во время процесса регистрации отсутствуют риски и время простоя, а SQL Server и виртуальная машина не будут перезагружены. 

Дополнительные сведения о поставщике ресурсов виртуальной машины SQL см. в [этой статье](sql-vm-resource-provider-register.md). 

## <a name="prerequisites"></a>Предварительные требования

Для регистрации виртуальной машины SQL Server с помощью поставщика ресурсов понадобится: 

- [Подписка Azure](https://azure.microsoft.com/free/), которая [зарегистрирована с помощью поставщика ресурсов](sql-vm-resource-provider-register.md#register-subscription-with-rp) и содержит незарегистрированные виртуальные машины SQL Server. 
- Учетные данные клиента, используемые для регистрации виртуальных машин, есть в любой из следующих ролей RBAC: **Участник виртуальных машин**, **Участник** или **Владелец**. 
- Последняя версия [Az PowerShell](/powershell/azure/new-azureps-module-az). 
- Последняя версия [Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="get-started"></a>Начало работы

Прежде чем продолжить, необходимо создать локальную копию скрипта, импортировать ее в качестве модуля PowerShell и подключиться к Azure. 

### <a name="create-the-script"></a>Создание скрипта

Чтобы создать скрипт, скопируйте [полный скрипт](#full-script) в конце этой статьи и сохраните его локально как `RegisterSqlVMs.psm1`. 

### <a name="import-the-script"></a>Импорт скрипта

После создания скрипта его можно импортировать как модуль в терминал PowerShell. 

Откройте административный терминал PowerShell и перейдите к сохраненному файлу `RegisterSqlVMs.psm1`. Чтобы импортировать скрипт как модуль, выполните следующий командлет PowerShell: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Подключение к Azure

Для подключения к Azure используйте следующий командлет PowerShell:

```powershell-interactive
Connect-AzAccount
```


## <a name="register-all-vms-in-a-list-of-subscriptions"></a>Регистрация всех виртуальных машин в списке подписок 

Используйте следующий командлет, чтобы зарегистрировать все виртуальные машины SQL Server в списке подписок:

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

## <a name="register-all-vms-in-a-single-subscription"></a>Регистрация всех виртуальных машин в одной подписке

Используйте следующий командлет, чтобы зарегистрировать все виртуальные машины SQL Server в одной подписке: 

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

## <a name="register-all-vms-in-multiple-resource-groups"></a>Регистрация всех виртуальных машин в нескольких группах ресурсов

Используйте следующий командлет, чтобы зарегистрировать все виртуальные машины SQL Server в нескольких группах ресурсов в пределах одной подписки:

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

## <a name="resister-all-vms-in-a-resource-group"></a>Регистрация всех виртуальных машин в группе ресурсов

Используйте следующий командлет, чтобы зарегистрировать все виртуальные машины SQL Server в одной группе ресурсов: 

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

## <a name="register-specific-vms-in-single-resource-group"></a>Регистрация конкретных виртуальных машин в одной группе ресурсов

Используйте следующий командлет, чтобы зарегистрировать конкретные виртуальные машины SQL Server в одной группе ресурсов:

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

## <a name="register-a-specific-vm"></a>Регистрация конкретной виртуальной машины

Используйте следующий командлет, чтобы зарегистрировать конкретные виртуальные машины SQL Server: 

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


## <a name="output-description"></a>Описание выходных данных

Отчет и файл журнала создаются каждый раз при использовании командлета `Register-SqlVMs`. 

### <a name="report"></a>Report

Отчет создается как файл `.txt` с именем `RegisterSqlVMScriptReport<Timestamp>.txt`, в котором время начала выполнения командлета используется в качестве метки времени. В отчете перечислены следующие сведения:

| **Выходное значение** | **Описание** |
| :--------------  | :-------------- | 
| Количество подписок, которые не удалось зарегистрировать, так как у вас нет доступа или учетные данные неверны | Предоставляет число и список подписок, в которых возникли проблемы с указанной проверкой подлинности. Подробные сведения об ошибке можно найти в журнале, выполнив поиск по идентификатору подписки. | 
| Количество подписок, которые не удалось опробовать, так как они не зарегистрированы в поставщике ресурсов | В этом разделе содержится количество и список подписок, которые не были зарегистрированы в поставщике ресурсов виртуальной машины SQL. |
| Общее число найденных виртуальных машин | Число виртуальных машин, обнаруженных в области параметров, переданных в командлет. | 
| Количество уже зарегистрированных виртуальных машин | Количество виртуальных машин, которые были пропущены, так как они уже зарегистрированы в поставщике ресурсов. |
| Количество успешно зарегистрированных виртуальных машин | Количество виртуальных машин, успешно зарегистрированных после выполнения командлета. Список зарегистрированных виртуальных машин в формате `SubscriptionID, Resource Group, Virtual Machine`. | 
| Количество виртуальных машин, которые не удалось зарегистрировать из-за ошибки | Количество виртуальных машин, которые не удалось зарегистрировать из-за ошибки. Подробные сведения об ошибке можно найти в файле журнала. | 
| Количество виртуальных машин, пропущенных из-за того, что не запущена виртуальная машина или расположенный на ней гостевой агент | Количество и список виртуальных машин, которые не удалось зарегистрировать, так как не запущена виртуальная машина или расположенный на ней гостевой агент. Регистрацию можно повторить после запуска виртуальной машины или гостевого агента. Дополнительные сведения см. в файле журнала. |
| Количество пропущенных виртуальных машин, на которых не запущен SQL Server в Windows | Количество виртуальных машин, которые были пропущены, так как на них не запущен SQL Server или они не являются виртуальными машинами Windows. Виртуальные машины перечислены в формате `SubscriptionID, Resource Group, Virtual Machine`. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Журнал 

Ошибки записываются в файл журнала с именем `VMsNotRegisteredDueToError<Timestamp>.log`, в котором время запуска скрипта используется в качестве метки времени. Если ошибка возникла на уровне подписки, в журнале содержится идентификатор подписки и сообщение об ошибке (с разделителями-запятыми). Если ошибка связана с регистрацией виртуальной машины, в журнале содержится идентификатор подписки, имя группы ресурсов, имя виртуальной машины, код ошибки и сообщение, разделяемые запятыми. 

## <a name="remarks"></a>Remarks

При регистрации виртуальных машин SQL Server с помощью поставщика ресурсов с использованием предоставленного скрипта учитывайте следующее:

- Для выполнения регистрации с помощью поставщика ресурсов требуется гостевой агент, работающий на виртуальной машине SQL Server. Образы Windows Server 2008 не имеют гостевого агента, поэтому эти виртуальные машины завершаются сбоем и должны быть зарегистрированы вручную с помощью [режима управления NoAgent](sql-vm-resource-provider-register.md#management-modes).
- Существует встроенная логика повторных попыток для устранения прозрачных ошибок. Если виртуальная машина успешно зарегистрирована, эта операция выполняется быстро. Однако в случае сбоя регистрации каждая виртуальная машина будет выполнена повторно.  Таким образом, для завершения процесса регистрации необходимо значительное время, хотя требования к фактическому времени зависят от типа и количества ошибок. 

## <a name="full-script"></a>Полный сценарий

Полный скрипт см. на сайте GitHub со сведениями [о массовой регистрации виртуальных машин SQL с помощью модуля Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Скопируйте полный скрипт и сохраните его как файл `RegisterSqLVMs.psm1`.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](sql-server-on-azure-vm-iaas-what-is-overview.md).
* [Вопросы и ответы об SQL Server на виртуальных машинах Windows](frequently-asked-questions-faq.md)
* [Руководство по ценам для виртуальных машин SQL Server в Azure](pricing-guidance.md)
* [Заметки о выпуске SQL Server на виртуальных машинах Windows](../../database/doc-changes-updates-release-notes.md)
