---
title: Настройка публичной конечная точка - управляемый экземпляр
description: Узнайте, как настроить общедоступную точку для управляемого экземпляра
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256163"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Настройка общедоступной конечной точки в Управляемом экземпляре Базы данных SQL Azure

Публичная конечная точка для [управляемого экземпляра](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) позволяет получить доступ к управляемому экземпляру из-за пределов [виртуальной сети.](../virtual-network/virtual-networks-overview.md) Вы можете получить доступ к управляемому экземпляру из нескольких клиентов Azure, таких как Power BI, Служба приложений Azure или в сети. Используя общедоступную точку в управляемом экземпляре, вам не нужно использовать VPN, который может помочь избежать проблем с пропускной мощностью VPN.

В этой статье вы узнаете, как выполнять следующие задачи.

> [!div class="checklist"]
> - Включить общедоступную точку для управляемого экземпляра на портале Azure
> - Включить общедоступную точку для управляемого экземпляра с помощью PowerShell
> - Наверскакните группу сетевой безопасности управляемого экземпляра, чтобы обеспечить трафик в общедоступный конечный момент управляемого экземпляра
> - Получение управляемого экземпляра общедоступной строки соединения конечных точек

## <a name="permissions"></a>Разрешения

Из-за чувствительности данных, наиболее управляемой, конфигурация для включения управляемого экземпляра общедоступной конечной точки требует двухэтапного процесса. Эта мера безопасности придерживается разделения обязанностей (SoD):

- Включение общедоступной точки назначения в управляемом экземпляре должно быть сделано админом управляемого экземпляра. Управляющий экземпляр можно найти на странице **«Обзор»** вашего ресурса управляемого экземпляра S'L.
- Разрешение трафика с помощью группы сетевой безопасности, что должно быть сделано с помощью сетевого админа. Для получения дополнительной [информации](../virtual-network/manage-network-security-group.md#permissions)см.

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Включение общедоступной точки назначения для управляемого экземпляра на портале Azure

1. Запуск портала Azure на<https://portal.azure.com/.>
1. Откройте группу ресурсов с управляемым экземпляром и выберите **управляемый экземпляр S'L,** на котором необходимо настроить общедоступную точку.
1. В настройках **безопасности** выберите вкладку **Virtual Network.**
1. На странице конфигурации виртуальной сети выберите **включить,** а затем значок **Сохранить** для обновления конфигурации.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Включение общедоступной точки назначения для управляемого экземпляра с помощью PowerShell

### <a name="enable-public-endpoint"></a>Включение общедоступной конечной точки

Выполните приведенные ниже команды PowerShell. Замените **идентификатор подписки** на идентификатор подписки. Также замените **rg-name** группой ресурсов для управляемого экземпляра и замените **mi-name** именем управляемого экземпляра.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Отключить публичную конечную точку

Чтобы отключить публичную конечную точку с помощью PowerShell, вы полнить следующую команду (а также не забудьте закрыть NSG для входящего порта 3342, если он настроен):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Разрешить общедоступный конечный трафик в группе сетевой безопасности

1. Если страница конфигурации управляемого экземпляра все еще открыта, перейдите на вкладку **«Обзор».** **SQL managed instance** Выберите виртуальную **ссылку сети/подсети,** которая приведет вас к странице конфигурации виртуальной сети.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Выберите вкладку **Subnets** на левом панели конфигурации виртуальной сети и обратите внимание на **SECURITY GROUP** для управляемого экземпляра.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Вернитесь к группе ресурсов, содержащей управляемый экземпляр. Вы должны увидеть имя **группы безопасности Сети,** указанное выше. Выберите имя для входной страницы конфигурации группы безопасности сети.

1. Выберите вкладку **входящие правила безопасности** и **добавьте** правило с более высоким приоритетом, чем **правило deny_all_inbound** со следующими настройками: </br> </br>

    |Параметр  |Рекомендуемое значение  |Описание  |
    |---------|---------|---------|
    |**Источник**     |Любой IP-адрес или тег службы         |<ul><li>Для служб Azure, таких как Power BI, выберите тег облачного сервиса Azure</li> <li>Для вашего компьютера или Azure VM используйте IP-адрес NAT</li></ul> |
    |**Диапазоны исходных портов**     |*         |Оставьте это на q (любой), как исходные порты, как правило, динамически распределены и как таковые, непредсказуемые |
    |**Назначения**     |Любой         |Оставляя назначения как Любой, чтобы позволить трафик в подсеть управляемого экземпляра |
    |**Диапазоны портов назначения**     |3342         |Порт назначения области области до 3342, который является управляемой публичной конечной точкой TDS экземпляра |
    |**Протокол**     |TCP         |Управляемый экземпляр использует протокол TCP для TDS |
    |**Действие**     |Allow         |Разрешить входящий трафик в управляемый экземпляр через публичную конечную точку |
    |**Priority**     |1300         |Убедитесь, что это правило является более приоритетным, чем правило **deny_all_inbound** |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Порт 3342 используется для общедоступных конечных соединений в управляемый экземпляр и не может быть изменен на данный момент.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Получение управляемого экземпляра общедоступной строки соединения конечных точек

1. Перейдите на страницу конфигурации конфигурации управляемого экземпляра S'L, которая была включена для общедоступной конечной точки. Выберите вкладку **строки Connection** в конфигурации **«Настройки».**
1. Обратите внимание, что общедоступное имя хоста конечных точек происходит в формате <mi_name>. **public**.<dns_zone>.database.windows.net и что порт, используемый для подключения 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте об [использовании управляемой экземпляром базы данных Azure S'L с общедоступной конечной точкой.](sql-database-managed-instance-public-endpoint-securely.md)