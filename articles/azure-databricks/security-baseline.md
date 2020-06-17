---
title: Базовая конфигурация системы безопасности Azure для Azure Databricks
description: Базовая конфигурация системы безопасности Azure для Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 23057723d46ed5d9203741ab2eb1a15edb5bd510
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681693"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Базовая конфигурация системы безопасности Azure для Azure Databricks

Базовая конфигурация безопасности для Azure Databricks содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовая конфигурация безопасности для этой службы взята из [Эталона безопасности Azure версии 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), содержащего рекомендации по обеспечению безопасности облачных решений в Azure с помощью наших практических рекомендаций.

Дополнительные сведения см. в статье [Обзор базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в статье [Управление безопасностью: безопасность сети](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1. Защита ресурсов в виртуальной сети с помощью групп безопасности сети или Брандмауэра Azure

**Руководство**. Развертывание Azure Databricks в виртуальной сети Azure (VNet). Azure Databricks, развернутая по умолчанию, — это полностью управляемая служба в Azure: все ресурсы плоскости данных, включая виртуальную сеть, с которой будут связаны все кластеры, развертываются в заблокированной группе ресурсов. Однако если требуется настройка сети, можно развернуть ресурсы плоскости данных Azure Databricks в собственной виртуальной сети (внедрение виртуальной сети), что позволит реализовать пользовательские конфигурации сети. Вы можете применить собственную группу безопасности сети (NSG) с настраиваемыми правилами к определенным ограничениям исходящего трафика.

Кроме того, можно настроить правила NSG, чтобы указать ограничения исходящего трафика в подсети, в которой развернут экземпляр Azure Databricks. Брандмауэр Azure можно настроить для рабочих областей, внедренных в виртуальную сеть.

* [Как развернуть Azure Databricks в виртуальной сети Azure](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Как управлять группами безопасности сети](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Мониторинг Центра безопасности Azure**. Сейчас это недоступно.

**Ответственность**. Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Развертывание Azure Databricks в виртуальной сети Azure (VNet). Группа безопасности сети (NSG) не создается автоматически. Вы должны создать базовую NSG только с правилами Azure по умолчанию. При развертывании рабочей области добавляются необходимые правила для Databricks. Вы можете также начать с пустой группы NSG. Соответствующие правила будут добавлены автоматически. Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков в рабочую область Log Analytics и использовать Аналитику трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитики трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять некорректные сетевые настройки.

* [Как развернуть Azure Databricks в виртуальной сети Azure](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Как включить журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Как включать и использовать Аналитику трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Как включить Наблюдатель за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

### <a name="13-protect-critical-web-applications"></a>1.3. Защита критических веб-приложений

**Руководство**. Неприменимо. Эта рекомендация предназначена для веб-приложений, работающих в Службе приложений Azure или в ресурсах вычислений.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Неприменимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4. Запрет взаимодействия с известными опасными IP-адресами

**Руководство**. Включите Защиту от атак DDoS Azure уровня "Стандартный" в виртуальных сетях Azure, связанных с экземплярами Azure Databricks, чтобы защититься от атак типа "отказ в обслуживании". Используйте интегрированную аналитику угроз Центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми общедоступными IP-адресами.

* [Управление защитой от атак DDoS Azure уровня "Стандартный" с помощью портала Azure](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Защите от угроз для сетевого уровня Azure](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5. Запись сетевых пакетов и журналов потоков

**Руководство**. Развертывание Azure Databricks в виртуальной сети Azure (VNet). Группа безопасности сети (NSG) не создается автоматически. Вы должны создать базовую NSG только с правилами Azure по умолчанию. При развертывании рабочей области добавляются необходимые правила для Databricks. Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков в рабочую область Log Analytics и использовать Аналитику трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитики трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять некорректные сетевые настройки.

* [Как развернуть Azure Databricks в виртуальной сети Azure](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Как включить журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Как включать и использовать Аналитику трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Как включить Наблюдатель за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Развертывание сетевых систем обнаружения и предотвращения вторжений (IDS/IPS)

**Руководство**. Реализация сетевого виртуального модуля (NVA) с поддержкой IDP/IPS из Azure Marketplace для создания интегрированной в виртуальную сеть рабочей области, в которой все кластеры Azure Databricks имеют один исходящий IP-адрес. Этот IP-адрес можно использовать в качестве дополнительного уровня безопасности с другими службами и приложениями Azure, разрешающими доступ с конкретных IP-адресов. Для управления входящим и исходящим трафиком можно использовать брандмауэр Azure или другие сторонние средства, такие как NVA.

Если система обнаружения или предотвращения вторжений на основе проверки атакующего кода не является обязательной, можно использовать Брандмауэр Azure с аналитикой угроз. Фильтрация Брандмауэра Azure на основе аналитики угроз может создавать оповещения и запрещать трафик, поступающий с известных вредоносных IP-адресов и доменов, а также передающий на них. IP-адреса и домены также передаются из канала Microsoft Threat Intelligence.

* [Как назначить один общедоступный IP-адрес для рабочих областей, внедренных в виртуальную сеть, с помощью Брандмауэра Azure](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [Как создать NVA](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7. Управление трафиком к веб-приложениям

**Руководство**. Неприменимо. Эта рекомендация предназначена для веб-приложений, работающих в Службе приложений Azure или в ресурсах вычислений.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Неприменимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Уменьшение сложности и дополнительных затрат на администрирование в правилах безопасности сети

**Руководство**. Используйте теги службы для определения управления доступом к сети групп безопасности сети, подключенных к подсетям, с которыми связан экземпляр Azure Databricks. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, ApiManagement) в соответствующем исходном поле или поле назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, входящих в тег службы, и автоматически обновляет этот тег при изменении адресов. Теги службы не поддерживаются с не внедренными рабочими областями виртуальной сети.

* [Теги службы виртуальной сети](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9. Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определите и реализуйте сетевые конфигурации безопасности для экземпляров Azure Databricks с помощью Политики Azure. Вы можете использовать псевдонимы Политик Azure в пространстве имен Microsoft.Databricks для определения пользовательских определений политики. Политики не применяются к ресурсам в пределах управляемой группы ресурсов.

Вы также можете использовать Azure Blueprints для упрощения крупномасштабных развертываний Azure с помощью упаковки ключевых артефактов среды, таких как шаблоны Azure Resource Manager, элементы управления доступом на основе ролей (RBAC) и политики, в единое определение схемы. Простое применение схемы к новым подпискам и средам, а также управление точной настройкой и управление с помощью версионирования.

* [Как настроить Политику Azure и управлять ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Сведения о псевдонимах Политики Azure и структуре определения](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Как создать схему Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10. Документация по правилам конфигурации трафика

**Руководство**. Используйте теги для групп безопасности сети и других ресурсов, связанных с сетевой безопасностью и потоком трафика, с помощью экземпляра Azure Databricks. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и длительность (и т. д.) любых правил, которые разрешают трафик в сеть и из нее.

* [Как создавать и использовать теги](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Используйте журнал действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений для сетевых ресурсов, связанных с экземплярами Azure Databricks. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых ресурсов.

* [Как просматривать и извлекать события журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Как создать оповещения в службе Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в статье [Управление безопасностью: Ведение журналов и мониторинг](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1. Использование утвержденных источников синхронизации времени

**Руководство**. Корпорация Майкрософт поддерживает источники времени для ресурсов Azure. Однако вы можете управлять параметрами синхронизации времени для вычислительных ресурсов. Так как Azure Databricks является службой PaaS, вы не имеете прямого доступа к виртуальным машинам в кластере Azure Databricks и не можете настроить параметры синхронизации времени.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2. Настройка централизованного управления журналами безопасности

**Руководство**. Прием журналов с помощью Azure Monitor для агрегирования данных безопасности, созданных Azure Databricks. В Azure Monitor можно выполнить запрос к рабочей области Log Analytics, настроенной для получения журналов Databricks и диагностики. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения журналов или концентраторов событий для экспорта данных в другие системы. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним системам управления информационной безопасностью и событиями безопасности (SIEM).

Примечание. Для журналов диагностики Azure Databricks требуется план Azure Databricks уровня "Премиум".

* [Как настроить параметры диагностики](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Начало работы с Azure Monitor и интеграция SIEM стороннего производителя](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Включение журналов аудита для ресурсов Azure

**Руководство**. Включите параметры диагностики журнала действий Azure и отправьте журналы в рабочую область Log Analytics, концентратор событий Azure или учетную запись хранения Azure для архивации. С помощью данных журнала действий Azure вы можете определить, что нужно сделать, с чем и когда, для операций записи (PUT, POST, DELETE), выполненных на уровне управления для ресурсов Azure.

Для ведения журнала аудита Azure Databricks предоставляет исчерпывающие комплексные журналы диагностики действий, выполняемых пользователями Azure Databricks, позволяя корпоративным предприятиям отслеживать подробные закономерности в использовании Azure Databricks.

Примечание. Для журналов диагностики Azure Databricks требуется план Azure Databricks уровня "Премиум".

* [Как включить параметры диагностики для журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Как включить параметры диагностики для Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Мониторинг Центра безопасности Azure**. Сейчас это недоступно.

**Ответственность**. Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Сбор журналов безопасности из операционных систем

**Руководство**. Azure Databricks предоставляет исчерпывающие комплексные журналы диагностики действий, выполняемых пользователями Azure Databricks, позволяя корпоративным предприятиям отслеживать подробные закономерности в использовании Azure Databricks.

Примечание. Для журналов диагностики Azure Databricks требуется план Azure Databricks уровня "Премиум". Ведение журнала безопасности ОС недоступно.

* [Как включить параметры диагностики для журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Как включить параметры диагностики для Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Мониторинг Центра безопасности Azure**. Сейчас это недоступно.

**Ответственность**. Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5. Настройка хранения журнала безопасности

**Руководство**. Включите параметры диагностики для Azure Databricks. Если вы хотите сохранить журналы в рабочей области Log Analytics, задайте для нее период хранения согласно нормативным требованиям вашей организации. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения. Действия, связанные с безопасностью, отслеживаются в журналах аудита Databricks.

Примечание. Для журналов диагностики Azure Databricks требуется план Azure Databricks уровня "Премиум".

* [Как включить параметры диагностики в Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Как задать параметры хранения журнала для рабочих областей Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="26-monitor-and-review-logs"></a>2.6. Мониторинг и просмотр журналов

**Руководство**. Включите параметры диагностики для Azure Databricks и отправьте журналы в рабочую область Log Analytics. Используйте рабочую область Log Analytics для анализа и мониторинга журналов Azure Databricks для получения результатов регулярного просмотра и аномального поведения.

Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

Примечание. Для журналов диагностики Azure Databricks требуется план Azure Databricks уровня "Премиум".

* [Как включить параметры диагностики в Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Как выполнить запросы журналов Azure Databricks, отправляемых в рабочую область Log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7. Включение оповещений об аномальных действиях

**Руководство**. Настройте параметры диагностики для экземпляра Azure Databricks и отправьте журналы в рабочую область Log Analytics. В рабочей области Log Analytics настройте оповещения, которые должны выполняться при заданном наборе условий.

Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

Примечание. Для журналов диагностики Azure Databricks требуется план Azure Databricks уровня "Премиум".

* [Как отправить журналы Azure Databricks в рабочую область Log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery)

* [Как настроить оповещения журнала в рабочей области Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Мониторинг Центра безопасности Azure**. Сейчас это недоступно.

**Ответственность**. Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8. Централизованное ведение журнала защиты от вредоносных программ

**Руководство**. Неприменимо.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Неприменимо

### <a name="29-enable-dns-query-logging"></a>2.9. Включение ведения журнала запросов DNS

**Руководство**. Неприменимо. Azure Databricks не обрабатывает и не создает доступные пользователям журналы, связанные с DNS.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Неприменимо

### <a name="210-enable-command-line-audit-logging"></a>2.10. Включение ведения журнала аудита для командной строки

**Руководство**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Неприменимо

## <a name="identity-and-access-control"></a>Идентификаторы и управление доступом

*Дополнительные сведения см. в статье [Управление безопасностью: идентификаторы и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Инвентаризация учетных записей администраторов

**Руководство**. Интерфейсы API SCIM Azure Databricks можно использовать для управления пользователями в рабочей области Azure Databricks и предоставления прав администратора для определенных пользователей. Вы также можете управлять ими с помощью пользовательского интерфейса Azure Databricks.

* [Как использовать API-интерфейсы SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Как добавить и удалить пользователей в Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2. Изменение паролей по умолчанию (где применимо)

**Руководство**. Azure Databricks использует Azure Active Directory (AD) для предоставления доступа к порталу Azure, а также к консоли администрирования Azure Databricks. В Azure AD нет концепции паролей по умолчанию, однако вы несете ответственность за изменение или запрет паролей по умолчанию для пользовательских или сторонних приложений.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3. Применение выделенных административных учетных записей

**Руководство**. Вы можете использовать интерфейсы API SCIM Azure Databricks для добавления пользователей с правами администратора в Azure Databricks. Вы также можете управлять ими с помощью пользовательского интерфейса Azure Databricks.

* [Как использовать API-интерфейсы SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Как добавить и удалить пользователей в Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4. Использование единого входа с Azure Active Directory

**Руководство**. Azure Databricks автоматически настраивается для использования единого входа Azure Active Directory, чтобы выполнить проверку подлинности пользователей. Пользователи за пределами организации должны завершить процедуру приглашения и их необходимо добавить в ваш клиент Active Directory, прежде чем они смогут войти в Azure Databricks с помощью единого входа. Вы можете реализовать SCIM для автоматизации подготовки и отзыва пользователей из рабочих областей.

* [Как использовать API-интерфейсы SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Настройка единого входа](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Как пригласить внешних пользователей в свой клиент Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5. Использование многофакторной проверки подлинности для любого доступа на основе Azure Active Directory

**Руководство**. Включите многофакторную проверку подлинности Azure AD и следуйте рекомендациям по управлению идентификацией и доступом в Центре безопасности Azure.

* [Как включить MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Мониторинг идентификации и доступа в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Используйте выделенные компьютеры (рабочие станции с привилегированным доступом) для всех административных задач

**Руководство**. Используйте рабочие станции привилегированного доступа (PAW) с настроенной многофакторной проверкой подлинности для входа в ресурсы Azure и их настройки.

* [Рабочие станции с привилегированным доступом](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Как включить MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7. Ведение журнала и создание оповещений по подозрительным действиям учетных записей администраторов

**Руководство**. Используйте отчеты безопасности Azure Active Directory для создания журналов и оповещений при возникновении подозрительных или небезопасных действий в окружении. Используйте Центр безопасности Azure для мониторинга действий идентификации и доступа. Кроме того, можно использовать журналы диагностики Azure Databricks.

* [Как определить пользователей Azure AD, помеченных для события риска](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Мониторинг пользовательских действий идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8. Управление ресурсами Azure только из утвержденных расположений

**Руководство**. Используйте именованные расположения с условным доступом, чтобы разрешить доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

* [Как настроить именованные расположения](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="39-use-azure-active-directory"></a>3.9. Использование Azure Active Directory

**Руководство**. Azure Databricks автоматически настраивается для использования единого входа Azure Active Directory, чтобы выполнить проверку подлинности пользователей. Пользователи за пределами организации должны завершить процедуру приглашения и их необходимо добавить в ваш клиент Active Directory, прежде чем они смогут войти в Azure Databricks с помощью единого входа.

* [Настройка единого входа](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Как пригласить внешних пользователей в свой клиент Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10. Регулярная проверка и согласование доступа пользователей

**Руководство**. Azure AD предоставляет журналы для облегчения поиска устаревших учетных записей. Кроме того, используйте проверку доступа удостоверений Azure для эффективного управления членством в группах, доступа к корпоративным приложениям и назначения ролей. Доступ пользователей можно проверять на регулярной основе, чтобы только у авторизованных пользователей был постоянный доступ. Вы также можете реализовать интерфейсы API SCIM и журналы диагностики Azure Databricks для проверки доступа пользователей. Для проверки доступа пользователей можно также использовать интерфейсы API SCIM и журналы диагностики Azure Databricks.

Кроме того, регулярно проверяйте и управляйте доступом пользователей в консоли администрирования Azure Databricks.

* [Отчеты Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Использование проверок доступа для идентификации Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Как проверить и управлять доступом пользователей в консоли администрирования Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11. Отслеживание попыток доступа к отключенным учетным записям

**Руководство**. У вас есть доступ к отчетам о действиях входа в Azure AD, журналу событий риска и аудита. Эти источники позволяют интегрироваться с любым средством мониторинга или SIEM. Кроме того, для проверки действия доступа пользователей можно использовать журналы диагностики Azure Databricks.

Этот процесс можно упростить, создав параметры диагностики для учетных записей пользователей Azure Active Directory и отправив журналы аудита и журналы входа в рабочую область Log Analytics. Вы можете настроить необходимые оповещения в рабочей области Log Analytics.

* [Как использовать API-интерфейсы SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Как интегрировать журналы действий Azure в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12. Предупреждение при подозрительном входе в учетную запись

**Руководство**. Используйте функции защиты идентификации и обнаружения рисков Azure AD, чтобы настроить автоматическое реагирование для обнаружения подозрительных действий, связанных с удостоверениями пользователей. Вы также можете включить данные в Azure Sentinel для дальнейшего изучения. Кроме того, для проверки действия доступа пользователей можно использовать журналы диагностики Azure Databricks.

* [Как просматривать рискованные входы в Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Как настроить и включить политики рисков с помощью защиты идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг Центра безопасности Azure**. Сейчас это недоступно.

**Ответственность**. Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13. Предоставление корпорации Майкрософт доступа к соответствующим данным клиентов в рамках сценариев поддержки

**Руководство**. Если запросы в службу поддержки открыты, инженеры службы поддержки клиентов запросят согласие на доступ к соответствующим данным клиента.

**Мониторинг Центра безопасности Azure**. Сейчас это недоступно.

**Ответственность**. Сейчас это недоступно.

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в статье [Управление безопасностью: защита данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Инвентаризация конфиденциальных данных

**Руководство**. Используйте теги для пометки экземпляров Azure Databricks, в которых обрабатываются конфиденциальные данные.

* [Как создавать и использовать теги](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2. Изолирование систем, хранящих или обрабатывающих конфиденциальные данные

**Руководство**. Реализуйте отдельные подписки и группы управления для разработки, тестирования и производства. Azure Databricks, развернутая по умолчанию, — это полностью управляемая служба, развернутая в собственной виртуальной сети. Если требуется настройка сети, вы можете развернуть Azure Databricks в своей виртуальной сети. Рекомендуется создавать отдельные рабочие области Azure Databricks для различных бизнес-команд или отделов.

* [Как развернуть Azure Databricks в виртуальной сети Azure](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Как создать дополнительные подписки Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Как создать Группы управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Как создать виртуальную сеть](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Руководство**. Следуйте архитектуре защиты от утечки данных Databricks, чтобы уменьшить вероятность этого события.

* [Защита от утечки данных с помощью Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Корпорация Майкрософт управляет базовой инфраструктурой Azure Databricks и реализовала надежные элементы управления для предотвращения потери или раскрытия данных клиента.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг Центра безопасности Azure**. Сейчас это недоступно.

**Ответственность**. Сейчас это недоступно.

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Шифрование любой конфиденциальной информации при передаче

**Руководство**. Корпорация Майкрософт будет согласовывать протокол TLS 1.2 по умолчанию при администрировании экземпляра Azure Databricks с помощью портала Azure или консоли Azure Databricks. Веб-приложение Databricks поддерживает TLS 1.3.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Использование средства активного обнаружения для распознавания конфиденциальных данных

**Руководство**. Сейчас это недоступно. Функции идентификации, классификации и предотвращения потери данных пока недоступны для Azure Databricks. Пометьте экземпляры Azure Databricks и связанные ресурсы, которые могут обрабатывать конфиденциальную информацию, и реализуйте сторонние решения, если это необходимо для обеспечения соответствия требованиям.

Платформа Databricks предназначена только для вычислений, а все данные хранятся в других службах данных Azure. Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и предпринимает все возможные усилия для защиты клиентов от потери данных и раскрытия информации. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг Центра безопасности Azure**. Сейчас это недоступно.

**Ответственность**. Сейчас это недоступно.

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6. Контроль доступа к ресурсам с помощью RBAC

**Руководство**. В Azure Databricks вы можете с помощью списков управления доступом настраивать разрешения на доступ к таблицам данных, кластерам, пулам, заданиям и объектам рабочей области (записные книжки, эксперименты и папки). Списками управления доступом могут управлять все пользователи с правами администратора, а также пользователи, которым делегированы разрешения на управление списками управления доступом. Для настройки разрешений в рабочей области Azure Databricks используйте RBAC.

Примечание. Управление доступом к таблицам, кластерам, пулам, заданиям и рабочим областям доступно только в плане Azure Databricks ценовой категории "Премиум".

* [Как управлять контролем доступа Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

Корпорация Майкрософт управляет базовой инфраструктурой Azure Databricks и реализовала надежные элементы управления для предотвращения потери или раскрытия данных клиента.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Неприменимо

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Шифрование конфиденциальной информации при хранении

**Руководство**. Рабочая область Azure Databricks состоит из плоскости управления, размещенной в виртуальной сети, управляемой Azure Databricks, и плоскости данных, развернутой в управляемой клиентом виртуальной сети. В плоскости управления хранятся все записные книжки пользователей и связанные результаты записных книжек в базе данных. По умолчанию все записные книжки и результаты шифруются при хранении с помощью другого ключа шифрования.

Файловая система Databricks (DBFS) — это распределенная файловая система, подключенная к рабочей области Azure Databricks и доступная в кластерах Azure Databricks. DBFS реализуется как учетная запись хранения в управляемой группе ресурсов рабочей области Azure Databricks. По умолчанию учетная запись хранения шифруется с помощью ключей, управляемых корпорацией Майкрософт. Ваши данные хранятся в службах данных Azure (которыми вы владеете), и вы можете их зашифровать. Не рекомендуется использовать DBFS для хранения рабочих данных.

Примечание. Эти функции недоступны для всех подписок Azure Databricks. Чтобы запросить доступ, обратитесь к представителю корпорации Майкрософт или Databricks.

* [Как активировать управляемые пользователем ключи для записных книжек Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Как включить управляемые пользователем ключи для файловой системы Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Совмещаемая блокировка

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Включение в журнал и создание оповещений по изменениям критических ресурсов Azure

**Руководство**. Используйте Azure Monitor с журналом действий Azure для создания оповещений о критических изменениях в рабочей области Azure Databricks.

* [Как создать оповещения для событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

## <a name="vulnerability-management"></a>управление уязвимостями;

*Дополнительные сведения см. в статье [Управление безопасностью: управление уязвимостями](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Выполнение автоматизированных средств анализа уязвимостей

**Руководство**. Реализуйте стороннее решение по управлению уязвимостями.

Если у вас есть подписка на платформу управления уязвимостями, вы можете использовать сценарии инициализации Azure Databricks, чтобы установить агенты оценки уязвимостей на узлах кластера Azure Databricks и управлять узлами с помощью соответствующего портала. Обратите внимание, что каждое стороннее решение работает по-разному.

* [Как вручную установить агент Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Как вручную установить агент Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Скрипты инициализации Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**. Корпорация Майкрософт поддерживает базовые образы узлов кластера Azure Databricks, но вы несете ответственность за установку для них исправлений. Чтобы добавить обновление для обслуживания в существующем работающем кластере, необходимо его перезапустить.

* [Общие сведения по обновлению Runtime в рамках обслуживания Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3. Развертывание автоматизированного решения для управления исправлениями ПО сторонних производителей

**Руководство**. Корпорация Майкрософт поддерживает базовые образы узлов кластера Azure Databricks, но вы несете ответственность за исправления для установленных вами сторонних приложений.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4. Сравнение проверок смежных уязвимостей

**Руководство**. Реализуйте стороннее решение по управлению уязвимостями, которое может сравнивать результаты проверки уязвимостей с течением времени. Если у вас есть подписка на управление уязвимостями, можно использовать портал этого поставщика для просмотра и сравнения проверок смежных уязвимостей. Обратите внимание, что каждое стороннее решение работает по-разному.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Использование процесса оценки рисков для определения приоритета в устранении обнаруженных уязвимостей

**Руководство**. Используйте общую программу оценки рисков (например, Common Vulnerability Scoring System) или оценки рисков по умолчанию, предоставляемые сторонним средством сканирования.

**Мониторинг Центра безопасности Azure**. Недоступно

**Ответственность**. Customer

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в статье [Управление безопасностью: инвентаризация и управление ресурсами](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1. Использование обнаружения ресурсов Azure

**Руководство**. Используйте Azure Resource Graph для запроса и обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов и протоколов и т. д.) в ваших подписках. Убедитесь, что в вашем клиенте есть соответствующие разрешения (на чтение) и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

Хотя классические ресурсы Azure можно обнаружить через Resource Graph, настоятельно рекомендуется в дальнейшем создавать и использовать ресурсы Azure Resource Manager.

* [Как создавать запросы с помощью Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Как просматривать подписки Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Основные сведения об Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="62-maintain-asset-metadata"></a>6.2. Ведение метаданных активов

**Руководство**. Применяйте к ресурсам Azure теги, чтобы логически классифицировать их на основе метаданных.

* [Как создавать и использовать теги](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Удаление неавторизованных ресурсов Azure

**Руководство**. При необходимости используйте теги, группы управления и отдельные подписки, чтобы упорядочить и отслеживать ресурсы Azure. Регулярно сверяйте ресурсы, чтобы своевременно удалять неавторизованные ресурсы из подписки.

Кроме того, используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик.
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

* [Как создать дополнительные подписки Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Как создать Группы управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Как создавать и использовать теги](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4. Ведение каталога утвержденных ресурсов Azure и наименований программного обеспечения

**Руководство**. Определите утвержденные ресурсы Azure и программное обеспечение для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5. Отслеживание неутвержденных ресурсов Azure

**Руководство**. Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик.
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

Используйте Azure Resource Graph для запроса или обнаружения ресурсов в подписках. Убедитесь, что все ресурсы Azure, представленные в окружении, утверждены.

* [Как настроить Политику Azure и управлять ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Как создавать запросы с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6. Отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**. Неприменимо. Azure Databricks является службой PaaS, у клиентов нет прямого доступа к виртуальным машинам в кластере Azure Databricks.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**. Используйте Azure Resource Graph для запроса и обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов и протоколов и т. д.), включая экземпляр Azure Databricks, в ваших подписках. Удалите все неутвержденные ресурсы Azure, которые вы обнаружите. Для узлов кластера Azure Databricks реализуйте стороннее решение, чтобы удалить неутвержденное программное обеспечение или оповещения о нем.

* [Как создавать запросы с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="68-use-only-approved-applications"></a>6.8. Использование только утвержденных приложений

**Руководство**. Неприменимо. Azure Databricks является службой PaaS, у вас нет прямого доступа к виртуальным машинам в кластере Azure Databricks.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="69-use-only-approved-azure-services"></a>6.9. Использование только утвержденных служб Azure

**Руководство**. Используйте Политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик.
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

* [Как настроить Политику Azure и управлять ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Как отказаться от определенного типа ресурса с помощью Политики Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="610-implement-approved-application-list"></a>6.10. Реализация списка утвержденных приложений

**Руководство**. Неприменимо. Azure Databricks является службой PaaS, у вас нет прямого доступа к виртуальным машинам в кластере Azure Databricks.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11. <div>Ограничение возможности пользователей взаимодействовать с Azure Resource Manager с помощью сценариев</div>

**Руководство**. Используйте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "Блокировать доступ" для приложения "Управление Microsoft Azure".

* [Как настроить условный доступ для блокировки доступа к Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12. Ограничьте возможность пользователей выполнять сценарии в вычислительных ресурсах

**Руководство**. Неприменимо. Это неприменимо к Azure Databricks, так как пользователям (не администраторам) кластера не требуется доступ к отдельным узлам для выполнения заданий. По умолчанию администратор кластера имеет корневой доступ ко всем узлам кластера.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Неприменимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13. Физическое или логическое разделение приложений с высоким риском

**Руководство**. Неприменимо. Тест производительности предназначен для Службы приложений Azure или вычислительных ресурсов для размещения веб-приложений.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Неприменимо

## <a name="secure-configuration"></a>Безопасная конфигурация

*Дополнительные сведения см. в статье [Управление безопасностью: безопасная конфигурация](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Установка безопасных конфигураций для всех ресурсов Azure

**Руководство**. Определите и реализуйте стандартные конфигурации безопасности для экземпляров Azure Databricks с помощью Политики Azure. Используйте псевдонимы Политик Azure в пространствах имен "Microsoft.Databricks" для создания настраиваемых политик, предназначенных для аудита или принудительного применения конфигурации для экземпляров Azure Databricks. Обратите внимание, что примененные политики не работают в управляемой группе ресурсов Databricks.

* [Как просмотреть доступные псевдонимы Политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Как настроить Политику Azure и управлять ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Сохранение безопасных конфигураций для операционных систем

**Руководство**. Неприменимо. Azure Databricks является службой PaaS, у вас нет прямого доступа к виртуальным машинам в кластере Azure Databricks.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3. Сохранение безопасных конфигураций для ресурсов Azure

**Руководство**. Определите и реализуйте стандартные конфигурации безопасности для экземпляров Azure Databricks с помощью Политики Azure. Используйте псевдонимы Политик Azure в пространствах имен "Microsoft.Databricks" для создания настраиваемых политик, предназначенных для аудита или принудительного применения конфигурации для экземпляров Azure Databricks. Используйте политику Azure [отказывать] и [развернуть, если не существует], чтобы обеспечить безопасность параметров в ресурсах Azure.

* [Как настроить Политику Azure и управлять ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Сведения о действии Политик Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4. Сохранение безопасных конфигураций для операционных систем

**Руководство**. Управление образами операционной системы Azure Databricks и их обслуживание осуществляет корпорация Майкрософт. Вы несете ответственность за реализацию настройки состояния на уровне ОС.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Совмещаемая блокировка

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5. Безопасное хранение конфигурации ресурсов Azure

**Руководство**. Если вы используете пользовательские определения политики Azure, используйте Azure DevOps или Azure Repos для безопасного хранения кода и управления им.

* [Как хранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Документация по Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6. Безопасное хранение пользовательских образов операционной системы

**Руководство**. При использовании пользовательских образов для узлов кластера Azure Databricks отправьте пользовательский базовый образ в реестр Docker, например в Реестр контейнеров Azure (ACR).

* [Как настраивать контейнеры с помощью служб контейнеров Databricks](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Общие сведения о Реестре контейнеров Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7. Развертывание инструментов управления конфигурацией системы

**Руководство**. Определите и реализуйте стандартные конфигурации безопасности для экземпляров Azure Databricks с помощью Политики Azure. Используйте псевдонимы Политик Azure в пространствах имен "Microsoft.Databricks" для создания настраиваемых политик, предназначенных для аудита или принудительного применения конфигурации для экземпляров Azure Databricks.

* [Как настроить Политику Azure и управлять ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Как просмотреть доступные псевдонимы Политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Как настроить Политику Azure и управлять ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8. Развертывание средств управления конфигурацией системы для операционных систем

**Руководство**. Неприменимо. Azure Databricks является службой PaaS, у вас нет прямого доступа к виртуальным машинам в кластере Azure Databricks.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9. Реализация автоматизированного мониторинга конфигурации для служб Azure

**Руководство**. Определите и реализуйте стандартные конфигурации безопасности для экземпляров Azure Databricks с помощью Политики Azure. Используйте псевдонимы Политик Azure в пространствах имен "Microsoft.Databricks" для создания настраиваемых политик, предназначенных для аудита или принудительного применения конфигурации для экземпляров Azure Databricks.

* [Как просмотреть доступные псевдонимы Политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Как настроить Политику Azure и управлять ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10. Реализация автоматизированного мониторинга конфигурации для операционных систем

**Руководство**. Реализуйте стороннее решение для мониторинга состояния операционных систем узла кластера Azure Databricks.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11. Безопасное управление секретами Azure

**Руководство**. Используйте Azure Key Vault с областью секрета Azure Databricks для безопасного управления секретами и их использования.

* [Как использовать Azure Key Vault с Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12. Безопасное и автоматическое управление удостоверениями

**Руководство**. Сейчас это недоступно. Управляемые удостоверения сейчас недоступны для Azure Databricks.

* [Службы с поддержкой управляемых удостоверений для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Мониторинг Центра безопасности Azure**. Сейчас это недоступно.

**Ответственность**. Сейчас это недоступно.

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13. Устранение непреднамеренного раскрытия учетных данных

**Руководство**. Реализуйте сканер учетных данных для обнаружения учетных данных в коде. Сканер учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault.

* [Как настроить сканер учетных данных](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в статье [Управление безопасностью: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Руководство**. Если у вас есть подписка на платформу управления уязвимостями, вы можете использовать сценарии инициализации Azure Databricks, чтобы установить агенты оценки уязвимостей на узлах кластера Azure Databricks и управлять узлами с помощью соответствующего портала. Обратите внимание, что каждое стороннее решение работает по-разному.

* [Как вручную установить агент Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Как вручную установить агент Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Скрипты инициализации Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2. Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся вычислительными

**Руководство**. Защита от вредоносных программ Майкрософт (Microsoft Antimalware) включена на базовом узле, поддерживающем службы Azure (например, Служба приложений Azure), но не выполняется в вашем содержимом.

Предварительно проверяйте все файлы, которые передаются в узлы кластера Azure Databricks или связанные ресурсы.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3. Своевременное обновление программного обеспечения для защиты от вредоносных программ и подписей

**Руководство**. Если у вас есть подписка на платформу управления уязвимостями, вы можете использовать сценарии инициализации Azure Databricks, чтобы установить агенты оценки уязвимостей на узлах кластера Azure Databricks и управлять узлами с помощью соответствующего портала. Обратите внимание, что каждое стороннее решение работает по-разному.

* [Как вручную установить агент Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Как вручную установить агент Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Скрипты инициализации Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в статье [Управление безопасностью: восстановление данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1. Обеспечение регулярного автоматического резервного копирования

**Руководство**. Для источников данных Azure Databricks убедитесь, что вы настроили соответствующий уровень избыточности данных для вашего варианта использования. Например, если для хранилища данных Azure Databricks используется учетная запись хранения Azure, выберите соответствующий параметр избыточности (LRS, ZRS, GRS, RA-GRS).

* [Источники данных для Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Региональное аварийное восстановление кластеров Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Выполнение полного резервного копирования системы и любых ключей, управляемых клиентом

**Руководство**. Создайте резервную копию всех управляемых клиентом ключей, связанных с реализациями Azure Databricks в Azure Key Vault. Вы также можете использовать REST API и CLI для создания ежедневной резервной копии конфигураций Databricks. Вы также можете использовать REST API и CLI для создания ежедневной резервной копии конфигураций Databricks.

* [Как создать резервную копию ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3. Проверка всех резервных копий, включая управляемые клиентом ключи

**Руководство**. Проверьте восстановление резервных копий управляемых клиентом ключей, связанных с реализациями Azure Databricks.

* [Как восстановить резервную копию ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Обеспечение защиты резервных копий и управляемых клиентом ключей

**Руководство**. Убедитесь, что обратимое удаление включено в Key Vault для защиты ключей от случайного или злонамеренного удаления.

* [Как включить обратимое удаление в Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

## <a name="incident-response"></a>Реагирование на инциденты

*Дополнительные сведения см. в статье [Управление безопасностью: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Создание руководства по реагированию на инциденты

**Руководство**. Создайте руководство по реагированию на инциденты для вашей организации. Убедитесь, что имеются письменные планы реагирования на инциденты, которые определяют все действия персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.

* [Как настроить автоматизацию рабочих процессов в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Руководство по созданию собственного процесса реагирования на инциденты безопасности](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Анатомия инцидента Центра Майкрософт по реагированию на угрозы](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Использование руководства по обработке инцидентов безопасности компьютера от NIST для разработки собственного плана реагирования на инциденты](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Создание процедуры оценки инцидента и определения приоритетов

**Руководство**. Центр безопасности назначает каждому оповещению серьезность, которая поможет определить, какие предупреждения следует расследовать первыми. Серьезность основывается на том, насколько уверен Центр безопасности в исследовании или аналитике, используемой для оповещения, а также на уровне достоверности злонамеренности события, приведшего к оповещению.

Кроме того, четко помечайте подписки (например, производственная, непроизводственная) и создайте систему именования, чтобы четко определить и классифицировать ресурсы Azure.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="103-test-security-response-procedures"></a>10.3. Проверка процедур реагирования на угрозы

**Руководство**. Выполните упражнения, чтобы периодически тестировать возможности ваших систем реагировать на угрозы. Выявите слабые точки и пробелы и пересмотрите план по мере необходимости.

* [См. публикацию NIST: руководство по тестированию, обучению и выполнению программ для ИТ-планов и возможностей](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Предоставление контактных сведений и настройка уведомлений по инцидентам безопасности

**Руководство**. Корпорация Майкрософт будет использовать информацию об инциденте безопасности для связи с вами, если центр Microsoft Security Response Center (MSRC) обнаружит, что к вашим пользовательским данным был получен незаконный или несанкционированный доступ. Проверьте инциденты после факта обращения, чтобы убедиться, что проблемы устранены.

* [Как задать контакт безопасности Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**. Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**. Экспортируйте оповещения и рекомендации Центра безопасности Azure с помощью функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать оповещения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Можно использовать соединитель данных Центра безопасности Azure для потоковой передачи оповещений Sentinel.

* [Как настроить непрерывный экспорт данных](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Как выполнить потоковую передачу оповещений в Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6. Автоматизация реагирования на оповещения системы безопасности

**Руководство**. Используйте функцию автоматизации рабочих процессов в Центре безопасности Azure для автоматического запуска реагирования с помощью Logic Apps в оповещениях и рекомендациях системы безопасности.

* [Как настроить автоматизацию рабочего процесса и Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в статье [Управление безопасностью: тесты на проникновение и попытки нарушения безопасности "красной командой"](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1. Регулярное тестируйте на проникновение ресурсов Azure и следите за исправлением всех критических точек безопасности в течение 60 дней

**Руководство**. * [Следуйте правилам взаимодействия Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Дополнительные сведения о стратегии корпорации Майкрософт и реализации "красной команды", а также о тестировании на основе уязвимости для облачной инфраструктуры, служб и приложений Майкрософт см. здесь](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Совмещаемая блокировка

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со статьей [Тесты производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview).
- Дополнительные сведения о [базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).
