---
title: Руководство по пулам экземпляров базы данных SQL Azure (Предварительная версия)
description: В этой статье описывается, как создавать пулы экземпляров базы данных SQL Azure (Предварительная версия) и управлять ими.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 461de4da35905ed860fb2ed9d2c655b55b777683
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689731"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Руководство по пулам экземпляров баз данных SQL Azure (Предварительная версия)

В этой статье содержатся сведения о создании [пулов экземпляров](sql-database-instance-pools.md)и управлении ими.

## <a name="instance-pool-operations"></a>Операции с пулом экземпляров

В следующей таблице показаны доступные операции, связанные с пулами экземпляров и их доступности в портал Azure и PowerShell.

|Команда|Портал Azure|PowerShell|
|:---|:---|:---|
|Создание пула экземпляров|Нет|Да|
|Обновление пула экземпляров (ограниченное число свойств)|Нет |Да |
|Проверка использования и свойств пула экземпляров|Нет|Да |
|Удаление пула экземпляров|Нет|Да|
|Создание управляемого экземпляра в пуле экземпляров|Нет|Да|
|Обновление использования ресурсов управляемого экземпляра|Да |Да|
|Проверка использования и свойств управляемого экземпляра|Да|Да|
|Удаление управляемого экземпляра из пула|Да|Да|
|Создание базы данных в управляемом экземпляре, размещенной в пуле|Да|Да|
|Удаление базы данных из управляемого экземпляра|Да|Да|

