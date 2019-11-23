---
title: Группы отработки отказа
description: Группы автоматической отработки отказа являются возможностью Базы данных SQL, которая позволяет управлять репликацией и автоматической или согласованной отработкой отказа группы баз данных на сервере Базы данных SQL или во всех базах данных в управляемом экземпляре.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 11/07/2019
ms.openlocfilehash: 470e9a9c36b6b4ec2e40db5dfc47ae03fb6b5aa8
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421371"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Использование групп автоматической отработки отказа для включения прозрачной и согласованной отработки отказа в нескольких базах данных

Auto-failover groups is a SQL Database feature that allows you to manage replication and failover of a group of databases on a SQL Database server or all databases in a managed instance to another region. It is a declarative abstraction on top of the existing [active geo-replication](sql-database-active-geo-replication.md) feature, designed to simplify deployment and management of geo-replicated databases at scale. Вы можете запустить отработку отказа вручную или делегировать службе Базы данных SQL, используя определяемую пользователем политику. Последний вариант позволяет автоматически восстановить несколько связанных баз данных в дополнительном регионе после катастрофических сбоев или других незапланированных событий, которые приводят к полной или частичной потере доступности службы Базы данных SQL в основном регионе. A failover group can include one or multiple databases, typically used by the same application. Кроме того, клиенты могут использовать доступные для чтения базы данных-получатели для разгрузки рабочих нагрузок запросов, доступных только для чтения. Так как группы автоматической отработки отказа включают в себя несколько баз данных, базы данных необходимо настроить на сервере-источнике. Группы автоматической отработки отказа поддерживают репликацию всех баз данных в группе только на один сервер-получатель в другом регионе.

> [!NOTE]
> Если при работе с отдельными базами данных или с базами данных в пуле на сервере базы данных SQL вы хотите создать несколько вторичных реплик в одном или в разных регионах, используйте [активную георепликацию](sql-database-active-geo-replication.md). 

Во время работы с группами автоматической отработки отказа с политикой автоматической отработки отказа любой сбой, который влияет на одну или на несколько баз данных в группе, приведет к автоматической отработке отказа. Typically these are incidents that cannot be self-mitigated by the built-in automatic high availability operations. The examples of failover triggers include an incident caused by a SQL tenant ring or control ring being down due to an OS kernel memory leak on several compute nodes, or an incident caused by one or more tenant rings being down because a wrong network cable was cut during routine hardware decommissioning.  For more information, see [SQL Database High Availability](sql-database-high-availability.md).

Кроме того, группы автоматической отработки отказа предоставляют конечные точки прослушивателя только для чтения и для чтения-записи, которые во время отработки отказа не изменяются. Независимо от того, используете ли вы активацию вручную или автоматическую активацию отработки отказа, отработка отказа переключит все базы данных-получатели в группе в базу данных-источник. После выполнения автоматической отработки отказа базы данных запись DNS автоматически обновляется, чтобы перенаправить конечные точки в новый регион. Конкретные сведения о RPO и RTO можно найти в [обзоре обеспечения непрерывности бизнес-процессов](sql-database-business-continuity.md).

Во время работы с группами автоматической отработки отказа с политикой автоматической отработки отказа любой сбой, который влияет на базы данных на сервере Базы данных SQL или в управляемом экземпляре, приведет к автоматической отработки отказа. Управляйте группами автоматической отработки отказа используя:

