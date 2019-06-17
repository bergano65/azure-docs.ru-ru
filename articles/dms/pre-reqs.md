---
title: Предварительные требования для использования службы Azure Database Migration Service | Документация Майкрософт
description: Ознакомьтесь с предварительными требованиями для использования службы Azure Database Migration Service для переноса баз данных.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/29/2019
ms.openlocfilehash: 4e21014f7b4ed86846a100ed9a2b1cd4b0400974
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304260"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Предварительные требования для использования службы Azure Database Migration Service

Существует несколько предварительных условий, необходимых, чтобы убедиться, что Azure Database Migration Service прошел при переносе баз данных. Одни предварительные требования применяются во всех сценариях (с парами исходного и целевого объектов), поддерживаемых службой, другие уникальны и используются в определенных сценариях.

В следующих разделах приводятся предварительные требования, касающиеся использования службы Azure Database Migration Service.

## <a name="prerequisites-common-across-migration-scenarios"></a>Общие предварительные требования для сценариев миграции

Ниже приведены предварительные требования для использования службы Azure Database Migration Service, которые применяются во всех поддерживаемых сценариях миграции.

* Создайте виртуальную сеть Azure для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Убедитесь, что правила группы безопасности сети виртуальной сети (NSG) не блокируют следующие порты связи 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Plan virtual networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Планирование виртуальных сетей).
* Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.
* Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* При установке SQL Server Express протокол TCP/IP отключен по умолчанию. Включите его, выполнив инструкции в статье [Включение или отключение сетевого протокола сервера](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > Создание экземпляра Azure Database Migration Service требуется доступ к параметрам виртуальной сети, которые обычно находятся в той же группе ресурсов. Таким образом пользователь, создающий экземпляр DMS требуется разрешение на уровне подписки. Чтобы создать необходимые роли, которые можно назначить при необходимости, выполните следующий сценарий:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.DataMigration/services/*/read", `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Предварительные требования для переноса SQL Server в Базу данных SQL Azure

Помимо предварительных требований для службы Azure Database Migration Service, которые являются общими для всех сценариев миграции, существуют также требования, которые применяются только к определенному сценарию.

При использовании службы Azure Database Migration Service для миграции SQL Server в Базу данных SQL Azure, помимо предварительных требований, которые являются общими для всех сценариев миграции, обязательно выполните следующие дополнительные требования:

* Создайте экземпляр базы данных SQL Azure, следуя инструкциям из статьи [Создание базы данных SQL Azure на портале Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Скачайте и установите [Помощник по миграции данных](https://www.microsoft.com/download/details.aspx?id=53595) версии 3.3 или более поздней.
* Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
* Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
* Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для сервера Базы данных SQL Azure, чтобы предоставить службе Azure Database Migration Service доступ к целевым базам данных. Задайте диапазон подсети в виртуальной сети, которая используется для Azure Database Migration Service.
* Убедитесь, что учетные данные, используемые для подключения к исходному экземпляру SQL Server, имеют разрешения [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Убедитесь, что учетные данные, используемые для подключения к целевому экземпляру базы данных SQL Azure, имеют разрешения CONTROL DATABASE в целевых базах данных SQL Azure.

   > [!NOTE]
   > Полный список предварительных требований, необходимых для использования службы Azure Database Migration Service для миграции из SQL Server в Базу данных SQL Azure, см. в руководстве [Миграция с SQL Server в базу данных SQL Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Необходимые условия для миграции SQL Server в базе данных SQL управляемого экземпляра

* Создание управляемого экземпляра базы данных SQL Azure, ознакомившись со сведениями в статье [создать управляемый экземпляр базы данных SQL Azure на портале Azure](https://aka.ms/sqldbmi).
* В брандмауэрах разрешите передачу трафика SMB через порт 445 для IP-адреса или диапазона подсети Azure Database Migration Service.
* Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
* Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
* Убедитесь, что для подключения к исходному серверу SQL Server и целевому управляемому экземпляру используются имена, входящие в серверную роль sysadmin.
* Создайте общую сетевую папку, которую Azure Database Migration Service сможет использовать для резервного копирования базы данных-источника.
* Предоставьте учетной записи службы, от имени которой выполняется исходный экземпляр SQL Server, права на запись в эту созданную сетевую папку, а учетной записи компьютера для исходного сервера — права на чтение и запись для этой папки.
* Запишите имя пользователя и пароль учетной записи Windows, которой предоставлены полные права доступа к этой сетевой папке. Служба Azure Database Migration Service олицетворяет пользователя с этими учетными данными, чтобы отправить файлы резервных копий в контейнер службы хранилища Azure для операции восстановления.
* Создайте контейнер больших двоичных объектов и получите универсальный код ресурса (URI) SAS, следуя инструкциям в статье [Управление ресурсами хранилища BLOB-объектов Azure с помощью обозревателя хранилищ (предварительная версия)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Обязательно выберите все разрешения (чтение, запись, удаление и вывод списка) в окне политики при создании универсального кода ресурса (URI) SAS.

   > [!NOTE]
   > Полный список предварительных требований, необходимых для использования службы Azure Database Migration Service для переноса данных из SQL Server в службу "Управляемый экземпляр Базы данных SQL Azure", см. в руководстве [Перенос SQL Server в Управляемый экземпляр Базы данных SQL Azure](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Дальнейшие действия

Общие сведения о службе Azure Database Migration Service и доступности по регионам см. в статье [Что такое Azure Database Migration Service](dms-overview.md).
