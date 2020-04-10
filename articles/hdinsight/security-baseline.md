---
title: Базовый унизел безопасности Azure для HDInsight
description: Базовый унизел безопасности Azure для HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 93a5bcd77bb4f42d9099cc1ddb1b5c3130c19059
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010143"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Базовый унизел безопасности Azure для HDInsight

Базовый унизел безопасности Azure для HDInsight содержит рекомендации, которые помогут вам улучшить безопасность развертывания.

Базовая версия этой службы взята из [версии 1.0 Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)которая содержит рекомендации о том, как можно обезопасить свои облачные решения в Azure с нашими рекомендациями по передовым практикам.

Для получения дополнительной информации смотрите [обзор базовых линий безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Защита ресурсов, использующих группы сетевой безопасности или брандмауэр Azure в виртуальной сети

**Руководство**: Безопасность периметра в Azure HDInsight достигается с помощью виртуальных сетей. Корпоративный администратор может создать кластер внутри виртуальной сети и использовать группу сетевой безопасности (NSG) для ограничения доступа к виртуальной сети. Только разрешенные IP-адреса в входящих правилах Группы сетевой безопасности смогут общаться с кластером Azure HDInsight. Эта конфигурация обеспечивает безопасность периметра. Все кластеры, развернутые в виртуальной сети, также будут иметь частную конечную точку, которая разрешает сядрь частному IP-адресу внутри Виртуальной сети для частного доступа HTTP к кластерным шлюзам.

Чтобы снизить риск потери данных с помощью эксфильтрации, ограничьте исходящий сетевой трафик для кластеров Azure HDInsight с помощью Azure Firewall.

Как развернуть Azure HDInsight в виртуальной сети и безопасно с группой сетевой безопасности:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Как ограничить исходящий трафик для кластеров Azure HDInsight с помощью брандмауэра Azure Firewall:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Мониторинг и регистрация конфигурации и трафика Vnets, Subnets и NICs

**Руководство**: Использование Центра безопасности Azure и рекомендации по защите сети для виртуальной сети, подсети и группы сетевой безопасности, используемые для обеспечения безопасности кластера Azure HDInsight. Включить журналы потока группы сетевой безопасности (NSG) и отправить журналы в учетную запись хранения Azure для аудита трафика. Вы также можете отправлять журналы потоков NSG в рабочее пространство Analytics Журналов журналов Azure и использовать аналитику azure Traffic Analytics для получения информации о движении в облаке Azure. Некоторые преимущества Azure Traffic Analytics заключаются в способности визуализировать сетевую активность и выявлять горячие точки, выявлять угрозы безопасности, понимать шаблоны движения и определять неправильное конфигурации сетей.

Как включить журналы потока NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить и использовать аналитику трафика Azure:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Понимание сетевой безопасности, предоставляемой Центром безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="13-protect-critical-web-applications"></a>1.3: Защита критически важных веб-приложений

**Руководство**: Не применимо; бенчмарк предназначен для Службы приложений Azure или вычисления ресурсов, размещая веб-приложения.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Запретить связь с известными вредоносными IP-адресами

**Руководство:** Для защиты от DDoS-атак включите стандартную защиту Azure DDoS в виртуальной сети, где развернут ваш Azure HDInsight. Используйте интегрированную систему информации об угрозах Azure Security Center, чтобы отказать в связи с известными вредоносными или неиспользуемыми IP-адресами в Интернете.

Как настроить DDoS-защиту:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Поймите Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Запись сетевых пакетов и журналов потока

**Руководство:** Включить группу сетевой безопасности (NSG) пороть журналы для NSG прилагается к подсети, используемой для защиты кластера Azure HDInsight. Запись журналов потока NSG в учетную запись хранения Azure для создания записей потоков. При необходимости для исследования аномальной активности включите пакет пакетAzового просмотра сети.

Как включить журналы потока NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить сетевой наблюдатель:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Развертывание сетевых систем обнаружения/предотвращения вторжений (IDS/IPS)

**Руководство**: Требование может быть выполнено Azure Security control ID 1.1; Развертывание кластера Azure HDInsight в виртуальную сеть и обеспечение безопасности с помощью группы сетевой безопасности (NSG).

Для Azure HDInsight существует несколько зависимостей, требующих входящего трафика. Входящий трафик управления не может быть отправлен через брандмауэр. Источники адресов для требуемого трафика управления известны и опубликованы. Создавайте правила Network Security Group с этой информацией, чтобы разрешить трафик из только доверенных мест, обеспечивая входящий трафик в кластеры.

Чтобы снизить риск потери данных с помощью эксфильтрации, ограничьте исходящий сетевой трафик для кластеров Azure HDInsight с помощью Azure Firewall.

Как развернуть HDInsight в виртуальной сети и безопасно с группой сетевой безопасности:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Понять зависимости HDInsight и использование брандмауэра:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

IP-адреса управления HDInsight:https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Управление трафиком для веб-приложений

**Руководство**: Не применимо; бенчмарк предназначен для Службы приложений Azure или вычисления ресурсов, размещая веб-приложения.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Минимизация сложности и административных накладных расходов на правила сетевой безопасности

**Руководство**: Используйте виртуальные сетевые теги для определения элементов управления доступом к сети в группах сетевой безопасности (NSG), которые подключены к подсети, в которую развернут кластер Azure HDInsight. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указывая имя тега службы (например, ApiManagement) в соответствующем поле источника или назначения правила, можно разрешить или отклонить трафик для соответствующей службы. Корпорация Майкрософт управляет адресными префиксами, охватываемыми тегом службы, и автоматически обновляет тег службы при изменении адресов.

Понять и использовать теги служб ы для Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**: Определите и внедрить стандартные конфигурации безопасности для сетевых ресурсов, связанных с кластером Azure HDInsight. Используйте псевдонимы Azure Policy в пространствах имен "Microsoft.HDInsight" и "Microsoft.Network" для создания пользовательских политик для аудита или обеспечения безопасности сетевой конфигурации кластера Azure HDInsight.

Можно также использовать планы Azure для упрощения крупномасштабных развертываний Azure путем упаковки ключевых элементов среды, таких как шаблоны управления ресурсами Azure, элементы управления RBAC и политики, в единое определение чертежа. Легко применять план к новым подпискам и средам, а также тонкой настройки управления и управления с помощью версий.

Как просмотреть доступные Aliases Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как создать план Azure:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="110-document-traffic-configuration-rules"></a>1.10: Правила конфигурации трафика документов

**Руководство**: Используйте теги для группы сетевой безопасности (NSGs) и других ресурсов, связанных с сетевой безопасностью и потоком трафика, которые связаны с кластером Azure HDInsight. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и/или продолжительность (и т.д.) для любых правил, разрешающие трафик в/из сети.

Используйте любое из встроенных определений политики Azure, связанных с пометкой, например "Требуемый тег и его значение", чтобы гарантировать, что все ресурсы создаются с помощью тегов и уведомлять вас о существующих немаркированных ресурсах.

Вы можете использовать интерфейс командной строки Azure PowerShell или Azure (CLI) для поиска или выполнения действий на ресурсах на основе их тегов.

Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Как создать виртуальную сеть:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Как создать NSG с config безопасности:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Используйте автоматизированные инструменты для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**: Используйте журнал действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений для сетевых ресурсов, связанных с развертыванием Azure HDInsight. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критически важных сетевых ресурсов.

Как просмотреть и получить события журнала активности Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Как создать оповещения в Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Используйте утвержденные источники синхронизации времени

**Руководство**: Microsoft поддерживает источники времени для компонентов кластера Azure HDInsight, можно обновить синхронизацию времени для развертывания вычислений.

Как настроить синхронизацию времени для вычислительных ресурсов Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Корпорация Майкрософт

### <a name="22-configure-central-security-log-management"></a>2.2: Настройка централизованного управления журналами безопасности

**Руководство**: Вы можете на борту кластера Azure HDInsight в Azure Monitor агрегировать данные безопасности, генерируемые кластером. Используйте пользовательские запросы для обнаружения угроз в среде и реагирования на них. 

Как сесть на борт кластера Azure HDInsight в Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Как создать пользовательские запросы для кластера Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Включить журнал аудита для ресурсов Azure

**Руководство:** Включите azure Monitor для кластера HDInsight, направьте его в рабочее пространство Log Analytics. Это позволит регистрировать соответствующую кластерную информацию и метрики ОС для всех кластерных узлов Azure HDInsight.

Как включить журнал для кластера HDInsight:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Как задать запрос журналов HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Сбор журналов безопасности из операционных систем

**Руководство**: На борту кластера Azure HDInsight для Azure Monitor. Убедитесь, что используемое рабочее пространство Log Analytics имеет период хранения журналов, установленный в соответствии с правилами соответствия организации.

Как сесть на борт кластера Azure HDInsight для Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Как настроить период удержания рабочего пространства analytics log:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="25-configure-security-log-storage-retention"></a>2.5: Настройка хранения журналов безопасности

**Руководство**: На борту кластера Azure HDInsight для Azure Monitor. Убедитесь, что используемое рабочее пространство Azure Log Analytics имеет период хранения журналов в соответствии с правилами соответствия организации.

Как сесть на борт кластера Azure HDInsight для Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Как настроить период удержания рабочего пространства analytics log:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="26-monitor-and-review-logs"></a>2.6: Мониторинг и обзор журналы

**Руководство**: Используйте запросы рабочего пространства Azure Log Analytics для запроса журналов Azure HDInsight:

Как создать пользовательские запросы для кластеров Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Включить оповещения об аномальной активности

**Руководство:** Используйте рабочее пространство Azure Log Analytics для мониторинга и оповещения об аномальных действиях в журналах безопасности и событиях, связанных с кластером Azure HDInsight.

Как управлять оповещениями в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Как упомяните данные журнала аналитики:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="28-centralize-anti-malware-logging"></a>2.8: Централизованное заготовку от вредоносных программ

**Руководство**: Azure HDInsight поставляется с предварительно установленным Clamscan и включенным для изображений кластерных узлов, однако вы должны управлять программным обеспечением и вручную агрегировать/контролировать любые журналы, которые производит Clamscan.

Поймите Clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="29-enable-dns-query-logging"></a>2.9: Включить журнал запросов DNS

**Руководство**: Реализация решения сторонних сторон для регистрации dns.

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="210-enable-command-line-audit-logging"></a>2.10: Включить журнал аудита командной строки

**Руководство**: Вручную настроить консоль входа на основе одного узла.

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Ведение перечня административных счетов

**Руководство**: Ведение записи локальной административной учетной записи, созданной во время кластерного подготовки кластера Azure HDInsight, а также любых других созданных учетных записей. Кроме того, при использовании интеграции Azure AD Azure AD есть встроенные роли, которые должны быть четко назначены и поэтому должны быть заданы. Используйте модуль Azure AD PowerShell для выполнения запросов на адок для обнаружения учетных записей, входят в состав административных групп.

Кроме того, можно использовать рекомендации Центра идентификации безопасности Azure и рекомендации по управлению доступом.

Как получить роль каталога в Azure AD с PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Как получить роль участников каталога в Azure AD с PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Как контролировать идентификацию и доступ в Центр безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Изменение паролей по умолчанию, где это применимо

**Руководство**: При подготовке кластера Azure требует создания новых паролей для веб-портала и безопасного доступа Shell (SSH). Нет никаких паролей по умолчанию для изменения, однако вы можете указать различные пароли для доступа SSH и веб-портала.

Как установить пароли при подготовке кластера Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Использование выделенных административных учетных записей

**Руководство:** Интеграция аутентификации для кластера Azure HDInsight с активным каталогом Azure. Создание политик и процедур, посвященных использованию выделенных административных учетных записей.

Кроме того, можно использовать рекомендации Центра идентификации безопасности Azure и рекомендации по управлению доступом.

Как интегрировать аутентификацию Azure HDInsight с помощью active-каталога Azure:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Как контролировать идентификацию и доступ в Центр безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Используйте одиночный вписаться (SSO) с активным каталогом Azure

**Руководство**: Используйте Azure HDInsight ID Broker для входной регистрации в кластеры Enterprise Security Package (ESP) с помощью многофакторной аутентификации без предоставления паролей. Если вы уже зарегистрировались в других службах Azure, таких как портал Azure, вы можете войти в свой кластер Azure HDInsight с одним интерфейсом регистрации (SSO).

Как включить Azure HDInsight ID Брокер:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Используйте многофакторную аутентификацию для всего доступа на основе активного каталога Azure

**Руководство**: Включите Azure AD MFA и следуйте рекомендациям Центра идентификации безопасности Azure и рекомендациям по управлению доступом. Кластеры Azure HDInsight с настроенным пакетом корпоративной безопасности могут быть подключены к домену, чтобы пользователи домена могли использовать свои учетные данные домена для проверки подлинности с кластерами и выполнения заданий больших данных. При включении аутентификации с помощью многофакторной аутентификации (MFA) пользователям будет предложено предоставить второй фактор аутентификации.

Как включить МИД в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Как контролировать личность и доступ в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Используйте выделенные машины (Привилегированные рабочие станции доступа) для всех административных задач

**Руководство**: Используйте PAW (привилегированные рабочие станции доступа) с многофакторной аутентификацией (MFA), настроенной для входа в кластеры Azure HDInsight и связанных с ними ресурсов.

Узнайте о рабочих станциях привилегированном доступе:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Как включить МИД в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Войти и предупредить о подозрительной активности с административных счетов

**Руководство**: Кластеры Azure HDInsight с настроенным пакетом корпоративной безопасности могут быть подключены к домену, чтобы пользователи домена могли использовать свои учетные данные домена для проверки подлинности. Вы можете использовать отчеты безопасности Azure Active Directory (AAD) для генерации журналов и предупреждений при возникновении подозрительной или небезопасной активности в среде AAD. Используйте Центр безопасности Azure для мониторинга активности идентификации и доступа.

Как определить пользователей AAD, помеченных для рискованной деятельности:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Как отслеживать идентификацию пользователей и доступ к деятельности в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Управление ресурсами Azure только из утвержденных мест

**Руководство**: Кластеры Azure HDInsight с настроенным пакетом корпоративной безопасности могут быть подключены к домену, чтобы пользователи домена могли использовать свои учетные данные домена для проверки подлинности. Используйте условные места доступа, чтобы обеспечить доступ только из конкретных логических групп диапазонов IP-адресов или стран/регионов.

Как настроить именованные места в Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="39-use-azure-active-directory"></a>3.9: Используйте активный каталог Azure

**Руководство**: Использование активного каталога Azure (AAD) в качестве центральной системы аутентификации и авторизации. AAD защищает данные, используя надежное шифрование данных в состоянии покоя и в пути. AAD также соли, хэши, и надежно хранит учетные данные пользователей.

Кластеры Azure HDInsight с настройкой Enterprise Security Package (ESP) могут быть подключены к домену, чтобы пользователи домена могли использовать свои учетные данные домена для проверки подлинности с помощью кластеров.

Как создать и настроить экземпляр AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Как настроить пакет корпоративной безопасности с помощью служб домена Active Directory в Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Регулярно проверяйте и согласовывая доступ пользователей

**Руководство**: Используйте активную проверку подлинности Azure Active Directory (AAD) с кластером Azure HDInsight. AAD предоставляет журналы, чтобы помочь обнаружить устаревшие учетные записи. Кроме того, используйте Azure Identity Access Reviews для эффективного управления членством в группах, доступом к корпоративным приложениям и ролевым назначениям. Доступ пользователя может быть рассмотрен на регулярной основе, чтобы убедиться, что только право пользователей имеют постоянный доступ. 

Как использовать обзоры доступа к идентификационным данным Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Попытка monitor получить доступ к деактивированным учетным записям

**Руководство**: Используйте журналы входа в систему и аудитА Azure Active Directory (AAD) для мониторинга попыток доступа к деактивированным учетным записям; эти журналы могут быть интегрированы в любой сторонний инструмент SIEM/monitoring.

Этот процесс можно упорядочить, создав диагностические настройки для учетных записей пользователей AAD, отправив журналы аудита и журналы входа в рабочее пространство Analytics Azure Log Analytics. Настройте желаемые оповещения в рабочей области Аналитики журналов Azure.

Как интегрировать журналы активности Azure в Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Оповещение об отклонении поведения входа в учетную запись

**Руководство**: Кластеры Azure HDInsight с настраиваемым пакетом корпоративной безопасности (ESP) могут быть подключены к домену, чтобы пользователи домена могли использовать свои учетные данные домена для проверки подлинности с помощью кластеров.  Используйте функцию обнаружения рисков и защиты от личных данных Azure Active Directory (AAD) для настройки автоматизированных ответов на обнаруженные подозрительные действия, связанные с идентификаторами пользователей. Кроме того, можно проемить данные в Azure Sentinel для дальнейшего изучения.

Как посмотреть AAD рискованные ввоза:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Как настроить и включить политики защиты от идентификации:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Предоставить корпорации Майкрософт доступ к соответствующим данным клиентов во время сценариев поддержки

**Руководство**: Недоступно; Локбокс клиента еще не поддерживается для Azure HDInsight.

Список поддерживаемых служб блокировки клиентов:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

## <a name="data-protection"></a>Защита данных

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Ведение перечня конфиденциальной информации

**Руководство**: Используйте теги на ресурсах, связанных с развертыванием Azure HDInsight, чтобы помочь отслеживать ресурсы Azure, которые хранят или обрабатывают конфиденциальную информацию.

Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Изолировать системы хранения или обработки конфиденциальной информации

**Руководство**: Реализация отдельных подписок и/или групп управления для разработки, тестирования и производства. Кластеры Azure HDInsight и любые связанные с ними учетные записи хранения должны быть разделены виртуальной сетью/подсетью, соответствующим образом помечены и защищены в группе сетевой безопасности (NSG) или Azure Firewall. Кластерные данные должны содержаться в защищенной учетной записи хранения azure или в хранилище озер данных Azure (Gen1 или Gen2).

Выберите параметры хранения для кластера Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Как обеспечить безопасность хранилища озер данных Azure:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Как защитить учетные записи хранения данных Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Руководство**: Для кластеров Azure HDInsight, хранящихся или обрабатывающих конфиденциальную информацию, отметьте кластер и связанные с ними ресурсы как конфиденциальные с помощью тегов. Чтобы снизить риск потери данных с помощью эксфильтрации, ограничьте исходящий сетевой трафик для кластеров Azure HDInsight с помощью Azure Firewall.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт рассматривает все содержимое клиента как конфиденциальный и делает все усилия для защиты от потери данных клиентов и их воздействия. Для обеспечения безопасности данных клиентов в Azure корпорация Майкрософт внедрила и поддерживает набор надежных средств управления и возможностей защиты данных.

Как ограничить исходящий трафик для кластеров Azure HDInsight с помощью брандмауэра Azure Firewall:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Понимание защиты данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Общие сведения

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Шифрование всей конфиденциальной информации в пути

**Руководство**: Шифрование всей конфиденциальной информации в пути. Убедитесь, что клиенты, подключившись к кластеру Azure HDInsight или кластерным хранилищам данных (учетные записи хранения данных Azure Data Lake Gen1/Gen2), могут вести переговоры о TLS 1.2 или более. Ресурсы Microsoft Azure будут вести переговоры о TLS 1.2 по умолчанию. 

Поймите шифрование хранилища данных Azure Data Lake в пути:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Поймите шифрование учетной записи хранения данных Azure в пути:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Общие сведения

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Используйте инструмент активного обнаружения для идентификации конфиденциальных данных

**Руководство**: Функции идентификации, классификации и предотвращения потерь пока не доступны для ресурсов Azure Storage или вычислений. Внедрить стороннее решение, если это необходимо для целей соответствия.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт рассматривает все содержимое клиента как конфиденциальный и делает все усилия для защиты от потери данных клиентов и их воздействия. Для обеспечения безопасности данных клиентов в Azure корпорация Майкрософт внедрила и поддерживает набор надежных средств управления и возможностей защиты данных.

Понимание защиты данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Общие сведения

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Используйте Azure RBAC для управления доступом к ресурсам

**Руководство**: С пакетом безопасности предприятия Azure HDInsight (ESP) можно использовать Apache Ranger для создания и управления мелкозернистыми политиками управления доступом и запутывания данных для данных, хранящихся в файлах, папках, базах данных, таблицах и строках/столбах. Админ hadoop может настроить ролевой контроль доступа (RBAC) для обеспечения Apache Hive, HBase, Kafka и Spark с помощью этих плагинов в Apache Ranger.

Настройка политик RBAC с Apache Ranger позволяет связать разрешения с ролью в организации. Этот уровень абстракции позволяет гарантировать, что у людей есть только разрешения, необходимые для выполнения их рабочих обязанностей.

Конфигурации пакета корпоративной безопасности с помощью служб домена Active Directory в HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Обзор корпоративной безопасности в Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Используйте предотвращение потери данных на основе хоста для обеспечения контроля доступа

**Руководство**: Для кластеров Azure HDInsight, хранящихся или обрабатывающих конфиденциальную информацию, отметьте кластер и связанные с ними ресурсы как конфиденциальные с помощью тегов. Функции идентификации, классификации и предотвращения потерь пока не доступны для ресурсов Azure Storage или вычислений. Внедрить стороннее решение, если это необходимо для целей соответствия.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт рассматривает все содержимое клиента как конфиденциальный и делает все усилия для защиты от потери данных клиентов и их воздействия. Для обеспечения безопасности данных клиентов в Azure корпорация Майкрософт внедрила и поддерживает набор надежных средств управления и возможностей защиты данных.

Понимание защиты данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Общие сведения

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Шифрование конфиденциальной информации в состоянии покоя

**Руководство**: При использовании базы данных Azure S'L для хранения метаданных Apache Hive и Apache Oozie убедитесь, что данные S'L всегда остаются зашифрованными. Для учетных записей хранения данных Azure и хранилища озер данных (Gen1 или Gen2) рекомендуется разрешить корпорации Майкрософт управлять ключами шифрования, однако у вас есть возможность управлять своими ключами.

Как управлять ключами шифрования для учетных записей хранения Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Как создать хранилище озер данных Azure с помощью ключей шифрования, управляемых клиентом:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Понять шифрование для базы данных Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Как настроить прозрачное шифрование данных для базы данных СЗЛ с помощью ключей, управляемых клиентом:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Общие сведения

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Войти и предупредить об изменениях в критических ресурсах Azure

**Руководство:** Настройка диагностических настроек для учетных записей хранения Azure, связанных с кластерами Azure HDInsight, для мониторинга и регистрации всех операций CRUD с помощью кластерных данных. Включить аудит любых учетных записей хранения данных или складов озер данных, связанных с кластером Azure HDInsight.

Как включить дополнительную систему регистрации/аудит учетной записи хранения Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Как включить дополнительную регистрацию/аудит для хранения озер данных Azure:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

## <a name="vulnerability-management"></a>Управление уязвимостями

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Запуск автоматизированных инструментов сканирования уязвимостей

**Руководство**: Реализация решения для управления уязвимостями сторонних сторон.

В качестве опционов, если у вас есть подписка Rapid7, qualys или любая другая подписка на платформу управления уязвимостями, вы можете использовать действия скрипта для установки агентов оценки уязвимостей на кластерных узлах Azure HDInsight и управления узлами через соответствующий портал.

Как установить агента Rapid7 вручную:

https://insightvm.help.rapid7.com/v1.0/docs/agent-installation-on-linux

Как установить агента Квалис вручную:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Как использовать действия скрипта:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: Автоматические обновления системы были включены для изображений кластерных узлов, однако необходимо периодически перезагружать кластерные узлы, чтобы обеспечить применение обновлений.

Корпорация Майкрософт для обслуживания и обновления базовых изображений узлов Azure HDInsight.

Как настроить график исправления ОС для кластеров HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Общие сведения

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Развертывание автоматизированного решения для управления патчами сторонних программ

**Руководство**: Используйте действия скриптов или другие механизмы для исправления кластеров Azure HDInsight. Созданные кластеры будут всегда содержать последние обновления, включая самые последние обновления безопасности.

Как настроить график исправления ОС для кластеров Azure HDInsight на основе Linux:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Как использовать действия скрипта:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Сравните сканирование уязвимости спина к спине

**Руководство**: Реализация решения для управления уязвимостями сторонних сторонних стран, которое имеет возможность сравнивать сканирование уязвимостей с течением времени. Если у вас есть подписка Rapid7 или qualys, вы можете использовать портал этого поставщика для просмотра и сравнения сканирования уязвимостей«бэк-назад».

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Используйте процесс оценки рисков, чтобы определить приоритеты в устранении обнаруженных уязвимостей

**Руководство**: Используйте общую программу оценки рисков (например, общую систему оценки уязвимости) или оценки риска по умолчанию, предоставляемые вашим сторонним инструментом сканирования.

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Используйте обнаружение активов Azure

**Руководство**: Используйте график ресурсов Azure для запроса/обнаружения всех ресурсов (таких как вычисления, хранение, сеть, порты и протоколы и т.д.), включая кластеры Azure HDInsight в рамках подписки(ы).  Убедитесь, что у вас есть соответствующие (читай) разрешения в арендаторе и можете перечислить все подписки Azure, а также ресурсы в рамках подписки.

Хотя классические ресурсы Azure могут быть обнаружены с помощью диаграммы ресурсов, настоятельно рекомендуется создавать и использовать ресурсы Azure Resource Manager в будущем.

Как создавать запросы с помощью Графика Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Как просмотреть подписку на Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Поймите Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="62-maintain-asset-metadata"></a>6.2: Поддержание метаданных активов

**Руководство**: Применяйте теги к ресурсам Azure, давая метаданные, чтобы логически организовать их в таксономию.

Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Удалить несанкционированные ресурсы Azure

**Руководство**: Используйте пометки, группы управления и отдельные подписки, где это уместно, для организации и отслеживания активов. Регулярно согласовывать инвентаризацию и своевременно исключать несанкционированные ресурсы из подписки.

Как создать дополнительные подписки Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Как создать группы управления:

https://docs.microsoft.com/azure/governance/management-groups/create

Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Ведение перечня утвержденных ресурсов Azure и названий программного обеспечения

**Руководство**: Определите список утвержденных ресурсов Azure и утвержденного программного обеспечения для ваших вычислительных ресурсов

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Мониторинг неодобренных ресурсов Azure

**Руководство**: Используйте политику Azure для ограничения типа ресурсов, которые могут быть созданы в подписке клиента (ы) с использованием следующих встроенных определений политики:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Используйте график ресурсов Azure для запроса/обнаружения ресурсов в подписке(s). Убедитесь, что все ресурсы Azure, присутствующие в среде, утверждены.

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как создавать запросы с помощью Графика Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Мониторинг неодобренных программных приложений в вычислительных ресурсах

**Руководство**: Реализация стороннего решения для мониторинга кластерных узлов для неодобренных программных приложений.

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Удалить неодобренные ресурсы и программные приложения Azure

**Руководство**: Используйте график ресурсов Azure для запроса/обнаружения всех ресурсов (таких как вычисления, хранение, сеть, порты и протоколы и т.д.), включая кластеры Azure HDInsight в рамках подписки(ы).  Удалите неутвержденные ресурсы Azure, которые вы обнаружите. Для кластерных узлов Azure HDInsight внедрить стороннее решение для удаления или оповещения о неодобренном программном обеспечении.

Как создавать запросы с помощью Графика Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="68-use-only-approved-applications"></a>6.8: Используйте только одобренные приложения

**Руководство**: Для кластерных узлов Azure HDInsight внедрить стороннее решение для предотвращения выполнения несанкционированного программного обеспечения.


**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="69-use-only-approved-azure-services"></a>6.9: Используйте только утвержденные службы Azure

**Руководство**: Используйте политику Azure для ограничения типа ресурсов, которые могут быть созданы в подписке клиента (ы) с использованием следующих встроенных определений политики:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как отказать в определенном типе ресурсов с помощью политики Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="610-implement-approved-application-list"></a>6.10: Реализация утвержденного списка заявок

**Руководство:** Для кластерных узлов Azure HDInsight реализуем стороннее решение для предотвращения выполнения несанкционированных типов файлов.


**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Ограничьте возможности пользователей взаимодействовать с менеджером ресурсов Azure с помощью скриптов

**Руководство**: Используйте Azure Conditional Access, чтобы ограничить возможности пользователей взаимодействовать с менеджером ресурсов Azure, нанастройки "Блок-доступ" для приложения "Управление Microsoft Azure".

Как настроить условный доступ, чтобы заблокировать доступ к менеджеру ресурсов Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ограничьте возможности пользователей по выполнению скриптов в вычислительных ресурсах

**Руководство**: Не применимо; Это не применимо к Azure HDInsight, так как пользователям (не администраторам) кластера не требуется доступ к отдельным узлам для выполнения заданий. Администратор кластера имеет корневой доступ ко всем кластерных узлам.

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Не применимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Физически или логически сегрегировать приложения высокого риска

**Руководство**: Не применимо; бенчмарк предназначен для Службы приложений Azure или вычисления ресурсов, размещая веб-приложения.

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Не применимо

## <a name="secure-configuration"></a>Настройка безопасности

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Создание безопасных конфигураций для всех ресурсов Azure

**Руководство:** Используйте псевдонимы Azure Policy в пространстве имен "Microsoft.HDInsight" для создания пользовательских политик для аудита или обеспечения безопасности сетевой конфигурации кластера HDInsight.

Как просмотреть доступные Aliases Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Создание безопасных конфигураций операционных систем

**Руководство**: Azure HDInsight Операционные системы Изображения управляется и поддерживается корпорацией Майкрософт. Клиент, ответственный за реализацию безопасных конфигураций для операционной системы кластерных узлов. 


**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Поддержание безопасных конфигураций ресурсов Azure

**Руководство**: Используйте политику Azure «отрицать» и «развертывать, если не существует» для обеспечения безопасных настроек для кластеров Azure HDInsight и связанных с ними ресурсов.

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Понять эффекты политики Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Поддержание безопасных конфигураций операционных систем

**Руководство**: Azure HDInsight Операционные системы Изображения управляется и поддерживается корпорацией Майкрософт. Клиент, ответственный за реализацию конфигурации состояния уровня ОС.


**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Общие сведения

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Безопасное хранение конфигурации ресурсов Azure

**Руководство:** При использовании пользовательских определений политики Azure используйте Azure DevOps или Azure Repos для безопасного хранения и управления кодом.

Как хранить код в Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Документация Репос Azure:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Безопасно хранить пользовательские изображения операционной системы

**Руководство**: Не применимо; пользовательские изображения, не применимые к Azure HDInsight.

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Не применимо

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Инструменты управления конфигурацией конфигурации системы развертывание

**Руководство**: Используйте псевдонимы Azure Policy в пространстве имен "Microsoft.HDInsight" для создания пользовательских политик для оповещения, аудита и обеспечения безопасности конфигураций систем. Кроме того, разработайте процесс и конвейер для управления исключениями политики.

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Развертывать инструменты управления конфигурацией системы для операционных систем

**Руководство**: Реализация стороннего решения для поддержания желаемого состояния для операционных систем кластерного узла.

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Внедрение автоматизированного мониторинга конфигурации для служб Azure

**Руководство:** Используйте псевдонимы Azure Policy в пространстве имен «Microsoft.HDInsight» для создания пользовательских политик для аудита или обеспечения конфигурации кластера HDInsight.

Как просмотреть доступные Aliases Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Внедрение автоматизированного мониторинга конфигурации для операционных систем

**Руководство**: Реализация стороннего решения для мониторинга состояния операционных систем кластерного узла.

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="711-manage-azure-secrets-securely"></a>7.11: Надежное управление секретами Azure

**Руководство**: Azure HDInsight включает в себя поддержку Apache Kafka для Apache Kafka. Эта функция позволяет вам владеть ключами, используемыми для шифрования неактивных данных, и управлять ими.

Все управляемые диски в Azure HDInsight защищены шифрованием службы хранения данных Azure (SSE). По умолчанию данные на этих дисках зашифрованы с помощью ключей, управляемых корпорацией Майкрософт. Если вы включите BYOK, вы предоставляете ключ шифрования для Azure HDInsight для использования и управления им с помощью Azure Key Vault.

Key Vault также может использоваться при развертывании Azure HDInsight для управления ключами для хранения кластеров (учетные записи хранения azure и хранилище озер лазурных данных)

Как взять с собой свой ключ для Apache Kafka на Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Как управлять ключами шифрования для учетных записей хранения Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Управление идентификаторами безопасно и автоматически

**Руководство:** Управляемые идентификаторы могут быть использованы в Azure HDInsight, чтобы позволить группам получить доступ к службам домена Azure Active Directory, получить доступ к файлам Azure Key Vault или получить доступ к файлам в Azure Data Lake Storage Gen2.

Понять управляемые идентификаторы с azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Устранить непреднамеренное воздействие учетных данных

**Руководство**: При использовании какого-либо кода, связанного с развертыванием Azure HDInsight, можно реализовать сканер учетных данных для идентификации учетных данных в коде. Сканер учетных данных также будет способствовать перемещению обнаруженных учетных данных в более безопасные места, такие как Убежище ключей Azure. 

Как настроить сканер учетных данных:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="malware-defense"></a>Защита от вредоносных программ

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Используйте централизованно управляемое программное обеспечение для борьбы с вредоносным ПО

**Руководство**: Azure HDInsight поставляется с предварительно установленным Clamscan и включенным для изображений кластерных узлов, однако вы должны управлять программным обеспечением и вручную агрегировать/контролировать любые журналы, которые производит Clamscan.

Поймите Clamscan для Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Предварительное сканирование файлов, которые будут загружены на некомпьютерные ресурсы Azure

**Руководство**: Microsoft Antimalware включенна на базовом узеле, который поддерживает службы Azure, однако она не выполняется на содержимом клиента.

Предварительное сканирование любых файлов, загруженных на любые ресурсы Azure, связанные с развертыванием кластера Azure HDInsight, такие как хранение озер данных, хранилище blob и т.д. Корпорация Майкрософт не может получить доступ к данным клиентов в этих случаях.

Поймите Microsoft Antimalware для облачных служб Azure и виртуальных машин:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Общие сведения

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Обеспечить обновление антивредоносного программного обеспечения и подписей

**Руководство**: Azure HDInsight поставляется с предварительно установленным Clamscan и включенным для изображений кластерных узлов. Clamscan будет выполнять обновления двигателя и определения автоматически, однако, агрегирование и управление журналами должны быть выполнены вручную.

Поймите Clamscan для Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

## <a name="data-recovery"></a>Восстановление данных

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Обеспечить регулярные автоматизированные резервные истектоли

**Руководство**: При использовании учетной записи хранения Azure для кластерного хранилища данных HDInsight выберите подходящий вариант избыточности (LRS, RS, GRS, RA-GRS).  При использовании базы данных Azure S'L для кластерного хранилища данных Azure HDInsight нанастройка Active Geo-репликации.

Как настроить избыточность хранилища для учетных записей хранения Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Как настроить избыточность для баз данных Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Выполните полное резервное копирование системы и резервное копирование любых ключей, управляемых клиентом

**Руководство:** При использовании учетной записи хранения Azure для кластерного хранилища кластера Azure HDInsight выберите подходящий вариант избыточности (LRS, ЗрС, GRS, RA-GRS). При использовании Azure Key Vault для любой части развертывания Azure HDInsight убедитесь, что ключи будут резервироваться.

Выберите параметры хранения для кластера Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Как настроить избыточность хранилища для учетных записей хранения Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Как создать резервную связь с ключами Убежища в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Проверка всех резервных ups, включая управляемые ключей клиента

**Руководство:** Если Azure Key Vault используется при развертывании Azure HDInsight, тест-восстановление резервных ключей, управляемых клиентами.

Как взять с собой свой ключ для Apache Kafka на Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Как восстановить ключи хранилища в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Обеспечить защиту резервных и управляемых ключей клиента

**Руководство**: Если Azure Key Vault используется при развертывании Azure HDInsight, включите Soft-Delete в Key Vault для защиты ключей от случайного или вредоносного удаления.

Как включить Soft-Delete в Хранилище ключей Azure:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

## <a name="incident-response"></a>реагирование на инциденты.

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)*

