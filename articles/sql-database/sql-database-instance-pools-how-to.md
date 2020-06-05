---
title: Руководство по пулам экземпляров (предварительная версия)
description: В этой статье описывается, как создавать пулы экземпляров базы данных SQL Azure (предварительная версия) и управлять ими.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 005ed634830190a947045964ff01d126853bdc64
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773047"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Руководство по пулам экземпляров баз данных SQL Azure (предварительная версия)

В этой статье содержатся сведения о создании [пулов экземпляров](sql-database-instance-pools.md) и управлении ими.

## <a name="instance-pool-operations"></a>Операции с пулом экземпляров

В следующей таблице показаны доступные операции, связанные с пулами экземпляров, и их доступность на портале Azure и PowerShell.

|Get-Help|Портал Azure|PowerShell|
|:---|:---|:---|
|Создание пула экземпляров|Нет|Да|
|Обновление пула экземпляров (ограниченное число свойств)|Нет |Да |
|Проверка использования и свойств пула экземпляров|Нет|Да |
|Удаление пула экземпляров|Нет|Да|
|Создание управляемого экземпляра в пуле экземпляров|Нет|Да|
|Обновление использования ресурсов управляемого экземпляра|Да |Да|
|Проверка использования и свойств управляемого экземпляра|Да|Да|
|Удаление управляемого экземпляра из пула|Да|Да|
|Создание базы данных в управляемом экземпляре, размещенном в пуле|Да|Да|
|Удаление базы данных из управляемого экземпляра|Да|Да|

