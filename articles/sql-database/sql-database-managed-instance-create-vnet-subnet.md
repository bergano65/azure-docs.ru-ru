---
title: Создание виртуальной сети для Управляемого экземпляра Базы данных SQL Azure | Документация Майкрософт
description: В этой статье описывается создание виртуальной сети, в которой можно развернуть Управляемый экземпляр Базы данных SQL Azure.
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
ms.date: 09/20/2018
ms.openlocfilehash: a8000fb26ce5496a9c62ba475b862f8f80adf6b7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041770"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Настройка виртуальной сети для Управляемого экземпляра Базы данных SQL Azure

В этой статье объясняется, как создать допустимую виртуальную сеть и подсеть, в которой можно развернуть Управляемый экземпляр Базы данных SQL Azure.

Управляемый экземпляр Базы данных SQL Azure необходимо развернуть в [виртуальной сети](../virtual-network/virtual-networks-overview.md) Azure. Это развертывание поддерживает следующие сценарии:

- Защищенный частный IP-адрес.
- Подключение к управляемому экземпляру непосредственно из локальной сети
- Подключение управляемого экземпляра к связанному серверу или другому локальному хранилищу данных
- Подключение управляемого экземпляра к ресурсам Azure  

  > [!Note]
  > Вам необходимо [определить размер подсети для Управляемого экземпляра](sql-database-managed-instance-determine-size-vnet-subnet.md) перед развертыванием первого экземпляра, так как размер подсети невозможно изменить после помещения в нее ресурсов.
  > Если вы планируете использовать имеющуюся виртуальную сеть, необходимо изменить ее конфигурацию, чтобы она соответствовала управляемому экземпляру. Дополнительные сведения см. в разделе [Изменение существующей виртуальной сети для управляемого экземпляра](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-new-virtual-network"></a>Создание виртуальной сети

Самый простой способ создания и настройки виртуальной сети — использовать шаблон развертывания Azure Resource Manager.

1. Войдите на портал Azure.

2. Используйте кнопку **Развертывание в Azure**, чтобы развернуть виртуальную сеть в облаке Azure.

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Эта кнопка откроет форму, которую можно использовать для настройки сетевой среды, в которой можно развернуть управляемый экземпляр.

   > [!Note]
   > Этот шаблон Azure Resource Manager развертывает виртуальную сеть с двумя подсетями. Одна подсеть, называемая **ManagedInstances**, зарезервирована для управляемых экземпляров и имеет предварительно настроенную таблицу маршрутов, а другая подсеть, называемая **Defaul**, используется для других ресурсов, которые должны получить доступ к управляемому экземпляру (например, виртуальные машины Azure). Вы можете удалить подсеть **Default**, если она не нужна.

3. Настройка сетевой среды. В приведенной ниже форме можно настроить параметры сетевой среды.

![Настройка сети Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

Вы можете изменить имена виртуальной сети и подсетей и настроить диапазоны IP-адресов, связанные с сетевыми ресурсами. Как только вы нажмете кнопку "Купить", эта форма создаст и настроит вашу среду. Если вам не нужны две подсети, можно удалить подсеть, заданную по умолчанию.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье [Использование Управляемого экземпляра Базы данных SQL с виртуальными сетями и почти полной совместимостью](sql-database-managed-instance.md).
- [Архитектура подключения к Управляемому экземпляру Базы данных SQL Azure](sql-database-managed-instance-connectivity-architecture.md).
- См. сведения об [изменении имеющейся виртуальной сети для Управляемого экземпляра](sql-database-managed-instance-configure-vnet-subnet.md).
- Чтобы узнать как создать виртуальную сеть и управляемый экземпляр, а также восстановить базу данных из резервной копии базы данных см. статью [Создание Управляемого экземпляра Базы данных SQL Azure на портале Azure](sql-database-managed-instance-get-started.md).
- Во избежание проблем при настройке DNS см. статью [Configuring a Custom DNS for Azure SQL Database Managed Instance](sql-database-managed-instance-custom-dns.md) (Настройка пользовательской DNS для Управляемого экземпляра Базы данных SQL Azure)
