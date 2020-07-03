---
title: Базовый план безопасности Azure для HDInsight
description: Базовый план безопасности Azure для HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 62188aefffd05af49bb00c242b266e808b991c84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188180"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Базовый план безопасности Azure для HDInsight

Базовый план безопасности Azure для HDInsight содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовая информация для этой службы взята из [теста безопасности Azure версии 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), который содержит рекомендации по защите облачных решений в Azure с помощью наших рекомендаций.

Дополнительные сведения см. в статье [Общие сведения о базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в разделе Security [Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Защитите ресурсы с помощью групп безопасности сети или брандмауэра Azure в виртуальной сети.

**Руководство**. безопасность периметра в Azure HDInsight достигается через виртуальные сети. Администратор предприятия может создать кластер в виртуальной сети и использовать группу безопасности сети (NSG) для ограничения доступа к виртуальной сети. Только разрешенные IP-адреса в правилах группы безопасности входящих сетей могут взаимодействовать с кластером Azure HDInsight. Эта конфигурация обеспечивает безопасность периметра. Все кластеры, развернутые в виртуальной сети, также будут иметь закрытую конечную точку, которая разрешается в частный IP-адрес в виртуальной сети для доступа через частный протокол HTTP к шлюзам кластера.

Чтобы снизить риск потери данных через утечка, Ограничьте исходящий сетевой трафик для кластеров Azure HDInsight с помощью брандмауэра Azure.

Как развернуть Azure HDInsight в виртуальной сети и обеспечить безопасность с помощью группы безопасности сети:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Ограничение исходящего трафика для кластеров Azure HDInsight с помощью брандмауэра Azure:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Используйте центр безопасности Azure и исправьте рекомендации по защите сети для виртуальной сети, подсети и группы безопасности сети, используемой для защиты кластера Azure HDInsight. Включите журналы потоков группы безопасности сети (NSG) и отправьте журналы в учетную запись хранения Azure для аудита трафика. Вы также можете отправить журналы потоков NSG в рабочую область Azure Log Analytics и использовать Аналитика трафика Azure для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Azure Аналитика трафика — это возможность визуализировать сетевые активности и выявлять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и определять сетевые конфигурации.

Как включить журналы потоков NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить и использовать Аналитика трафика Azure:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Общие сведения о безопасности сети, предоставляемой центром безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="13-protect-critical-web-applications"></a>1,3: защита критически важных веб-приложений

**Руководство**: неприменимо; тест производительности предназначен для служб приложений Azure или ресурсов, в которых размещены веб-приложения.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: запретите обмен данными с известными вредоносными IP-адресами

**Рекомендации**. для защиты от атак от атак DDoS включите защиту Azure от атак DDoS Standard в виртуальной сети, в которой развернута Azure HDInsight. Используйте интегрированную аналитику угроз центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми IP-адресами Интернета.

Настройка защиты от атак DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Общие сведения о центре безопасности Azure, интегрированной с системой анализа угроз.

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: запись сетевых пакетов и журналов потоков

**Руководство**. Включение журналов необработанных группы безопасности сети (NSG) для NSG, подключенных к подсети, используемой для защиты кластера Azure HDInsight. Запишите журналы потоков NSG в учетную запись хранения Azure, чтобы создать записи о потоках. Если требуется для изучения аномальных действий, включите запись пакетов наблюдателя за сетями Azure.

Как включить журналы потоков NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить наблюдатель за сетями:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание систем обнаружения вторжений на основе сети и предотвращения вторжений (ИДЕНТИФИКАТОРы и IP-адреса)

**Руководство**: требование может быть УДОВЛЕТВОРЕНо идентификатором управления безопасностью Azure 1,1; Разверните кластер Azure HDInsight в виртуальной сети и обеспечьте безопасность с помощью группы безопасности сети (NSG).

Существует несколько зависимостей для Azure HDInsight, требующих входящего трафика. Входящий трафик управления не может быть отправлен через устройство брандмауэра. Исходные адреса для требуемого трафика управления известны и публикуются. Создайте правила группы безопасности сети с этими сведениями, чтобы разрешить трафик только из надежных расположений, защищая входящий трафик к кластерам.

Чтобы снизить риск потери данных через утечка, Ограничьте исходящий сетевой трафик для кластеров Azure HDInsight с помощью брандмауэра Azure.

Как развернуть HDInsight в виртуальной сети и обеспечить безопасность с помощью группы безопасности сети:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Общие сведения о зависимостях и использовании брандмауэра HDInsight:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

IP-адреса управления HDInsight:https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="17-manage-traffic-to-web-applications"></a>1,7. Управление трафиком к веб-приложениям

**Руководство**: неприменимо; тест производительности предназначен для служб приложений Azure или ресурсов, в которых размещены веб-приложения.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: сократите сложность и издержки администрирования правил безопасности сети

**Руководство**. Использование тегов службы виртуальной сети для определения элементов управления доступом к сети для групп безопасности сети (NSG), подключенных к подсети, в которой развернут кластер Azure HDInsight. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, ApiManagement) в соответствующем поле источника или назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, включенными в тег службы, и автоматически обновляет тег службы при изменении адресов.

Общие сведения и использование тегов службы для Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определение и реализация стандартных конфигураций безопасности для сетевых ресурсов, связанных с кластером Azure HDInsight. Используйте псевдонимы политик Azure в пространствах имен Microsoft. HDInsight и Microsoft. Network, чтобы создать настраиваемые политики для аудита или принудительного применения конфигурации сети кластера Azure HDInsight.

Вы также можете использовать схемы Azure для упрощения крупномасштабных развертываний Azure с помощью ключевых артефактов среды пакетов, таких как шаблоны Azure Resource Manager, элементы управления RBAC и политики, в одном определении схемы. Простое применение схемы к новым подпискам и средам, а также тонкая настройка управления и управления с помощью управления версиями.

Просмотр доступных псевдонимов политик Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Создание Azure Blueprint.

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="110-document-traffic-configuration-rules"></a>1,10: правила конфигурации трафика документов

**Руководство**. Использование тегов для группы безопасности сети (группы безопасности сети) и других ресурсов, относящихся к сетевой безопасности и потоку трафика, связанным с кластером Azure HDInsight. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и (или) Длительность (т. д.) для любых правил, которые разрешают трафик из сети в сеть.

Используйте любое из встроенных определений политик Azure, связанных с тегами, например "требовать тег и его значение", чтобы убедиться, что все ресурсы созданы с помощью тегов и уведомлять вас о существующих ресурсах без тегов.

Вы можете использовать Azure PowerShell или интерфейс командной строки Azure (CLI) для поиска или выполнения действий с ресурсами на основе их тегов.

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Создание виртуальной сети.

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Создание NSG с конфигурацией безопасности:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11. использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений сетевых ресурсов, связанных с развертываниями Azure HDInsight. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых ресурсов.

Просмотр и получение событий журнала действий Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Создание оповещений в Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в разделе [Security Control: logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: используйте источники синхронизации утвержденного времени

**Руководство**. Майкрософт поддерживает источники времени для компонентов кластера Azure HDInsight. Вы можете обновить синхронизацию времени для развертывания вычислений.

Как настроить синхронизацию времени для ресурсов вычислений Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: Майкрософт

### <a name="22-configure-central-security-log-management"></a>2,2: Настройка централизованного управления журналом безопасности

**Руководство**. Вы можете подключить кластер Azure HDInsight к Azure Monitor для агрегирования данных безопасности, создаваемых кластером. Используйте пользовательские запросы для обнаружения угроз в среде и реагирования на них. 

Как подключить кластер Azure HDInsight к Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Создание настраиваемых запросов для кластера Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Включите ведение журнала аудита для ресурсов Azure.

**Рекомендации**. Включите Azure Monitor для кластера HDInsight, направьте его в рабочую область log Analytics. При этом будут регистрироваться соответствующие сведения о кластере и метрики ОС для всех узлов кластера Azure HDInsight.

Как включить ведение журнала для кластера HDInsight:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Как выполнять запросы к журналам HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: получение журналов безопасности из операционных систем

**Руководство**. Подключение кластера HDInsight Azure к Azure Monitor. Убедитесь, что в рабочей области Log Analytics используется срок хранения журнала, заданный в соответствии с нормативными требованиями вашей организации.

Как подключить кластер Azure HDInsight к Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Настройка срока хранения Log Analytics рабочей области:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="25-configure-security-log-storage-retention"></a>2,5: Настройка хранения журнала безопасности

**Руководство**. Подключение кластера HDInsight Azure к Azure Monitor. Убедитесь, что в рабочей области Azure Log Analytics используется срок хранения журнала, заданный в соответствии с нормативными требованиями вашей организации.

Как подключить кластер Azure HDInsight к Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Настройка срока хранения Log Analytics рабочей области:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="26-monitor-and-review-logs"></a>2,6: мониторинг и просмотр журналов

**Руководство**. Использование запросов к рабочей области Azure log Analytics для запроса журналов Azure HDInsight:

Создание настраиваемых запросов для кластеров Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Включение оповещений для аномальных действий

**Руководство**. Использование рабочей области Azure log Analytics для мониторинга и оповещения о аномальных действиях в журналах безопасности и событиях, связанных с кластером Azure HDInsight.

Управление оповещениями в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Как создавать оповещения для данных журнала Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="28-centralize-anti-malware-logging"></a>2,8: централизованное ведение журнала защиты от вредоносных программ

**Руководство**. Azure HDInsight поставляется с предварительно установленным и включенным для образов узлов кластера, однако необходимо управлять программным обеспечением и вручную собирать и отслеживать любые журналы кламскан.

Общие сведения о Кламскан:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="29-enable-dns-query-logging"></a>2,9: Включение ведения журнала запросов DNS

**Руководство**. Реализация стороннего решения для ведения журнала DNS.

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="210-enable-command-line-audit-logging"></a>2,10: Включение ведения журнала аудита в командной строке

**Руководство**. Ручная настройка ведения журнала консоли для каждого узла.

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Дополнительные сведения см. в разделе [Управление безопасностью: идентификация и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: ведение инвентаризации учетных записей администраторов

**Руководство**. Сохранение записи локальной административной учетной записи, созданной во время подготовки кластера Azure HDInsight, а также любых других создаваемых учетных записей. Кроме того, если используется интеграция Azure AD, в Azure AD есть встроенные роли, которые должны быть явно назначены и, следовательно, доступны для запросов. Используйте модуль Azure AD PowerShell, чтобы выполнять нерегламентированные запросы для обнаружения учетных записей, входящих в группы администраторов.

Кроме того, вы можете использовать рекомендации по управлению удостоверениями и доступом в центре безопасности Azure.

Как получить роль каталога в Azure AD с помощью PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Как получить членов роли каталога в Azure AD с помощью PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Мониторинг удостоверений и доступа с помощью центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Измените пароли по умолчанию, если это применимо

**Руководство**. при подготовке кластера Azure требует создания новых паролей для доступа к веб-порталу и Secure Shell (SSH). Пароли по умолчанию для изменения отсутствуют, однако можно указать разные пароли для доступа по протоколу SSH и веб-порталу.

Как задать пароли при подготовке кластера Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: используйте выделенные учетные записи администратора

**Руководство**. Интеграция аутентификации для кластера Azure HDInsight с Azure Active Directory. Создание политик и процедур, связанных с использованием выделенных административных учетных записей.

Кроме того, вы можете использовать рекомендации по управлению удостоверениями и доступом в центре безопасности Azure.

Как интегрировать аутентификацию Azure HDInsight с Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Мониторинг удостоверений и доступа с помощью центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4. Использование единого входа (SSO) с Azure Active Directory

**Руководство**. Использование БРОКЕРа идентификаторов Azure HDInsight для входа в кластеры корпоративный пакет безопасности (ESP) с помощью многофакторной проверки подлинности без предоставления паролей. Если вы уже вошли в другие службы Azure, такие как портал Azure, вы можете войти в кластер Azure HDInsight с помощью единого входа.

Как включить брокер ИДЕНТИФИКАТОРов Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: используйте многофакторную проверку подлинности для всех Azure Active Directoryного доступа

**Руководство**. Включение Azure AD MFA и соблюдение рекомендаций по управлению удостоверениями и доступом в центре безопасности Azure. Кластеры Azure HDInsight с настроенным Корпоративный пакет безопасности могут быть подключены к домену, чтобы пользователи домена могли использовать свои учетные данные домена для проверки подлинности в кластерах и запуска заданий обработки больших данных. При проверке подлинности с включенной многофакторной проверкой подлинности пользователям будет выдаваться запрос второго фактора проверки подлинности.

Как включить MFA в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Мониторинг удостоверений и доступа в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: используйте выделенные компьютеры (рабочие станции с привилегированным доступом) для всех административных задач

**Руководство**. Используйте лапы (рабочие станции с привилегированным доступом) с поддержкой многофакторной идентификации (MFA), настроенной для входа в и настройки кластеров Azure HDInsight и связанных ресурсов.

Дополнительные сведения о рабочих станциях с привилегированным доступом:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Как включить MFA в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: регистрация и оповещение о подозрительных действиях из учетных записей администраторов

**Руководство**. кластеры Azure HDInsight с настроенным корпоративный пакет безопасности могут быть подключены к домену, чтобы пользователи домена могли использовать свои учетные данные домена для проверки подлинности. Вы можете использовать отчеты о безопасности Azure Active Directory (AAD) для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде AAD. Используйте центр безопасности Azure, чтобы отслеживать действия по удостоверениям и доступу.

Как опознать пользователей AAD, помеченных для рискованных действий:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Как отслеживать действия пользователей и доступа в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: управление ресурсами Azure из только утвержденных расположений

**Руководство**. кластеры Azure HDInsight с настроенным корпоративный пакет безопасности могут быть подключены к домену, чтобы пользователи домена могли использовать свои учетные данные домена для проверки подлинности. Используйте условный доступ с именованными расположениями, чтобы разрешить доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

Как настроить именованные расположения в Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="39-use-azure-active-directory"></a>3,9: используйте Azure Active Directory

**Руководство**. Использование Azure Active Directory (AAD) в качестве централизованной системы проверки подлинности и авторизации. AAD защищает данные с помощью надежного шифрования для неактивных и транзитных данных. AAD также содержит Salt-записи, хэши и безопасно хранит учетные данные пользователя.

Кластеры Azure HDInsight с настроенным Корпоративный пакет безопасности (ESP) могут быть подключены к домену, чтобы пользователи домена могли использовать свои учетные данные домена для проверки подлинности в кластерах.

Создание и настройка экземпляра AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Как настроить Корпоративный пакет безопасности с помощью доменных служб Azure Active Directory в Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: регулярно просматривайте и выверяйте доступ пользователей

**Руководство**. Использование проверки подлинности Azure Active Directory (AAD) в кластере Azure HDInsight. AAD предоставляет журналы для облегчения поиска устаревших учетных записей. Кроме того, используйте проверку доступа удостоверений Azure, чтобы эффективно управлять членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователя можно проверить регулярно, чтобы убедиться, что доступ к ним имеют только нужные пользователи. 

Использование проверок доступа для идентификации Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: монитор пытается получить доступ к отключенным учетным записям

**Руководство**. Использование Azure Active Directory (AAD) для входа и журналов аудита для наблюдения за попытками доступа к отключенным учетным записям; Эти журналы можно интегрировать в любое стороннее средство SIEM/Monitoring.

Этот процесс можно упростить, создав параметры диагностики для учетных записей пользователей AAD, отправив журналы аудита и журналы входа в рабочую область Azure Log Analytics. Настройте нужные оповещения в рабочей области Azure Log Analytics.

Как интегрировать журналы действий Azure в Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: предупреждение об отклонении режима входа учетной записи

**Руководство**. кластеры Azure HDInsight с настроенным корпоративный пакет безопасности (ESP) могут быть подключены к домену, чтобы пользователи домена могли использовать свои учетные данные домена для проверки подлинности в кластерах.  Используйте Azure Active Directory обнаружения рисков (AAD) и функцию защиты идентификации, чтобы настроить автоматические ответы на обнаруженные подозрительные действия, связанные с удостоверениями пользователей. Кроме того, вы можете принимать данные в метку Azure для дальнейшего изучения.

Как просмотреть рискованные входы в AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Настройка и включение политик риска для защиты идентификации.

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: предоставьте корпорации Майкрософт доступ к соответствующим пользовательским данным во время сценариев поддержки

**Руководство**: недоступно; Защищенное хранилище еще не поддерживаются для Azure HDInsight.

Список поддерживаемых защищенное хранилище служб:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в разделе [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: поддержание инвентаризации конфиденциальной информации

**Руководство**. Использование тегов в ресурсах, связанных с развертываниями Azure HDInsight, для помощи в отслеживании ресурсов Azure, в которых хранятся или обрабатываются конфиденциальные данные.

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Изолируйте системы, хранящие или обрабатывающие конфиденциальные данные

**Руководство**. Реализация отдельных подписок и групп управления для разработки, тестирования и производства. Кластеры Azure HDInsight и все связанные учетные записи хранения должны быть разделены виртуальной сетью или подсетью, помечены соответствующим образом и защищены в группе безопасности сети (NSG) или брандмауэре Azure. Данные кластера должны содержаться в защищенной учетной записи хранения Azure или Azure Data Lake Storage (GEN1 или Gen2).

Выберите параметры хранения для кластера Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Как защитить Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Как защитить учетные записи хранения Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Отслеживайте и блокируйте несанкционированную пересылку конфиденциальной информации

**Руководство**. для кластеров Azure HDInsight, в которых хранятся или обрабатываются конфиденциальные данные, пометьте кластер и связанные ресурсы как конфиденциальные с помощью тегов. Чтобы снизить риск потери данных через утечка, Ограничьте исходящий сетевой трафик для кластеров Azure HDInsight с помощью брандмауэра Azure.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и имеет большую длину для защиты от потери данных и доступности клиентов. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

Ограничение исходящего трафика для кластеров Azure HDInsight с помощью брандмауэра Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Общие сведения о защите данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: Общая

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: шифрование всех конфиденциальных данных во время передачи

**Руководство**. шифрование всех конфиденциальных данных во время передачи. Убедитесь, что все клиенты, подключающиеся к кластеру Azure HDInsight или хранилищам данных кластера (учетные записи хранения Azure или Azure Data Lake Storage 1-го поколения/Gen2), могут согласовать TLS 1,2 или более поздней версии. Microsoft Azure ресурсы будут согласовывать TLS 1,2 по умолчанию. 

Общие сведения о шифровании Azure Data Lake Storage при передаче:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Общие сведения о шифровании учетной записи хранения Azure при передаче:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Общая

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: использование активного средства обнаружения для распознавания конфиденциальных данных

**Руководство**. функции для идентификации, классификации и предотвращения потерь данных пока недоступны для службы хранилища Azure или ресурсов для вычислений. Реализуйте сторонние решения, если это необходимо для обеспечения соответствия требованиям.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и имеет большую длину для защиты от потери данных и доступности клиентов. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

Общие сведения о защите данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: Общая

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6. Использование Azure RBAC для управления доступом к ресурсам

**Руководство**. с помощью Azure HDInsight корпоративный пакет безопасности (ESP) можно использовать Apache Ranger для создания детализированных политик управления доступом и маскировки данных и управления ими для данных, хранящихся в файлах, папках, базах данных, таблицах и строках и столбцах. Администратор Hadoop может настроить управление доступом на основе ролей (RBAC) для защиты Apache Hive, HBase, Kafka и Spark, используя эти подключаемые модули в Apache Ranger.

Настройка политик RBAC с помощью Apache Ranger позволяет связать разрешения с ролью в Организации. Этот уровень абстракции позволяет гарантировать, что у людей есть только разрешения, необходимые для выполнения их рабочих обязанностей.

Корпоративный пакет безопасности конфигурации с доменными службами Azure Active Directory в HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Обзор корпоративной безопасности в Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**. для кластеров Azure HDInsight, в которых хранятся или обрабатываются конфиденциальные данные, пометьте кластер и связанные ресурсы как конфиденциальные с помощью тегов. Функции защиты идентификации, классификации и предотвращения потери данных пока недоступны для службы хранилища Azure или ресурсов. Реализуйте сторонние решения, если это необходимо для обеспечения соответствия требованиям.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и имеет большую длину для защиты от потери данных и доступности клиентов. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

Общие сведения о защите данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: Общая

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: шифрование конфиденциальной информации при хранении

**Руководство**. Если для хранения Apache Hive и метаданных Apache Oozie используется база данных SQL Azure, убедитесь, что данные SQL остаются зашифрованными в любое время. Для учетных записей хранения Azure и Data Lake Storage (GEN1 или Gen2) рекомендуется разрешить корпорации Майкрософт управлять ключами шифрования, однако у вас есть возможность управлять собственными ключами.

Как управлять ключами шифрования для учетных записей хранения Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Создание Azure Data Lake Storage с помощью управляемых клиентом ключей шифрования:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Общие сведения о шифровании базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Как настроить прозрачное шифрование данных для базы данных SQL с помощью управляемых клиентом ключей:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Общая

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: журналы и оповещения об изменениях критических ресурсов Azure

**Руководство**. Настройка параметров диагностики для учетных записей хранения Azure, связанных с кластерами Azure HDInsight, для отслеживания и регистрации всех операций CRUD с данными кластера. Включите аудит для всех учетных записей хранения или хранилищ Data Lake, связанных с кластером Azure HDInsight.

Как включить дополнительное ведение журнала и аудит для учетной записи хранения Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Включение дополнительного ведения журнала и аудита для Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

## <a name="vulnerability-management"></a>Управление уязвимостями

*Дополнительные сведения см. в разделе [Security Control: управлением уязвимостей](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: Запуск автоматизированных средств проверки уязвимостей

**Руководство**. Реализация решения по управлению уязвимостью от сторонних производителей.

При необходимости, если у вас есть подписка на Rapid7, Qualys или любую другую уязвимость платформы управления уязвимостью, вы можете использовать действия сценария для установки агентов оценки уязвимостей на узлах кластера Azure HDInsight и управлять узлами с помощью соответствующего портала.

Как установить агент Rapid7 вручную:

https://insightvm.help.rapid7.com/docs/install

Как установить агент Qualys вручную:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Как использовать действия сценария:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**. Автоматическое обновление системы включено для образов узлов кластера, однако для обеспечения применения обновлений необходимо периодически перезагружать узлы кластера.

Корпорация Майкрософт поддерживает и обновляет базовые образы узлов Azure HDInsight.

Настройка расписания обновления ОС для кластеров HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: Общая

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Развертывание автоматизированного решения по управлению исправлениями сторонних производителей

**Руководство**. Используйте действия сценария или другие механизмы для обновления кластеров Azure HDInsight. Созданные кластеры будут всегда содержать последние обновления, включая самые последние обновления безопасности.

Как настроить расписание обновления ОС для кластеров Azure HDInsight под управлением Linux:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Как использовать действия сценария:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: сравнение проверок уязвимостей "назад-назад"

**Руководство**. Реализация решения для управления уязвимостью от сторонних производителей, которое позволяет сравнивать проверки уязвимостей с течением времени. Если у вас есть подписка Rapid7 или Qualys, вы можете использовать портал этого поставщика для просмотра и сравнения проверок уязвимостей обратной передачи.

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5. Использование процесса оценки рисков для определения приоритета устранения обнаруженных уязвимостей

**Руководство**. Используйте общую программу оценки рисков (например, общую систему оценки уязвимостей) или оценки рисков по умолчанию, предоставляемые сторонним средством сканирования.

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в разделе [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1. Использование обнаружения ресурсов Azure

**Руководство**. Использование графа ресурсов Azure для запроса или обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов и протоколов и т. д.), включая кластеры Azure HDInsight, в рамках ваших подписок.  Убедитесь, что у вас есть соответствующие разрешения (на чтение) в клиенте и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

Хотя классические ресурсы Azure могут быть обнаружены с помощью графа ресурсов, настоятельно рекомендуется создавать и использовать Azure Resource Manager ресурсы, идущие вперед.

Как создавать запросы с помощью графа ресурсов Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Как просмотреть подписки Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Общие сведения об Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="62-maintain-asset-metadata"></a>6,2: сохранение метаданных активов

**Руководство**. применение тегов к ресурсам Azure с помощью метаданных для логической организации их в таксономию.

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: удалите неавторизованные ресурсы Azure

**Руководство**. Использование тегов, групп управления и отдельных подписок (при необходимости) для Организации и мониторинга ресурсов. Регулярно выверка инвентаризации и своевременное удаление неавторизованных ресурсов из подписки.

Как создать дополнительные подписки Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание Группы управления:

https://docs.microsoft.com/azure/governance/management-groups/create

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: ведение инвентаризации утвержденных ресурсов Azure и наименований программного обеспечения

**Руководство**. Определение списка утвержденных ресурсов Azure и утвержденного программного обеспечения для ресурсов вычислений

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: отслеживание неутвержденных ресурсов Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в подписках клиентов, с помощью следующих встроенных определений политик:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Используйте граф ресурсов Azure для запроса или обнаружения ресурсов в ваших подписках. Убедитесь, что все ресурсы Azure, представленные в окружении, утверждены.

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как создавать запросы с помощью Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**. Реализация стороннего решения для мониторинга узлов кластера для неутвержденных программных приложений.

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**. Использование графа ресурсов Azure для запроса или обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов и протоколов и т. д.), включая кластеры Azure HDInsight, в рамках ваших подписок.  Удалите все неутвержденные ресурсы Azure, которые вы обнаружите. Для узлов кластера Azure HDInsight реализуйте стороннее решение для удаления или оповещения о неутвержденном программном обеспечении.

Как создавать запросы с помощью Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="68-use-only-approved-applications"></a>6,8: Используйте только утвержденные приложения

**Рекомендации**. для узлов кластера Azure HDInsight реализуйте стороннее решение, чтобы предотвратить выполнение несанкционированного программного обеспечения.


**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="69-use-only-approved-azure-services"></a>6,9: Используйте только утвержденные службы Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в подписках клиентов, с помощью следующих встроенных определений политик:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как отказаться от определенного типа ресурса с помощью политики Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="610-implement-approved-application-list"></a>6,10: реализация списка утвержденных приложений

**Рекомендации**. для узлов кластера Azure HDInsight реализуйте стороннее решение, чтобы предотвратить выполнение несанкционированных типов файлов.


**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: ограничьте возможность пользователей взаимодействовать с диспетчером ресурсов Azure с помощью сценариев

**Руководство**. Использование условного доступа Azure для ограничения возможности пользователей взаимодействовать с Azure Resource Manager путем настройки "блокировать доступ" для приложения "Управление Microsoft Azure".

Как настроить условный доступ для блокировки доступа к Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: ограничьте возможность пользователей выполнять сценарии в ресурсах вычислений

**Руководство**: неприменимо; Это неприменимо к Azure HDInsight, так как пользователям (не администраторам) кластера не требуется доступ к отдельным узлам для выполнения заданий. Администратор кластера имеет корневой доступ ко всем узлам кластера.

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: неприменимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: физическое или логическое разделение приложений с высоким риском

**Руководство**: неприменимо; тест производительности предназначен для служб приложений Azure или ресурсов, в которых размещены веб-приложения.

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: неприменимо

## <a name="secure-configuration"></a>Настройка безопасности

*Дополнительные сведения см. в разделе [Security Control: Secure Configuration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Установите безопасные конфигурации для всех ресурсов Azure.

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. HDInsight для создания настраиваемых политик для аудита или принудительного применения конфигурации сети кластера HDInsight.

Просмотр доступных псевдонимов политик Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Установка безопасных конфигураций операционной системы

**Руководство**. образы операционной системы Azure HDInsight, управляемые и обслуживаемые корпорацией Майкрософт. Клиент, ответственный за реализацию безопасных конфигураций для операционной системы узлов кластера. 


**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: поддержание защищенных конфигураций ресурсов Azure

**Рекомендации**. Используйте политику Azure [Deny] и [развертывание, если не существует], чтобы применить параметры безопасности для кластеров Azure HDInsight и связанных ресурсов.

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Общие сведения о влиянии политики Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: поддержание защищенных конфигураций операционных систем

**Руководство**. образы операционной системы Azure HDInsight, управляемые и обслуживаемые корпорацией Майкрософт. Клиент, ответственный за реализацию конфигурации состояния на уровне ОС.


**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: Общая

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: безопасное хранение конфигурации ресурсов Azure

**Руководство**. Если вы используете пользовательские определения политики Azure, используйте Azure DevOps или Azure Repos для безопасного хранения кода и управления им.

Как сохранить код в Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Документация по Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: безопасное хранение пользовательских образов операционной системы

**Руководство**: неприменимо; пользовательские образы неприменимы к Azure HDInsight.

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: неприменимо

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: развертывание средств управления конфигурацией системы

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. HDInsight для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией системы для операционных систем

**Руководство**. Реализация стороннего решения для поддержания требуемого состояния операционных систем узла кластера.

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9. Реализация автоматического мониторинга конфигурации для служб Azure

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. HDInsight для создания настраиваемых политик для аудита или принудительного применения конфигурации кластера HDInsight.

Просмотр доступных псевдонимов политик Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10. Реализация автоматического мониторинга конфигурации для операционных систем

**Руководство**. Реализация стороннего решения для отслеживания состояния операционных систем узла кластера.

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="711-manage-azure-secrets-securely"></a>7,11: безопасное управление секретами Azure

**Руководство**. Azure HDInsight включает поддержку создание собственных ключей (BYOK) для Apache Kafka. Эта функция позволяет вам владеть ключами, используемыми для шифрования неактивных данных, и управлять ими.

Все управляемые диски в Azure HDInsight защищены с помощью Azure Шифрование службы хранилища (SSE). По умолчанию данные на этих дисках зашифрованы с помощью ключей, управляемых корпорацией Майкрософт. Если вы включаете BYOK, вы предоставляете ключ шифрования для Azure HDInsight, чтобы использовать его и управлять им с помощью Azure Key Vault.

Key Vault также можно использовать с развертываниями Azure HDInsight для управления ключами для хранилища кластера (учетные записи хранения Azure и Azure Data Lake Storage).

Как внедрить собственный ключ для Apache Kafka в Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Как управлять ключами шифрования для учетных записей хранения Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: безопасно управляйте удостоверениями и автоматически

**Руководство**. управляемые удостоверения можно использовать в Azure HDInsight, чтобы предоставить вашим кластерам доступ к Azure Active Directory доменным службам, доступу к Azure Key Vault или доступу к файлам в Azure Data Lake Storage 2-го поколения.

Общие сведения об управляемых удостоверениях в Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: устранение непреднамеренной раскрытия учетных данных

**Руководство**. при использовании любого кода, связанного с развертыванием Azure HDInsight, можно реализовать средство проверки учетных данных для указания учетных данных в коде. Средство проверки учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault. 

Настройка средства проверки учетных данных:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в разделе [Security Control: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Руководство**. Azure HDInsight поставляется с предварительно установленным и включенным для образов узлов кластера, однако необходимо управлять программным обеспечением и вручную собирать и отслеживать любые журналы кламскан.

Общие сведения о Кламскан для Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся средами вычислений

**Руководство**. антивредоносное по Майкрософт включено на базовом узле, который поддерживает службы Azure, но не выполняется в содержимом клиента.

Предварительно проверять все файлы, отправляемые в любые ресурсы Azure, связанные с развертыванием кластера Azure HDInsight, например Data Lake Storage, хранилище BLOB-объектов и т. д. Корпорация Майкрософт не может получить доступ к данным клиентов в этих экземплярах.

Сведения о антивредоносном по Майкрософт для облачных служб и виртуальных машин Azure:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: Общая

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Убедитесь, что программное обеспечение и подписи для защиты от вредоносных программ обновлены

**Руководство**. Azure HDInsight поставляется с предварительно установленным и включенным кламскан для образов узлов кластера. Кламскан будет автоматически выполнять обновления модулей и определений, однако агрегирование и управление журналами потребуется выполнять вручную.

Общие сведения о Кламскан для Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в разделе [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Обеспечьте регулярную автоматическую архивацию

**Руководство**. при использовании учетной записи хранения Azure для хранилища данных кластера HDInsight выберите соответствующий параметр избыточности (LRS, ZRS, GRS, RA-GRS).  При использовании базы данных SQL Azure для хранилища данных кластера Azure HDInsight настройте активную георепликацию.

Как настроить избыточность хранилища для учетных записей хранения Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Как настроить избыточность для баз данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом.

**Руководство**. при использовании учетной записи хранения Azure для хранилища данных кластера Azure HDInsight выберите соответствующий параметр избыточности (LRS, ZRS, GRS, RA-GRS). Если вы используете Azure Key Vault для любой части развертывания Azure HDInsight, убедитесь, что резервное копирование ключей выполнено.

Выберите параметры хранения для кластера Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Как настроить избыточность хранилища для учетных записей хранения Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Резервное копирование ключей Key Vault в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Рекомендации**. Если Azure Key Vault используется в развертывании Azure HDInsight, протестируйте резервные копии ключей, управляемых клиентом.

Как внедрить собственный ключ для Apache Kafka в Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Как восстановить ключи хранилища ключей в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и управляемых клиентом ключей

**Рекомендации**. Если Azure Key Vault используется в развертывании Azure HDInsight, включите обратимое удаление в Key Vault, чтобы защитить ключи от случайного или вредоносного удаления.

Включение обратимого удаления в Azure Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

## <a name="incident-response"></a>реагирование на инциденты.

*Дополнительные сведения см. в разделе [контроль безопасности: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: создайте руководство по реагированию на инциденты

**Руководство**. Убедитесь, что существуют планы реагирования на инциденты, определяющие роли персонала, а также этапы обработки инцидентов и управления ими.

Как настроить автоматизацию рабочих процессов в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: создание оценки инцидента и процедуры определения приоритетов

**Руководство**. Центр безопасности назначает серьезность предупреждениям, чтобы помочь вам определить порядок, в котором вы задаете каждое оповещение, чтобы при компрометации ресурса вы могли сразу перейти к нему. Серьезность основывается на том, насколько надежным является центр безопасности в поиске или аналитике, используемой для выдаче оповещения, а также об уровне достоверности, который был вредоносной задачей, вызвавшей оповещение.

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="103-test-security-response-procedures"></a>10,3: тестирование процедур реагирования на безопасность

**Руководство**. проведение упражнений по тестированию возможностей реагирования на инциденты систем на регулярной ритмичности. Выявление слабых точек и пробелов и пересмотр плана по мере необходимости. Обратитесь к публикации NIST: руководство по тестированию, обучению и упражнениям для ИТ-планов и возможностей.https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: укажите контактные данные инцидента безопасности и настройте уведомления о событиях для инцидентов безопасности.

**Руководство**. контактные данные инцидентов безопасности будут использоваться корпорацией Майкрософт для связи с вами, если Microsoft Security Response Center (MSRC) обнаруживает, что доступ к данным был получен незаконные или неавторизованной стороной.

Как задать контакт безопасности центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**. Экспорт оповещений и рекомендаций центра безопасности Azure с помощью функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Вы можете использовать соединитель данных центра безопасности Azure для потоковой передачи метки предупреждений.

Как настроить непрерывный экспорт:

https://docs.microsoft.com/azure/security-center/continuous-export

Как выполнить потоковую передачу предупреждений в Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Автоматизируйте ответ на оповещения системы безопасности

**Руководство**. Использование функции автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" в оповещениях и рекомендациях системы безопасности.

Как настроить автоматизацию рабочего процесса и Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в разделе [контроль безопасности: тесты на проникновение и примеры для красных команд](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критических обновлений безопасности в течение 60 дней.

**Рекомендации**. Следуйте правилам взаимодействия Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Дополнительные сведения о стратегии корпорации Майкрософт и ее выполнении, а также о тестировании на основе уязвимости для управляемой облачной инфраструктуры, служб и приложений Майкрософт см. здесь:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со статьей о [производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Дополнительные сведения о [базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
