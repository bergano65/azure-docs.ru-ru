---
title: Базовый унизел безопасности Azure для Cosmos DB
description: Базовый унизел безопасности Azure для Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd2706b6a6880d7c9454619e1315b2b5b5404561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244279"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Базовый унизел безопасности Azure для Cosmos DB

Базовый униза Azure Security для Cosmos DB содержит рекомендации, которые помогут вам улучшить безопасность развертывания.

Базовая версия этой службы взята из [версии 1.0 Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)которая содержит рекомендации о том, как можно обезопасить свои облачные решения в Azure с нашими рекомендациями по передовым практикам.

Для получения дополнительной информации смотрите [обзор базовых линий безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Защита ресурсов, использующих группы сетевой безопасности или брандмауэр Azure в виртуальной сети

**Руководство**: Используя частную ссылку Azure, вы можете подключиться к учетной записи Azure Cosmos через частную конечную точку. Трафик между виртуальной сетью и службой проходит через магистральную сеть Майкрософт, что позволяет избежать рисков общедоступного Интернета. Вы также можете уменьшить риск эксфильтрации данных, настроив строгий набор исходящих правил в группе сетевой безопасности (NSG) и связав эту NSG с вашей подсетью.

Вы также можете использовать конечные точки службы для защиты своей учетной записи Azure Cosmos. Включив конечную точку службы, можно настроить учетные записи Azure Cosmos, чтобы обеспечить доступ только из определенной подсети виртуальной сети Azure. После включения точки доступа службы Azure Cosmos DB можно ограничить доступ к учетной записи Azure Cosmos соединениями из подсети в виртуальной сети.

Вы также можете защитить данные, хранящиеся в учетной записи Azure Cosmos, с помощью брандмауэров IP. Для фильтрации входящего трафика Azure Cosmos DB поддерживает политики управления доступом на основе IP-адресов. Вы можете установить брандмауэр IP в учетной записи Azure Cosmos с помощью портала Azure, шаблонов управления ресурсами Azure или через Azure CLI или Azure PowerShell.

Обзор частной ссылки Azure:https://docs.microsoft.com/azure/private-link/private-link-overview

Как настроить частную конечную точку для Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Как создать группу сетевой безопасности с config безопасности:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Как настроить IP-брандмауэр в Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Мониторинг и регистрация конфигурации и трафика Vnets, Subnets и NICs

**Руководство**: Используйте Центр безопасности Azure и следуйте рекомендациям по защите сети, чтобы помочь защитить сетевые ресурсы, связанные с учетной записью Azure Cosmos.

Когда виртуальные машины развернуты в той же виртуальной сети, что и ваша учетная запись Azure Cosmos, можно использовать группу сетевой безопасности (NSG) для снижения риска эксфильтрации данных. Включите журналы потока NSG и отправьте журналы в учетную запись хранения Azure для аудита трафика. Вы также можете отправлять журналы потоков NSG в рабочее пространство Log Analytics и использовать аналитику трафика для получения информации о движении в облаке Azure. Некоторые преимущества Traffic Analytics — это возможность визуализации сетевой активности и выявления горячих точек, выявления угроз безопасности, понимания моделей движения и неправильной настройки сети.

Понимание сетевой безопасности, предоставляемой Центром безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Как включить журналы потока NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить и использовать аналитику трафика:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="13-protect-critical-web-applications"></a>1.3: Защита критически важных веб-приложений

**Руководство**: Используйте функцию Cross-Origin Resource Sharing (CORS), чтобы веб-приложение, работаваееруеео под одним доменом, было доступно для доступа к ресурсам в другом домене. Веб-браузеры реализуют ограничение безопасности, известное как политика одного источника, которая не позволяет веб-странице вызывать API-интерфейсы в другом домене. Тем не менее, CORS предоставляет безопасный способ разрешить домену происхождения вызывать AA в другом домене. При включении поддержки CORS для учетной записи Azure Cosmos только прошедшие проверку подлинности запросы оцениваются, чтобы определить, разрешены ли они в соответствии с правилами, которые вы указали.

Нанастройка кросс-Origin обмен ресурсами:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Запретить связь с известными вредоносными IP-адресами

**Руководство**: Используйте расширенную защиту угроз (ATP) для Azure Cosmos DB. ATP для Azure Cosmos DB предоставляет дополнительный уровень интеллекта безопасности, который обнаруживает необычные и потенциально опасные попытки получить доступ к учетным записям Azure Cosmos или использовать их. Этот уровень защиты позволяет устракиваться в угрозах и интегрировать их в центральные системы мониторинга безопасности.

Включите стандарт Защиты DDoS в виртуальных сетях, связанных с экземплярами Azure Cosmos DB, для защиты от DDoS-атак. Используйте Azure Security Center Integrated Threat Intelligence, чтобы отказать в сообщении с известными вредоносными или неиспользованными IP-адресами в Интернете.

Как настроитьAzure Космос DB Расширенная защита от угроз:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Как настроить DDoS-защиту:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Поймите Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Запись сетевых пакетов и журналов потока

**Руководство**: Включить журналы потока группы сетевой безопасности (NSG) и отправить журналы в учетную запись хранения для аудита трафика. В рабочее пространство analytics log можно отправлять журналы потоков НСГ и использовать аналитику трафика для получения информации о движении в облаке Azure. Некоторые преимущества Traffic Analytics — это возможность визуализации сетевой активности и выявления горячих точек, выявления угроз безопасности, понимания моделей движения и неправильной настройки сети.

Как включить журналы потока NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить и использовать аналитику трафика:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Развертывание сетевых систем обнаружения/предотвращения вторжений (IDS/IPS)

**Руководство**: Используйте расширенную защиту угроз (ATP) для Azure Cosmos DB. ATP для Azure Cosmos DB предоставляет дополнительный уровень интеллекта безопасности, который обнаруживает необычные и потенциально опасные попытки получить доступ к учетным записям Azure Cosmos или использовать их. Этот уровень защиты позволяет устракиваться в угрозах и интегрировать их в центральные системы мониторинга безопасности. 

Как настроить Космос DB Расширенная защита от угроз:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Управление трафиком для веб-приложений

**Руководство**: Не применимо; Рекомендация предназначена для веб-приложений, работающих на Azure App Service или вычислять ресурсы.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Минимизация сложности и административных накладных расходов на правила сетевой безопасности

**Руководство**: Для ресурсов, которым необходим доступ к учетной записи Azure Cosmos, используйте теги служб ы виртуальной сети для определения элементов управления доступом к сети в группах сетевой безопасности или брандмауэре Azure Firewall. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указывая имя тега службы (например, AzureCosmosDB) в соответствующем поле источника или назначения правила, можно разрешить или отклонить трафик для соответствующей службы. Корпорация Майкрософт управляет адресными префиксами, охватываемыми тегом службы, и автоматически обновляет тег службы при изменении адресов.

Для получения дополнительной информации об использовании сервисных тегов:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**: Определение и внедрение стандартных конфигураций безопасности для сетевых ресурсов с помощью политики Azure. Используйте псевдонимы Azure Policy в пространствах имен "Microsoft.DocumentDB" и "Microsoft.Network" для создания пользовательских политик для аудита или обеспечения безопасности конфигурации сети экземпляров Azure Cosmos DB. Можно также использовать встроенные определения политики для Azure Cosmos DB, такие как:

- Развертывание Расширенной защиты от угроз для учетных записей Cosmos DB

- Служба Cosmos DB должна использовать конечную точку службы виртуальной сети

Можно также использовать шаблоны Azure для упрощения крупномасштабных развертываний Azure путем упаковки ключевых артефактов среды, таких как шаблоны управления ресурсами Azure, управление доступом на основе ролей (RBAC) и политики в единое определение чертежа. Вы можете легко применить этот план к новым подпискам, средам и тонкому управлению настройками с помощью версий.

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как создать план Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="110-document-traffic-configuration-rules"></a>1.10: Правила конфигурации трафика документов

**Руководство**: Используйте теги для сетевых ресурсов, связанных с развертыванием Azure Cosmos DB, чтобы логически организовать их в таксономию.

Как создавать и использовать теги:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Используйте автоматизированные инструменты для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**: Используйте журнал активности Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений для сетевых ресурсов, связанных с экземплярами Azure Cosmos DB. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критически важных сетевых ресурсов. 

Как просмотреть и получить события журнала активности Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Как создать оповещения в Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Используйте утвержденные источники синхронизации времени

**Руководство**: Корпорация Майкрософт поддерживает источник времени, используемый для ресурсов Azure, таких как Azure Cosmos DB для меток времени в журналах.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="22-configure-central-security-log-management"></a>2.2: Настройка централизованного управления журналами безопасности

**Руководство**: Ingest журналы через Azure Monitor для агрегирования данных безопасности, генерируемых Azure Cosmos DB. В Azure Monitor используйте рабочее пространство Log Analytics (ы) для запроса и выполнения аналитики, а также для хранения данных для долгосрочного/архивного хранения. Кроме того, вы можете включить и бортовые данные в Azure Sentinel или сторонних инцидентов безопасности и управления событиями (SIEM). 

Как включить диагностические журналы для Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Как на борту Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Включить журнал аудита для ресурсов Azure

**Руководство**: Включите диагностические настройки для Azure Cosmos DB и отправьте журналы в рабочее пространство или учетную запись для хранения данных Log Analytics. Диагностические настройки в Azure Cosmos DB используются для сбора журналов ресурсов. Эти журналы фиксируются в запросе, и они также называются "журналы плоскости данных". Некоторые примеры операций плоскости данных включают удаление, вставку и чтение. Можно также включить диагностические настройки входа в журнал Azure Ит-Аналитики и отправить их в одно и то же рабочее пространство для анализа журналов.

Как включить диагностические настройки для Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Как включить диагностические настройки для журнала активности Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Сбор журналов безопасности из операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="25-configure-security-log-storage-retention"></a>2.5: Настройка хранения журналов безопасности

**Руководство**: В Azure Monitor установите период хранения журналов для рабочих областей log Analytics, связанных с экземплярами Azure Cosmos DB, в соответствии с правилами соответствия требованиям организации.

Как установить параметры удержания журнала:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="26-monitor-and-review-logs"></a>2.6: Мониторинг и обзор журналы

**Руководство**: Вы можете выполнять запросы в журнале Analytics рабочее пространство для поиска терминов, выявления тенденций, анализа шаблонов и предоставления многих других сведений на основе собранных журналов Azure Cosmos DB.

Как выполнять запросы для Azure Cosmos DB в рабочих областях аналитики журналов:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Включить оповещения об аномальной активности

**Руководство**: В Центре безопасности Azure позволяет расширенной защите угроз для Azure Cosmos DB отслеживать аномальные действия в журналах и событиях безопасности. Включите диагностические настройки в Azure Cosmos DB и отправьте журналы в рабочее пространство Log Analytics.

 

Вы также можете на борту рабочего пространства журнала Analytics в Azure Sentinel, поскольку оно обеспечивает решение для автоматического реагирования на систему безопасности (SOAR). Это позволяет создавать и использовать игровые книги (автоматизированные решения) для устранения проблем безопасности. Кроме того, можно создавать пользовательские оповещения о журналах в рабочем пространстве журнала Analytics с помощью Azure Monitor.

Список предупреждений о защите угроз для Azure Cosmos DB:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Как на борту Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Создавайте, просматривайте и управляйте оповещениями о журналах с помощью Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="28-centralize-anti-malware-logging"></a>2.8: Централизованное заготовку от вредоносных программ

**Руководство**: Не применимо; Azure Cosmos DB не обрабатывает и не производит журналы, связанные с вредоносными программами.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="29-enable-dns-query-logging"></a>2.9: Включить журнал запросов DNS

**Руководство**: Не применимо; Azure Cosmos DB не обрабатывает и не производит журналы, связанные с DNS.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="210-enable-command-line-audit-logging"></a>2.10: Включить журнал аудита командной строки

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Ведение перечня административных счетов

**Руководство**: Вы можете использовать панель управления идентификацией и доступом (IAM) на портале Azure для настройки элемента управления доступом на основе ролей (RBAC) и ведения инвентаризации ресурсов Azure Cosmos DB. Роли применяются к пользователям, группам, директорам служб и управляемым идентификаторам в Active Directory. Встроенные роли или пользовательские роли можно использовать для отдельных лиц и групп.

Azure Cosmos DB предоставляет встроенный RBAC для общих сценариев управления в Azure Cosmos DB. Человек, у которого есть профиль в Active Directory (AD), может назначить эти роли RBAC пользователям, группам, директорам служб или управляемым идентификаторам, чтобы предоставить или отказать в доступе к ресурсам и операциям на ресурсах Azure Cosmos DB.

Вы также можете использовать модуль Azure AD PowerShell для выполнения запросов на адок для обнаружения учетных записей, входят в состав административных групп. 

Кроме того, некоторые действия в Azure Cosmos DB можно контролировать с помощью Active Directory Azure и мастер-ключей для конкретных учетных записей.  Для управления доступом к ключам используйте настройки учетной записи 'disableKeyBasedMetadataWriteAccess'.

Понимание элемента управления доступом на основе ролей в Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Создавайте собственные пользовательские роли с помощью действий Azure Cosmos DB (пространство имен Microsoft.DocumentDB):https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Создайте новую роль в Active Directory Azure:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Как получить роль каталога в Active Directory Azure с PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Как получить роль участников каталога в Active Directory Azure с Помощью PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Ограничьте доступ пользователей только к операциям данных:https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Изменение паролей по умолчанию, где это применимо

**Руководство**: Концепция паролей по умолчанию или пустых не существует по отношению к Azure AD или Azure Cosmos DB. Вместо этого Azure Cosmos DB использует два типа ключей для проверки подлинности пользователей и предоставления доступа к своим данным и ресурсам; мастер-ключи и токены ресурсов. Ключи могут быть восстановлены в любое время.

Понимание безопасного доступа к данным в Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Как регенерировать Azure Cosmos DB Keys:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Как программно получить доступ к клавишам с помощью Active Directory Azure:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Общие сведения

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Использование выделенных административных учетных записей

**Руководство**: Не применимо; Azure Cosmos DB не поддерживает учетные записи администраторов.  Весь доступ интегрирован с Azure Active Directory и управлением ролевой базой доступа Azure (RBAC).



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Используйте одиночный вписаться (SSO) с активным каталогом Azure

**Руководство**: Azure Cosmos DB использует два типа ключей для авторизации пользователей и не поддерживает Single Sign-On (SSO) на уровне плоскости данных. Доступ к плоскости управления для Cosmos DB доступен через REST API и поддерживает SSO. Чтобы проверить подлинность, установите заголовок авторизации для запросов в маркер JSON Web Token, полученный в каталоге Active Azure.

Поймите базу данных Azure для Cosmos DB REST API:https://docs.microsoft.com/rest/api/cosmos-db/

Поймите SSO с помощью active-каталога Azure:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Используйте многофакторную аутентификацию для всего доступа на основе активного каталога Azure

**Руководство**: Включите многофакторную аутентификацию Azure Active Directory Multi-Factor Authentication и следуйте рекомендациям Центра идентификации безопасности Azure и управлению доступом.

Как включить МИД в Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Как контролировать личность и доступ в Центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Используйте выделенные машины (Привилегированные рабочие станции доступа) для всех административных задач

**Руководство**: Используйте рабочие станции привилегированного доступа (PAW) с мультифакторной аутентификацией, настроенной для входа в ресурсы Azure и настройки.

Узнайте о рабочих станциях привилегированном доступе:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Как включить МИД в Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Войти и предупредить о подозрительной активности с административных счетов

**Руководство**: Используйте расширенную защиту угроз (ATP) для Azure Cosmos DB. ATP для Azure Cosmos DB предоставляет дополнительный уровень интеллекта безопасности, который обнаруживает необычные и потенциально опасные попытки получить доступ к учетным записям Azure Cosmos или использовать их. Этот уровень защиты позволяет устракиваться в угрозах и интегрировать их в центральные системы мониторинга безопасности. 

Кроме того, можно использовать Azure Active Directory (AD) Privileged Identity Management (PIM) для генерации журналов и оповещений при возникновении подозрительной или небезопасной активности в среде.

Используйте обнаружения рисков Azure AD для просмотра предупреждений и отчетов о рискованном поведении пользователей.

Как развернуть Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Поймите обнаружения рисков Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Управление ресурсами Azure только из утвержденных мест

**Руководство**: Наверсти состояние местоположения политики условного доступа и управление указанными местоположениями. С названными местоположениями можно создавать логические группирования диапазонов IP-адресов или стран и регионов. Можно ограничить доступ к конфиденциальным ресурсам, таким как экземпляры Azure Cosmos DB, к настроенным местоположениям.

Как настроить именованные места в Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="39-use-azure-active-directory"></a>3.9: Используйте активный каталог Azure

**Руководство**: Использование активного каталога Azure (AD) в качестве центральной системы аутентификации и авторизации. Azure AD защищает данные, используя надежное шифрование данных в состоянии покоя и в пути. Azure AD также солит, хэширует и надежно хранит учетные данные пользователей.

Как создать и настроить экземпляр Active Каталог Azure:https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Как настроить и управлять аутентификацией активных каталогов Azure с помощью Azure S'L:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Регулярно проверяйте и согласовывая доступ пользователей

**Руководство**: Активный каталог Azure предоставляет журналы, чтобы помочь обнаружить устаревшие учетные записи. Кроме того, можно использовать Обзоры доступа к идентификационным данным Azure для эффективного управления членством в группах, доступом к корпоративным приложениям и назначениям ролей. Доступ пользователя может быть рассмотрен на регулярной основе, чтобы убедиться, что только право пользователей имеют постоянный доступ.

Как использовать обзоры доступа к идентификационным данным Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Попытка monitor получить доступ к деактивированным учетным записям

**Руководство**: Можно создать диагностические настройки для учетных записей пользователей Azure Active Directory, отправляя журналы аудита и журналы входа в рабочее пространство журнала Analytics, где можно настроить нужные оповещения.

Как интегрировать журналы активности Azure в Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Оповещение об отклонении поведения входа в учетную запись

**Руководство**: Используйте расширенную защиту угроз (ATP) для Azure Cosmos DB. ATP для Azure Cosmos DB предоставляет дополнительный уровень интеллекта безопасности, который обнаруживает необычные и потенциально опасные попытки получить доступ к учетным записям Azure Cosmos или использовать их. Этот уровень защиты позволяет устракиваться в угрозах и интегрировать их в центральные системы мониторинга безопасности. 

Вы также можете использовать функцию Azure AD Identity Protection и обнаружения рисков для настройки автоматизированных ответов на обнаруженные подозрительные действия, связанные с идентификаторами пользователей. Кроме того, вы можете глотать журналы в Azure Sentinel для дальнейшего исследования.

Как посмотреть рискованные вхливые системы Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Как настроить и включить политики защиты от идентификации:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Как на борту Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Предоставить корпорации Майкрософт доступ к соответствующим данным клиентов во время сценариев поддержки

**Руководство**: В настоящее время недоступно; Локбокс клиента еще не поддерживается для базы данных Azure для Cosmos DB.

Список поддерживаемых служб блокировки клиентов:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Не применимо

## <a name="data-protection"></a>Защита данных

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Ведение перечня конфиденциальной информации

**Руководство**: Используйте теги для отслеживания экземпляров Azure Cosmos DB, которые хранят или обрабатывают конфиденциальную информацию.

Как создавать и использовать теги:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Изолировать системы хранения или обработки конфиденциальной информации

**Руководство**: Реализация отдельных подписок и/или групп управления для разработки, тестирования и производства. Экземпляры Azure Cosmos DB разделены виртуальной сетью/подсетью, помечены соответствующим образом и защищены в группе сетевой безопасности (NSG) или Azure Firewall. Случаи Azure Cosmos DB, храниме конфиденциальных данных, должны быть изолированы. Используя частную ссылку Azure, можно подключиться к учетной записи экземпляра Azure Cosmos DB через частную конечную точку. Частная конечная точка представляет собой набор частных IP-адресов в подсети в вашей виртуальной сети. Затем можно ограничить доступ к выбранным частным IP-адресам. 

Как создать дополнительные подписки Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Как создать группы управления:https://docs.microsoft.com/azure/governance/management-groups/create

Как создавать и использовать теги:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Как настроить частную конечную точку для Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Как создать группу сетевой безопасности с config безопасности:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Руководство**: Вы можете развернуть Расширенную защиту угроз для Azure Cosmos DB, которая будет обнаруживать аномальные действия, указывающие на необычные и потенциально опасные попытки получить доступ к базам данных или эксплуатировать их. В настоящее время он может вызвать следующие предупреждения:

- Доступ из необычных мест

- Необычная извлечение данных

Кроме того, при использовании виртуальных машин для доступа к экземплярам Azure Cosmos DB используйте Private Link, Firewall, группы сетевой безопасности и теги служб, чтобы уменьшить вероятность эксфильтрации данных. Корпорация Майкрософт управляет базовой инфраструктурой для Azure Cosmos DB и внедрила строгие средства контроля для предотвращения потери или выдержки данных клиентов.

Как настроить Космос DB Расширенная защита от угроз:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Понимание защиты данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Общие сведения

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Шифрование всей конфиденциальной информации в пути

**Руководство**: Все подключения к Azure Cosmos DB поддерживают HTTPS. Azure Cosmos DB также поддерживает TLS1.2. Можно обеспечить минимальный сервер версии TLS. Для этого, пожалуйста, свяжитесь с [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com).

Обзор безопасности Космос DB:https://docs.microsoft.com/azure/cosmos-db/database-security

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Используйте инструмент активного обнаружения для идентификации конфиденциальных данных

**Руководство**: Автоматическая идентификация данных, классификация и предотвращение потерь пока не доступны для Azure Cosmos DB. Тем не менее, для классификации и анализа данных можно использовать интеграцию Azure Cognitive Search. Вы также можете реализовать стороннее решение, если это необходимо для целей соответствия.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт рассматривает все содержимое клиента как конфиденциальный и делает все усилия для защиты от потери данных клиентов и их воздействия. Для обеспечения безопасности данных клиентов в Azure корпорация Майкрософт внедрила и поддерживает набор надежных средств управления и возможностей защиты данных.

Данные индекса Azure Cosmos DB https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&ampс помощью Azure Cognitive Search: ;bc/azure/cosmos-db/breadcrumb/toc.json

Понимание защиты данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Общие сведения

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Используйте Azure RBAC для управления доступом к ресурсам

**Руководство**: Azure Cosmos DB обеспечивает встроенный элемент управления доступом на основе ролей (RBAC) для общих сценариев управления в Azure Cosmos DB. Человек, у которого есть профиль в Active Directory Azure, может назначить эти роли RBAC пользователям, группам, руководителям служб или управляемым идентификаторам, чтобы предоставить или лишить доступа к ресурсам и операциям на ресурсах Azure Cosmos DB. Назначения ролей используются только для доступа к управлению самолетом, который включает доступ к учетным записям Azure Cosmos, базам данных, контейнерам и предложениям (пропускной способ).

Как реализовать RBAC в Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Используйте предотвращение потери данных на основе хоста для обеспечения контроля доступа

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.

Корпорация Майкрософт управляет базовой инфраструктурой для Cosmos DB и внедрила строгие средства контроля для предотвращения потери или выдержки данных клиентов.

Понимание защиты данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Шифрование конфиденциальной информации в состоянии покоя

**Руководство**: Все пользовательские данные, хранящиеся в Cosmos DB, шифруются в состоянии по умолчанию. Нет элементов управления, чтобы выключить его. Azure Cosmos DB использует шифрование AES-256 во всех регионах, где работает учетная запись.

По умолчанию корпорация Майкрософт управляет ключами, которые используются для шифрования данных в вашей учетной записи Azure Cosmos. Вы можете по желанию добавить второй уровень шифрования с вашими собственными ключами.

Понимание шифрования в состоянии покоя с Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Понимание управления ключами для шифрования в состоянии покоя с Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Как настроить управляемые клиентом ключи для учетной записи Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Общие сведения

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Войти и предупредить об изменениях в критических ресурсах Azure

**Руководство**: Используйте Azure Monitor с журналом активности Azure для создания оповещений о случаях, когда происходят изменения в производственных экземплярах DB Azure Cosmos.

Как создать оповещения для событий журнала активности Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Как создать оповещения для событий журнала активности Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="vulnerability-management"></a>Управление уязвимостями

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Запуск автоматизированных инструментов сканирования уязвимостей

**Руководство**: Следуйте рекомендациям Центра безопасности Azure для экземпляров Azure Cosmos DB. 

Корпорация Майкрософт выполняет системное исправление и управление уязвимостями на базовых узлах, поддерживающих экземпляры Azure Cosmos DB. Для обеспечения безопасности данных клиентов в Azure корпорация Майкрософт внедрила и поддерживает набор надежных средств управления и возможностей защиты данных.

Поддерживаемые функции, доступные в Центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Общие сведения

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Развертывание автоматизированного решения для управления патчами сторонних программ

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Сравните сканирование уязвимости спина к спине

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Используйте процесс оценки рисков, чтобы определить приоритеты в устранении обнаруженных уязвимостей

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Используйте обнаружение активов Azure

**Руководство**: Используйте портал Azure или график ресурсов Azure, чтобы обнаружить все ресурсы (не ограничиваемые DB Azure Cosmos, но также включающих такие ресурсы, как вычисления, другие хранилища, сети, порты и протоколы и т.д.) в подписке (ы).  Убедитесь, что у вас есть соответствующие разрешения в арендаторе и вы можете перечислить все подписки Azure, а также ресурсы в рамках подписки.

Хотя классические ресурсы Azure могут быть обнаружены с помощью диаграммы ресурсов, настоятельно рекомендуется создавать и использовать ресурсы Azure Resource Manager в будущем.

Как создавать запросы с помощью Графика Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Как просмотреть подписку на Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Понимание управления доступом на основе ролей Azure:https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="62-maintain-asset-metadata"></a>6.2: Поддержание метаданных активов

**Руководство**: Приложить теги к экземплярам Azure Cosmos DB и связанным с ними ресурсам с метаданными, чтобы логически организовать их в таксономию.

Как создавать и использовать теги:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Какие ресурсы Azure Cosmos DB поддерживают теги:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Удалить несанкционированные ресурсы Azure

**Руководство**: Используйте пометки, группы управления и отдельные подписки, где это уместно, для организации и отслеживания ресурсов, в том числе ресурсов Azure Cosmos DB. Регулярно согласовывать инвентаризацию и своевременно исключать несанкционированные ресурсы из подписки.

Как создать дополнительные подписки Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Как создать группы управления:https://docs.microsoft.com/azure/governance/management-groups/create

Как создавать и использовать теги:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Ведение перечня утвержденных ресурсов Azure и названий программного обеспечения

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов и Azure в целом.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Мониторинг неодобренных ресурсов Azure

**Руководство**: Используйте политику Azure для ограничения типа ресурсов, которые могут быть созданы в подписке клиента (ы) с использованием следующих встроенных определений политики:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Кроме того, используйте график ресурсов Azure для запроса/обнаружения ресурсов в подписке(ы).

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как создавать запросы с помощью Графика Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Мониторинг неодобренных программных приложений в вычислительных ресурсах

**Руководство**: Не применимо; этот базовый упор предназначен для вычислений ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Удалить неодобренные ресурсы и программные приложения Azure

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов и Azure в целом.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="68-use-only-approved-applications"></a>6.8: Используйте только одобренные приложения

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="69-use-only-approved-azure-services"></a>6.9: Используйте только утвержденные службы Azure

**Руководство**: Используйте политику Azure для ограничения типа ресурсов, которые могут быть созданы в подписке клиента (ы) с использованием следующих встроенных определений политики:

- Недопустимые типы ресурсов 

- Допустимые типы ресурсов

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как отказать в определенном типе ресурсов с помощью политики Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="610-implement-approved-application-list"></a>6.10: Реализация утвержденного списка заявок

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Ограничьте возможности пользователей взаимодействовать с менеджером AzureResources с помощью скриптов

**Руководство**: Используйте условный доступ Azure, чтобы ограничить возможности пользователей взаимодействовать с менеджером ресурсов Azure, нанастройки "Блок-доступ" для приложения "Управление Microsoft Azure". Это может предотвратить создание и изменение ресурсов в условиях повышенной безопасности.

Как настроить условный доступ, чтобы заблокировать доступ к менеджеру ресурсов Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ограничьте возможности пользователей по выполнению скриптов в вычислительных ресурсах

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Физически или логически сегрегировать приложения высокого риска

**Руководство**: Не применимо; это руководство предназначено для веб-приложений, работающих на Azure App Service или для вычисления ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

## <a name="secure-configuration"></a>Настройка безопасности

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Создание безопасных конфигураций для всех ресурсов Azure

**Руководство**: Определите и внедрить стандартные конфигурации безопасности для экземпляров Cosmos DB с помощью политики Azure. Используйте псевдонимы Azure Policy в пространстве имен "Microsoft.DocumentDB" для создания пользовательских политик для аудита или обеспечения соблюдения конфигурации экземпляров Cosmos DB. Можно также использовать встроенные определения политики для Azure Cosmos DB, такие как:

- Развертывание Расширенной защиты от угроз для учетных записей Cosmos DB

- Служба Cosmos DB должна использовать конечную точку службы виртуальной сети

Как просмотреть доступные Aliases Azure Policy:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Создание безопасных конфигураций операционных систем

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Поддержание безопасных конфигураций ресурсов Azure

**Руководство**: Используйте политику Azure «отрицать» и «развертывать, если не существует» для обеспечения безопасных настроек в ресурсах Azure.

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Понять эффекты политики Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Поддержание безопасных конфигураций операционных систем

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Безопасное хранение конфигурации ресурсов Azure

**Руководство:** При использовании пользовательских определений политики Azure для вашего Cosmos DB или связанных с ними ресурсов, используйте Azure Repos для безопасного хранения и управления кодом.

Документация Репос https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops Azure:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Безопасно хранить пользовательские изображения операционной системы

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Инструменты управления конфигурацией конфигурации системы развертывание

**Руководство**: Используйте псевдонимы Azure Policy в пространстве имен "Microsoft.DocumentDB" для создания пользовательских политик для оповещения, аудита и обеспечения безопасности конфигураций систем. Кроме того, разработайте процесс и конвейер для управления исключениями политики.

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Развертывать инструменты управления конфигурацией системы для операционных систем

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Внедрение автоматизированного мониторинга конфигурации для служб Azure

**Руководство**: Используйте псевдонимы Azure Policy в пространстве имен "Microsoft.DocumentDB" для создания пользовательских политик для оповещения, аудита и обеспечения безопасности конфигураций систем. Используйте политику Azure «аудит», «отрицать» и «развернуть, если не существует», чтобы автоматически принудить конфигурации для экземпляров DB Azure Cosmos и связанных с ними ресурсов. 

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Внедрение автоматизированного мониторинга конфигурации для операционных систем

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="711-manage-azure-secrets-securely"></a>7.11: Надежное управление секретами Azure

**Руководство:** Для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, используемых для доступа к экземплярам Azure Cosmos DB, используйте идентификатор управляемой службы совместно с Azure Key Vault для упрощения и защиты секретного управления Azure Cosmos DB. Убедитесь, что Key Vault Soft Delete включен.

Как интегрироваться с управляемыми идентификаторами Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Как создать Убежище ключей:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Как обеспечить аутентификацию Key Vault с управляемой идентификацией:https://docs.microsoft.com/azure/key-vault/managed-identity

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Управление идентификаторами безопасно и автоматически

**Руководство:** Для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, используемых для доступа к экземплярам Azure Cosmos DB, используйте идентификатор управляемой службы совместно с Azure Key Vault для упрощения и защиты секретного управления Azure Cosmos DB.

Используйте управляемые идентификаторы для предоставления службам Azure автоматического управляемого идентификатора в Active Directory (AD). Управляемые идентификаторы позволяют аутентифицировать проверку любой службы, которая поддерживает аутентификацию Azure AD, включая Key Vault, без каких-либо учетных данных в коде.

Как настроить управляемые идентификаторы:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Как интегрироваться с управляемыми идентификаторами Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Устранить непреднамеренное воздействие учетных данных

**Руководство**: Реализация сканера учетных данных для идентификации учетных данных в коде. Сканер учетных данных также будет способствовать перемещению обнаруженных учетных данных в более безопасные места, такие как Убежище ключей Azure.

Как настроить сканер учетных данных:https://secdevtools.azurewebsites.net/helpcredscan.html

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="malware-defense"></a>Защита от вредоносных программ

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Используйте централизованно управляемое программное обеспечение для борьбы с вредоносным ПО

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов. Microsoft Antimalware включена на базовом узеле, который поддерживает службы Azure (например, служба приложений Azure), однако она не работает на содержимом клиента.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Предварительное сканирование файлов, которые будут загружены на некомпьютерные ресурсы Azure

**Руководство**: Microsoft Antimalware включенна на базовом узеле, который поддерживает службы Azure (например, служба приложений Azure), однако она не выполняется на содержимом клиента.

Вы несете ответственность за предварительное сканирование любых файлов, загруженных на некомпьютерные ресурсы Azure, включая Azure Cosmos DB. Корпорация Майкрософт не может получить доступ к данным клиентов и, следовательно, не может проводить анти-вредоносные сканирование содержимого клиента от вашего имени.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Обеспечить обновление антивредоносного программного обеспечения и подписей

**Руководство**: Не применимо; бенчмарк предназначен для вычислительных ресурсов. Microsoft Antimalware включена на базовом узеле, который поддерживает службы Azure, однако она не выполняется на содержимом клиента.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

## <a name="data-recovery"></a>Восстановление данных

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Обеспечить регулярные автоматизированные резервные истектоли

**Руководство**: Azure Cosmos DB делает снимки данных каждые четыре часа. Для обеспечения устойчивости в случае региональной аварии все резервные копии хранятся отдельно в службе хранилища, а также глобально реплицируются. В любое время сохраняются только два последних моментальных снимка. Однако при удалении контейнера или базы данных Azure Cosmos DB хранит существующие моментальные снимки в течение 30 дней. Свяжитесь с службой поддержки Azure для восстановления из резервного копирования.

Понимание Azure Космос DB Автоматизированное резервное копирование:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Выполните полное резервное копирование системы и резервное копирование любых ключей, управляемых клиентом

**Руководство**: Azure Cosmos DB автоматически принимает резервные данные через регулярные промежутки времени. Если база данных или контейнер удалены, вы можете подать билет поддержки или вызвать поддержку Azure для восстановления данных из автоматических онлайн-резервных upups. Поддержка Azure доступна только для отдельных планов, таких как Standard, Developer и планирует их более высокое, чем они. Чтобы восстановить определенный моментальный снимок архива, службе Azure Cosmos DB требуется, чтобы данные были доступны в течение цикла архивации для данного снимка. 

При использовании Key Vault для хранения учетных данных для экземпляров Cosmos DB, обеспечите регулярное автоматическое резервное копирование ключей.

Понять Azure Космос DB Автоматизированное резервное копирование:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Как восстановить данные в Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Как создать резервную резервную резервную часть ключей Убежища:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Общие сведения

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Проверка всех резервных ups, включая управляемые ключей клиента

**Руководство**: Если база данных или контейнер удалены, вы можете подать билет поддержки или вызвать поддержку Azure для восстановления данных из автоматических онлайн-резервных копирований. Поддержка Azure доступна только для отдельных планов, таких как Standard, Developer и планирует их более высокое, чем они. Чтобы восстановить определенный моментальный снимок архива, службе Azure Cosmos DB требуется, чтобы данные были доступны в течение цикла архивации для данного снимка.

Тестируйте восстановление секретов, хранящихся в Хранилище ключей Azure с помощью PowerShell. Cmdlet Restore-AzureKeyVaultKey создает ключ в указанном хранилище ключей. Этот ключ является копией резервного ключа в файле ввода и имеет то же имя, что и исходный ключ.

Понять Azure Космос DB Автоматизированное резервное копирование:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Как восстановить данные в Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Как восстановить секреты убежища ключей Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Общие сведения

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Обеспечить защиту резервных и управляемых ключей клиента

**Руководство**: Поскольку все пользовательские данные, хранящиеся в Cosmos DB, шифруются в состоянии покоя и в транспорте, вам не нужно предпринимать никаких действий. Другими словами, шифрование неактивных данных включено по умолчанию. Элементов управления для его включения и отключения нет. Azure Cosmos DB использует шифрование AES-256 во всех регионах, где работает учетная запись.

Включите Soft-Delete в Key Vault для защиты ключей от случайного или вредоносного удаления.

Понимание шифрования данных в Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Как включить Soft-Delete в Key Vault:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Общие сведения

## <a name="incident-response"></a>реагирование на инциденты.

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)*