### <a name="101-create-an-incident-response-guide"></a>10.1: Создать руководство по реагированию на инциденты

**Руководство**: Убедитесь, что существуют письменные планы реагирования на инциденты, которые определяют роли персонала, а также этапы обработки/управления инцидентами.

Как настроить автоматизацию рабочих процессов в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Создание процедуры оценки и приоритетов инцидентов

**Руководство**: Центр безопасности присваивает серьезность предупреждениям, чтобы помочь вам определить приоритеты порядка, в котором вы посещаете каждое предупреждение, так что, когда ресурс скомпрометирован, вы можете добраться до него сразу. Тяжесть основана на том, насколько уверен центр безопасности в поиске или аналитический используется для выдачи оповещения, а также уровень уверенности, что был злой умысел за деятельность, которая привела к оповещению.

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="103-test-security-response-procedures"></a>10.3: Процедуры реагирования на тестовую безопасность

**Руководство**: Проведение упражнений для проверки возможностей реагирования на инциденты систем на регулярной каденции. Выявление слабых мест и пробелов и пересмотр плана по мере необходимости. Обратитесь к публикации NIST: Руководство по тестированию, обучению и осуществлению программ для ИТ-планов и возможностей:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Предоставьте контактные данные об инцидентах безопасности и назначьте уведомления о поместью для инцидентов безопасности

