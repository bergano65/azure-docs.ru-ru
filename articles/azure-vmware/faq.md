---
title: Часто задаваемые вопросы
description: Содержит ответы на некоторые распространенные вопросы о решении Azure VMware.
ms.topic: conceptual
ms.date: 1/14/2021
ms.openlocfilehash: 8245cd8da983ce48ba88d7faef76ab9b7ceb8c26
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218592"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Часто задаваемые вопросы о решении Azure VMware

В этой статье мы будем отвечать на часто задаваемые вопросы о решении Azure VMware.

## <a name="general"></a>Общие сведения

### <a name="what-is-azure-vmware-solution"></a>Что такое Решение Azure VMware?

Так как предприятия модернизации стратегии для повышения гибкости бизнеса, снижения затрат и ускорения инноваций, гибридные облачные платформы появилось в качестве ключевых созидателями цифровых преобразований клиентов. Решение VMware для Azure объединяет программное обеспечение Software-Defined Data Center (SDDC) VMware с глобальной экосистемой облачных служб Microsoft Azure. Решение Azure VMware управляется в соответствии с требованиями к производительности, доступности, безопасности и соответствию.

## <a name="azure-vmware-solution-service"></a>Служба решения Azure VMware

### <a name="where-is-azure-vmware-solution-available-today"></a>Где находится решение Azure VMware, доступное сегодня?

Служба постоянно добавляется в новые регионы, поэтому для получения дополнительных сведений просмотрите [последние сведения о доступности службы](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) . 

### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Могут ли рабочие нагрузки, работающие в экземпляре решения Azure VMware, использовать или интегрировать со службами Azure?

Все службы Azure будут доступны клиентам Azure VMware. Ограничения производительности и доступности для конкретных служб следует решать на индивидуальной основе.

### <a name="what-guest-operating-systems-are-compatible-with-azure-vmware-solution"></a>Какие операционные системы на виртуальной машине совместимы с решением VMware для Azure?