- [портал Azure](sql-database-implement-geo-distributed-database.md).
- [PowerShell: группу отработки отказа](scripts/sql-database-add-single-db-to-failover-group-powershell.md);
- [REST API: группу отработки отказа](https://docs.microsoft.com/rest/api/sql/failovergroups).

После отработки отказа убедитесь, что для новой базы данных-источника настроены требования аутентификации ваших сервера и базы данных. Дополнительные сведения см. в разделе [Безопасность базы данных SQL после аварийного восстановления](sql-database-geo-replication-security-config.md).

Добавление избыточности баз данных между центрами обработки данных — только часть решения для достижения настоящей непрерывности бизнес-процессов. Для комплексного восстановления приложения (службы) после катастрофического сбоя необходимо восстановить все компоненты, из которых состоит служба и все зависимые службы. К примерам этих компонентов относятся клиентское программное обеспечение (например, браузер с пользовательским кодом JavaScript), интерфейсные веб-серверы, хранилище и DNS. Очень важно, чтобы все компоненты были устойчивы к одинаковым сбоям и становились доступными в соответствии с целевым временем восстановления (RTO) вашего приложения. Следовательно, необходимо определить все зависимые службы и получить сведения о гарантиях и возможностях, которые они предоставляют. Затем необходимо выполнить соответствующие действия, чтобы обеспечить работу службы во время отработки отказа служб, от которых она зависит. Дополнительные сведения о проектировании решений для аварийного восстановления см. в статье о [разработке облачных решений для аварийного восстановления с помощью активной георепликации](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Терминология и способности группы автоматической отработки отказа

- **Failover group (FOG)**

  A failover group is a named group of databases managed by a single SQL Database server or within a single managed instance that can fail over as a unit to another region in case all or some primary databases become unavailable due to an outage in the primary region. When created for managed instances, a failover group contains all user databases in the instance and therefore only one failover group can be configured on an instance.
  
  > [!IMPORTANT]
  > The name of the failover group must be globally unique within the `.database.windows.net` domain.

- **Серверы Базы данных SQL**

     С помощью серверов Базы данных SQL можно разместить некоторые или все пользовательские базы данных с отдельного сервера Базы данных SQL в группе отработки отказа. Сервер Базы данных SQL также поддерживает несколько групп отработки отказа на отдельном сервере Базы данных SQL.

- **Источник**

  The SQL Database server or managed instance that hosts the primary databases in the failover group.

- **Получатель**

  The SQL Database server or managed instance that hosts the secondary databases in the failover group. Источники и получатели не могут находиться в одном и том же регионе.

- **Добавление отдельных баз данных в группу отработки отказа**

  Вы можете разместить несколько отдельных баз данных на одном сервере Базы данных SQL в одной группе отработки отказа. Если вы добавите отдельную базу данных в группу отработки отказа, она автоматически создаст базу данных-получатель, используя тот же выпуск и объем вычислительных ресурсов на сервере-получателе.  Этот сервер следует указать при создании группы отработки отказа. Если вы добавите базу данных, у которой уже есть база данных-получатель на сервере-получателе, эта ссылка на георепликацию наследуется группой. Во время добавления базы данных, у которой уже есть база данных-получатель на сервере, которая не является частью группы отработки отказа, на этом сервере-получателе будет создана новая база данных-получатель.

  > [!IMPORTANT]
  > Make sure that the secondary server doesn't have a database with the same name unless it is an existing secondary database. In failover groups for managed instance all user databases are replicated. Нельзя выбирать подмножество для репликации пользовательских баз данных в группе отработки отказа.

- **Добавление баз данных в эластичный пул в группе отработки отказа**

  Вы можете разместить несколько баз данных или их все в эластичном пуле в одну группу отработки отказа. Если база данных-источник расположена в эластичном пуле, в нем автоматически создается база данных-получатель с тем же именем (вторичный пул). Необходимо убедиться, что сервер-получатель содержит эластичный пул с тем же именем и имеет достаточную емкость для размещения баз данных-получателей, которые будут созданы группой отработки отказа. Если вы добавите базу данных в пул, у которого уже есть база данных-получатель на вторичном пуле, эта ссылка на георепликацию наследуется группой. Во время добавления базы данных, у которой уже есть база данных-получатель на сервере, которая не является частью группы отработки отказа, в этом вторичном пуле будет создана новая база данных-получатель.
  
- **Зона DNS**

  A unique ID that is automatically generated when a new instance is created. A multi-domain (SAN) certificate for this instance is provisioned to authenticate the client connections to any instance in the same DNS zone. The two managed instances in the same failover group must share the DNS zone.
  
  > [!NOTE]
  > A DNS zone ID is not required for failover groups created for SQL Database servers.

- **Прослушиватель чтения и записи для группы отработки отказа**

  A DNS CNAME record that points to the current primary's URL. It is created automatically when the failover group is created and allows the read-write SQL workload to transparently reconnect to the primary database when the primary changes after failover. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.database.windows.net`.

- **Прослушиватель только для чтения для группы отработки отказа**

  Формируется запись DNS CNAME, которая указывает на прослушиватель только для чтения, указывающий на URL-адрес получателя. It is created automatically when the failover group is created and allows the read-only SQL workload to transparently connect to the secondary using the specified load-balancing rules. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.secondary.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.secondary.database.windows.net`.

- **Политика автоматической отработки отказа**

  По умолчанию группа отработки отказа настроена с помощью политики автоматической отработки отказа. Служба базы данных SQL запускает отработку отказа после обнаружения сбоя и истечения льготного периода. Системе необходимо убедиться, что сбой нельзя устранить с помощью встроенной [инфраструктуры обеспечения высокой доступности Службы базы данных SQL](sql-database-high-availability.md) из-за масштаба влияния. Если вы хотите управлять рабочим процессом отработки отказа из приложения, вы можете отключить автоматическую отработку отказа.
  
  > [!NOTE]
  > Because verification of the scale of the outage and how quickly it can be mitigated involves human actions by the operations team, the grace period cannot be set below one hour.  This  limitation applies to all databases in the failover group regardless of their data synchronization state. 

- **Политика отработки отказа только для чтения**

  По умолчанию группа отработки отказа только для чтения отключена. Это гарантирует, что производительность базы данных-источника не изменится, если база данных-получателя отключена. Тем не менее, это также означает, что невозможно будет активировать сеансы только для чтения, пока база данных-получателя не восстановится. If you cannot tolerate downtime for the read-only sessions and are OK to temporarily use the primary for both read-only and read-write traffic at the expense of the potential performance degradation of the primary, you can enable failover for the read-only listener by configuring the `AllowReadOnlyFailoverToPrimary` property. In that case, the read-only traffic will be automatically redirected to the primary if the secondary is not available.

- **Плановая отработка отказа**

   Плановая отработка отказа выполняет полную синхронизацию между базой данных-источник и базой данных-получатель, прежде чем база данных-получатель переключится на основную роль. Такая отработка отказа гарантирует отсутствие потери данных. Плановая отработка отказа используется в следующих сценариях.

  - Тестирование аварийного восстановления (DR) в рабочей среде в случае, если потеря данных неприемлема.
  - Перемещение баз данных в другой регион.
  - Возвращение баз данных в основной регион после устранения сбоя (восстановление размещения).

- **Незапланированная отработка отказа**

   При незапланированной или принудительной отработке отказа база данных-получатель немедленно переключается на основную роль без какой-либо синхронизации с базой данных-источником. Эта операция приведет к потере данных. Незапланированная отработка отказа используется в качестве метода восстановления при сбоях, когда база данных-источник недоступна. When the original primary is back online, it will automatically reconnect without synchronization and become a new secondary.

- **Отработка отказа вручную**

  Вы можете запустить отработку отказа вручную в любое время, независимо от настройки автоматической отработки отказа. Если политика автоматической отработки отказа не настроена, для восстановления баз данных в группе отработки отказа в получателя требуется отработка отказа вручную. Вы можете запустить принудительную отработку отказа или адаптированную (с полной синхронизацией данных). Адаптированная отработка отказа может использоваться для перемещения источника в дополнительный регион. После завершения отработки отказа записи DNS автоматически обновляются для установления подключения к новому источнику.

- **Льготный период с потерей данных**

  Так как база данных-источник и база данных-получатель синхронизированы с помощью асинхронной репликации, отработка отказа может привести к потере данных. Вы можете настроить политику автоматической отработки отказа, чтобы представить устойчивость приложения к потере данных. By configuring `GracePeriodWithDataLossHours`, you can control how long the system waits before initiating the failover that is likely to result data loss.

- **Несколько групп отработки отказа**

  Вы можете настроить несколько групп отработки отказа для одной пары серверов, чтобы управлять масштабом отработок отказа. Каждая группа выполняет отработку отказа независимо от других групп. Если ваше мультитенантное приложение использует эластичные пулы, вы можете воспользоваться этой возможностью, чтобы объединить базу данных-источник и базу данных-получатель в каждом пуле. Таким образом вы сможете снизить последствия сбоя для половины клиентов.

  > [!NOTE]
  > Управляемый экземпляр не поддерживает несколько групп отработки отказа.
  
## <a name="permissions"></a>Разрешения

Permissions for a failover group are managed via [role-based access control (RBAC)](../role-based-access-control/overview.md). The [SQL Server Contributor](../role-based-access-control/built-in-roles.md#sql-server-contributor) role has all the necessary permissions to manage failover groups.

### <a name="create-failover-group"></a>Create failover group

To create a failover group, you need RBAC write access to both the primary and secondary servers, and to all databases in the failover group. For a managed instance, you need RBAC write access to both the primary and secondary managed instance, but permissions on individual databases are not relevant since individual managed instance databases cannot be added to or removed from a failover group. 

### <a name="update-a-failover-group"></a>Update a failover group

To update a failover group, you need RBAC write access to the failover group, and all databases on the current primary server or managed instance.  

### <a name="failover-a-failover-group"></a>Failover a failover group

To fail over a failover group, you need RBAC write access to the failover group on the new primary server or managed instance.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Рекомендации по использованию групп отработки отказа с отдельными базами данных и эластичными пулами

Группу автоматической отработки отказа необходимо настроить на сервере-источнике Базы данных SQL и подключить к серверу-получателю Базы данных SQL в другом регионе Azure. Группы могут содержать все или некоторые базы данных на этих серверах. На следующей схеме показана типичная конфигурация геоизбыточного облачного приложения, использующего несколько баз данных и активную георепликацию.

![автоматическая отработка отказа](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> See [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a single database to a failover group.

При разработке службы с обеспечением непрерывности бизнес-процессов придерживайтесь следующих рекомендаций.

- **Использование одной или нескольких групп отработки отказа для управления отработкой отказа нескольких баз данных**

  Вы можете создать одну или несколько групп отработки отказа между двумя серверами в различных регионах (для основного сервера и сервера-получателя). Каждая группа может содержать одну или несколько баз данных, которые восстанавливаются единым блоком в случае, когда все или часть баз данных-источников становятся недоступными из-за сбоя в основном регионе. Группа отработки отказа создает базу данных-получатель в дополнительном географическом регионе с таким же целевым уровнем служб, который указан для базы данных-источника. При добавлении существующей связи георепликации к группе отработки отказа убедитесь, что база данных-получатель геореплицируемых данных настроена с тем же уровнем служб и объемом вычислительных ресурсов, что и база данных-источник.
  
  > [!IMPORTANT]
  > Creating failover groups between two servers in different subscriptions is not currently supported for single databases and elastic pools. If you move the primary or secondary server to a different subscription after the failover group has been created, it could result in failures of the failover requests and other operations.

- **Использование прослушивателя чтения и записи для рабочей нагрузки OLTP**

  При выполнении операций OLTP используйте `<fog-name>.database.windows.net` в качестве URL-адреса сервера, чтобы все подключения автоматически перенаправлялись на сервер-источник. Этот URL-адрес не будет изменяться после отработки отказа. Обратите внимание на то, что отработка отказа включает в себя обновление DNS-записи, поэтому клиентские подключения будут перенаправляться на новый сервер-источник только после обновления кэша DNS на стороне клиента.

- **Использование прослушивателя только для чтения для рабочей нагрузки только для чтения**

  Если вы используете логически изолированную рабочую нагрузку в режиме только для чтения, устойчивую к некоторым задержкам в обновлении данных, в приложении можно использовать базу данных-получатель. Для сеансов с доступом только для чтения используйте `<fog-name>.secondary.database.windows.net` в качестве URL-адреса сервера, чтобы подключение автоматически перенаправлялось на сервер-получатель. It is also recommended that you indicate in connection string read intent by using `ApplicationIntent=ReadOnly`. If you want to ensure that the read-only workload can reconnect after failover or in case the secondary server goes offline, make sure to configure the `AllowReadOnlyFailoverToPrimary` property of the failover policy.

- **Будьте готовы к снижению производительности**

  Решение отработки отказа SQL не зависит от остальной части приложения или других используемых служб. Приложение может смешаться с некоторыми компонентами в одном регионе и с некоторыми в другом. Чтобы избежать снижения производительности, обеспечьте избыточное развертывание приложения в регионе аварийного восстановления и следуйте этим [рекомендациям по безопасности в сети](#failover-groups-and-network-security).

  > [!NOTE]
  > Приложение в регионе аварийного восстановления не требует использования другой строки подключения.  

- **Будьте готовы к потере данных**

  If an outage is detected, SQL waits for the period you specified by `GracePeriodWithDataLossHours`. Значение этого свойства по умолчанию – 1 час. If you cannot afford data loss, make sure to set `GracePeriodWithDataLossHours` to a sufficiently large number, such as 24 hours. Используйте группу ручной отработки отказа для восстановления размещения из получателя в источник.

  > [!IMPORTANT]
  > Эластичные пулы с 800 DTU или менее и более чем 250 базами данных, использующими георепликацию, могут испытывать проблемы, включая более длительные плановые операции отработки отказа и снижение производительности.  Эти проблемы чаще всего возникают из-за рабочих нагрузок, интенсивно записывающих данные, если конечные точки георепликации физически находятся далеко друг от друга или если используется несколько дополнительных конечных точек для каждой базы данных.  Симптомы этих проблем проявляются при увеличении задержки георепликации с течением времени.  Эту задержку можно отслеживать с помощью [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Если эти проблемы возникли, к возможным способам их устранения относятся увеличение числа DTU пула или уменьшение количества геореплицируемых баз данных в одном пуле.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Best practices of using failover groups with managed instances

Группы автоматической отработки отказа должны быть настроены на экземпляре источника и подключены к экземпляру получателя в другом регионе Azure.  Все базы данных в экземпляре будут реплицированы в экземпляр получателя.

На следующей схеме показана типичная конфигурация геоизбыточного облачного приложения, использующего несколько управляемых экземпляров и активную георепликацию.

![автоматическая отработка отказа](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> See [Add managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a managed instance to use failover group.

If your application uses managed instance as the data tier, follow these general guidelines when designing for business continuity:

- **Создание экземпляра получателя в той же зоне DNS, что и для экземпляра источника**

  Чтобы обеспечить беспрерывное подключение к экземпляру источника после отработки отказа, экземпляры и источника, и получателя должны находиться в одной зоне DNS. It will guarantee that the same multi-domain (SAN) certificate can be used to authenticate the client connections to either of the two instances in the failover group. После подготовки вашего приложения к рабочему развертыванию создайте экземпляр получателя в другом регионе и убедитесь, что он использует ту же зону DNS, что и экземпляр источника. You can do it by specifying a `DNS Zone Partner` optional parameter using the Azure portal, PowerShell, or the REST API.

> [!IMPORTANT]
> First instance created in the subnet determines DNS zone for all subsequent instances in the same subnet. This means that two instances from the same subnet cannot belong to different DNS zones.

  For more information about creating the secondary instance in the same DNS zone as the primary instance, see [Create a secondary managed instance](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

- **Включение трафика репликации между двумя экземплярами**

  Поскольку каждый экземпляр изолирован в своей виртуальной сети, двусторонний трафик между этими виртуальными сетями должен быть разрешен. См. [VPN-шлюзы Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Create a failover group between managed instances in different subscriptions**

  You can create a failover group between managed instances in two different subscriptions. When using PowerShell API you can do it by  specifying the `PartnerSubscriptionId` parameter for the secondary instance. When using REST API, each instance ID included in the `properties.managedInstancePairs` parameter can have its own subscriptionID.
  
  > [!IMPORTANT]
  > Azure Portal does not support failover groups across different subscriptions.

- **Настройка группы отработки отказа для управления отработками отказов для всего экземпляра**

  Группа отработки отказа будет управлять отработкой отказа всех баз данных в экземпляре. При создании группы каждая база данных в экземпляре будет автоматически геореплицироваться в экземпляр получателя. Вы не можете использовать группы отработки отказа для запуска частичной отработки отказа подмножества баз данных.

  > [!IMPORTANT]
  > Если база данных удалена из экземпляра источника, то она также будет автоматически удалена из экземпляра геополучателя.

- **Использование прослушивателя чтения и записи для рабочей нагрузки OLTP**

  При выполнении операций OLTP используйте `<fog-name>.zone_id.database.windows.net` в качестве URL-адреса сервера, чтобы все подключения автоматически перенаправлялись на сервер-источник. Этот URL-адрес не будет изменяться после отработки отказа. Отработка отказа включает в себя обновление DNS-записи, поэтому клиентские подключения будут перенаправляться на новый сервер-источник только после обновления кэша DNS на стороне клиента. Because the secondary instance shares the DNS zone with the primary, the client application will be able to reconnect to it using the same SAN certificate.

- **Прямое подключение к георепликации получателя для запросов только для чтения**

  Если вы используете логически изолированную рабочую нагрузку в режиме только для чтения, устойчивую к некоторым задержкам в обновлении данных, в приложении можно использовать базу данных-получатель. Для прямого подключения к георепликации получателя используйте `server.secondary.zone_id.database.windows.net` в качестве URL-сервера.

  > [!NOTE]
  > На некоторых уровнях служб База данных SQL Azure поддерживает использование [реплик только для чтения](sql-database-read-scale-out.md) для балансировки рабочих нагрузок запросов только для чтения, используя возможности одной реплики и параметра `ApplicationIntent=ReadOnly` в строке соединения. После настройки георепликации получателя вы можете использовать эту возможность для соединения с репликой только для чтения либо в основном расположении, либо в геореплицированном расположении.
  > - Для подключения к реплике только для чтения в основном расположении используйте `<fog-name>.zone_id.database.windows.net`.
  > - To connect to a read-only replica in the secondary location, use `<fog-name>.secondary.zone_id.database.windows.net`.

- **Будьте готовы к снижению производительности**

  Решение отработки отказа SQL не зависит от остальной части приложения или других используемых служб. Приложение может смешаться с некоторыми компонентами в одном регионе и с некоторыми в другом. Чтобы избежать снижения производительности, обеспечьте избыточное развертывание приложения в регионе аварийного восстановления и следуйте этим [рекомендациям по безопасности в сети](#failover-groups-and-network-security).

- **Будьте готовы к потере данных**

  При обнаружении сбоя SQL автоматически активирует отработку отказа с чтением и записью, если потери данных нулевые, насколько нам известно. В противном случае он ожидает в течение периода, указанного в `GracePeriodWithDataLossHours`. Если вы указали `GracePeriodWithDataLossHours`, будьте готовы к потере данных. Во время сбоев Azure, как правило, повышает доступность. Если вы не можете позволить себе потерю данных, установите для параметра GracePeriodWithDataLossHours достаточно большое количество времени, например 24 часа.

  После запуска отработки отказа DNS обновления прослушивателя чтения и записи произойдут автоматически. Эта операция не приведет к потере данных. Однако процесс смены ролей баз данных в обычных условиях может занять до 5 минут. До окончания этого процесса некоторые базы данных в новом экземпляре источника будут оставаться в режиме только для чтения. If failover is initiated using PowerShell, the entire operation is synchronous. If it is initiated using the Azure portal, the UI will indicate completion status. Если она инициируется с помощью REST API, то используйте стандартный механизм опроса Azure Resource Manager для мониторинга выполнения.

  > [!IMPORTANT]
  > Используйте группу ручной отработки отказа для перемещения источников в исходное расположение. После устранения сбоя, вызвавшего отработку отказа, вы можете переместить базы данных-источники в исходное расположение. Для этого вам следует инициировать ручную отработку отказа группы.

- **Acknowledge known limitations of failover groups**

  Database rename is not supported for instances in failover group. You will need to temporarily delete failover group to be able to rename a database.

## <a name="failover-groups-and-network-security"></a>Группы отработки отказа и сетевая безопасность

For some applications the security rules require that the network access to the data tier is restricted to a specific component or components such as a VM, web service etc. This requirement presents some challenges for business continuity design and the use of the failover groups. Consider the following options when implementing such restricted access.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Использование групп отработки отказа и правил виртуальной сети

Если вы используете [конечные точки служб и правила виртуальной сети](sql-database-vnet-service-endpoint-rule-overview.md) для ограничения доступа к базе данных SQL, помните, что каждая конечная точка службы для виртуальной сети применяется только к одному региону Azure. Конечная точка не поддерживает прием подключений из подсети в других регионах. Таким образом, только клиентские приложения, развернутые в одном регионе, могут подключаться к базе данных-источнику. Так как результаты отработки отказа в сеансах клиента SQL пересылаются на сервер в другом (дополнительном) регионе, эти сеансы завершатся сбоем, если будут исходить из клиента за пределами этого региона. По этой причине политику автоматической отработки отказа невозможно включить, если на серверы-участники распространяются правила виртуальной сети. Для поддержки перехода на другой ресурс вручную выполните следующие действия:

1. Подготовьте избыточные копии интерфейсных компонентов приложения (веб-службы, виртуальные машины и т. д.) в дополнительном регионе.
2. Настройте [правила виртуальной сети](sql-database-vnet-service-endpoint-rule-overview.md) по отдельности для основного и дополнительного сервера.
3. Включите [отработку отказа внешнего интерфейса с помощью конфигурации диспетчера трафика](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime).
4. Инициируйте отработку отказа вручную при обнаружении сбоя. Этот параметр оптимизирован для приложений, требующих последовательной задержки между внешним интерфейсом и уровнем данных, и поддерживает восстановление после сбоя внешнего интерфейса уровня данных или их обоих.

> [!NOTE]
> При использовании **прослушивателя только для чтения** для балансировки рабочей нагрузки только для чтения убедитесь, что эта рабочая нагрузка выполняется на виртуальной машине или в другом ресурсе в дополнительном регионе, чтобы прослушиватель мог подключаться к базе данных-получателю.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Использование групп отработки отказа и правил брандмауэра базы данных SQL

Если для плана обеспечения непрерывности бизнес-процессов требуется отработка отказа с помощью групп с автоматической отработкой отказа, можно ограничить доступ к базе данных SQL с помощью традиционных правил брандмауэра. Для поддержки автоматического перехода на другой ресурс выполните следующие действия:

1. [создайте общедоступный IP-адрес](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address);
2. [создайте общедоступный балансировщик нагрузки](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) и назначьте ему общедоступный IP-адрес;
3. [создайте виртуальную сеть и виртуальные машины](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) для интерфейсных компонентов;
4. [создайте группу безопасности сети](../virtual-network/security-overview.md) и настройте входящие подключения.
5. Убедитесь, что исходящие подключения открыты для базы данных SQL Azure с помощью [тега службы](../virtual-network/security-overview.md#service-tags) "Sql".
6. Создайте [правило брандмауэра базы данных SQL](sql-database-firewall-configure.md), чтобы разрешить входящий трафик из общедоступного IP-адреса, созданного на этапе 1.

Дополнительные сведения о том, как настроить исходящий доступ и какой IP-адрес нужно использовать в правилах брандмауэра, см. в статье [Исходящие подключения в Azure](../load-balancer/load-balancer-outbound-connections.md).

Приведенная выше конфигурация гарантирует, что автоматический переход на другой ресурс не будет блокировать подключения из интерфейсных компонентов, и предполагает, что приложение может выдержать более длительную задержку между интерфейсном и уровнем данных.

> [!IMPORTANT]
> Для обеспечения непрерывности бизнес-процессов при региональных сбоях должна присутствовать географическая избыточность интерфейсных компонентов и баз данных.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Enabling geo-replication between managed instances and their VNets

When you set up a failover group between primary and secondary managed instances in two different regions, each instance is isolated using an independent virtual network. To allow replication traffic between these VNets ensure these prerequisites are met:

1. The two managed instances need to be in different Azure regions.
1. The two managed instances need to be the same service tier, and have the same storage size.
1. Your secondary managed instance must be empty (no user databases).
1. The virtual networks used by the managed instances need to be connected through a [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) or Express Route. When two virtual networks connect through an on-premises network, ensure there is no firewall rule blocking ports 5022, and 11000-11999. Пиринг глобальной виртуальной сети не поддерживается.
1. The two managed instance VNets cannot have overlapping IP addresses.
1. You need to set up your Network Security Groups (NSG) such that ports 5022 and the range 11000~12000 are open inbound and outbound for connections from the other managed instanced subnet. Это позволяет обеспечить трафик репликации между экземплярами.

   > [!IMPORTANT]
   > Неправильная настройка правил безопасности NSG приводит к задержке операций копирования баз данных.

1. The secondary instance is configured with the correct DNS zone ID. DNS zone is a property of a managed instance and virtual cluster, and its ID is included in the host name address. The zone ID is generated as a random string when the first managed instance is created in each VNet and the same ID is assigned to all other instances in the same subnet. Once assigned, the DNS zone cannot be modified. Managed instances included in the same failover group must share the DNS zone. You accomplish this by passing the primary instance's zone ID as the value of DnsZonePartner parameter when creating the secondary instance. 

   > [!NOTE]
   > For a detailed tutorial on configuring failover groups with managed instance, see [add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Повышение или понижение уровня производительности базы данных-источника

Вы можете повышать или понижать объем вычислительных ресурсов базы данных-источника (в рамках одного уровня служб, а не между уровнем общего назначения и критически важным для бизнеса) без отключения каких-либо баз данных-получателей. When upgrading, we recommend that you upgrade all of the secondary databases first, and then upgrade the primary. When downgrading, reverse the order: downgrade the primary first, and then downgrade all of the secondary databases. Когда вы повышаете или понижаете базу данных до следующего уровня служб, особенно важно выполнять рекомендацию выше.

This sequence is recommended specifically to avoid the problem where the secondary at a lower SKU gets overloaded and must be re-seeded during an upgrade or downgrade process. You could also avoid the problem by making the primary read-only, at the expense of impacting all read-write workloads against the primary.

> [!NOTE]
> Если вы создали базу данных-получатель как часть конфигурации группы отработки отказа, понижение ее уровня не рекомендуется. Это необходимо, чтобы обеспечить достаточную емкость уровня данных для обработки регулярных рабочих нагрузок после активации отработки отказа.

## <a name="preventing-the-loss-of-critical-data"></a>Предотвращение потери важных данных

Из-за высокой задержки глобальных сетей непрерывная копия использует механизм асинхронной репликации. Ввиду асинхронной репликации потеря данных в случае сбоя неизбежна. Тем не менее для некоторых приложений терять данные недопустимо. Чтобы защитить эти критические обновления, разработчик приложения сразу же после фиксации транзакции может вызывать системную процедуру [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Calling `sp_wait_for_database_copy_sync` blocks the calling thread until the last committed transaction has been transmitted to the secondary database. Но вызов не дожидается воспроизведения и фиксации переданных транзакций в базе данных-получателе. `sp_wait_for_database_copy_sync` is scoped to a specific continuous copy link. Эту процедуру может вызвать любой пользователь с правами подключения к базе данных-источнику.

> [!NOTE]
> `sp_wait_for_database_copy_sync` prevents data loss after failover, but does not guarantee full synchronization for read access. The delay caused by a `sp_wait_for_database_copy_sync` procedure call can be significant and depends on the size of the transaction log at the time of the call.

## <a name="failover-groups-and-point-in-time-restore"></a>Группы отработки отказа и восстановление до точки во времени

Сведения об использовании восстановления до точки во времени с группами отработки отказа см. в разделе [Восстановление до точки во времени (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Программное управление группами отработки отказа

Как уже говорилось ранее, группами автоматической отработки отказа и активной георепликацией можно также управлять программно с помощью Azure PowerShell и REST API. В приведенных ниже таблицах описан доступный для этого набор команд. Активная георепликация включает в себя набор API-интерфейсов Azure Resource Manager для управления, в том числе [REST API Базы данных SQL Azure](https://docs.microsoft.com/rest/api/sql/) и [командлеты Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Эти интерфейсы API требуют использования групп ресурсов и поддерживают безопасность на основе ролей (RBAC). Дополнительные сведения о том, как реализовать контроль доступа на основе ролей, см. в статье [Использование управления доступом на основе ролей для контроля доступа к ресурсам в подписке Azure](../role-based-access-control/overview.md).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Управление отработкой отказа баз данных SQL с отдельными базами данных и эластичными пулами

| Командлет | Описание |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Создает группу отработки отказа и регистрирует ее на основном сервере и сервере-получателе.|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Удаляет группу отработки отказа с сервера, а также удаляет все входящие в нее базы данных-получатели. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает конфигурацию группы отработки отказа. |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Изменяет конфигурацию группы отработки отказа. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Запускает отработку отказа группы отработки отказа на сервер-получатель. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Добавляет одну или несколько баз данных в группу отработки отказа Базы данных SQL Azure.|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| Командлет | Описание |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Создает группу отработки отказа и регистрирует ее на основном сервере и сервере-получателе.|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Изменяет конфигурацию группы отработки отказа.|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Возвращает конфигурацию группы отработки отказа.|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Запускает отработку отказа группы отработки отказа на сервер-получатель.|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Удаляет группу отработки отказа.|

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Управление отработкой отказа баз данных SQL с отдельными базами данных и эластичными пулами

| Команда | Описание |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Создает группу отработки отказа и регистрирует ее на основном сервере и сервере-получателе.|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Удаляет группу отработки отказа с сервера, а также удаляет все входящие в нее базы данных-получатели. |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Возвращает конфигурацию группы отработки отказа. |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modifies the configuration of the failover group and/or adds one or more databases to an Azure SQL Database failover group|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Запускает отработку отказа группы отработки отказа на сервер-получатель. |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| Команда | Описание |
| --- | --- |
| [az sql instance-failover-group create](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Создает группу отработки отказа и регистрирует ее на основном сервере и сервере-получателе.|
| [az sql instance-failover-group update](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Изменяет конфигурацию группы отработки отказа.|
| [az sql instance-failover-group show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Возвращает конфигурацию группы отработки отказа.|
| [az sql instance-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Запускает отработку отказа группы отработки отказа на сервер-получатель.|
| [az sql instance-failover-group delete](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Удаляет группу отработки отказа. |

* * *

> [!IMPORTANT]
> Пример сценария см. в статье [Use PowerShell to configure an active geo-replication failover group for a single Azure SQL database](scripts/sql-database-add-single-db-to-failover-group-powershell.md) (Использование PowerShell для настройки группы отработки отказа активной георепликации для отдельной базы данных SQL Azure).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Manage SQL database failover groups with single and pooled databases

| API | Описание |
| --- | --- |
| [Создание или обновление группы отработки отказа](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Создает или обновляет группу отработки отказа. |
| [Удаление группы отработки отказа](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Удаляет группу отработки отказа с сервера. |
| [Плановая отработка отказа](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Выполняет отработку отказа из текущего основного сервера на этот сервер. |
| [Принудительная отработка отказа (возможна потеря данных)](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Выполняет отработку отказа из текущего основного сервера на этот сервер. Эта операция может привести к потере данных. |
| [Получение группы отработки отказа](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Получает группу отработки отказа. |
| [Вывод списка групп отработки отказа с фильтрацией по серверу](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Перечисляет группы отработки отказа на сервере. |
| [Обновление группы отработки отказа](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Обновляет группу отработки отказа. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: Manage failover groups with Managed Instances

| API | Описание |
| --- | --- |
| [Создание или обновление группы отработки отказа](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Создает или обновляет группу отработки отказа. |
| [Удаление группы отработки отказа](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Удаляет группу отработки отказа с сервера. |
| [Плановая отработка отказа](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Выполняет отработку отказа из текущего основного сервера на этот сервер. |
| [Принудительная отработка отказа (возможна потеря данных)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |Выполняет отработку отказа из текущего основного сервера на этот сервер. Эта операция может привести к потере данных. |
| [Получение группы отработки отказа](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Получает группу отработки отказа. |
| [Перечисление групп отработки отказа – Перечисление по расположению](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Перечисляет группы отработки отказа в расположении. |

## <a name="next-steps"></a>Дальнейшие действия

- For detailed tutorials, see
    - [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md)
    - [Add elastic pool to a failover group](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md)
- Ознакомьтесь с примерами скриптов в следующих статьях:
  - [Use PowerShell to configure active geo-replication for a single database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Use PowerShell to configure active geo-replication for a pooled database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Use PowerShell to add an Azure SQL Database single database to a failover group](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md)
- Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь с разделом [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).
- Чтобы узнать об использовании автоматически создаваемых резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md).
- Дополнительные сведения о требованиях к проверке подлинности для новых сервера-источника и базы данных-источника см. в статье [Безопасность базы данных SQL после аварийного восстановления](sql-database-geo-replication-security-config.md).
