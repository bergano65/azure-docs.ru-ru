---
title: Базовый план безопасности Azure для Azure Databricks
description: Базовый план безопасности Azure для Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7d417f412592d87dec751b9d98a26bf786c719e5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796854"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Базовый план безопасности Azure для Azure Databricks

Базовый план безопасности Azure для Azure Databricks содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовая информация для этой службы взята из [теста безопасности Azure версии 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), который содержит рекомендации по защите облачных решений в Azure с помощью наших рекомендаций.

Дополнительные сведения см. в статье [Общие сведения о базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в разделе Security [Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Защитите ресурсы с помощью групп безопасности сети или брандмауэра Azure в виртуальной сети.

**Руководство**. Развертывание Azure Databricks в собственной виртуальной сети Azure (VNet). Развертывание Azure Databricks по умолчанию — это полностью управляемая служба в Azure: все ресурсы плоскости данных, включая виртуальную сеть, с которой будут связаны все кластеры, развертываются в заблокированной группе ресурсов. Однако если требуется настройка сети, можно развернуть Azure Databricks ресурсы плоскости данных в собственной виртуальной сети (внедрение в виртуальную сеть), что позволит реализовать пользовательские конфигурации сети. Вы можете применить собственную группу безопасности сети (NSG) с настраиваемыми правилами к определенным ограничениям трафика исходящих данных.

Кроме того, можно настроить правила NSG, чтобы указать ограничения трафика исходящих данных в подсети, в которой развернут экземпляр Azure Databricks. Брандмауэр Azure можно настроить для добавления рабочих областей виртуальной сети.

* [Развертывание Azure Databricks в своей виртуальной сети](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Управление группы безопасности сети](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Развертывание Azure Databricks в собственной виртуальной сети Azure (VNet). Группа безопасности сети (NSG) не создается автоматически. Вы должны создать базовые NSG с правилами Azure по умолчанию. При развертывании рабочей области добавляются правила, необходимые для кирпичей. Можно также начать с пустого NSG, и соответствующие правила будут добавлены автоматически. Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков в рабочую область Log Analytics и использовать Аналитика трафика для получения сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитика трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять несетевые настройки.

* [Развертывание Azure Databricks в своей виртуальной сети](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Включение журналов потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Как включить и использовать Аналитика трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Включение наблюдателя за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="13-protect-critical-web-applications"></a>1,3: защита критически важных веб-приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: запретите обмен данными с известными вредоносными IP-адресами

**Руководство**. Включение стандарта защиты Azure от атак DDoS в виртуальных сетях Azure, связанных с экземплярами Azure Databricks, для защиты от атак типа "отказ в обслуживании". Используйте интегрированную аналитику угроз центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми общедоступными IP-адресами

* [Управление защитой от атак DDoS Azure уровня "Стандартный" с помощью портала Azure](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Общие сведения о защите от угроз для сетевого уровня Azure в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: запись сетевых пакетов и журналов потоков

**Руководство**. Развертывание Azure Databricks в собственной виртуальной сети Azure (VNet). Группа безопасности сети (NSG) не создается автоматически. Вы должны создать базовые NSG с правилами Azure по умолчанию. При развертывании рабочей области добавляются правила, необходимые для кирпичей. Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков в рабочую область Log Analytics и использовать Аналитика трафика для получения сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитика трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять несетевые настройки.

* [Развертывание Azure Databricks в своей виртуальной сети](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Включение журналов потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Как включить и использовать Аналитика трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Включение наблюдателя за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание систем обнаружения вторжений на основе сети и предотвращения вторжений (ИДЕНТИФИКАТОРы и IP-адреса)

**Руководство**. Реализация виртуального сетевого устройства с поддержкой IDP/IP-адресов (NVA) из Azure Marketplace для создания интегрированной в виртуальную сеть рабочей области, в которой все Azure Databricks кластеры имеют один исходящий IP-адрес. Один IP-адрес можно использовать в качестве дополнительного уровня безопасности с другими службами и приложениями Azure, которые разрешают доступ на основе конкретных IP-адресов. Для управления входящим и исходящим трафиком можно использовать брандмауэр Azure или другие сторонние средства, такие как NVA.

Если обнаружение вторжений и/или предотвращение на основе проверки полезной нагрузки не является обязательным, можно использовать брандмауэр Azure с функцией анализа угроз. Фильтрация на основе интеллектуального анализа угроз Azure может оповещать и отклонять трафик от известных вредоносных IP-адресов и доменов. IP-адреса и домены также передаются из канала Microsoft Threat Intelligence.

* [Как назначить один общедоступный IP-адрес для рабочих областей, внедренных в виртуальную сеть, с помощью Брандмауэра Azure](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [Создание NVA](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="17-manage-traffic-to-web-applications"></a>1,7. Управление трафиком к веб-приложениям

**Руководство**: неприменимо; Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: сократите сложность и издержки администрирования правил безопасности сети

**Руководство**. Использование тегов служб для определения управления доступом к сети для групп безопасности сети, подключенных к подсетям, с которыми связан экземпляр Azure Databricks. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, ApiManagement) в соответствующем поле источника или назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, включенными в тег службы, и автоматически обновляет тег службы при изменении адресов. Теги службы не поддерживаются для невнедренных рабочих областей виртуальной сети.

* [Общие сведения о тегах служб](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определение и реализация конфигураций безопасности сети для экземпляров Azure Databricks с помощью политики Azure. Вы можете использовать псевдонимы политик Azure в пространстве имен Microsoft. кирпичи для определения пользовательских определений политики. Политики не применяются к ресурсам в пределах управляемой группы ресурсов.

Вы также можете использовать схемы Azure для упрощения крупномасштабных развертываний Azure с помощью ключевых артефактов среды пакетов, таких как шаблоны управления ресурсами Azure, управление доступом на основе ролей (RBAC) и политики, в одном определении схемы. Простое применение схемы к новым подпискам и средам, а также тонкая настройка управления и управления с помощью управления версиями.

* [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Общие сведения о псевдонимах и структуре политик Azure](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Создание Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="110-document-traffic-configuration-rules"></a>1,10: правила конфигурации трафика документов

**Руководство**. Использование тегов для группы безопасности сети и других ресурсов, связанных с сетевой безопасностью и потоком трафика, связанными с экземпляром Azure Databricks. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и (или) Длительность (т. д.) для любых правил, которые разрешают трафик из сети в сеть.

* [Создание и использование тегов](httphttps://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11. использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений сетевых ресурсов, связанных с экземплярами Azure Databricks. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых ресурсов.

* [Просмотр и получение событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Создание оповещений в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в разделе [Security Control: logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: используйте источники синхронизации утвержденного времени

**Руководство**. Майкрософт поддерживает источники времени для ресурсов Azure. Однако вы можете управлять параметрами синхронизации времени для ресурсов вычислений. Поскольку Azure Databricks является службой PaaS, вы не имеете прямого доступа к виртуальным машинам в кластере Azure Databricks и не можете настроить параметры синхронизации времени.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="22-configure-central-security-log-management"></a>2,2: Настройка централизованного управления журналом безопасности

**Руководство**. прием журналов с помощью Azure Monitor для агрегирования данных безопасности, создаваемых Azure Databricks. В Azure Monitor можно выполнить запрос к рабочей области Log Analytics, настроенной для получения ваших кирпичей и журналов диагностики. Используйте учетные записи хранения Azure для долгосрочного и архивированного хранения журналов или концентраторов событий для экспорта данных в другие системы. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним инцидентам безопасности и управлению событиями (SIEM).

Примечание. для Azure Databricks журналов диагностики требуется план Azure Databricks (цен. категория "Премиум")

* [Настройка параметров диагностики](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Как подключить метку Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Как приступить к работе с Azure Monitor и интеграцией сторонних поставщиков SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Включите ведение журнала аудита для ресурсов Azure.

**Руководство**. Включение параметров диагностики журнала действий Azure и отправка журналов в рабочую область log Analytics, в концентратор событий Azure или в учетную запись хранения Azure для архивации. С помощью данных журнала действий Azure можно определить "что, кто и когда" для любых операций записи (размещение, публикация, удаление), выполненных на уровне плоскости управления для ресурсов Azure.

Для ведения журнала аудита Azure Databricks предоставляет исчерпывающие сквозные журналы диагностики действий, выполняемых Azure Databricks пользователями, позволяя корпоративным предприятиям отслеживать подробные закономерности Azure Databricks использования.

Примечание. для Azure Databricks журналов диагностики требуется план Azure Databricks (цен. категория "Премиум")

* [Включение параметров диагностики для журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Включение параметров диагностики для Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: получение журналов безопасности из операционных систем

**Руководство**. Azure Databricks предоставляет исчерпывающие комплексные журналы диагностики действий, выполняемых Azure Databricks пользователями, позволяя корпоративным предприятиям отслеживать подробные закономерности Azure Databricks использования.

Примечание. для Azure Databricks журналов диагностики требуется план Azure Databricks (цен. категория "Премиум"). Журнал безопасности ОС недоступен.

* [Включение параметров диагностики для журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Включение параметров диагностики для Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="25-configure-security-log-storage-retention"></a>2,5: Настройка хранения журнала безопасности

**Руководство**. Включение параметров диагностики для Azure Databricks. Если вы хотите сохранить журналы в рабочей области Log Analytics, задайте срок хранения Log Analytics рабочей области в соответствии с нормативными требованиями Организации. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения. Действия, связанные с безопасностью, отправляются в журналах аудита модулями сбора.

Примечание. для Azure Databricks журналов диагностики требуется план Azure Databricks (цен. категория "Премиум")

* [Включение параметров диагностики в Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Настройка параметров хранения журнала для рабочих областей Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="26-monitor-and-review-logs"></a>2,6: мониторинг и просмотр журналов

**Руководство**. Включение параметров диагностики для Azure Databricks и отправка журналов в рабочую область log Analytics. Используйте рабочую область Log Analytics для анализа и мониторинга журналов Azure Databricks для аномального поведения и регулярного просмотра результатов.

Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

Примечание. для Azure Databricks журналов диагностики требуется план Azure Databricks (цен. категория "Премиум")

* [Включение параметров диагностики в Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Запрос Azure Databricks журналов, отправленных в Log Analytics рабочую область](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Как подключить метку Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Включение оповещений для аномальных действий

**Руководство**. Настройка параметров диагностики для экземпляра Azure Databricks и отправка журналов в log Analytics рабочую область. В рабочей области Log Analytics Настройте оповещения, которые должны выполняться при выполнении предварительно определенного набора условий.

Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

Примечание. для Azure Databricks журналов диагностики требуется план Azure Databricks (цен. категория "Премиум")

* [Как отправить журналы Azure Databricks в log Analytics рабочую область](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery))

* [Настройка оповещений в рабочей области Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="28-centralize-anti-malware-logging"></a>2,8: централизованное ведение журнала защиты от вредоносных программ

**Руководство**: неприменимо.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="29-enable-dns-query-logging"></a>2,9: Включение ведения журнала запросов DNS

**Руководство**: неприменимо; Azure Databricks не обрабатывает и не создает связанные с пользователем журналы, относящиеся к DNS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="210-enable-command-line-audit-logging"></a>2,10: Включение ведения журнала аудита в командной строке

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="identity-and-access-control"></a>Идентификаторы и управление доступом

*Дополнительные сведения см. в разделе [Управление безопасностью: идентификация и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: ведение инвентаризации учетных записей администраторов

**Руководство**. Вы можете использовать Azure DATABRICKS API scim для управления пользователями в рабочей области Azure Databricks и предоставления прав администратора для определенных пользователей. Вы также можете управлять ими с помощью пользовательского интерфейса Azure Databricks.

* [Использование API-интерфейсов SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Добавление и удаление пользователей в Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Измените пароли по умолчанию, если это применимо

**Руководство**. Azure Databricks использует Azure Active Directory (AD) для предоставления доступа к портал Azure, а также к консоли администрирования Azure Databricks. В Azure AD нет концепции паролей по умолчанию, однако вы несете ответственность за изменение или запрет паролей по умолчанию для пользовательских или сторонних приложений.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: используйте выделенные учетные записи администратора

**Руководство**. Вы можете использовать Azure DATABRICKS API scim для добавления пользователей с правами администратора в Azure Databricks. Вы также можете управлять ими с помощью пользовательского интерфейса Azure Databricks.

* [Использование API-интерфейсов SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Добавление и удаление пользователей в Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4. Использование единого входа (SSO) с Azure Active Directory

**Руководство**. Azure Databricks автоматически настраивается для использования Azure Active Directory единого входа для проверки подлинности пользователей. Пользователи за пределами организации должны завершить процесс приглашения и добавить их в клиент Active Directory, прежде чем они смогут войти в Azure Databricks с помощью единого входа. Вы можете реализовать SCIM для автоматизации подготовки и отмены подготовки пользователей из рабочих областей.

* [Использование API-интерфейсов SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Общие сведения об едином входе в Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Как пригласить пользователей в клиент Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: используйте многофакторную проверку подлинности для всех Azure Active Directoryного доступа.

**Руководство**. Включение Azure AD MFA и соблюдение рекомендаций по управлению удостоверениями и доступом в центре безопасности Azure.

* [Как включить MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Мониторинг удостоверений и доступа в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: используйте выделенные компьютеры (рабочие станции с привилегированным доступом) для всех административных задач

**Руководство**. Используйте лапы (рабочие станции с привилегированным доступом) с MFA, настроенным для входа и настройки ресурсов Azure.

* [Дополнительные сведения о рабочих станциях с привилегированным доступом](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Как включить MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: регистрация и оповещение о подозрительных действиях из учетных записей администраторов

**Рекомендации**. Используйте Azure Active Directory отчеты о безопасности для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде. Используйте центр безопасности Azure, чтобы отслеживать действия по удостоверениям и доступу. Кроме того, можно использовать журналы диагностики Azure Databricks.

* [Определение пользователей Azure AD, помеченных для рискованных действий](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Мониторинг действий идентификации и доступа пользователей в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: управление ресурсами Azure из только утвержденных расположений

**Руководство**. Используйте условный доступ с именованными расположениями, чтобы разрешить доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

* [Настройка именованных расположений в Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="39-use-azure-active-directory"></a>3,9: используйте Azure Active Directory

**Руководство**. Azure Databricks автоматически настраивается для использования Azure Active Directory единого входа для проверки подлинности пользователей. Пользователи за пределами организации должны завершить процесс приглашения и добавить их в клиент Active Directory, прежде чем они смогут войти в Azure Databricks с помощью единого входа.

* [Общие сведения об едином входе в Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Как пригласить пользователей в клиент Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: регулярно просматривайте и выверяйте доступ пользователей

**Руководство**. Azure AD предоставляет журналы для облегчения поиска устаревших учетных записей. Кроме того, используйте проверку доступа удостоверений Azure, чтобы эффективно управлять членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователей можно проверить регулярно, чтобы убедиться, что доступ к ним имеют только нужные пользователи. Вы также можете реализовать интерфейсы API SCIM и Azure Databricks журналы диагностики для проверки доступа пользователей. Для просмотра доступа пользователей можно также использовать интерфейсы API SCIM и Azure Databricks журналы диагностики.

Кроме того, регулярно просматривайте и управляйте доступом пользователей в консоли администрирования Azure Databricks.

* [Отчеты Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Использование проверок доступа для идентификации Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Управление доступом пользователей в консоли администрирования Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: монитор пытается получить доступ к отключенным учетным записям

**Руководство**. у вас есть доступ к источникам журналов событий входа в Azure AD, аудита и риску, которые позволяют интегрироваться с любым средством SIEM/Monitoring. Кроме того, можно использовать журналы диагностики Azure Databricks для просмотра действий доступа пользователей.

Этот процесс можно упростить, создав параметры диагностики для Azure Active Directory учетных записей пользователей и отправив журналы аудита и журналы входа в рабочую область Log Analytics. Вы можете настроить нужные оповещения в Log Analytics рабочей области.

* [Как использовать API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Как интегрировать журналы действий Azure в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: предупреждение об отклонении режима входа учетной записи

**Руководство**. Использование функций защиты учетных записей и рисков Azure AD для настройки автоматических ответов на обнаруженные подозрительные действия, связанные с удостоверениями пользователей. Вы также можете принимать данные в метку Azure для дальнейшего изучения. Кроме того, можно использовать журналы диагностики Azure Databricks для просмотра действий доступа пользователей.

* [Как просмотреть рискованные входы в Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Настройка и включение политик риска для защиты идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Как подключить метку Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: предоставьте корпорации Майкрософт доступ к соответствующим пользовательским данным во время сценариев поддержки

**Руководство**. когда запросы в службу поддержки будут открыты, инженеры службы поддержки и обслуживания будут запрашивать согласие на доступ к соответствующим данным клиента.

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: сейчас недоступна

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в разделе [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: поддержание инвентаризации конфиденциальной информации

**Руководство**. Использование тегов для отслеживания экземпляров Azure Databricks, обрабатывающих конфиденциальную информацию.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Изолируйте системы, хранящие или обрабатывающие конфиденциальные данные

**Руководство**. Реализация отдельных подписок и групп управления для разработки, тестирования и производства. Развертывание Azure Databricks по умолчанию — это полностью управляемая служба, развернутая в собственной виртуальной сети. Если требуется настройка сети, можно развернуть Azure Databricks в своей виртуальной сети. Рекомендуется создавать отдельные рабочие области Azure Databricks для различных бизнес-групп или отделов.

* [Развертывание Azure Databricks в своей виртуальной сети](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Создание Группы управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Создание виртуальной сети](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Отслеживайте и блокируйте несанкционированную пересылку конфиденциальной информации.

**Рекомендации**. Следуйте рекомендациям по архитектуре защиты утечка данных, чтобы снизить вероятность утечкая.

* [Защита утечка данных с помощью Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Майкрософт управляет базовой инфраструктурой для Azure Databricks и реализует четкие элементы управления для предотвращения потери или раскрытия данных клиента.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: сейчас недоступна

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: шифрование всех конфиденциальных данных во время передачи

**Руководство**. Корпорация Майкрософт будет согласовывать TLS 1,2 по умолчанию при администрировании экземпляра Azure Databricks с помощью консоли портал Azure или Azure Databricks. Веб-приложение "кирпичы" поддерживает TLS 1,3.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: использование активного средства обнаружения для распознавания конфиденциальных данных

**Руководство**: Сейчас недоступно; функции предотвращения идентификации, классификации и защиты от потери данных в настоящее время недоступны для Azure Databricks. Пометьте Azure Databricks экземпляры и связанные ресурсы, которые могут обрабатывать конфиденциальную информацию, и реализуйте сторонние решения, если это необходимо для обеспечения соответствия требованиям.

Платформа "кирпичи данных" предназначена только для вычислений, и все данные хранятся в других службах данных Azure. Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и имеет большую длину для защиты от потери данных и доступности клиентов. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: сейчас недоступна

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6. Использование Azure RBAC для управления доступом к ресурсам

**Руководство**. в Azure Databricks можно использовать списки управления доступом (ACL) для настройки разрешений на доступ к таблицам данных, кластерам, пулам, заданиям и объектам рабочей области, таким как записные книжки, эксперименты и папки. Списками управления доступом могут управлять все пользователи с правами администратора, а также пользователи, которым делегированы разрешения на управление списками управления доступом. Вы можете использовать Azure RBAC для настройки разрешений в рабочей области Azure Databricks.

Примечание. Управление доступом к таблицам, кластерам, пулам, заданиям и рабочим областям доступно только в плане Azure Databricks (цен. категория "Премиум")

* [Управление доступом в Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

Майкрософт управляет базовой инфраструктурой для Azure Databricks и реализует четкие элементы управления для предотвращения потери или раскрытия данных клиента.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: шифрование конфиденциальной информации при хранении

**Руководство**. рабочая область Azure Databricks состоит из плоскости управления, размещенной в Azure Databricks управляемой виртуальной сети, и плоскости данных, развернутой в управляемой клиентом виртуальной сети. В плоскости управления хранятся все записные книжки всех пользователей и связанные записные книжки в базе данных. По умолчанию все записные книжки и результаты шифруются с использованием другого ключа шифрования.

Файловая система кирпичей (DBFS) — это распределенная файловая система, подключенная к рабочей области Azure Databricks и доступная в кластерах Azure Databricks. DBFS реализуется как учетная запись хранения в управляемой группе ресурсов рабочей области Azure Databricks. По умолчанию учетная запись хранения шифруется с помощью ключей, управляемых корпорацией Майкрософт. Ваши данные хранятся в собственных службах данных Azure, и вы можете их зашифровать. Не рекомендуется использовать DBFS для хранения рабочих данных.

Примечание. Эти функции доступны не для всех подписок Azure Databricks. Чтобы запросить доступ, свяжитесь с представителями учетной записи Майкрософт или модулями.

* [Как включить управляемые клиентом ключи для записных книжек Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Как включить управляемые клиентом ключи для Azure Databricks файловой системы](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: журналы и оповещения об изменениях критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналом действий Azure для создания оповещений о том, когда изменения происходят в критических рабочих областях Azure Databricks.

* [Создание оповещений для событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="vulnerability-management"></a>управление уязвимостями;

*Дополнительные сведения см. в разделе [Security Control: управлением уязвимостей](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: Запуск автоматизированных средств проверки уязвимостей

**Руководство**. Реализация решения по управлению уязвимостью от сторонних производителей.

Если у вас есть подписка на платформу управления уязвимостью, вы можете использовать сценарии инициализации Azure Databricks, чтобы установить агенты оценки уязвимостей на узлах кластера Azure Databricks и управлять узлами с помощью соответствующего портала. Обратите внимание, что каждое стороннее решение работает по-разному.

* [Установка агента Rapid7 вручную](https://insightagent.help.rapid7.com/docs/install)

* [Установка агента Qualys вручную](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks скриптов инициализации](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**. корпорация майкрософт поддерживает Azure Databricks базовых образов узлов кластера, но вы несете ответственность за обеспечение исправлений для узлов кластера. Чтобы добавить обновление для обслуживания в существующем работающем кластере, необходимо перезапустить кластер.

* [Общие сведения об обновлениях поддержки среды выполнения для Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Развертывание автоматизированного решения по управлению исправлениями сторонних производителей

**Руководство**. корпорация майкрософт поддерживает Azure Databricks базовых образов узлов кластера, но вы несете ответственность за то, чтобы все устанавливаемые сторонние приложения остались исправленными.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: сравнение проверок уязвимостей "назад-назад"

**Руководство**. Реализация решения по управлению уязвимостью от сторонних производителей, которое может сравнивать проверки уязвимостей с течением времени. Если у вас есть подписка на управление уязвимостью, вы можете использовать портал этого поставщика для просмотра и сравнения проверок уязвимостей обратной передачи. Обратите внимание, что каждое стороннее решение работает по-разному.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Используйте процесс оценки рисков для определения приоритета устранения обнаруженных уязвимостей.

**Руководство**. Используйте общую программу оценки рисков (например, общую систему оценки уязвимостей) или оценки рисков по умолчанию, предоставляемые сторонним средством сканирования.

**Мониторинг центра безопасности Azure**: н/д

**Ответственность**: клиент

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в разделе [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1. Использование обнаружения ресурсов Azure

**Руководство**. Использование графа ресурсов Azure для запроса или обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов и протоколов и т. д.) в рамках ваших подписок. Убедитесь, что в вашем клиенте есть соответствующие разрешения (чтение) и перечислите все подписки Azure, а также ресурсы в ваших подписках.

Хотя классические ресурсы Azure могут быть обнаружены с помощью графа ресурсов, настоятельно рекомендуется создавать и использовать Azure Resource Manager ресурсы, идущие вперед.

* [Создание запросов с помощью графа ресурсов Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Просмотр подписок Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Основные сведения об Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="62-maintain-asset-metadata"></a>6,2: сохранение метаданных активов

**Руководство**. применение тегов к ресурсам Azure с помощью метаданных для логической организации их в таксономию.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: удалите неавторизованные ресурсы Azure

**Руководство**. Использование тегов, групп управления и отдельных подписок (при необходимости) для Организации и мониторинга ресурсов Azure. Регулярно выверка инвентаризации и своевременное удаление неавторизованных ресурсов из подписки.

Кроме того, используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик:
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

* [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Создание Группы управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: ведение инвентаризации утвержденных ресурсов Azure и наименований программного обеспечения.

**Руководство**. Определение утвержденных ресурсов Azure и утвержденного программного обеспечения для ресурсов вычислений.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: отслеживание неутвержденных ресурсов Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в подписках клиентов, с помощью следующих встроенных определений политик:
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

Используйте граф ресурсов Azure для запроса или обнаружения ресурсов в своих подписках. Убедитесь, что все ресурсы Azure, представленные в окружении, утверждены.

* [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Создание запросов с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**: неприменимо; Azure Databricks является службой PaaS, клиенты не имеют прямого доступа к виртуальным машинам в кластере Azure Databricks.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**. Использование графа ресурсов Azure для запроса или обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов и протоколов и т. д.), включая Azure Databricks экземпляры в ваших подписках. Удалите все неутвержденные ресурсы Azure, которые вы обнаружите. Для Azure Databricks узлов кластера реализуйте стороннее решение для удаления или оповещения о неутвержденном программном обеспечении.

* [Создание запросов с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="68-use-only-approved-applications"></a>6,8: Используйте только утвержденные приложения

**Руководство**: неприменимо; Azure Databricks является службой PaaS, у вас нет прямого доступа к виртуальным машинам в кластере Azure Databricks.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="69-use-only-approved-azure-services"></a>6,9: Используйте только утвержденные службы Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в подписках клиентов, с помощью следующих встроенных определений политик:
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

* [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Как отказаться от определенного типа ресурса с помощью политики Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="610-implement-approved-application-list"></a>6,10: реализация списка утвержденных приложений

**Руководство**: неприменимо; Azure Databricks является службой PaaS, у вас нет прямого доступа к виртуальным машинам в кластере Azure Databricks.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Ограничение возможности пользователей взаимодействовать с Azure Resource Manager с помощью сценариев</div>

**Руководство**. Использование условного доступа Azure для ограничения возможности пользователей взаимодействовать с Azure Resource Manager путем настройки "блокировать доступ" для приложения "Управление Microsoft Azure".

* [Настройка условного доступа для блокировки доступа к Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: ограничьте возможность пользователей выполнять сценарии в ресурсах вычислений

**Руководство**: неприменимо; это неприменимо к Azure Databricks, так как пользователям (не администраторам) кластера не требуется доступ к отдельным узлам для выполнения заданий. По умолчанию администратор кластера имеет корневой доступ ко всем узлам кластера.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: физическое или логическое разделение приложений с высоким риском

**Руководство**: неприменимо; тест производительности предназначен для служб приложений Azure или ресурсов, в которых размещены веб-приложения.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="secure-configuration"></a>Безопасная конфигурация

*Дополнительные сведения см. в разделе [Security Control: Secure Configuration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Установите безопасные конфигурации для всех ресурсов Azure.

**Руководство**. Определение и реализация стандартных конфигураций безопасности для экземпляров Azure Databricks с помощью политики Azure. Используйте псевдонимы политик Azure в пространстве имен Microsoft. данных для создания настраиваемых политик для аудита или принудительного применения конфигурации экземпляров Azure Databricks. Обратите внимание, что примененные политики не работают в управляемой группе ресурсов "кирпичы".

* [Просмотр доступных псевдонимов политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Установка безопасных конфигураций операционной системы

**Руководство**: неприменимо; Azure Databricks является службой PaaS, у вас нет прямого доступа к виртуальным машинам в кластере Azure Databricks.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: поддержание защищенных конфигураций ресурсов Azure

**Руководство**. Определение и реализация стандартных конфигураций безопасности для экземпляров Azure Databricks с помощью политики Azure. Используйте псевдонимы политик Azure в пространстве имен Microsoft. данных для создания настраиваемых политик для аудита или принудительного применения конфигурации экземпляров Azure Databricks. Используйте политику Azure [Deny] и [развернуть, если не существует], чтобы обеспечить безопасность параметров в ресурсах Azure.

* [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Общие сведения о влиянии политики Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: поддержание защищенных конфигураций операционных систем

**Руководство**. Azure Databricks образов операционных систем, управляемых и обслуживаемых корпорацией Майкрософт. Вы несете ответственность за реализацию конфигурации состояния на уровне ОС.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: безопасное хранение конфигурации ресурсов Azure

**Руководство**. Если вы используете пользовательские определения политики Azure, используйте Azure DevOps или Azure Repos для безопасного хранения кода и управления им.

* [Как сохранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Документация по Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: безопасное хранение пользовательских образов операционной системы

**Руководство**. Если вы используете пользовательские образы для узлов кластера Azure Databricks, отправьте пользовательский базовый образ в реестр DOCKER, например в реестр контейнеров Azure (запись контроля доступа).

* [Настройка контейнеров с помощью служб контейнеров в модулях](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Общие сведения о реестре контейнеров Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: развертывание средств управления конфигурацией системы

**Руководство**. Определение и реализация стандартных конфигураций безопасности для экземпляров Azure Databricks с помощью политики Azure. Используйте псевдонимы политик Azure в пространстве имен Microsoft. данных для создания настраиваемых политик для аудита или принудительного применения конфигурации экземпляров Azure Databricks.

* [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Просмотр доступных псевдонимов политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией системы для операционных систем

**Руководство**: неприменимо; Azure Databricks является службой PaaS, у вас нет прямого доступа к виртуальным машинам в кластере Azure Databricks.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9. Реализация автоматического мониторинга конфигурации для служб Azure

**Руководство**. Определение и реализация стандартных конфигураций безопасности для экземпляров Azure Databricks с помощью политики Azure. Используйте псевдонимы политик Azure в пространстве имен Microsoft. данных для создания настраиваемых политик для аудита или принудительного применения конфигурации экземпляров Azure Databricks.

* [Просмотр доступных псевдонимов политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10. Реализация автоматического мониторинга конфигурации для операционных систем

**Руководство**. Реализация стороннего решения для отслеживания состояния операционных систем узла кластера Azure Databricks.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="711-manage-azure-secrets-securely"></a>7,11: безопасное управление секретами Azure

**Руководство**. Использование Azure Key Vault с областью действия Azure Databricks секрета для безопасного управления и использования секретов.

* [Использование Azure Key Vault с Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: безопасно управляйте удостоверениями и автоматически

**Руководство**: Сейчас недоступно; Управляемые удостоверения сейчас недоступны для Azure Databricks

* [Службы с поддержкой управляемых удостоверений для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: сейчас недоступна

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: устранение непреднамеренной раскрытия учетных данных

**Руководство**. Реализация средства проверки учетных данных для указания учетных данных в коде. Средство проверки учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault.

* [Настройка средства проверки учетных данных](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в разделе [Security Control: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Рекомендации**. Если у вас есть подписка на платформу управления уязвимостью, вы можете использовать сценарии инициализации Azure Databricks, чтобы установить агенты оценки уязвимостей на узлах кластера Azure Databricks и управлять узлами с помощью соответствующего портала. Обратите внимание, что каждое стороннее решение работает по-разному.

* [Установка агента Rapid7 вручную](https://insightagent.help.rapid7.com/docs/install)

* [Установка агента Qualys вручную](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks скриптов инициализации](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся средами вычислений

**Руководство**. антивредоносное по Майкрософт включено на базовом узле, который поддерживает службы Azure (например, служба приложений Azure), но не выполняется в вашем содержимом.

Предварительно проверять все файлы, отправляемые на Azure Databricks узлы кластера или связанные ресурсы.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Убедитесь, что программное обеспечение и подписи для защиты от вредоносных программ обновлены

**Рекомендации**. Если у вас есть подписка на платформу управления уязвимостью, вы можете использовать сценарии инициализации Azure Databricks, чтобы установить агенты оценки уязвимостей на узлах кластера Azure Databricks и управлять узлами с помощью соответствующего портала. Обратите внимание, что каждое стороннее решение работает по-разному.

* [Установка агента Rapid7 вручную](https://insightagent.help.rapid7.com/docs/install)

* [Установка агента Qualys вручную](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks скриптов инициализации](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в разделе [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Обеспечьте регулярную автоматическую архивацию

**Рекомендации**. для источников данных Azure Databricks убедитесь, что вы настроили соответствующий уровень избыточности данных для вашего варианта использования. Например, если для хранилища данных Azure Databricks используется учетная запись хранения Azure, выберите соответствующий параметр избыточности (LRS, ZRS, GRS, RA-GRS).

* [Источники данных для Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Региональное аварийное восстановление кластеров Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом.

**Руководство**. Резервное копирование всех управляемых клиентом ключей, связанных с реализациями Azure Databricks в Azure Key Vault. Можно также использовать REST API и CLI для создания ежедневной резервной копии конфигураций кирпичей данных. Можно также использовать REST API/CLI для создания ежедневной резервной копии конфигураций кирпичей данных.

* [Резервное копирование ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Руководство**. Тестирование восстановления резервных копий ключей, управляемых клиентом, связанных с реализациями Azure Databricks.

* [Восстановление ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и управляемых клиентом ключей

**Рекомендации**. Включите обратимое удаление в Key Vault для защиты ключей от случайного или вредоносного удаления.

* [Включение обратимого удаления в Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

## <a name="incident-response"></a>Реагирование на инциденты

*Дополнительные сведения см. в разделе [контроль безопасности: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: создайте руководство по реагированию на инциденты

**Руководство**. Создание руководства по реагированию на инциденты для вашей организации. Убедитесь, что имеются письменные планы реагирования на инциденты, которые определяют все роли персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.

* [Настройка автоматизации рабочих процессов в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Руководство по созданию собственного процесса реагирования на инциденты безопасности](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Анатомия инцидента в центре Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Вы также можете воспользоваться руководством по обработке инцидентов безопасности компьютера NIST, чтобы помочь в создании собственного плана реагирования на инциденты.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: создание оценки инцидента и процедуры определения приоритетов

**Руководство**. Центр безопасности назначает серьезность каждому оповещению, чтобы вы могли определить, какие предупреждения следует изучить первыми. Серьезность основывается на том, насколько надежным является центр безопасности в поиске или аналитике, используемой для выдаче оповещения, а также об уровне достоверности, который был вредоносной задачей, вызвавшей оповещение.

Кроме того, четко помечать подписки (например, Рабочая, не производственная) и создайте систему именования, чтобы четко определить и классифицировать ресурсы Azure.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="103-test-security-response-procedures"></a>10,3: тестирование процедур реагирования на безопасность

**Руководство**. проведение упражнений по тестированию возможностей реагирования на инциденты систем на регулярной ритмичности. Выявление слабых точек и пробелов и пересмотр плана по мере необходимости.

* [Обратитесь к публикации NIST: руководство по тестированию, обучению и упражнениям для ИТ-планов и возможностей.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: укажите контактные данные инцидента безопасности и настройте уведомления о событиях для инцидентов безопасности.

**Руководство**. контактные данные инцидентов безопасности будут использоваться корпорацией Майкрософт для связи с вами, если центр Microsoft Security Response Center (MSRC) обнаруживает, что доступ к данным клиента был осуществлен с помощью незаконные или неавторизованной стороны. Проверьте инциденты после факта, чтобы убедиться, что проблемы устранены.

* [Как задать контакт безопасности центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**. Экспорт оповещений и рекомендаций центра безопасности Azure с помощью функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Вы можете использовать соединитель данных центра безопасности Azure для потоковой передачи метки предупреждений.

* [Настройка непрерывного экспорта](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Потоковая передача предупреждений в Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Автоматизируйте ответ на оповещения системы безопасности

**Руководство**. Использование функции автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" в оповещениях и рекомендациях системы безопасности.

* [Настройка автоматизации рабочего процесса и Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в разделе [контроль безопасности: тесты на проникновение и примеры для красных команд](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критически важных результатов безопасности в течение 60 дней.

**Руководство**: * [соблюдение правил корпорации Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Дополнительные сведения о стратегии корпорации Майкрософт и ее выполнении, а также о тестировании на основе уязвимости для облачной инфраструктуры, служб и приложений Майкрософт см. здесь.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со статьей о [производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Дополнительные сведения о [базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
