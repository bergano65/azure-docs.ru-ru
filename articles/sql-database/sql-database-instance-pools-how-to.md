---
title: Инстанции пулы как-к-руководству (предварительный просмотр)
description: В этой статье описывается, как создавать и управлять пулами экземпляров данных данных Azure S'L (предварительный просмотр).
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
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299368"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Пулы экземпляров баз данных Azure S'L (предварительный просмотр) руководства

В этой статье приводится подробная информация о том, как создавать и управлять [пулами экземпляров.](sql-database-instance-pools.md)

## <a name="instance-pool-operations"></a>Операции пула инстанций

В следующей таблице показаны доступные операции, связанные с пулами экземпляров и их наличием на портале Azure и PowerShell.

|Команда|Портал Azure|PowerShell|
|:---|:---|:---|
|Создание пула экземпляров|нет|Да|
|Пул экземпляров обновления (ограниченное количество свойств)|нет |Да |
|Проверка использования и свойств пула экземпляров|нет|Да |
|Удаление пула экземпляров|нет|Да|
|Создание управляемого экземпляра в пуле экземпляров|нет|Да|
|Обновление управляемого использования ресурсов экземпляра|Да |Да|
|Проверка управляемого использования экземпляра и свойств|Да|Да|
|Удаление управляемого экземпляра из пула|Да|Да|
|Создание базы данных в управляемом экземпляре, размещенном в пуле|Да|Да|
|Удаление базы данных из управляемого экземпляра|Да|Да|