Доступные [команды PowerShell](https://docs.microsoft.com/powershell/module/az.sql/)

|Командлет |Description (Описание) |
|:---|:---|
|[New-Азсклинстанцепул](/powershell/module/az.sql/new-azsqlinstancepool/) | Создает пул экземпляров базы данных SQL Azure. |
|[Get-Азсклинстанцепул](/powershell/module/az.sql/get-azsqlinstancepool/) | Возвращает сведения о пуле экземпляров SQL Azure. |
|[Set-Азсклинстанцепул](/powershell/module/az.sql/set-azsqlinstancepool/) | Задает свойства для пула экземпляров базы данных SQL Azure. |
|[Remove-Азсклинстанцепул](/powershell/module/az.sql/remove-azsqlinstancepool/) | Удаляет пул экземпляров базы данных SQL Azure. |
|[Get-Азсклинстанцепулусаже](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Возвращает сведения об использовании пула экземпляров SQL Azure. |


Чтобы использовать PowerShell, [установите последнюю версию PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core)и следуйте инструкциям по [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Для операций, связанных с экземплярами в пулах и в одном экземпляре, используйте стандартные [команды управляемого экземпляра](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances), но при использовании этих команд для экземпляра в пуле необходимо заполнить свойство *имя пула экземпляров* .

## <a name="how-to-deploy-managed-instances-into-pools"></a>Развертывание управляемых экземпляров в пулах

Процесс развертывания экземпляра в пуле состоит из следующих двух шагов:

1. Развертывание пула экземпляров с одним отделам. Это длительная операция, в которой длительность совпадает с развертыванием [одного экземпляра, созданного в пустой подсети](sql-database-managed-instance.md#managed-instance-management-operations).

2. Повторяющееся развертывание экземпляра в пуле экземпляров. Параметр пула экземпляров должен быть явно указан в рамках этой операции. Это относительно быстрая операция, которая обычно занимает до 5 минут.

В общедоступной предварительной версии оба действия поддерживаются только с помощью шаблонов PowerShell и диспетчер ресурсов. Портал Azure интерфейс в настоящее время недоступен.

После развертывания управляемого экземпляра в пуле *можно* использовать портал Azure для изменения его свойств на странице ценовая категория.


## <a name="create-an-instance-pool"></a>Создание пула экземпляров

Чтобы создать пул экземпляров, выполните следующие действия.

1. [Создайте виртуальную сеть с подсетью](#create-a-virtual-network-with-a-subnet).
2. [Создайте пул экземпляров](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Создание виртуальной сети с подсетью. 

Сведения о размещении нескольких пулов экземпляров в одной виртуальной сети см. в следующих статьях:

- [Определите размер подсети VNet для управляемого экземпляра базы данных SQL Azure](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Создайте новую виртуальную сеть и подсеть с помощью [шаблона портал Azure](sql-database-managed-instance-create-vnet-subnet.md) или следуйте инструкциям по [подготовке существующей виртуальной сети](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Создание пула экземпляров 

После выполнения описанных выше действий можно приступать к созданию пула экземпляров.

К пулам экземпляров применяются следующие ограничения.

- В общедоступной предварительной версии доступны только общего назначения и го поколения.
- Имя пула может содержать только строчные буквы, цифры и дефис и не может начинаться с дефиса.
- Чтобы получить идентификатор подсети, используйте `Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork`.
- Если вы хотите использовать АХБ (Преимущество гибридного использования Azure), он применяется на уровне пула экземпляров. Вы можете задать тип лицензии во время создания пула или обновить его в любое время после создания.

> [!IMPORTANT]
> Развертывание пула экземпляров — это длительная операция, которая занимает примерно 4,5 часов.

Чтобы создать пул экземпляров, выполните следующие действия.

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId "/subscriptions/subscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/miPoolVirtualNetwork/subnets/miPoolSubnet" `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Поскольку развертывание пула экземпляров является длительной операцией, необходимо подождать, пока она завершится, прежде чем выполнять следующие действия в этой статье.

## <a name="create-a-managed-instance-inside-the-pool"></a>Создание управляемого экземпляра в пуле 

После успешного развертывания пула экземпляров найдется время создания в нем экземпляра.

Чтобы создать управляемый экземпляр, выполните следующую команду:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Развертывание экземпляра в пуле занимает несколько минут. После создания первого экземпляра можно создать дополнительные экземпляры:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Создание базы данных внутри экземпляра 

Для создания и управления базами данных в управляемом экземпляре, находящихся в пуле, используйте команды одного экземпляра.

Создание базы данных внутри управляемого экземпляра:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Получение сведений об использовании пула экземпляров 
 
Чтобы получить список экземпляров в пуле, выполните следующие действия.

```powershell
$instancePool | Get-AzSqlInstance
```


Чтобы получить сведения об использовании ресурсов пула, сделайте следующее:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Чтобы получить подробные сведения об использовании пула и экземпляров внутри него, сделайте следующее:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Чтобы получить список баз данных в экземпляре, выполните следующие действия.

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Существует ограничение в 100 баз данных на пул (не для каждого экземпляра).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Масштабирование управляемого экземпляра в пуле 


После заполнения управляемого экземпляра данными базы данных могут быть достигнуты ограничения на количество экземпляров в отношении хранилища или производительности. В этом случае, если использование пула не превышено, можно масштабировать экземпляр.
Масштабирование управляемого экземпляра в пуле — это операция, которая занимает несколько минут. Предварительные требования для масштабирования доступны в виртуальных ядер и хранилище на уровне пула экземпляров.

Чтобы обновить число виртуальных ядер и размер хранилища, сделайте следующее:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Обновление только размера хранилища:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Подключение к управляемому экземпляру в пуле

Чтобы подключиться к управляемому экземпляру в пуле, необходимо выполнить два следующих действия.

1. [Включите общедоступную конечную точку для экземпляра](#enable-the-public-endpoint-for-the-instance).
2. [Добавьте правило входящего трафика в группу безопасности сети (NSG)](#add-an-inbound-rule-to-the-network-security-group).

После завершения обоих шагов можно подключиться к экземпляру, используя адрес общедоступной конечной точки, порт и учетные данные, указанные во время создания экземпляра. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Включение общедоступной конечной точки для экземпляра

Включение общедоступной конечной точки для экземпляра можно выполнить с помощью портал Azure или с помощью следующей команды PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Этот параметр можно задать также во время создания экземпляра.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Добавление правила входящего трафика в группу безопасности сети 

Этот шаг можно выполнить с помощью портал Azure или с помощью команд PowerShell. это можно сделать в любое время после подготовки подсети для управляемого экземпляра.

Дополнительные сведения см. [в разделе Разрешение трафика общедоступной конечной точки в группе безопасности сети](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Перемещение существующего отдельного экземпляра в пуле экземпляров 
 
Перемещение экземпляров в пул и из него является одним из ограничений общедоступной предварительной версии. Обходной путь, который можно использовать, зависит от восстановления базы данных на момент времени из экземпляра, находящегося за пределами пула, до экземпляра, который уже находится в пуле. 

Оба экземпляра должны находиться в одной подписке и регионе. Восстановление между регионами и между подписками в настоящее время не поддерживается.

Этот процесс имеет период простоя.

Перемещение существующих баз данных:

1. Приостановка рабочих нагрузок на управляемом экземпляре, с которого выполняется миграция.
2. Создайте скрипты для создания системных баз данных и выполните их на экземпляре, который находится внутри пула экземпляров.
3. Выполните восстановление каждой базы данных на момент времени из одного экземпляра в экземпляр в пуле.

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

4. Наведите приложение на новый экземпляр и возобновите его рабочие нагрузки.

Если имеется несколько баз данных, повторите эту процедуру для каждой базы данных.


## <a name="next-steps"></a>Дальнейшие действия

- Сведения о функциях и список сравнения см. в статье [Сравнение функций Базы данных SQL Azure и SQL Server](sql-database-features.md).
- Дополнительные сведения о настройке виртуальной сети для управляемого экземпляра базы данных SQL Azure см. в [этой статье](sql-database-managed-instance-connectivity-architecture.md).
- Сведения о создании управляемого экземпляра и восстановлении базы данных из файла резервной копии см. в [этой статье](sql-database-managed-instance-get-started.md).
- Сведения об использовании Azure Database Migration Service (DMS) для миграции см. в статье о [миграции SQL Server в управляемый экземпляр базы данных SQL Azure](../dms/tutorial-sql-server-to-managed-instance.md).
- Дополнительные сведения о производительности базы данных управляемого экземпляра с помощью встроенных средств аналитики по устранению неполадок см. в статье [мониторинг базы данных SQL Azure с использованием аналитика SQL Azure](../azure-monitor/insights/azure-sql.md).
- Информацию о ценах см. на странице [цен на управляемый экземпляр Базы данных SQL Azure](https://azure.microsoft.com/pricing/details/sql-database/managed/).