Доступные [команды PowerShell](https://docs.microsoft.com/powershell/module/az.sql/)

|Командлет |Описание |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Создает пул экземпляров Базы данных SQL Azure. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Возвращает информацию о пуле экземпляров SQL Azure. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Задает свойства для пула экземпляров Базы данных SQL Azure. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Удаляет пул экземпляров Базы данных SQL Azure. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Возвращает информацию об использовании пула экземпляров SQL Azure. |


Чтобы использовать PowerShell, [установите последнюю версию PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell) и следуйте инструкциям по [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Для операций, относящихся к экземплярам внутри пулов и отдельным экземплярам, используйте стандартные [команды управляемого экземпляра](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances), но при использовании этих команд для экземпляра в пуле необходимо заполнить свойство *имени пула экземпляров*.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Развертывание управляемых экземпляров в пулах

Процесс развертывания экземпляра в пуле состоит из следующих двух шагов.

1. Однократное развертывание пула экземпляров. Это долгая операция, которая требует столько же времени, сколько [развертывание единственного экземпляра, созданного в пустой подсети](sql-database-managed-instance.md#managed-instance-management-operations).

2. Повторяющееся развертывание экземпляра в пуле экземпляров. Параметр пула экземпляров должен быть явно указан в рамках этой операции. Это относительно быстрая операция, которая обычно занимает до 5 минут.

В общедоступной предварительной версии оба действия поддерживаются только с помощью шаблонов PowerShell и Resource Manager. Портал Azure в настоящее время недоступен.

После развертывания управляемого экземпляра в пуле *можно* использовать портал Azure для изменения свойств на странице ценовой категории.


## <a name="create-an-instance-pool"></a>Создание пула экземпляров

Создание пула экземпляров:

1. [Создание виртуальной сети с подсетью](#create-a-virtual-network-with-a-subnet).
2. [Создание пула экземпляров](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Создание виртуальной сети с подсетью. 

Сведения о размещении нескольких пулов экземпляров в одной виртуальной сети см. в следующих статьях.

- [Определение размера подсети виртуальной сети для управляемого экземпляра Базы данных SQL Azure](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Создайте новую виртуальную сеть и подсеть с помощью [шаблона портала Azure](sql-database-managed-instance-create-vnet-subnet.md) или следуйте инструкциям для [подготовки существующей виртуальной сети](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Создание пула экземпляров 

После выполнения описанных выше действий можно приступать к созданию пула экземпляров.

На пулы экземпляров налагаются следующие ограничения.

- В общедоступной предварительной версии доступны только пулы общего назначения и 5-го поколения.
- Имя пула может содержать только строчные буквы, цифры и дефис и не может начинаться с дефиса.
- Если вы хотите использовать AHB (преимущество гибридного использования Azure), оно применяется на уровне пула экземпляров. Вы можете задать тип лицензии во время создания пула или изменить его в любое время после создания.

> [!IMPORTANT]
> Развертывание пула экземпляров — это длительная операция, которая занимает примерно 4,5 часа.

Получение параметров сети:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Создание пула экземпляров:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Поскольку развертывание пула экземпляров является длительной операцией, необходимо подождать, пока она завершится, прежде чем выполнять следующие действия в этой статье.

## <a name="create-a-managed-instance-inside-the-pool"></a>Создание управляемого экземпляра в пуле 

После успешного развертывания пула экземпляров можно создать в нем экземпляр.

Чтобы создать управляемый экземпляр, выполните следующую команду:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Развертывание экземпляра в пуле занимает несколько минут. После создания первого экземпляра можно создать дополнительные экземпляры:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Создание базы данных внутри экземпляра 

Для создания баз данных и управления ими в управляемом экземпляре в пуле, используйте команды одного экземпляра.

Создание базы данных в управляемом экземпляре:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Получение сведений об использовании пула экземпляров 
 
Получение списка экземпляров в пуле:

```powershell
$instancePool | Get-AzSqlInstance
```


Получение сведений об использовании ресурсов пула:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Получение подробных сведений об использовании пула и экземпляров внутри него:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Получение списка баз данных в экземпляре:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Существует ограничение в 100 баз данных на пул (не для каждого экземпляра).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Масштабирование управляемого экземпляра в пуле 


После заполнения управляемого экземпляра базами данных вы можете достичь ограничения на количество экземпляров в связи с хранением или производительностью. В этом случае, если ограничение на использование пула не превышено, можно масштабировать экземпляр.
Масштабирование управляемого экземпляра в пуле занимает несколько минут. Предварительные требования для масштабирования — виртуальные ядра и хранилище на уровне пула экземпляров.

Обновление числа виртуальных ядер и размера хранилища:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Обновление только размера хранилища:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Подключение к управляемому экземпляру в пуле

Чтобы подключиться к управляемому экземпляру в пуле, необходимо выполнить два следующих действия:

1. [Включите общедоступную конечную точку для экземпляра](#enable-the-public-endpoint-for-the-instance).
2. [Добавьте правило входящего трафика для группы безопасности сети](#add-an-inbound-rule-to-the-network-security-group).

После выполнения обоих шагов можно подключиться к экземпляру, используя адрес общедоступной конечной точки, порт и учетные данные, указанные во время создания экземпляра. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Включение общедоступной конечной точки для экземпляра

Включение общедоступной конечной точки для экземпляра можно выполнить на портале Azure или с помощью следующей команды PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Этот параметр можно также задать во время создания экземпляра.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Добавление правила входящего трафика для группы безопасности сети 

Этот шаг можно выполнить на портале Azure или с помощью команд PowerShell. Это можно сделать в любое время после подготовки подсети для управляемого экземпляра.

Дополнительные сведения см. в разделе [Разрешение общедоступного трафика конечной точки в группе безопасности сети](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Перемещение существующего отдельного экземпляра в пуле экземпляров 
 
Перемещение экземпляров в пул и из него является одним из ограничений общедоступной предварительной версии. Обходной путь — восстановление базы данных на момент времени из экземпляра, находящегося за пределами пула, в экземпляр, который уже находится в пуле. 

Оба экземпляра должны относиться к той же подписке и тому же региону. Резервное копирование между регионами и восстановление между подписками в настоящее время не поддерживается.

Этот процесс подразумевает период простоя.

Перемещение существующих баз данных:

1. Приостановите рабочие нагрузки на управляемом экземпляре, с которого выполняется миграция.
2. Создайте скрипты для создания системных баз данных и выполните их на экземпляре, который находится внутри пула экземпляров.
3. Выполните восстановление каждой базы данных на момент времени из единичного экземпляра в экземпляр в пуле.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Укажите приложению на новый экземпляр и возобновите его рабочие нагрузки.

Если имеется несколько баз данных, повторите эту процедуру для каждой базы данных.


## <a name="next-steps"></a>Дальнейшие действия

- Сведения о функциях и список сравнения см. в статье [Сравнение функций Базы данных SQL Azure и SQL Server](sql-database-features.md).
- Дополнительные сведения о настройке виртуальной сети для управляемого экземпляра базы данных SQL Azure см. в [этой статье](sql-database-managed-instance-connectivity-architecture.md).
- Сведения о создании управляемого экземпляра и восстановлении базы данных из файла резервной копии см. в [этой статье](sql-database-managed-instance-get-started.md).
- Сведения об использовании Azure Database Migration Service (DMS) для миграции см. в статье о [миграции SQL Server в управляемый экземпляр базы данных SQL Azure](../dms/tutorial-sql-server-to-managed-instance.md).
- Сведения о расширенном мониторинге производительности для управляемого экземпляра базы данных с использованием встроенных средств анализа проблем см. в статье о [мониторинге Базы данных SQL Azure с помощью Аналитики SQL Azure](../azure-monitor/insights/azure-sql.md).
- Информацию о ценах см. на странице [цен на управляемый экземпляр Базы данных SQL Azure](https://azure.microsoft.com/pricing/details/sql-database/managed/).
