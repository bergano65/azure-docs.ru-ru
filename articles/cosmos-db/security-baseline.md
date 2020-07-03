---
title: Базовый план безопасности Azure для Cosmos DB
description: Базовый план безопасности Azure для Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e5c57ba56ce236bcf07478e1784eec6d4c632d1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195253"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Базовый план безопасности Azure для Cosmos DB

Базовый план безопасности Azure для Cosmos DB содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовая информация для этой службы взята из [теста безопасности Azure версии 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), который содержит рекомендации по защите облачных решений в Azure с помощью наших рекомендаций.

Дополнительные сведения см. в статье [Общие сведения о базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в разделе Security [Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Защитите ресурсы с помощью групп безопасности сети или брандмауэра Azure в виртуальной сети.

**Руководство**. с помощью частной ссылки Azure вы можете подключиться к учетной записи Azure Cosmos через закрытую конечную точку. Трафик между виртуальной сетью и службой проходит через магистральную сеть Майкрософт, что позволяет избежать рисков общедоступного Интернета. Кроме того, можно уменьшить риск утечка данных, настроив для группы безопасности сети (NSG) четкий набор правил для исходящего трафика и связав его с подсетью NSG.

Вы также можете использовать конечные точки службы для защиты учетной записи Azure Cosmos. Включив конечную точку службы, вы можете настроить учетные записи Cosmos Azure, чтобы разрешить доступ только из определенной подсети виртуальной сети Azure. После включения конечной точки службы Azure Cosmos DB можно ограничить доступ к учетной записи Azure Cosmos с помощью подключений из подсети в виртуальной сети.

Вы также можете защитить данные, хранящиеся в учетной записи Azure Cosmos, с помощью IP-брандмауэров. Для фильтрации входящего трафика Azure Cosmos DB поддерживает политики управления доступом на основе IP-адресов. Вы можете задать брандмауэр IP-адресов в учетной записи Azure Cosmos с помощью портал Azure, Azure Resource Manager шаблонов или с помощью Azure CLI или Azure PowerShell.

Общие сведения о частной ссылке Azure:https://docs.microsoft.com/azure/private-link/private-link-overview

Как настроить закрытую конечную точку для Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Создание группы безопасности сети с конфигурацией безопасности:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Настройка брандмауэра IP в Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Используйте центр безопасности Azure и следуйте рекомендациям по защите сети для защиты сетевых ресурсов, связанных с вашей учетной записью Azure Cosmos.

При развертывании виртуальных машин в той же виртуальной сети, что и учетная запись Azure Cosmos, можно использовать группу безопасности сети (NSG), чтобы снизить риск утечка данных. Включите журналы потоков NSG и отправьте журналы в учетную запись хранения Azure для аудита трафика. Вы также можете отправить журналы потоков NSG в рабочую область Log Analytics и использовать Аналитика трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитика трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять несетевые настройки.

Общие сведения о безопасности сети, предоставляемой центром безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Как включить журналы потоков NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить и использовать Аналитика трафика:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="13-protect-critical-web-applications"></a>1,3: защита критически важных веб-приложений

**Руководство**. Использование функции общего доступа к ресурсам в разных источниках (CORS) для обеспечения доступа веб-приложения, работающего в одном домене, к ресурсам в другом домене. Веб-браузеры реализуют ограничение безопасности, известное как политика одного источника, которая не позволяет веб-странице вызывать API-интерфейсы в другом домене. Однако CORS обеспечивает безопасный способ, позволяющий исходному домену вызывать интерфейсы API в другом домене. При включении поддержки CORS для учетной записи Azure Cosmos только прошедшие проверку подлинности запросы оцениваются, чтобы определить, разрешены ли они в соответствии с правилами, которые вы указали.

Настройка общего доступа к ресурсам между источниками:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: запретите обмен данными с известными вредоносными IP-адресами

**Руководство**. Использование Advanced Threat protection (ATP) для Azure Cosmos DB. ATP для Azure Cosmos DB предоставляет дополнительный уровень логики безопасности, который обнаруживает необычные и потенциально опасные попытки доступа или использования учетных записей Azure Cosmos. Этот уровень защиты позволяет решать угрозы и интегрировать их в централизованные системы мониторинга безопасности.

Включите стандарт защиты от атак DDoS в виртуальных сетях, связанных с экземплярами Azure Cosmos DB, чтобы защититься от атак от атак DDoS. Используйте интегрированную аналитику угроз центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми IP-адресами Интернета.

Как Конфигуреазуре Cosmos DB Advanced Threat protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Настройка защиты от атак DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Общие сведения о центре безопасности Azure, интегрированной с системой анализа угроз.https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: запись сетевых пакетов и журналов потоков

**Руководство**. Включение журналов потоков для группы безопасности сети (NSG) и отправка журналов в учетную запись хранения для аудита трафика. Журналы потоков NSG можно отправить в рабочую область Log Analytics и использовать Аналитика трафика для получения сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитика трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять несетевые настройки.

Как включить журналы потоков NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить и использовать Аналитика трафика:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание систем обнаружения вторжений на основе сети и предотвращения вторжений (ИДЕНТИФИКАТОРы и IP-адреса)

**Руководство**. Использование Advanced Threat protection (ATP) для Azure Cosmos DB. ATP для Azure Cosmos DB предоставляет дополнительный уровень логики безопасности, который обнаруживает необычные и потенциально опасные попытки доступа или использования учетных записей Azure Cosmos. Этот уровень защиты позволяет решать угрозы и интегрировать их в централизованные системы мониторинга безопасности. 

Настройка расширенной защиты от угроз Cosmos DB.https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="17-manage-traffic-to-web-applications"></a>1,7. Управление трафиком к веб-приложениям

**Руководство**: неприменимо; рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: сократите сложность и издержки администрирования правил безопасности сети

**Руководство**. для ресурсов, которым требуется доступ к учетной записи Azure Cosmos, используйте теги службы виртуальной сети для определения элементов управления доступом к сети в сетевом Секуритигграупс или брандмауэре Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, Азурекосмосдб) в соответствующем поле источника или назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, включенными в тег службы, и автоматически обновляет тег службы при изменении адресов.

Дополнительные сведения об использовании тегов служб:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определение и реализация стандартных конфигураций безопасности для сетевых ресурсов с помощью политики Azure. Используйте псевдонимы политик Azure в пространствах имен Microsoft. DocumentDB и Microsoft. Network, чтобы создать настраиваемые политики для аудита или принудительного применения сетевой конфигурации экземпляров Azure Cosmos DB. Вы также можете использовать встроенные определения политик для Azure Cosmos DB, например:

- Развертывание Расширенной защиты от угроз для учетных записей Cosmos DB

- Служба Cosmos DB должна использовать конечную точку службы виртуальной сети.

Вы также можете использовать схемы Azure для упрощения крупномасштабных развертываний Azure с помощью ключевых артефактов среды пакетов, таких как шаблоны Azure Resource Manager, управление доступом на основе ролей (RBAC) и политики в одном определении схемы. Вы можете легко применить схему к новым подпискам, средам, а также точно настраивать управление и управление с помощью управления версиями.

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Создание Azure Blueprint.https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="110-document-traffic-configuration-rules"></a>1,10: правила конфигурации трафика документов

**Руководство**. Используйте теги для сетевых ресурсов, связанных с развертыванием Azure Cosmos DB, чтобы логически упорядочить их в таксономии.

Создание и использование тегов:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11. использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений сетевых ресурсов, связанных с экземплярами Azure Cosmos DB. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых ресурсов. 

Просмотр и получение событий журнала действий Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Создание оповещений в Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в разделе [Security Control: logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: используйте источники синхронизации утвержденного времени

**Руководство**. Корпорация Майкрософт поддерживает источник времени, используемый для ресурсов Azure, например Azure Cosmos DB для меток времени в журналах.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="22-configure-central-security-log-management"></a>2,2: Настройка централизованного управления журналом безопасности

**Руководство**. прием журналов с помощью Azure Monitor для агрегирования данных безопасности, создаваемых Azure Cosmos DB. В Azure Monitor использовать рабочие области Log Analytics для запроса и выполнения анализа, а также использовать учетные записи хранения для долгосрочного и архивного хранения. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним инцидентам безопасности и управлению событиями (SIEM). 

Как включить журналы диагностики для Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Как подключить метку Azure:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Включите ведение журнала аудита для ресурсов Azure.

**Руководство**. Включение параметров диагностики для Azure Cosmos DB и отправка журналов в рабочую область log Analytics или в учетную запись хранения. Параметры диагностики в Azure Cosmos DB используются для получения журналов ресурсов. Эти журналы фиксируются для каждого запроса, а также называются "журналами плоскости данных". Некоторые примеры операций с плоскостью данных включают удаление, вставку и чтение. Вы также можете включить параметры диагностики журнала действий Azure и отправить их в ту же рабочую область Log Analytics.

Включение параметров диагностики для Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Как включить параметры диагностики для журнала действий Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: получение журналов безопасности из операционных систем

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="25-configure-security-log-storage-retention"></a>2,5: Настройка хранения журнала безопасности

**Рекомендации**. в Azure Monitor задайте срок хранения журнала для рабочих областей log Analytics, связанных с экземплярами Azure Cosmos DB в соответствии с нормативными требованиями Организации.

Как задать параметры хранения журнала:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="26-monitor-and-review-logs"></a>2,6: мониторинг и просмотр журналов

**Руководство**. Вы можете выполнять запросы в log Analytics рабочей области для поиска терминов, выявления тенденций, анализа закономерностей и предоставления множества других ценных сведений, основанных на собираемых журналах Azure Cosmos DB.

Выполнение запросов для Azure Cosmos DB в Log Analytics рабочих областях:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Включение оповещений для аномальных действий

**Руководство**. в центре безопасности Azure включите Advanced Threat Protection для Azure Cosmos DB, чтобы отслеживать аномальные действия в журналах и событиях безопасности. Включите параметры диагностики в Azure Cosmos DB и отправьте журналы в рабочую область Log Analytics.

 

Вы также можете подключить рабочую область Log Analytics к Azure Sentinel, так как она предоставляет решение для автоматического реагирования на согласованность безопасности (ВЗЛЕТЕЛ). Это позволяет создавать и использовать модули PlayBook (автоматизированные решения) для устранения проблем безопасности. Кроме того, можно создавать пользовательские оповещения журнала в рабочей области Log Analytics с помощью Azure Monitor.

Список оповещений защиты от угроз для Azure Cosmos DB:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Как подключить метку Azure:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Создание, просмотр и Управление оповещениями журнала с помощью Azure Monitor.https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="28-centralize-anti-malware-logging"></a>2,8: централизованное ведение журнала защиты от вредоносных программ

**Руководство**: неприменимо; Azure Cosmos DB не обрабатывает и не создает журналы, связанные с защитой от вредоносных программ.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="29-enable-dns-query-logging"></a>2,9: Включение ведения журнала запросов DNS

**Руководство**: неприменимо; Azure Cosmos DB не обрабатывает и не создает журналы, связанные с DNS.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="210-enable-command-line-audit-logging"></a>2,10: Включение ведения журнала аудита в командной строке

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Дополнительные сведения см. в разделе [Управление безопасностью: идентификация и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: ведение инвентаризации учетных записей администраторов

**Руководство**. Панель управления доступом и удостоверениями (IAM) в портал Azure можно использовать для настройки управления доступом на основе РОЛЕЙ (RBAC) и ведения инвентаризации в ресурсах Azure Cosmos DB. Роли применяются к пользователям, группам, субъектам-службам и управляемым удостоверениям в Active Directory. Для частных пользователей и групп можно использовать встроенные роли или пользовательские роли.

Azure Cosmos DB предоставляет встроенный RBAC для распространенных сценариев управления в Azure Cosmos DB. Пользователь, имеющий профиль в Azure Active Directory (AD), может назначить эти роли RBAC пользователям, группам, субъектам-службам или управляемым удостоверениям, чтобы предоставить или запретить доступ к ресурсам и операциям в ресурсах Azure Cosmos DB.

Вы также можете использовать модуль Azure AD PowerShell для выполнения нерегламентированных запросов для обнаружения учетных записей, входящих в группы администраторов. 

Кроме того, некоторые действия в Azure Cosmos DB можно контролировать с помощью Azure Active Directory и главных ключей учетной записи.  Используйте параметр учетной записи "Дисаблекэйбаседметадатавритеакцесс" для управления доступом к ключам.

Общие сведения об управлении доступом на основе ролей в Azure Cosmos DB.https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Создавайте собственные пользовательские роли с помощью действий Azure Cosmos DB (пространство имен Microsoft. DocumentDB):https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Создайте новую роль в Azure Active Directory:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Как получить роль каталога в Azure Active Directory с помощью PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Как получить членов роли каталога в Azure Active Directory с помощью PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Ограничьте доступ пользователей только к операциям с данными:https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Измените пароли по умолчанию, если это применимо

**Руководство**. понятие по умолчанию или пустые пароли не существуют по отношению к Azure AD или Azure Cosmos DB. Вместо этого Azure Cosmos DB использует два типа ключей для проверки подлинности пользователей и предоставления доступа к своим данным и ресурсам. главные ключи и маркеры ресурсов. Ключи можно создать повторно в любое время.

Общие сведения о безопасном доступе к данным в Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Повторное создание ключей Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Программный доступ к ключам с помощью Azure Active Directory:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: используйте выделенные учетные записи администратора

**Руководство**: неприменимо; Azure Cosmos DB не поддерживает учетные записи администратора.  Весь доступ интегрируется с Azure Active Directory и управлением доступом на основе ролей Azure (RBAC).



**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4. Использование единого входа (SSO) с Azure Active Directory

**Руководство**. Azure Cosmos DB использует два типа ключей для авторизации пользователей и не поддерживает единый вход (SSO) на уровне плоскости данных. Доступ к плоскости управления для Cosmos DB доступен через REST API и поддерживает единый вход. Для проверки подлинности задайте заголовок авторизации для запросов к JSON Web Token, полученному из Azure Active Directory.

Общие сведения о базе данных Azure для Cosmos DB REST API:https://docs.microsoft.com/rest/api/cosmos-db/

Общие сведения об использовании единого входа с Azure Active Directory:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: используйте многофакторную проверку подлинности для всех Azure Active Directoryного доступа

**Руководство**. Включение многофакторной идентификации Azure Active Directory и соблюдение рекомендаций по управлению удостоверениями и доступом в центре безопасности Azure.

Как включить MFA в Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Мониторинг удостоверений и доступа в центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: используйте выделенные компьютеры (рабочие станции с привилегированным доступом) для всех административных задач

**Руководство**. Использование рабочих станций привилегированного доступа (привилегированным доступом) с настроенной многофакторной проверкой подлинности для входа и настройки ресурсов Azure.

Дополнительные сведения о рабочих станциях с привилегированным доступом:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Как включить MFA в Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: регистрация и оповещение о подозрительных действиях из учетных записей администраторов

**Руководство**. Использование Advanced Threat protection (ATP) для Azure Cosmos DB. ATP для Azure Cosmos DB предоставляет дополнительный уровень логики безопасности, который обнаруживает необычные и потенциально опасные попытки доступа или использования учетных записей Azure Cosmos. Этот уровень защиты позволяет решать угрозы и интегрировать их в централизованные системы мониторинга безопасности. 

Кроме того, вы можете использовать Azure Active Directory (AD) управление привилегированными пользователями (PIM) для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде.

Используйте обнаружение рисков Azure AD для просмотра предупреждений и отчетов о поведении опасного пользователя.

Развертывание управление привилегированными пользователями (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Общие сведения об обнаружении рисков Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: управление ресурсами Azure из только утвержденных расположений

**Руководство**. Настройка условия расположения политики условного доступа и Управление именованными расположениями. С помощью именованных расположений можно создавать логические группы диапазонов IP-адресов или стран и регионов. Вы можете ограничить доступ к конфиденциальным ресурсам, таким как экземпляры Azure Cosmos DB, в настроенные именованные расположения.

Как настроить именованные расположения в Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="39-use-azure-active-directory"></a>3,9: используйте Azure Active Directory

**Руководство**. Использование Azure Active Directory (AD) в качестве централизованной системы проверки подлинности и авторизации. Azure AD защищает данные с помощью надежного шифрования для неактивных и транзитных данных. Кроме того, в Azure AD имеются Salt-записи, хэши и безопасно хранят учетные данные пользователя.

Создание и настройка экземпляра Azure Active Directory.https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Как настроить проверку подлинности Azure Active Directory и управлять ею с помощью Azure SQL:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: регулярно просматривайте и выверяйте доступ пользователей

**Руководство**. Azure Active Directory предоставляет журналы для облегчения поиска устаревших учетных записей. Кроме того, вы можете использовать проверки доступа удостоверений Azure для эффективного управления членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователя можно проверить регулярно, чтобы убедиться, что доступ к ним имеют только нужные пользователи.

Использование проверок доступа для идентификации Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: монитор пытается получить доступ к отключенным учетным записям

**Руководство**. Вы можете создать параметры диагностики для Azure Active Directory учетных записей пользователей, отправив журналы аудита и журналы входа в рабочую область log Analytics, где можно настроить нужные оповещения.

Как интегрировать журналы действий Azure в Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: предупреждение об отклонении режима входа учетной записи

**Руководство**. Использование Advanced Threat protection (ATP) для Azure Cosmos DB. ATP для Azure Cosmos DB предоставляет дополнительный уровень логики безопасности, который обнаруживает необычные и потенциально опасные попытки доступа или использования учетных записей Azure Cosmos. Этот уровень защиты позволяет решать угрозы и интегрировать их в централизованные системы мониторинга безопасности. 

Можно также использовать функции защита идентификации Azure AD и обнаружения рисков для настройки автоматических ответов на обнаруженные подозрительные действия, связанные с удостоверениями пользователей. Кроме того, вы можете принять журналы в Azure Sentinel для дальнейшего изучения.

Просмотр Azure Active Directory рискованных входов в систему:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Настройка и включение политик риска для защиты идентификации.https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Как подключить метку Azure:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: предоставьте корпорации Майкрософт доступ к соответствующим пользовательским данным во время сценариев поддержки

**Руководство**: Сейчас недоступно; Защищенное хранилище еще не поддерживаются для базы данных Azure для Cosmos DB.

Список поддерживаемых защищенное хранилище служб:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: неприменимо

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в разделе [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: поддержание инвентаризации конфиденциальной информации

**Руководство**. Использование тегов для отслеживания экземпляров Azure Cosmos DB, в которых хранятся или обрабатываются конфиденциальные данные.

Создание и использование тегов:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Изолируйте системы, хранящие или обрабатывающие конфиденциальные данные

**Руководство**. Реализация отдельных подписок и групп управления для разработки, тестирования и производства. Azure Cosmos DB экземпляры разделены с помощью виртуальной сети или подсети, помечаются соответствующим образом и защищаются в группе безопасности сети (NSG) или брандмауэре Azure. Azure Cosmos DB экземпляры, в которых хранятся конфиденциальные данные, должны быть изолированы. С помощью функции "Частная связь Azure" можно подключиться к учетной записи экземпляра Azure Cosmos DB с помощью частной конечной точки. Частная конечная точка — это набор частных IP-адресов в подсети в виртуальной сети. Затем можно ограничить доступ к выбранным частным IP-адресам. 

Как создать дополнительные подписки Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание групп управления:https://docs.microsoft.com/azure/governance/management-groups/create

Создание и использование тегов:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Как настроить закрытую конечную точку для Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Создание группы безопасности сети с конфигурацией безопасности:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Отслеживайте и блокируйте несанкционированную пересылку конфиденциальной информации

**Руководство**. Вы можете развернуть расширенную защиту от угроз для Azure Cosmos DB, которая будет обнаруживать аномальные действия, указывающие на необычные и потенциально опасные попытки доступа к базам данных или их использования. В настоящее время он может активировать следующие оповещения:

- Доступ из необычных расположений

- Необычное извлечение данных

Кроме того, при использовании виртуальных машин для доступа к экземплярам Azure Cosmos DB используйте частные ссылки, брандмауэры, группы безопасности сети и теги служб, чтобы снизить вероятность утечка данных. Майкрософт управляет базовой инфраструктурой для Azure Cosmos DB и реализует четкие элементы управления для предотвращения потери или раскрытия данных клиента.

Настройка расширенной защиты от угроз Cosmos DB.https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Общие сведения о защите данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Общая

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: шифрование всех конфиденциальных данных во время передачи

**Руководство**. все подключения к Azure Cosmos DB поддерживают протокол HTTPS. Azure Cosmos DB также поддерживает TLS 1.2. Можно применить минимальную версию протокола TLS на стороне сервера. Для этого обратитесь к [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com).

Общие сведения о Cosmos DB безопасности:https://docs.microsoft.com/azure/cosmos-db/database-security

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: использование активного средства обнаружения для распознавания конфиденциальных данных

**Руководство**. Автоматическая идентификация, классификация и защита от потери данных еще не доступны для Azure Cosmos DB. Однако вы можете использовать интеграцию с Azure Когнитивный поиск для классификации и анализа данных. Кроме того, можно реализовать стороннее решение, если это необходимо для обеспечения соответствия требованиям.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и имеет большую длину для защиты от потери данных и доступности клиентов. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

Индексирование Azure Cosmos DB данных с помощью Когнитивный поиск https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&ampAzure:; BC =/Азуре/космос-дБ/бреадкрумб/ток.жсон

Общие сведения о защите данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: Общая

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6. Использование Azure RBAC для управления доступом к ресурсам

**Руководство**. Azure Cosmos DB предоставляет встроенный контроль доступа на основе РОЛЕЙ (RBAC) для распространенных сценариев управления в Azure Cosmos DB. Пользователь, имеющий профиль в Azure Active Directory, может назначать эти роли RBAC пользователям, группам, субъектам-службам или управляемым удостоверениям для предоставления или запрета доступа к ресурсам и операциям в ресурсах Azure Cosmos DB. Назначения ролей предоставляются только для доступа к плоскости управления, включая доступ к учетным записям Azure Cosmos, базам данных, контейнерам и предложениям (пропускная способность).

Реализация RBAC в Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.

Майкрософт управляет базовой инфраструктурой для Cosmos DB и реализует четкие элементы управления для предотвращения потери или раскрытия данных клиента.

Общие сведения о защите данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: шифрование конфиденциальной информации при хранении

**Руководство**. все пользовательские данные, хранящиеся в Cosmos DB, шифруются по умолчанию в неактивных. Нет элементов управления для его отключения. Azure Cosmos DB использует шифрование AES-256 во всех регионах, где выполняется учетная запись.

По умолчанию Корпорация Майкрософт управляет ключами, которые используются для шифрования данных в учетной записи Azure Cosmos. При необходимости можно добавить второй уровень шифрования с собственными ключами.

Общие сведения о шифровании неактивных с помощью Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Основные сведения об управлении ключами для шифрования неактивных данными с помощью Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Как настроить ключи, управляемые клиентом, для учетной записи Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: журналы и оповещения об изменениях критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналом действий Azure для создания оповещений о том, когда изменения выполняются в рабочих экземплярах Azure Cosmos DB.

Создание оповещений для событий журнала действий Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Создание оповещений для событий журнала действий Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="vulnerability-management"></a>Управление уязвимостями

*Дополнительные сведения см. в разделе [Security Control: управлением уязвимостей](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: Запуск автоматизированных средств проверки уязвимостей

**Рекомендации**. Следуйте рекомендациям центра безопасности Azure для своих экземпляров Azure Cosmos DB. 

Корпорация Майкрософт выполняет установку исправлений и управления уязвимостью на базовых узлах, поддерживающих экземпляры Azure Cosmos DB. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

Поддерживаемые функции, доступные в центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Общая

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Развертывание автоматизированного решения по управлению исправлениями сторонних производителей

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: сравнение проверок уязвимостей "назад-назад"

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5. Использование процесса оценки рисков для определения приоритета устранения обнаруженных уязвимостей

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в разделе [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1. Использование обнаружения ресурсов Azure

**Руководство**. Использование портал Azure или графа ресурсов Azure для обнаружения всех ресурсов (не ограниченных Azure Cosmos DB, но также включая ресурсы, такие как вычисление, другое хранилище, сеть, порты и протоколы и т. д.) в рамках ваших подписок.  Убедитесь, что у вас есть соответствующие разрешения в клиенте и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

Хотя классические ресурсы Azure могут быть обнаружены с помощью графа ресурсов, настоятельно рекомендуется создавать и использовать Azure Resource Manager ресурсы, идущие вперед.

Как создавать запросы с помощью графа ресурсов Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Как просмотреть подписки Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Общие сведения об управлении доступом на основе ролей в Azure:https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="62-maintain-asset-metadata"></a>6,2: сохранение метаданных активов

**Руководство**. применение тегов к экземплярам Azure Cosmos DB и связанным ресурсам с помощью метаданных для логической организации их в таксономию.

Создание и использование тегов:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Какие Azure Cosmos DB ресурсы поддерживают Теги:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: удалите неавторизованные ресурсы Azure

**Руководство**. Использование тегов, групп управления и отдельных подписок (при необходимости) для Организации и мониторинга ресурсов, включая, но не ограничиваясь ресурсами Azure Cosmos DB. Регулярно выверка инвентаризации и своевременное удаление неавторизованных ресурсов из подписки.

Как создать дополнительные подписки Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание Группы управления:https://docs.microsoft.com/azure/governance/management-groups/create

Создание и использование тегов:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: ведение инвентаризации утвержденных ресурсов Azure и наименований программного обеспечения

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами и Azure в целом.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: отслеживание неутвержденных ресурсов Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в подписках клиентов, с помощью следующих встроенных определений политик:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Кроме того, используйте граф ресурсов Azure для запроса или обнаружения ресурсов в подписках.

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как создавать запросы с помощью Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**: неприменимо; Этот базовый план предназначен для ресурсов вычислений.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами и Azure в целом.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="68-use-only-approved-applications"></a>6,8: Используйте только утвержденные приложения

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="69-use-only-approved-azure-services"></a>6,9: Используйте только утвержденные службы Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в подписках клиентов, с помощью следующих встроенных определений политик:

- Недопустимые типы ресурсов 

- Допустимые типы ресурсов

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как отказаться от определенного типа ресурса с помощью политики Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="610-implement-approved-application-list"></a>6,10: реализация списка утвержденных приложений

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: ограничьте возможность пользователей взаимодействовать с Азурересаурцес Manager с помощью сценариев

**Руководство**. Используйте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "блокировать доступ" для приложения "Управление Microsoft Azure". Это может препятствовать созданию и изменению ресурсов в среде с высоким уровнем безопасности.

Как настроить условный доступ для блокировки доступа к Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: ограничьте возможность пользователей выполнять сценарии в ресурсах вычислений

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: физическое или логическое разделение приложений с высоким риском

**Руководство**: неприменимо; Это руководство предназначено для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="secure-configuration"></a>Настройка безопасности

*Дополнительные сведения см. в разделе [Security Control: Secure Configuration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Установите безопасные конфигурации для всех ресурсов Azure.

**Руководство**. Определение и реализация стандартных конфигураций безопасности для экземпляров Cosmos DB с помощью политики Azure. Используйте псевдонимы политик Azure в пространстве имен Microsoft. DocumentDB, чтобы создать настраиваемые политики для аудита или принудительного применения конфигурации экземпляров Cosmos DB. Вы также можете использовать встроенные определения политик для Azure Cosmos DB, например:

- Развертывание Расширенной защиты от угроз для учетных записей Cosmos DB

- Служба Cosmos DB должна использовать конечную точку службы виртуальной сети.

Просмотр доступных псевдонимов политик Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Установка безопасных конфигураций операционной системы

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: поддержание защищенных конфигураций ресурсов Azure

**Руководство**. Использование политики Azure [Deny] и [развертывание если не существует] для обеспечения безопасности параметров в ресурсах Azure.

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Общие сведения о влиянии политики Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: поддержание защищенных конфигураций операционных систем

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: безопасное хранение конфигурации ресурсов Azure

**Рекомендации**. Если вы используете пользовательские определения политики Azure для Cosmos DB или связанных ресурсов, используйте Azure Repos для безопасного хранения кода и управления им.

Документация по Azure Repos https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops :https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: безопасное хранение пользовательских образов операционной системы

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: развертывание средств управления конфигурацией системы

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. DocumentDB для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией системы для операционных систем

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9. Реализация автоматического мониторинга конфигурации для служб Azure

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. DocumentDB для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Используйте политику Azure [аудит], [запретить] и [развернуть, если не существует], чтобы автоматически применять конфигурации для экземпляров Azure Cosmos DB и связанных ресурсов. 

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10. Реализация автоматического мониторинга конфигурации для операционных систем

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="711-manage-azure-secrets-securely"></a>7,11: безопасное управление секретами Azure

**Руководство**. для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, используемых для доступа к экземплярам Azure Cosmos DB, используйте управляемое удостоверение службы в сочетании с Azure Key Vault для упрощения и защиты Azure Cosmos DB управления секретами. Убедитесь, что Key Vault обратимое удаление включено.

Как интегрироваться с управляемыми удостоверениями Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Создание Key Vault.https://docs.microsoft.com/azure/key-vault/quick-create-portal

Как обеспечить проверку подлинности Key Vault с помощью управляемого удостоверения:https://docs.microsoft.com/azure/key-vault/managed-identity

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: безопасно управляйте удостоверениями и автоматически

**Руководство**. для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, используемых для доступа к экземплярам Azure Cosmos DB, используйте управляемое удостоверение службы в сочетании с Azure Key Vault для упрощения и защиты Azure Cosmos DB управления секретами.

Используйте управляемые удостоверения для предоставления служб Azure с автоматически управляемым удостоверением в Azure Active Directory (AD). Управляемые удостоверения позволяют проходить проверку подлинности в любой службе, поддерживающей проверку подлинности Azure AD, включая Key Vault без каких бы то ни было учетных данных в коде.

Как настроить управляемые удостоверения:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Как интегрироваться с управляемыми удостоверениями Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: устранение непреднамеренной раскрытия учетных данных

**Руководство**. Реализация средства проверки учетных данных для указания учетных данных в коде. Средство проверки учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault.

Как настроить средство проверки учетных данных:https://secdevtools.azurewebsites.net/helpcredscan.html

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в разделе [Security Control: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами. Антивредоносное по Майкрософт включено на базовом узле, который поддерживает службы Azure (например, служба приложений Azure), но не выполняется в содержимом клиента.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся средами вычислений

**Руководство**. антивредоносное по Майкрософт включено на базовом узле, поддерживающем службы Azure (например, служба приложений Azure), но не выполняется в содержимом клиента.

Вы отвечаете за предварительную проверку всех файлов, передаваемых в ресурсы Azure, не связанные с выпуском вычислений, включая Azure Cosmos DB. Корпорация Майкрософт не может получить доступ к данным клиентов, поэтому не может проводить проверку содержимого клиентов от вредоносных программ от вашего имени.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Убедитесь, что программное обеспечение и подписи для защиты от вредоносных программ обновлены

**Руководство**: неприменимо; тест производительности предназначен для ресурсов вычислений. Антивредоносное по Майкрософт включено на базовом узле, который поддерживает службы Azure, но не выполняется в содержимом клиента.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в разделе [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Обеспечьте регулярную автоматическую архивацию

**Руководство**. Azure Cosmos DB создает моментальные снимки данных каждые четыре часа. Для обеспечения устойчивости в случае региональной аварии все резервные копии хранятся отдельно в службе хранилища, а также глобально реплицируются. В любое время сохраняются только два последних моментальных снимка. Однако при удалении контейнера или базы данных Azure Cosmos DB хранит существующие моментальные снимки в течение 30 дней. Обратитесь в службу поддержки Azure для восстановления из резервной копии.

Основные сведения об автоматической архивации Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом.

**Руководство**. Azure Cosmos DB автоматически создает резервные копии данных через регулярные интервалы. Если база данных или контейнер удалена, можно отправить запрос в службу поддержки или позвонить в службу поддержки Azure, чтобы восстановить данные из автоматических оперативного резервного копирования. Поддержка Azure доступна только для выбранных планов, таких как "Стандартный", "Разработчик" и "планы" выше. Чтобы восстановить определенный моментальный снимок архива, службе Azure Cosmos DB требуется, чтобы данные были доступны в течение цикла архивации для данного снимка. 

При использовании Key Vault для хранения учетных данных для экземпляров Cosmos DB Обеспечьте регулярное автоматическое резервное копирование ключей.

Общие сведения о Azure Cosmos DB автоматических резервных копий:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Восстановление данных в Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Резервное копирование Key Vault ключей:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: Общая

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Рекомендации**. Если база данных или контейнер удалена, можно отправить запрос в службу поддержки или позвонить в службу поддержки Azure, чтобы восстановить данные из автоматической оперативной архивации. Поддержка Azure доступна только для выбранных планов, таких как "Стандартный", "Разработчик" и "планы" выше. Чтобы восстановить определенный моментальный снимок архива, службе Azure Cosmos DB требуется, чтобы данные были доступны в течение цикла архивации для данного снимка.

Протестируйте сохраненные в Azure Key Vault секреты с помощью PowerShell. Командлет Restore-AzureKeyVaultKey создает ключ в указанном хранилище ключей. Этот ключ является репликой резервного ключа во входном файле и имеет то же имя, что и исходный ключ.

Общие сведения о Azure Cosmos DB автоматических резервных копий:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Восстановление данных в Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Восстановление секретов Azure Key Vault:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и управляемых клиентом ключей

**Рекомендации**. Поскольку все пользовательские данные, хранящиеся в Cosmos DB, шифруются при хранении и передаче, вам не нужно предпринимать никаких действий. Другими словами, шифрование неактивных данных включено по умолчанию. Элементов управления для его включения и отключения нет. Azure Cosmos DB использует шифрование AES-256 во всех регионах, где выполняется учетная запись.

Включите обратимое удаление в Key Vault, чтобы защитить ключи от случайного или вредоносного удаления.

Общие сведения о шифровании данных в Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Включение обратимого удаления в Key Vault:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Общая

## <a name="incident-response"></a>реагирование на инциденты.

*Дополнительные сведения см. в разделе [контроль безопасности: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: создайте руководство по реагированию на инциденты

**Руководство**. Создание руководства по реагированию на инциденты для вашей организации. Убедитесь, что имеются письменные планы реагирования на инциденты, которые определяют все роли персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.

Вы также можете использовать руководство по обработке инцидентов безопасности компьютера NIST, чтобы помочь в создании собственного плана реагирования на инциденты:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Как настроить автоматизацию рабочих процессов в центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Руководство по созданию собственного процесса реагирования на инциденты безопасности:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Анатомия инцидента Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: создание оценки инцидента и процедуры определения приоритетов

**Руководство**. Центр безопасности назначает серьезность каждому оповещению, чтобы вы могли определить, какие предупреждения следует изучить первыми. Серьезность основывается на том, насколько уверенности в поиске центра безопасности или аналитики, используемой для выдаче оповещения, а также об уровне достоверности, который был вредоносной задачей, вызвавшей оповещение.

Кроме того, четко помечать подписки (например, Рабочая, не производственная) и создайте систему именования, чтобы четко определить и классифицировать ресурсы Azure.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="103-test-security-response-procedures"></a>10,3: тестирование процедур реагирования на безопасность

**Руководство**. проведение упражнений по тестированию возможностей реагирования на инциденты систем на регулярной ритмичности. Выявление слабых точек и пробелов и пересмотр плана по мере необходимости.

Обратитесь к публикации NIST: руководство по тестированию, обучению и упражнениям для ИТ-планов и возможностей.https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: укажите контактные данные инцидента безопасности и настройте уведомления о событиях для инцидентов безопасности.

**Руководство**. контактные данные инцидентов безопасности будут использоваться корпорацией Майкрософт для связи с вами, если центр Microsoft Security Response Center (MSRC) обнаруживает, что доступ к данным клиента был получен незаконные или неавторизованной стороной.  Проверьте инциденты после факта, чтобы убедиться, что проблемы устранены.

Как задать контакт безопасности центра безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**. Экспорт оповещений и рекомендаций центра безопасности Azure с помощью функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Вы можете использовать соединитель данных центра безопасности Azure для потоковой передачи метки предупреждений.

Как настроить непрерывный экспорт:https://docs.microsoft.com/azure/security-center/continuous-export

Как выполнить потоковую передачу предупреждений в Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Автоматизируйте ответ на оповещения системы безопасности

**Руководство**. Использование функции автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" в оповещениях и рекомендациях системы безопасности.

Как настроить автоматизацию рабочего процесса и Logic Apps:https://docs.microsoft.com/azure/security-center/workflow-automation

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в разделе [контроль безопасности: тесты на проникновение и примеры для красных команд](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критических обновлений безопасности в течение 60 дней.

**Руководство**. соблюдение правил корпорации Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Дополнительные сведения о стратегии корпорации Майкрософт и ее выполнении, а также о тестировании на основе уязвимости для облачной инфраструктуры, служб и приложений Майкрософт см. здесь:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со статьей о [производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Дополнительные сведения о [базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