### <a name="101-create-an-incident-response-guide"></a>10.1: Создать руководство по реагированию на инциденты

**Руководство**: Создайте руководство по реагированию на инциденты для вашей организации. Убедитесь, что существуют письменные планы реагирования на инциденты, определяющие все роли персонала, а также этапы обработки/управления инцидентами от обнаружения до обзора после инцидента.

Вы также можете использовать NIST в компьютерной безопасности инцидентов Обработка Руководство для оказания помощи в создании собственного плана реагирования на инциденты:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Как настроить автоматизацию рабочих процессов в Центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Руководство по созданию собственного процесса реагирования на инциденты безопасности:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Анатомия инцидента в Центре реагирования на инцидент:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Создание процедуры оценки и приоритетов инцидентов

**Руководство**: Центр безопасности присваивает серьезность каждому оповещению, чтобы помочь вам определить приоритеты, какие предупреждения должны быть исследованы в первую очередь. Тяжесть основана на том, насколько уверен Центр безопасности в поиске или аналитики, используемой для выдачи оповещения, а также на уровне уверенности в том, что за действием, присутствовавшем к оповещению, был злой умысел.

Кроме того, четко пометить подписки (для бывших. производства, не-prod) и создать систему именования для четкой идентификации и классификации ресурсов Azure.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="103-test-security-response-procedures"></a>10.3: Процедуры реагирования на тестовую безопасность