Доступные [команды PowerShell](https://docs.microsoft.com/powershell/module/az.sql/)

|Командлет |Описание |
|:---|:---|
|[Нью-АзСклInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Создает пул экземпляров базы данных Azure S'L. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Возвращает информацию о пуле экземпляров Azure S'L. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Устанавливает свойства для экземпляра базы данных Azure S'L. |
|[Удалить-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Удаляет пул данных группы данных azure S'L. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Возвращает информацию об использовании пула экземпляров Azure S'L. |


Чтобы использовать PowerShell, [установите последнюю версию PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)и следуйте инструкциям по [установке модуля Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-az-ps)

Для операций, связанных с экземплярами как внутри пулов, так и отдельных экземпляров, используйте [стандартные управляемые команды экземпляра,](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)но свойство *имени экземпляра* должно быть заселено при использовании этих команд для экземпляра в пуле.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Как развернуть управляемые экземпляры в пулы

Процесс развертывания экземпляра в пул состоит из следующих двух этапов:

1. Развертывание одноразового пула экземпляров. Это длительная операция, продолжительность которой совпадает с развертыванием [одного экземпляра, созданного в пустой подсети.](sql-database-managed-instance.md#managed-instance-management-operations)

2. Повторное развертывание экземпляров в пуле экземпляров. Параметр пула экземпляров должен быть четко указан как часть этой операции. Это относительно быстрая операция, которая обычно занимает до 5 минут.

В общедоступном предварительном просмотре оба шага поддерживаются только с помощью шаблонов PowerShell и Resource Manager. Опыт портала Azure в настоящее время недоступен.

После развертывания управляемого экземпляра в пул *можно* использовать портал Azure для изменения его свойств на странице уровня ценообразования.


## <a name="create-an-instance-pool"></a>Создание пула экземпляров

Для создания экземпляра пула:

1. [Создайте виртуальную сеть с подсетью.](#create-a-virtual-network-with-a-subnet)
2. [Создание пула экземпляров.](#create-an-instance-pool)


### <a name="create-a-virtual-network-with-a-subnet"></a>Создание виртуальной сети с подсетью. 

Чтобы разместить несколько экземпляров бассейнов в одной виртуальной сети, см.

- [Определите размер подсети VNet для управляемого экземпляра базы данных Azure S'L.](sql-database-managed-instance-determine-size-vnet-subnet.md)
- Создайте новую виртуальную сеть и подсеть с помощью [шаблона портала Azure](sql-database-managed-instance-create-vnet-subnet.md) или следуйте инструкциям по [подготовке существующей виртуальной сети.](sql-database-managed-instance-configure-vnet-subnet.md)
 


### <a name="create-an-instance-pool"></a>Создание пула экземпляров 

После завершения предыдущих шагов вы готовы создать пул экземпляров.

Следующие ограничения применяются к пулам экземпляров:

- Только Общее назначение и Gen5 доступны в открытом доступе.
- Название пула может содержать только нижний регистр, цифры и дефис, и не может начаться с дефиса.
- Если вы хотите использовать AHB (Azure Hybrid Benefit), он применяется на уровне пула экземпляров. Вы можете установить тип лицензии во время создания пула или обновить его в любое время после создания.

> [!IMPORTANT]
> Развертывание экземпляра — это длительная операция, которая занимает около 4,5 часов.

Для получения параметров сети:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Для создания экземпляра пула:

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
> Поскольку развертывание пула экземпляров — это длительная операция, необходимо подождать, пока она не завершится, прежде чем выполнить любой из следующих шагов в этой статье.

## <a name="create-a-managed-instance-inside-the-pool"></a>Создание управляемого экземпляра внутри пула 

После успешного развертывания пула экземпляров пришло время создать экземпляр внутри него.

Чтобы создать управляемый экземпляр, выполните следующую команду:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Развертывание экземпляра в бассейне занимает несколько минут. После создания первой инстанции могут быть созданы дополнительные экземпляры:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Создание базы данных внутри экземпляра 

Для создания и управления базами данных в управляемом экземпляре, наемном пуле, используйте команды одного экземпляра.

Для создания базы данных внутри управляемого экземпляра:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Использование пула экземпляров 
 
Чтобы получить список экземпляров внутри пула:

```powershell
$instancePool | Get-AzSqlInstance
```


Чтобы получить использование ресурсов пула:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Чтобы получить подробный обзор использования пула и экземпляров внутри него:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Чтобы перечислить базы данных в экземпляре:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Существует ограничение в 100 баз данных на пул (не в каждом экземпляре).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Масштабирование управляемого экземпляра внутри пула 


После заполнения управляемого экземпляра базами данных можно нажать на пределы экземпляра, касающиеся хранения или производительности. В этом случае, если использование пула не было превышено, можно масштабировать экземпляр.
Масштабирование управляемого экземпляра внутри пула — это операция, которая занимает несколько минут. Предпосылкой для масштабирования являются vCores и хранение на уровне пула экземпляров.

Чтобы обновить количество vCores и размер хранилища:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Для обновления только размера хранилища:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Подключение к управляемому экземпляру внутри пула

Для подключения к управляемому экземпляру в пуле требуются следующие два шага:

1. [Включить публичную конечную точку для экземпляра.](#enable-the-public-endpoint-for-the-instance)
2. [Добавьте входящий правило в группу сетевой безопасности (NSG).](#add-an-inbound-rule-to-the-network-security-group)

После завершения обоих этапов можно подключиться к экземпляру, используя общедоступный адрес конечных точек, портины и учетные данные, предоставленные во время создания экземпляра. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Включить публичную конечную точку для экземпляра

Включение общедоступной точки назначения для экземпляра может быть сделано через портал Azure или с помощью следующей команды PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Этот параметр также может быть установлен при создании экземпляра.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Добавление входящего правила в группу сетевой безопасности 

Этот шаг можно сделать через портал Azure или с помощью команд PowerShell, и может быть выполнен в любое время после того, как подсеть будет подготовлена для управляемого экземпляра.

Для получения подробной информации [см. Разрешить общедоступный трафик конечных точек в группе сетевой безопасности.](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Перемещение существующего единого экземпляра в пул экземпляра 
 
Перемещение экземпляров в пул и из пула является одним из ограничений публичного предварительного просмотра. Обход, который может быть использован, зависит от своевременного восстановления баз данных из экземпляра за пределами пула в экземпляр, который уже находится в пуле. 

Оба экземпляра должны быть в одной подписке и в регионе. Кросс-региональный и кросс-подписка восстановления в настоящее время не поддерживается.

Этот процесс имеет период простоя.

Для перемещения существующих баз данных:

1. Приостановка рабочих нагрузок на управляемом экземпляре, из котором вы мигрируете.
2. Создавайте скрипты для создания системных баз данных и их выполнения в экземпляре, налетевом внутри пула экземпляров.
3. Восстановите время от опоры каждой базы данных из одного экземпляра в экземпляр в пуле.

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

4. Направьте приложение на новый экземпляр и возобновите его рабочие нагрузки.

Если существует несколько баз данных, повторите процесс для каждой базы данных.


## <a name="next-steps"></a>Дальнейшие действия

- Сведения о функциях и список сравнения см. в статье [Сравнение функций Базы данных SQL Azure и SQL Server](sql-database-features.md).
- Для получения дополнительной информации [managed instance VNet configuration](sql-database-managed-instance-connectivity-architecture.md)о конфигурации VNet см.
- Для быстрого запуска, который создает управляемый экземпляр и восстанавливает базу [create a managed instance](sql-database-managed-instance-get-started.md)данных из файла резервного копирования, см.
- В учебнике, используемом миграционной службой базы данных Azure (DMS) для миграции, [см.](../dms/tutorial-sql-server-to-managed-instance.md)
- Для расширенного мониторинга производительности управляемой базы данных экземпляров со встроенным интеллектом для устранения неполадок [см.](../azure-monitor/insights/azure-sql.md)
- Для получения информации о ценах [см.](https://azure.microsoft.com/pricing/details/sql-database/managed/)
