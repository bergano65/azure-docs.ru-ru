---
title: Конфигурация имеющейся виртуальной сети и подсети для Управляемого экземпляра Базы данных SQL Azure | Документация Майкрософт
description: В этой статье описывается, как настроить имеющуюся виртуальную сеть и подсеть, в которой можно развернуть Управляемый экземпляр Базы данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 1718177a0902bc7049eb6986e5a1d128eeb3f233
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040964"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>Настройка имеющейся виртуальной сети для Управляемого экземпляра Базы данных SQL Azure

Управляемый экземпляр Базы данных SQL Azure нужно развернуть в [виртуальной сети](../virtual-network/virtual-networks-overview.md) и подсети Azure, выделенных только для Управляемых экземпляров. Вы можете использовать имеющуюся виртуальную сеть и подсеть, если она настроена в соответствии с [требованиями к виртуальной сети Управляемого экземпляра](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Если у вас есть новая подсеть, которая все еще не настроена, вы не уверены, что подсеть соответствует [требованиям](sql-database-managed-instance-connectivity-architecture.md#network-requirements) или вы хотите проверить, соответствует ли подсеть [требованиям к сети](sql-database-managed-instance-connectivity-architecture.md#network-requirements) после внесения некоторых изменений, вы можете проверить и изменить свою сеть с помощью сценария, описанного в этом разделе.

  > [!Note]
  > Управляемый экземпляр можно создать только в виртуальных сетях Resource Manager. Виртуальные сети Azure, развернутые с помощью классической модели развертывания, не поддерживаются. Вычисление размера подсети для Управляемых экземпляров нужно выполнять в соответствии с рекомендациями, изложенными в статье [Определение размера подсети виртуальной сети для Управляемого экземпляра Базы данных SQL Azure](sql-database-managed-instance-determine-size-vnet-subnet.md), так как размер подсети нельзя изменить после внутреннего развертывания ресурсов.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Проверка и изменение имеющейся виртуальной сети

Если вы хотите создать Управляемый экземпляр внутри имеющейся подсети, мы рекомендуем использовать следующий сценарий PowerShell для подготовки подсети.

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Подготовка подсети выполняется в три простых шага:

1. Проверка. Выбранная виртуальная сеть и подсеть проверяются на соответствие сетевым требованиям Управляемого экземпляра.
2. Подтверждение. Пользователю предоставляется набор изменений, которые необходимо выполнить, чтобы подготовить подсеть для развертывания Управляемого экземпляра, и он получает запрос на согласие.
3. Подготовка. Виртуальная сеть и подсеть настраиваются надлежащим образом.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье [Общие сведения об Управляемом экземпляре Базы данных SQL Azure (предварительная версия)](sql-database-managed-instance.md).
- Чтобы узнать как создать виртуальную сеть и управляемый экземпляр, а также восстановить базу данных из резервной копии базы данных см. статью [Создание Управляемого экземпляра Базы данных SQL Azure на портале Azure](sql-database-managed-instance-get-started.md).
- Во избежание проблем при настройке DNS ознакомьтесь со сведениями в статье [Настройка пользовательской службы DNS для Управляемого экземпляра Базы данных SQL Azure](sql-database-managed-instance-custom-dns.md).
