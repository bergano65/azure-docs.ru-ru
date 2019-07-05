---
title: Azure Site Recovery. Часто задаваемые вопросы | Документация Майкрософт
description: В этой статье приводятся ответы на распространенные вопросы об использовании Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: raynew
ms.openlocfilehash: a9c7aa2be945e4fbaa65bdd2a145d576422c5539
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491767"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: вопросы и ответы
В этой статье приведены часто задаваемые вопросы об Azure Site Recovery.</br>
Для конкретных запросов на разных ASR сценариев см. сценарий определенные вопросы и ответы.<br>

- [Аварийное восстановление виртуальных Машин Azure в Azure](azure-to-azure-common-questions.md)
- [Аварийное восстановление виртуальных Машин VMware в Azure](vmware-azure-common-questions.md)
- [Аварийное восстановление виртуальных Машин Hyper-V в Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Общие сведения

### <a name="what-does-site-recovery-do"></a>Какие функции выполняет служба Site Recovery?
Служба Site Recovery помогает реализовать стратегии непрерывности бизнес-процессов и аварийного восстановления (BCDR), автоматизируя процессы репликации виртуальных машин Azure между регионами, локальных виртуальных машин и физических серверов в Azure, а также локальных виртуальных машин в дополнительный центр обработки данных и осуществляя управление ими. [Узнайте больше](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Защитить виртуальной машины с диском Docker?

Нет, этот сценарий не поддерживается.

## <a name="service-providers"></a>Поставщики услуг

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Я являюсь поставщиком услуг. Работает ли служба Site Recovery для моделей выделенной и общей инфраструктуры?
Да, Site Recovery поддерживает как выделенную, так и общую модели инфраструктуры.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Предоставляется ли удостоверение клиента поставщика услуг службе Site Recovery?
№ Клиент остается анонимным. Клиентам не требуется доступ к порталу Site Recovery. Только администратор поставщика служб взаимодействует с порталом.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Будут ли данные приложения клиента передаваться в Azure?
При репликации между сайтами, принадлежащими поставщику служб, данные приложения никогда не передаются в Azure. Данные шифруются при передаче и реплицируются непосредственно между сайтами поставщика услуг.

При репликации в Azure данные приложения передаются в службу хранилища Azure, а не в службу Site Recovery. Данные шифруются в процессе передачи и остаются зашифрованными в Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Будут ли клиенты получать счета за какие-либо службы Azure?
№ Azure выставляет счета только поставщику службы. Поставщики служб отвечают за создание специальных счетов для своих клиентов.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Нужно ли всегда запускать виртуальные машины при репликации в Azure?
Нет, данные реплицируются в службу хранилища Azure в вашей подписке. При тестовой отработке отказа (отработке аварийного восстановления) или фактической отработке отказа Site Recovery автоматически создает виртуальные машины в вашей подписке.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Обеспечивается ли изоляция на уровне клиента при репликации в Azure?
Да.

### <a name="what-platforms-do-you-currently-support"></a>Какие платформы поддерживаются на данный момент?
Поддерживаются развертывания на основе Azure Pack, системы облачной платформы и System Center 2012 и более поздних версий. [Узнайте больше](https://technet.microsoft.com/library/dn850370.aspx) об интеграции Azure Pack и Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Поддерживаются ли отдельные развертывания Azure Pack или односерверные развертывания VMM?
Да, можно выполнять репликацию виртуальных машин Hyper-V в Azure или между сайтами поставщиков служб.  Обратите внимание, что при репликации между сайтами поставщика услуг интеграция Runbook Azure недоступна.

## <a name="pricing"></a>Цены

### <a name="where-can-i-find-pricing-information"></a>Где можно найти сведения о ценах?
Просмотрите [цены на Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) сведения.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Как рассчитать приблизительные расходы при использовании Site Recovery?

Можно использовать [калькулятором цен](https://aka.ms/asr_pricing_calculator) для оценки затрат при использовании Site Recovery.

Подробные оценки затрат на запуск планировщика развертывания для [VMware](https://aka.ms/siterecovery_deployment_planner) или [Hyper-V](https://aka.ms/asr-deployment-planner)и использовать [отчет об оценке затрат](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Управляемые диски теперь используются для репликации виртуальных машин VMware и физических серверов. Понесу ли я дополнительная плата за учетную запись хранения кэша с управляемыми дисками?

Нет, были внесены никакие дополнительные платы для кэша. При репликации в стандартную учетную запись хранения, это хранилище кэша является частью одной целевой учетной записи хранения.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Я использую Azure Site Recovery больше месяца. Могу ли я по-прежнему рассчитывать на бесплатное использование в течение начального периода (31 день) для каждого защищенного экземпляра?

Да. В течение первого 31-дневного периода использования Azure Site Recovery плата за каждый защищенный экземпляр не взимается. Например если вы пользовались защитой 10 экземпляров для последних 6 месяцев, и теперь подключаете 11-й экземпляр к Azure Site Recovery, плата не для этого экземпляра в течение первого 31 дня. По-прежнему за первые 10 экземпляров плата за Azure Site Recovery взимается, так как они были защищены более 31 дня.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Будет ли с меня взиматься плата за другие услуги Azure в течение первого 31-дневного периода?

Да, несмотря на то, что Site Recovery предусмотрена бесплатная в течение первого 31-дневный период защищенного экземпляра, может взиматься плата за службу хранилища Azure, транзакции с хранилищем и передачу данных. При восстановлении виртуальной машины может взиматься плата за вычислительные операции Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Взимается ли плата для выполнения отработки отказа Детализирует и тестирования аварийного восстановления?

Отдельные затраты для аварийного восстановления отсутствуют. После создания виртуальной Машины после отработки отказа будет плата за вычислительные ресурсы.



## <a name="security"></a>Безопасность

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Отправляются ли данные репликации в службу Site Recovery?
Нет, служба Site Recovery не перехватывает реплицируемые данные и не получает сведения о компонентах, запущенных на ваших виртуальных машинах или физических серверах.
Обмен данными репликации осуществляется между локальными узлами Hyper-V, низкоуровневыми оболочками VMware или физическими серверами и хранилищем Azure или дополнительным сайтом. Служба Site Recovery не обладает способностью перехватывать эти данные. В службу Site Recovery передаются только метаданные, необходимые для управления репликацией и отработкой отказа.  

Site Recovery имеет сертификаты ISO 27001:2013, 27018, HIPAA, DPA и сейчас проходит проверки на соответствие SOC2 и FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Для соответствия требованиям даже метаданные из локальных сред должны оставаться в одном географическом регионе. Может ли служба Site Recovery помочь нам?
Да. При создании хранилища Site Recovery в регионе мы обеспечиваем хранение всех метаданных, необходимых для управления репликацией и отработкой отказа, в пределах этого региона.

### <a name="does-site-recovery-encrypt-replication"></a>Выполняет ли служба Site Recovery шифрование репликации?
При репликации виртуальных машин и физических серверов между локальными сайтами поддерживается шифрование в процессе передачи. При репликации виртуальных машин и физических серверов в Azure поддерживается как шифрование при передаче, так и [шифрование на месте (в Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption).




## <a name="disaster-recovery"></a>Аварийное восстановление

### <a name="what-can-site-recovery-protect"></a>Что можно защитить с помощью службы Site Recovery?
* **Виртуальные машины Azure**. Служба Site Recovery может реплицировать любую нагрузку, выполняющуюся на поддерживаемой виртуальной машине Azure.
* **Виртуальные машины Hyper-V**. Служба Site Recovery позволяет защитить любую рабочую нагрузку, выполняющуюся на виртуальной машине Hyper-V.
* **Физические серверы**. Служба Site Recovery позволяет защитить физические серверы под управлением Windows или Linux.
* **Виртуальные машины VMware**. Служба Site Recovery позволяет защитить любую рабочую нагрузку, выполняющуюся на виртуальной машине VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Какие рабочие нагрузки можно защитить с помощью службы Site Recovery?
Службу Site Recovery можно использовать для защиты большинства рабочих нагрузок, запущенных на поддерживаемой виртуальной машине или физическом сервере. Служба Site Recovery также поддерживает репликацию уровня приложения, позволяя восстанавливать приложения до программируемого состояния. Она интегрируется с приложениями Майкрософт, включая SharePoint, Exchange, Dynamics, SQL Server и Active Directory, а также тесно взаимодействует с решениями ведущих производителей, в том числе Oracle, SAP, IBM и Red Hat. [Подробнее](site-recovery-workload.md) о защите рабочей нагрузки.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Можно ли управлять аварийным восстановлением в офисах филиалов с помощью службы Site Recovery?
Да. Используя службу Site Recovery для управления репликацией и отработкой отказа в офисах филиалов, вы получаете объединенные возможности управления и представление для всех рабочих нагрузок офисов филиалов в центральном расположении. Таким образом, можно легко выполнить отработку отказа и управлять аварийным восстановлением для всех филиалов, не выходя из главного офиса.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Поддерживается аварийное восстановление для виртуальных машин Azure?

Да, Site Recovery поддерживает аварийного для виртуальных машин Azure между регионами Azure. [Просмотрите часто задаваемые вопросы](azure-to-azure-common-questions.md) о аварийного восстановления виртуальных Машин Azure.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Поддерживается аварийное восстановление для виртуальных машин VMware?

Да, Site Recovery поддерживает аварийное восстановление локальных виртуальных машин VMware. [Просмотрите часто задаваемые вопросы](vmware-azure-common-questions.md) для аварийного восстановления виртуальных машин VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Поддерживается ли аварийного восстановления для виртуальных машин Hyper-V?
Да, Site Recovery поддерживает аварийное восстановление локальных виртуальных машин Hyper-V. [Просмотрите часто задаваемые вопросы](hyper-v-azure-common-questions.md) для аварийного восстановления виртуальных машин Hyper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Аварийное восстановление поддерживается для физических серверов?
Да, Site Recovery поддерживает аварийное восстановление локальных физических серверов под управлением Windows и Linux в Azure или на дополнительный сайт. Дополнительные сведения о требованиях для аварийного восстановления в [Azure](vmware-physical-azure-support-matrix.md#replicated-machines), а в[вторичного сайта](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Обратите внимание, что физические серверы запустятся в качестве виртуальных машин в Azure после отработки отказа. Сейчас не поддерживается восстановление размещения из Azure на локальный физический сервер. Вы можете только восстановить размещение виртуальной машины VMware.





## <a name="replication"></a>Репликация

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Можно выполнить репликацию через VPN-подключение типа "сеть — сеть" в Azure?
Azure Site Recovery реплицирует данные в учетную запись хранилища Azure или управляемых дисков через общедоступную конечную точку. Репликация не выполняется через VPN-подключение типа "сеть — сеть". 

### <a name="why-cant-i-replicate-over-vpn"></a>Почему я не могу выполнить репликацию через VPN?

При выполнении репликации в Azure трафик репликации достигает общедоступных конечных точек в хранилище Azure. Таким образом репликацию можно выполнять только через общедоступный Интернет с помощью ExpressRoute (пиринг Майкрософт, или существующий общедоступный пиринг), а VPN не будет работать.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Можно ли использовать Riverbed SteelHeads для репликации?

Наш партнер, Riverbed, содержатся подробные указания по работе с Azure Site Recovery. Просмотрите их [руководство по решению](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Можно ли использовать ExpressRoute для репликации виртуальных машин в Azure?
Да, [ExpressRoute можно использовать](concepts-expressroute-with-site-recovery.md) для репликации локальных виртуальных машин в Azure.

- Azure Site Recovery реплицирует данные в хранилище Azure через общедоступную конечную точку. Вам нужно настроить [пиринг Майкрософт](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) или использовать существующее [общедоступного пиринга](../expressroute/expressroute-circuit-peerings.md#publicpeering) (не рекомендуется для новых каналов) использовать ExpressRoute для репликации Site Recovery.
- Пиринг Майкрософт является рекомендуемым доменом маршрутизации для репликации.
- Репликация через частный пиринг не поддерживается.
- Если вы защищаете VMware или физических компьютеров, убедитесь, что [требования к сети](vmware-azure-configuration-server-requirements.md#network-requirements) для сервера конфигурации также выполняются. Сервер конфигурации требуется подключение к определенным URL-адресам для оркестрации репликации Site Recovery. ExpressRoute не может использоваться для этого подключения.
- После отработки отказа виртуальных машин в виртуальную сеть Azure доступ к можно получать с помощью [частного пиринга](../expressroute/expressroute-circuit-peerings.md#privatepeering), настроенного в виртуальной сети Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>При репликации в Azure, укажите тип учетной записи хранения или управляемый диск необходимо?

Необходимо, чтобы в хранилище LRS или GRS. Рекомендуется использовать учетную запись хранения GRS, чтобы обеспечить устойчивость данных в случае отключения электричества в регионе или при отсутствии возможности восстановления основного региона. Учетная запись должна находиться в том же регионе, что и хранилище служб восстановления. При развертывании Site Recovery на портале Azure хранилище класса Premium поддерживается для репликации виртуальных машин VMware, Hyper-V и физических серверов. Управляемые диски поддерживают только LRS.

### <a name="how-often-can-i-replicate-data"></a>Как часто можно реплицировать данные?
* **Hyper-V**. Виртуальные машины Hyper-V можно реплицировать каждые пять минут, o 30 секунд (за исключением хранилище уровня "премиум")
* **Azure виртуальные машины, виртуальные машины VMware, физических серверов:** Здесь частота репликации не имеет значения. Репликация является непрерывной.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Можно ли расширить репликацию с существующего сайта восстановления до сайта третьего уровня?
Расширенная репликация и цепочка репликации не поддерживаются. Запросите эту функцию на [форуме отзывов и предложений](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Можно ли выполнить первую процедуру репликации в Azure в автономном режиме?
Эта возможность не поддерживается. Запросите эту функцию на [форуме отзывов и предложений](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Можно ли исключить из репликации отдельные диски?
Эта возможность поддерживается при репликации виртуальных машин VMware и Hyper-V в Azure с помощью портала Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Можно ли реплицировать виртуальные машины с динамическими дисками?
Динамические диски поддерживаются при репликации виртуальных машин Hyper-V, а также при репликации виртуальных машин VMware и физических компьютеров в Azure. Диск операционной системы должен представлять собой базовый диск.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Можно ли регулировать пропускную способность, выделенную для трафика репликации?
Да. Дополнительные сведения о регулировании пропускной способности в следующих статьях:

* [Планирование ресурсов для репликации виртуальных машин VMware и физических серверов](site-recovery-plan-capacity-vmware.md)
* [Запуск планировщика ресурсов Hyper-V для Site Recovery](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Отработка отказа
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Если я отработке отказа в Azure, как получить доступ к виртуальным машинам Azure после отработки отказа?

Доступ к виртуальным машинам Azure можно получить через безопасное подключение к Интернету, подключение VPN типа "сеть — сеть" или с помощью Azure ExpressRoute. Необходимо подготовить несколько действий для подключения. [Узнайте больше](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Каким образом среда Azure обеспечивает отказоустойчивость данных при отработке отказа?
Среда Azure разработана для обеспечения отказоустойчивости. Site Recovery изначально рассчитана на отработку отказа в дополнительный ЦОД Azure, в соответствии с соглашением об уровне ОБСЛУЖИВАНИЯ Azure. При обработке отказа Azure обеспечивает хранение метаданных и хранилищ в географическом регионе, выбранном для хранилища.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Что произойдет, если при выполнении репликации между двумя центрами обработки данных в основном центре обработки данных неожиданно возникнет сбой?
Можно активировать незапланированную отработку отказа на дополнительном сайте. Для отработки отказа службе Site Recovery не требуется подключение на основном сайте.

### <a name="is-failover-automatic"></a>Отработка отказа выполняется автоматически?
Отработка отказа не выполняется автоматически. Чтобы активировать ее, необходимо щелкнуть в соответствующем месте на портале или воспользоваться [Site Recovery PowerShell](/powershell/module/az.recoveryservices) . Восстановление размещения — простое действие, которое выполняется на портале Site Recovery.

Чтобы автоматизировать выполнение этих процедур, можно использовать локальное решение Orchestrator или Operations Manager для обнаружения сбоя виртуальной машины, а затем активировать отработку отказа, используя пакет SDK.

* Дополнительные сведения о планах восстановления см. [здесь](site-recovery-create-recovery-plans.md).
* [Узнайте больше](site-recovery-failover.md) об отработке отказа.
* [Узнайте больше](site-recovery-failback-azure-to-vmware.md) о восстановлении размещения виртуальных машин VMware и физических серверов.

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Если мой на локальный узел не отвечает или поврежден, можно ли выполнить восстановление размещения другой узел?
Да. Вы можете выполнить восстановление на другой узел из Azure.

* [Для виртуальных машин VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Для виртуальных машин Hyper-V](hyper-v-azure-failback.md#perform-failback)

## <a name="automation"></a>Служба автоматизации

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Можно ли автоматизировать сценарии Site Recovery с помощью пакета SDK?
Да. Рабочие процессы службы Site Recovery можно автоматизировать с помощью интерфейса REST API, PowerShell или пакета SDK для Azure. Ниже перечислены сценарии развертывания Site Recovery с помощью PowerShell, которые поддерживаются в настоящее время.

* [Репликация виртуальных машин Hyper-V из облаков VMM в Azure с помощью PowerShell (модель Resource Manager)](hyper-v-vmm-powershell-resource-manager.md)
* [Репликация виртуальных машин Hyper-V (без VMM) в Azure с помощью PowerShell (модель Resource Manager)](hyper-v-azure-powershell-resource-manager.md)
* [Репликация из VMware в Azure с помощью PowerShell для Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Обновление компонента или поставщик

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Где можно найти выпуска заметки или накопительные обновления Site Recovery

[Узнайте,](site-recovery-whats-new.md) о новых обновлениях и [получить сведения о свертке](service-updates-how-to.md).

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь со статьей [Обзор Site Recovery](site-recovery-overview.md)