Сведения о совместимости операционной системы на виртуальной машине с vSphere можно найти в статье Использование [руководств по совместимости с VMware](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=software&details=1&releases=485&page=1&display_interval=10&sortColumn=Partner&sortOrder=Asc&testConfig=16).  Чтобы определить версию vSphere, работающую в решении VMware для Azure, см. статью [версии программного обеспечения VMware](concepts-private-clouds-clusters.md#vmware-software-versions).

### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Будут ли у меня те же средства, которые я использую сейчас, для управления ресурсами частного облака?

Да. Портал Azure используется для развертывания и нескольких операций управления. Для управления ресурсами vSphere и NSX-T используются соответственно vCenter и NSX Manager.

### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Можно ли управлять частным облаком через локальный vCenter?

При запуске решение Azure VMware не поддерживает единый интерфейс управления в локальных и частных облачных средах. Для управления кластерами частного облака будут применяться vCenter и NSX Manager, расположенные в частном облаке.

### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Можно ли использовать vRealize Suite, работающий в локальной среде? 

Конкретные сценарии интеграции и использования могут оцениваться индивидуально.

### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Можно ли перенести виртуальные машины vSphere из локальных сред в частные облака решения Azure VMware?

Да. Миграцию виртуальных машин и vMotion можно использовать для перемещения виртуальных машин в частное облако, если выполняются стандартные требования Cross vCenter [VMotion](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952) .

### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>Требуется ли определенная версия vSphere в локальных средах?

Все облачные среды поставляются с VMware ХККС, vSphere 5,5 или более поздней версии в локальных средах для vMotion.

### <a name="what-does-the-change-control-process-look-like"></a>Как выглядит процесс управления изменениями?

Обновления, вносимые в службу, выполняются после стандартного процесса управления изменениями Microsoft Azure. За любые задачи администрирования рабочей нагрузки и связанные процессы управления изменениями клиенты отвечают самостоятельно.

### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Чем это решение отличается от Решения Azure VMware от CloudSimple?

Новое решение Azure VMware — это результат прямого сотрудничества корпорации Майкрософт и VMware на уровне поставщиков облачных служб. Новое решение полностью разработано, создано и поддерживается корпорацией Майкрософт, и оно было реализовано в VMware. В архитектуре решения являются единообразными, а технология VMware работает в выделенной инфраструктуре Azure.

### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Могут ли виртуальные машины Azure VMware управляться с помощью VMRC?
Да. Если система, на которой он установлен, имеет доступ к частному облаку vCenter и использует общедоступный DNS-сервер для разрешения имен узлов ESXi.

### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Существуют ли специальные инструкции по установке и использованию VMRC с виртуальными машинами Azure VMware?
Нет. Чтобы удовлетворить предварительные требования к виртуальной машине, следуйте [инструкциям, предоставленным VMware](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html). 

### <a name="is-vmware-hcx-supported-on-vpns"></a>Поддерживается ли VMware ХККС в VPN?
Нет, из-за требований к пропускной способности и задержкам.

### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>Можно ли использовать Azure бастиона для подключения к виртуальным машинам Azure VMware?
Azure бастиона — это служба, рекомендуемая для подключения к полю перехода, чтобы предотвратить предоставление доступа к решению VMware для Azure через Интернет. Вы не можете использовать Azure бастиона для подключения к виртуальным машинам Azure VMware, так как они не являются объектами Azure IaaS.

### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>Можно ли использовать внутренний Azure Load Balancer для виртуальных машин решения VMware Azure?
Нет. Azure Load Balancer Internal поддерживает только виртуальные машины Azure IaaS. Azure Load Balancer не поддерживает серверные пулы на основе IP-адресов; только виртуальные машины Azure или масштабируемые наборы виртуальных машин, в которых виртуальные машины Azure VMware не являются объектами Azure.

### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>Можно ли использовать существующий шлюз ExpressRoute для подключения к решению Azure VMware?
Да. Используйте существующий шлюз ExpressRoute для подключения к решению Azure VMware, если он не превышает ограничение в четыре канала ExpressRoute на виртуальную сеть. Для доступа к решению Azure VMware из локальной среды через ExpressRoute необходимо иметь Global Reach ExpressRoute, так как шлюз ExpressRoute не обеспечивает транзитную маршрутизацию между подключенными цепями.

## <a name="compute-network-storage-and-backup"></a>Расчеты, сеть, хранилище и резервное копирование

### <a name="is-there-more-than-one-type-of-host-available"></a>Доступно ли более одного типа узлов?

Доступен только один тип узла.

### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Каковы спецификации ЦП для каждого типа узла?

На серверах установлены по два 18-ядерных ЦП Intel с частотой 2,3 ГГц.

### <a name="how-much-memory-is-in-each-host"></a>Какой объем памяти на каждом узле?

На серверах установлено по 576 ГБ ОЗУ.

### <a name="what-is-the-storage-capacity-of-each-host"></a>Какая емкость хранилища на каждом узле?

Каждый узел ESXi имеет два vSAN дискграупс с уровнем емкости 15,2 ТБ и уровнем кэша NVMe 3,2 ТБ (1,6 ТБ в каждом дискграуп).

### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Какой объем пропускной способности сети доступен на каждом узле ESXi?

Каждый узел ESXi в решении VMware для Azure настроен с использованием сетевых карт 4 25 Гбит/с, подготовленных для трафика системы ESXi и двух сетевых адаптеров, подготовленных для трафика рабочей нагрузки. 

### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Хранятся ли данные в хранилищах данных vSAN, зашифрованных при хранении?

Да, все данные vSAN шифруются по умолчанию с помощью ключей, хранящихся в Azure Key Vault.

###  <a name="what-independent-software-vendors-isvs-backup-solutions-work-with-azure-vmware-solution"></a>Какие решения резервного копирования независимых поставщиков программного обеспечения (ISV) работают с решением VMware для Azure?

Commvault, Veritas и Veeam расширили свои решения резервного копирования для работы с решением VMware для Azure.  Однако любое решение резервного копирования, использующее VMware ВАДП с транспортным режимом Хотадд, будет работать прямо из Box в решении VMware для Azure.

### <a name="what-about-support-for-isv-backup-solutions"></a>Как насчет поддержки решений для резервного копирования ISV?

Поскольку эти решения резервного копирования устанавливаются и управляются клиентами, они могут обратиться к соответствующему поставщику программного обеспечения для поддержки. 

### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>Какова правильная политика хранения для настройки дедупликации?

Используйте политику хранилища *thin_provision* для шаблона виртуальной машины.  Значение по умолчанию — *thick_provision*.

### <a name="are-the-snmp-infrastructure-logs-shared"></a>Используются ли общие журналы инфраструктуры SNMP?

Нет.

## <a name="hosts-clusters-and-private-clouds"></a>Узлы, кластеры и частные облака

### <a name="is-the-underlying-infrastructure-shared"></a>Базовая инфраструктура используется совместно с другими?

Нет, узлы и кластеры частного облака выделяются эксклюзивно и безопасно очищаются до и после использования.

### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>Какое минимальное и максимальное число узлов на один кластер?

Кластеры могут масштабироваться в диапазоне от 3 до 16 узлов ESXi. Пробные кластеры ограничены тремя узлами.

### <a name="can-i-scale-my-private-cloud-clusters"></a>Можно ли масштабировать кластеры частного облака?

Да, кластеры масштабируются между минимальным и максимальным числом узлов ESXi. Пробные кластеры ограничены тремя узлами.

### <a name="what-are-trial-clusters"></a>Что такое пробные кластеры?

Пробные кластеры — это три кластера узлов, которые используются для одномесячных оценочных частных облаков решений Azure VMware.

### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Можно ли использовать высокопроизводительные узлы для пробных кластеров?

Нет. Высокопроизводительные узлы ESXi зарезервированы для использования в рабочих кластерах.

## <a name="azure-vmware-solution-and-vmware-software"></a>Решение Azure VMware и программное обеспечение VMware

### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Какие версии программного обеспечения VMware используются в частных облаках?

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


### <a name="do-private-clouds-use-vmware-nsx"></a>Используется ли VMware NSX в частных облаках?

Да, НСКС-T 2,5 используется для программно-определяемой сети в частных облаках решения VMware Azure.

### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Можно ли использовать VMware NSX-V в частном облаке?

Нет. В настоящее время поддерживается только версия NSX-T.

### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>Требуется ли НСКС в локальных средах или сетях, которые подключаются к частному облаку?

Нет, вам не обязательно использовать NSX локально.

### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Что такое расписание обновлений для программного обеспечения VMware в частном облаке?

Обновления пакетов для частного облака сохраняют программное обеспечение в одной версии пакета последних версий программного обеспечения из VMware. Версии программного обеспечения частного облака могут отличаться от самых последних версий отдельных программных компонентов (ESXi, НСКС-T, vCenter, vSAN).

### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Как часто будет обновляться стек программного обеспечения частного облака?

Программное обеспечение частного облака обновляется по расписанию, которое отслеживает выпуск пакета программного обеспечения из VMware. Для обновления частного облака не требуется время простоя.

## <a name="connectivity"></a>Соединение

### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Какие действия по планированию IP-адресов в сети потребуются для внедрения частных облаков в локальные среды?

Для развертывания частного облака решения Azure VMware требуется частная сеть/22-адресное пространство. Это частное адресное пространство не должно перекрываться с другими виртуальными сетями в подписке или в локальных сетях.
 
### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Разделы справки подключиться из локальных сред к частному облаку решения Azure VMware?

Подключиться к службе можно одним из двух способов: 

- Через виртуальную машину или шлюз приложений, развернутые в виртуальной сети Azure, для которой настроен пиринг с частным облаком через ExpressRoute.
- С помощью ExpressRoute Global Reach из локального центра обработки данных в канал Azure ExpressRoute.

### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Как подключить виртуальную машину с рабочей нагрузкой к Интернету или к конечной точке службы Azure?

Включите подключение к Интернету для частного облака на портале Azure. С помощью NSX-T Manager создайте маршрутизатор NSX-T T1 и логический коммутатор. Затем с помощью vCenter разверните виртуальную машину в том сегменте сети, который определен этим логическим коммутатором. Эта виртуальная машина будет иметь сетевой доступ к Интернету и службам Azure.

### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Нужно ли ограничивать доступ из Интернета к виртуальным машинам в логических сетях частного облака?

Нет. Сетевой трафик, входящий из Интернета напрямую в частные облака, по умолчанию запрещен.  Тем не менее вы можете предоставить виртуальные машины Azure VMware в Интернете через параметр [общедоступного IP-адреса](public-ip-usage.md) в портал Azure для частного облака решения Azure VMware.

### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Нужно ли ограничивать доступ к Интернету из виртуальных машин в логических сетях?

Да. Чтобы создать брандмауэр для ограничения доступа к виртуальной машине через Интернет, необходимо использовать диспетчер НСКС-T.


### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>Может ли решение Azure VMware использовать шлюзы ExpressRoute, размещенные в виртуальной сети Azure?
Да.

### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>Можно ли установить подключение между локальной сетью и решением VMware Azure через виртуальную сеть Azure через ExpressRoute Global Reach?
Виртуальная глобальная сеть Azure не обеспечивает транзитную маршрутизацию между двумя подключенными каналами ExpressRoute и невиртуальным шлюзом WAN ExpressRoute. С помощью ExpressRoute Global Reach обеспечивается подключение между локальной средой и решением VMware Azure, а также Глобальная сеть Майкрософт, а не виртуальный концентратор глобальной сети.

### <a name="could-i-use-hcx-through-public-internet-communications-as-a-workaround-for-the-non-supportability-of-hcx-when-using-vpn-s2s-with-vwan-for-on-premises-communications"></a>Можно ли использовать ХККС через общедоступную связь через Интернет в качестве обходного решения для неподдерживаемой ХККС при использовании VPN S2S с Вван для локальных подключений?

В настоящее время единственным поддерживаемым методом для VMware ХККС является подключение ExpressRoute.

## <a name="accounts-and-privileges"></a>Учетные записи и привилегии

### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Какие учетные записи и привилегии получит мое новое частное облако решения Azure VMware?

Вам предоставляются учетные данные для пользователя cloudadmin в vCenter и административный доступ к NSX-T Manager. Также доступна группа CloudAdmin, которую можно использовать для внедрения Azure Active Directory. Подробнее о концепциях доступа и удостоверений см. [здесь](concepts-identity.md).

### <a name="can-have-administrator-access-to-esxi-hosts"></a>Если ли у меня доступ администратора к узлам ESXi?

Нет, доступ администратора к узлам ESXi ограничен в соответствии с требованиями к безопасности этого решения.

### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Какие привилегии и разрешения я получу в vCenter?

У вас будут права группы CloudAdmin. Подробнее о концепциях доступа и удостоверений см. [здесь](concepts-identity.md).

### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Какие привилегии и разрешения я получу в NSX-T Manager?

Вы получите полные права администратора на НСКС-T и сможете управлять vSphere доступом на основе ролей, как и в случае с локальным центром обработки данных НСКС-T. Подробнее о концепциях доступа и удостоверений см. [здесь](concepts-identity.md).

> [!NOTE]
> В рамках развертывания частного облака создается и настраивается маршрутизатор T0. Любые изменения в этом логическом маршрутизаторе или на виртуальных машинах граничных узлов NSX-T могут повлиять на возможность подключения к частному облаку.

## <a name="billing-and-support"></a>Выставление счетов и поддержка

### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>Как будут структурированы цены для решения Azure VMware?

Общие вопросы по ценам см. на странице [цен](https://azure.microsoft.com/pricing/details/azure-vmware) на решения VMware для Azure. 

### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>Можно ли приобрести решение Azure VMware через CSP Майкрософт?

Да, клиенты могут развернуть решение VMware для Azure в подписке Azure, управляемой CSP.

### <a name="who-supports-azure-vmware-solution"></a>Кто поддерживает решение Azure VMware?

Корпорация Майкрософт предоставляет поддержку для решения VMware для Azure. Вы можете отправить [запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). 

Для подписок, управляемых CSP, первый уровень поддержки предоставляет поставщик решений таким же образом, как и CSP для других служб Azure.

### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Какие учетные записи нужно создать в частном облаке решения Azure VMware?

Вам потребуется учетная запись Azure в подписке Azure.

### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>Поддерживаются ли решения Red Hat в решении VMware для Azure?

Корпорация Майкрософт и Red Hat совместно используют интегрированную совместную службу поддержки, которая предоставляет единую контактную точку для экосистемы Red Hat, работающей на платформе Azure.  Как и другие службы платформы Azure, работающие с Red Hat Enterprise Linux, решение Azure VMware распространяется на облачный доступ и интегрированный тег поддержки. Red Hat Enterprise Linux поддерживается для работы на основе решения Azure VMware в Azure.

### <a name="is-vmware-hcx-enterprise-available-and-if-so-how-much-does-it-cost"></a>Доступна ли компания VMware ХККС Enterprise и, если да, то сколько ИТ стоит?

VMware ХККС Enterprise доступен в составе решения Azure VMware в виде *предварительной версии* функции или службы. Хотя VMware ХККС Enterprise для Azure VMware находится на этапе предварительной версии, это бесплатная функция или служба, которая подлежит предварительной версии условий обслуживания. После того, как служба VMware ХККС Enterprise станет общедоступной, вы получите 30-дневное уведомление о том, что выставление счетов будет переключаться. Вы можете отключить службу или отказаться от нее.

### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Разделы справки запросить увеличение квоты узла для решения VMware для Azure?

Для подписок, управляемых CSP, клиент должен отправить запрос партнеру. Затем Партнерская группа Взаимодействуйте с корпорацией Майкрософт, чтобы увеличить квоту для подписки. Дополнительные сведения см. в [статье Включение ресурсов для решения VMware в Azure](enable-azure-vmware-solution.md) . 

Для подписок EA используйте следующую процедуру. Во первых, вам потребуется:

* [Соглашение Enterprise Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) с корпорацией Майкрософт.
* Учетная запись Azure в подписке Azure.

Прежде чем можно будет создать ресурс решения Azure VMware, отправьте запрос в службу поддержки, чтобы узлы были распределены. Подтверждение и выполнение запроса занимает до пяти рабочих дней. Если у вас уже есть частное облако Решения Azure VMware и вы хотите выделить для него больше узлов, выполните те же действия.

1. В портал Azure в разделе **Справка и поддержка** создайте **[новый запрос на поддержку](https://rc.portal.azure.com/#create/Microsoft.Support)** и предоставьте следующие сведения для билета:
   - **Тип вопроса:** Технические требования
   - **Подписка:** Выберите подписку
   - **Служба:** Все службы > решение VMware для Azure
   - **Ресурс:** Общий вопрос 
   - **Сводка:** Требуется емкость
   - **Тип проблемы:** "Проблемы с управлением емкостью".
   - **Подтип проблемы:** "Запрос клиента на дополнительную квоту или емкость узла".

1. В **описании** запроса в службу поддержки на вкладке **сведения** укажите:

   - Проверка концепции или рабочая среда 
   - Имя региона
   - Число узлов
   - Другие сведения

   >[!NOTE]
   >Решение VMware для Azure предлагает минимум три узла для запуска частного облака и обеспечения избыточности N + 1 узлов. 

1. Выберите **Проверка + создать** , чтобы отправить запрос.

   Для подтверждения вашего запроса сотруднику службы поддержки потребуется до пяти рабочих дней.

   >[!IMPORTANT] 
   >Если у вас уже есть решение Azure VMware и вы запрашиваете дополнительные узлы, обратите внимание, что для размещения узлов требуется пять рабочих дней. 

1. Прежде чем можно будет подготавливать узлы, убедитесь, что вы зарегистрировали поставщик ресурсов **Microsoft. AVS** в портал Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Дополнительные способы регистрации поставщика ресурсов см. в статье [поставщики и типы ресурсов Azure](../azure-resource-manager/management/resource-providers-and-types.md). 

### <a name="are-reserved-instances-available-for-purchasing-through-the-cloud-solution-provider-csp-program"></a>Доступны ли зарезервированные экземпляры для приобретения с помощью программы поставщика облачных решений (CSP)?

Да. CSP может приобрести зарезервированные экземпляры для своих клиентов. Дополнительные сведения см. в разделе [экономия затрат с помощью зарезервированного экземпляра](reserved-instance.md). 

### <a name="does-azure-vmware-solution-offer-multi-tenancy-for-hosting-csp-partners"></a>Предлагает ли решение Azure VMware мультитенантность для размещения партнеров CSP?

Нет. Сейчас решение VMware для Azure не предлагает мультитенантность.

### <a name="will-traffic-between-on-premises-and-azure-vmware-solution-over-expressroute-incur-any-outbound-data-transfer-charge-in-the-metered-data-plan"></a>Будет ли трафик между локальной средой и решением VMware Azure через ExpressRoute привести к передаче исходящих данных в тарифный план данных?

Трафик в канале ExpressRoute решения Azure VMware не измеряется каким бы то ни было образом. Плата за трафик из канала ExpressRoute, подключающихся к локальной среде в Azure, будет рассчитываться в соответствии с тарифными планами ExpressRoute.


## <a name="customer-communication"></a>Взаимодействие с клиентами

### <a name="how-can-i-receive-an-alert-when-azure-sends-service-health-notifications-to-my-azure-subscription"></a>Как получить предупреждение, когда Azure отправляет уведомления о работоспособности службы в свою подписку Azure?

Проблемы службы, плановое техническое обслуживание, рекомендации по обеспечению безопасности, уведомления публикуются с помощью **службы работоспособности служб** в портал Azure.  Вы можете выполнять своевременные действия при настройке оповещений журнала действий для этих уведомлений. Дополнительные сведения см. [в статье Создание оповещений о работоспособности службы с помощью портал Azure](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

:::image type="content" source="media/service-health.png" alt-text="Снимок экрана с уведомлениями о работоспособности служб":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