**Руководство**: Контактная информация об инцидентах безопасности будет использоваться корпорацией Майкрософт для связи с вами, если Центр реагирования на безопасность Майкрософт (MSRC) обнаружит, что доступ к вашим данным был получен незаконной или несанкционированной стороной.

Как установить контакт с безопасностью Центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Включите оповещения о безопасности в систему реагирования на инциденты

**Руководство**: Экспорт оповещений и рекомендаций Центра безопасности Azure с использованием функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать оповещения и рекомендации либо вручную, либо в постоянном, непрерывном режиме. Для потоковой передачи оповещений Sentinel можно использовать разъем данных Центра безопасности Azure.

Как настроить непрерывный экспорт:

https://docs.microsoft.com/azure/security-center/continuous-export

Как передавать оповещения в Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Автоматизация реагирования на оповещения о безопасности

**Руководство**: Используйте функцию автоматизации рабочего процесса в Центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" на оповещения и рекомендации по безопасности.

Как настроить автоматизацию рабочего процесса и логические приложения:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Проведение регулярного тестирования на проникновение ресурсов Azure и обеспечение исправления всех критических данных по безопасности в течение 60 дней

**Руководство**: Пожалуйста, следуйте правилам взаимодействия корпорации Майкрософт, чтобы убедиться, что ваши тесты на проникновение не нарушают политики Майкрософт:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Вы можете найти более подробную информацию о стратегии Microsoft и выполнении Red Teaming и тестировании на проникновение в реальном времени в майкарософт в отношении управляемой Microsoft облачной инфраструктуры, служб и приложений:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Общие сведения

## <a name="next-steps"></a>Следующие шаги

- Посмотреть [тест безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Узнайте больше об [базовых линиях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