**Руководство**: Проведение упражнений для проверки возможностей реагирования на инциденты систем на регулярной каденции. Выявление слабых мест и пробелов и пересмотр плана по мере необходимости.

Обратитесь к публикации NIST: Руководство по тестированию, обучению и осуществлению программ для ИТ-планов и возможностей:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Предоставьте контактные данные об инцидентах безопасности и назначьте уведомления о поместью для инцидентов безопасности

**Руководство**: Контактная информация об инцидентах безопасности будет использоваться корпорацией Майкрософт для связи с вами, если Центр реагирования на безопасность Майкрософт (MSRC) обнаружит, что данные клиента были доступны незаконной или несанкционированной стороной.  Просмотрите инциденты после того, чтобы убедиться, что проблемы будут решены.

Как установить контакт с безопасностью Центра безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Включите оповещения о безопасности в систему реагирования на инциденты

**Руководство**: Экспорт оповещений и рекомендаций Центра безопасности Azure с использованием функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать оповещения и рекомендации либо вручную, либо в постоянном, непрерывном режиме. Для потоковой передачи оповещений Sentinel можно использовать разъем данных Центра безопасности Azure.

Как настроить непрерывный экспорт:https://docs.microsoft.com/azure/security-center/continuous-export

Как передавать оповещения в Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Автоматизация реагирования на оповещения о безопасности

**Руководство**: Используйте функцию автоматизации рабочего процесса в Центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" на оповещения и рекомендации по безопасности.

Как настроить автоматизацию рабочего процесса и логические приложения:https://docs.microsoft.com/azure/security-center/workflow-automation

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Проведение регулярного тестирования на проникновение ресурсов Azure и обеспечение исправления всех критических данных по безопасности в течение 60 дней

**Руководство**: Следуйте правилам взаимодействия корпорации Майкрософт, чтобы убедиться, что ваши тесты на проникновение не нарушают политики Майкрософт:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Вы можете найти более подробную информацию о стратегии Корпорации Майкрософт и выполнении Red Teaming и тестировании на проникновение в реальном времени в майкации в отношении облачной инфраструктуры, служб и приложений, управляемой корпорацией Майкрософт, здесь:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Общие сведения

## <a name="next-steps"></a>Дальнейшие действия

- Посмотреть [тест безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Узнайте больше об [базовых линиях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
