---
title: Базовая конфигурация безопасности для базы данных Azure для PostgreSQL (отдельный сервер)
description: Базовая конфигурация безопасности для базы данных Azure для PostgreSQL (отдельный сервер)
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 89d760e28025f64b8cd8940fc08f06d23c969e12
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655753"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql-single-server"></a>Базовая конфигурация безопасности для базы данных Azure для PostgreSQL (отдельный сервер)

Базовая конфигурация безопасности для базы данных Azure для PostgreSQL (отдельный сервер) содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовая конфигурация безопасности для этой службы взята из [Эталона безопасности Azure версии 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), содержащего рекомендации по обеспечению безопасности облачных решений в Azure с помощью наших практических рекомендаций.

Дополнительные сведения см. в статье [Обзор базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в статье [Управление безопасностью: безопасность сети](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Защита ресурсов в виртуальной сети с помощью групп безопасности сети или Брандмауэра Azure

**Руководство**: Настройте приватный канал для базы данных Azure для PostgreSQL с частными конечными точками. Приватный канал позволяет подключаться к различным службам PaaS в Azure через частную конечную точку. Приватный канал Azure, по сути, предоставляет службы Azure в частной виртуальной сети (VNet). Трафик между виртуальной сетью и экземпляром PostgreSQL проходит через магистральную сеть Майкрософт.

Кроме того, вы можете использовать конечные точки службы виртуальной сети для защиты и ограничения сетевого доступа к базе данных Azure для реализаций PostgreSQL. Правила виртуальной сети — это одна из функций безопасности брандмауэра, обеспечивающая управление приемом сервером Базы данных Azure для PostgreSQL подключений из определенных подсетей в виртуальных сетях.

Вы можете обезопасить вашу базу данных Azure для PostgreSQL правилами брандмауэра. Брандмауэр сервера запрещает любой доступ к серверу базы данных, пока вы не укажете компьютеры, у которых есть разрешение на доступ. Для настройки брандмауэра можно создать правила брандмауэра, которые указывают диапазон допустимых IP-адресов. Правила брандмауэра можно создавать на уровне сервера.

Настройте приватный канал для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Создание правил и конечных точек службы VNet и правил VNet в базе данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Настройка правил брандмауэра сервера базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-manage-firewall-using-portal

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**: Если база данных Azure для экземпляра PostgreSQL защищена частной конечной точкой, вы можете развернуть виртуальные машины в той же виртуальной сети. Чтобы снизить риск утечки данных, можно использовать группу безопасности сети (NSG). Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков NSG в рабочую область Log Analytics и использовать аналитику трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитики трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять некорректные сетевые настройки.

Настройте приватный канал для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Как включить журналы потоков NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включать и использовать Аналитику трафика: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: Защита критических веб-приложений

**Руководство**: Неприменимо; эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Запрет взаимодействия с известными опасными IP-адресами

**Руководство**: Расширенная защита от угроз для Базы данных Azure для PostgreSQL. Расширенная защита от угроз позволяет выявить подозрительную активность, указывающую на необычные и потенциально опасные попытки получить доступ к базам данных или воспользоваться ими.

Включите стандарт защиты от атак DDoS в виртуальных сетях, связанных с экземплярами базы данных Azure для PostgreSQL, чтобы защититься от атак типа DDoS. Используйте интегрированную аналитику угроз центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми IP-адресами Интернета.

Как настроить расширенную защиту от угроз для Базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Настройка защиты от атак DDoS: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Запись сетевых пакетов и журналов потоков

**Руководство**: Если база данных Azure для экземпляра PostgreSQL защищена частной конечной точкой, вы можете развернуть виртуальные машины в той же виртуальной сети. Чтобы снизить риск утечки данных, можно настроить группу безопасности сети (NSG). Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков NSG в рабочую область Log Analytics и использовать аналитику трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитики трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять некорректные сетевые настройки.

Как включить журналы потоков NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включать и использовать Аналитику трафика: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Развертывание сетевых систем обнаружения и предотвращения вторжений (IDS/IPS)

**Руководство**: Расширенная защита от угроз для Базы данных Azure для PostgreSQL. Расширенная защита от угроз позволяет выявить подозрительную активность, указывающую на необычные и потенциально опасные попытки получить доступ к базам данных или воспользоваться ими.

Как настроить расширенную защиту от угроз для Базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Управление трафиком к веб-приложениям

**Руководство**: Неприменимо; эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Уменьшение сложности и дополнительных затрат на администрирование в правилах безопасности сети

**Руководство**: Для ресурсов, которым требуется доступ к базе данных Azure для экземпляров PostgreSQL, используйте теги службы виртуальной сети для определения элементов управления доступом к сети в группах безопасности сети или брандмауэре Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, SQL.WestUs) в соответствующем исходном поле или поле назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, входящих в тег службы, и автоматически обновляет этот тег при изменении адресов.

Примечание. База данных Azure для PostgreSQL использует тег службы "Microsoft.Sql".

Дополнительные сведения об использовании тегов служб: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Общие сведения об использовании тегов службы для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet#terminology-and-description

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**: Определите и реализуйте стандартные конфигурации безопасности для сетевых настроек и сетевых ресурсов, связанных с вашей базой данных Azure для экземпляров PostgreSQL, с помощью политики Azure. Используйте псевдонимы политик Azure в пространствах имен "Microsoft.DBforPostgreSQL" и "Microsoft. Network" для создания настраиваемых политик, предназначенных для аудита или принудительного применения конфигурации сети для экземпляров базы данных Azure для PostgreSQL. Вы также можете использовать встроенные определения политик, связанные с сетью, или базу данных Azure для экземпляров PostgreSQL, например:

- Стандарт Защиты от атак DDoS должен быть включен.

- Для серверов баз данных PostgreSQL должно быть включено принудительное использование TLS-соединения

Как настроить политику SQL Azure и управлять ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Примеры политики Azure для работы с сетью: https://docs.microsoft.com/azure/governance/policy/samples/

Как создать схему Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: Документация по правилам конфигурации трафика

**Руководство**: Используйте теги для ресурсов, связанных с сетевой безопасностью и потоком трафика, для базы данных Azure для экземпляров PostgreSQL, чтобы предоставить метаданные и логическую организацию.

Используйте любые встроенные определения политик Azure, связанные с разметкой, например "требовать тег и его значение", чтобы обеспечить создание всех ресурсов с помощью тегов и уведомления о существующих непомеченных ресурсах.

Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий с ресурсами на основе их тегов.

Как создавать и использовать теги: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**: Используйте журнал действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений для сетевых ресурсов, связанных с экземплярами базы данных Azure для PostgreSQL. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых ресурсов.

Просмотр и извлечение событий журнала действий Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Создание оповещений в службе Azure Monitor https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в статье [Управление безопасностью: ведение журналов и мониторинг](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Использование утвержденных источников синхронизации времени

**Руководство**: Корпорация Майкрософт поддерживает источники времени для ресурсов Azure, например базу данных Azure для PostgreSQL для меток времени в журналах.


**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Настройка централизованного управления журналами безопасности

**Руководство**: Включите параметры диагностики и журналы сервера и включайте журналы для агрегирования данных безопасности, создаваемых экземплярами базы данных Azure для PostgreSQL. В Azure Monitor используйте рабочие области Log Analytics для запроса и выполнения анализа, а учетные записи хранения Azure — для долгосрочного и архивного хранения. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

Настройка и предоставление доступа к Журналам сервера для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Настройка и предоставление доступа к Журналам аудита для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/concepts-audit

Подключение к Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг центра безопасности Azure**: Сейчас это недоступно

**Ответственность**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Включение журналов аудита для ресурсов Azure

**Руководство**: Включите параметры диагностики в базе данных Azure для экземпляров PostgreSQL, чтобы получить доступ к журналам аудита, безопасности и ресурсов. Убедитесь, что вы включили именно журнал аудита PostgreSQL. Автоматически доступны журналы действий, включающие источник событий, дату, пользователя, метку времени, исходные адреса, адреса назначения и другие полезные элементы. Вы также можете включить параметры диагностики журнала действий Azure и отправить журналы в ту же рабочую область Log Analytics или учетную запись хранения.

Настройка и предоставление доступа к Журналам сервера для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Настройка и предоставление доступа к Журналам аудита для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/concepts-audit

Как настроить параметры диагностики для журнала действий Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Мониторинг центра безопасности Azure**: Сейчас это недоступно

**Ответственность**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Сбор журналов безопасности из операционных систем

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.


**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="25-configure-security-log-storage-retention"></a>2.5: Настройка хранения журнала безопасности

**Руководство**: В Azure Monitor для рабочей области Log Analytics, используемой для хранения журналов базы данных Azure для PostgreSQL, задайте срок хранения согласно нормативным требованиям вашей организации. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения.

Как задать параметры хранения журнала для рабочих областей Log Analytics: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Хранение журналов ресурсов в учетной записи хранения Azure: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: Мониторинг и просмотр журналов

**Руководство**: Анализируйте и отслеживайте журналы экземпляров базы данных Azure для PostgreSQL на предмет аномального поведения. Используйте аналитику журналов Azure Monitor для просмотра журналов и выполнения запросов к данным журнала. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

Подключение к Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Дополнительные сведения об аналитике журналов: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Как выполнять пользовательские запросы в Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Включение оповещений об аномальных действиях

**Руководство**: Включение расширенной защиты от угроз для Базы данных Azure для PostgreSQL. Расширенная защита от угроз позволяет выявить подозрительную активность, указывающую на необычные и потенциально опасные попытки получить доступ к базам данных или воспользоваться ими.

Кроме того, вы можете включить журналы сервера и параметры диагностики для PostgreSQL и отправить журналы в рабочую область Log Analytics. Подключите рабочую область Log Analytics к Azure Sentinel, так как она предоставляет решение для оркестрации событий безопасности и автоматического реагирования (SOAR). Это позволяет создавать и использовать сборники схем (автоматизированные решения) для устранения проблем безопасности.

Включение расширенной защиты от угроз для Базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Настройка и предоставление доступа к Журналам сервера для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Настройка и предоставление доступа к Журналам аудита для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/concepts-audit

Как настроить параметры диагностики для журнала действий Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Подключение к Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: Централизованное ведение журнала защиты от вредоносных программ

**Руководство**: Неприменимо; база данных Azure для PostgreSQL не обрабатывает и не создает журналы, связанные с защитой от вредоносных программ.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="29-enable-dns-query-logging"></a>2.9: Включение ведения журнала запросов DNS

**Руководство**: Неприменимо; база данных Azure для PostgreSQL не обрабатывает и не создает журналы, связанные с DNS.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="210-enable-command-line-audit-logging"></a>2.10: Включить ведение журнала аудита для командной строки

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Дополнительные сведения см. в статье [Управление безопасностью: идентификаторы и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Инвентаризация учетных записей администраторов

**Руководство**: Настройте инвентаризацию учетных записей пользователей, имеющих административный доступ к уровню управления (например, порталу Azure) для экземпляров базы данных Azure для PostgreSQL. Кроме того, поддерживайте инвентаризацию учетных записей администраторов, имеющих доступ к уровню данных (в самой базе данных) для экземпляров базы данных Azure для PostgreSQL. (При создании сервера PostgreSQL вы предоставляете учетные данные для пользователя с правами администратора. Этот администратор может использоваться для создания дополнительных пользователей PostgreSQL.)

База данных Azure для PostgreSQL не поддерживает встроенные функции управления доступом на основе ролей, но можно создавать пользовательские роли на основе конкретных операций с поставщиками ресурсов.

Общие сведения о пользовательских ролях для подписки Azure: https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Общие сведения об операциях поставщика ресурсов базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql 

Общие сведения об управлении доступном для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/concepts-security#access-management

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Измените пароли по умолчанию, где применимо

**Руководство**: Azure Active Directory и база данных Azure для PostgreSQL не используют пароли по умолчанию.

После создания самого ресурса базы данных Azure для PostgreSQL Azure заставляет создать пользователя с правами администратора с надежным паролем. Однако после создания экземпляра PostgreSQL можно использовать учетную запись первого администратора сервера, созданную для создания дополнительных пользователей и предоставления им административного доступа. При создании этих учетных записей убедитесь, что для каждой учетной записи настроен отличающийся надежный пароль.

Как создать дополнительные учетные записи для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-create-users

Как обновить пароль администратора: https://docs.microsoft.com/azure/postgresql/howto-create-manage-server-portal#update-admin-password

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Применение выделенных административных учетных записей

**Руководство**: Создайте стандартные операционные процедуры для использования выделенных административных учетных записей, имеющих доступ к вашим экземплярам баз данных Azure PostgreSQL. Используйте учетную запись центра безопасности Azure и управление доступом для мониторинга количества административных учетных записей. 

Как работает идентификация и доступ в центре безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access 

Узнайте, как создать дополнительных администраторов в Базе данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account


**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Использование единого входа с Azure Active Directory

**Руководство**: Вход в базу данных Azure для PostgreSQL поддерживается как с помощью имени пользователя/пароля, настроенных непосредственно в базе данных, а также с помощью идентификации Azure Active Directory (AD) и использования токена Azure AD для подключения. При использовании токена Azure AD поддерживаются различные методы, например пользователь Azure AD, Группа Azure AD или приложение Azure AD, подключающееся к базе данных.

Отдельный доступ к плоскости управления для PostgreSQL доступен через REST API и поддерживает единый вход. Чтобы войти, установите JSON Web Token, полученный из Azure Active Directory (AAD), в качестве заголовка авторизации.

Используйте Azure Active Directory для проверки подлинности в базе данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Сведения о REST API базы данных Azure для PostgreSQL: https://docs.microsoft.com/rest/api/postgresql/

Сведения о едином входе в Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Использование многофакторной проверки подлинности для любого доступа на основе Azure Active Directory

**Руководство**: Включите многофакторную идентификацию (MFA) в Azure Active Directory и следуйте рекомендациям по управлению идентификацией и доступом в центре безопасности Azure. При использовании маркеров Azure AD для входа в базу данных возможна многофакторная проверка подлинности для входа в базу данных.

Как включить MFA в Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Используйте Azure Active Directory для проверки подлинности в базе данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Мониторинг идентификации и доступа в центре безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Используйте выделенные компьютеры (рабочие станции с привилегированным доступом) для всех административных задач

**Руководство**: Используйте рабочие станции привилегированного доступа (PAW) с настроенной многофакторной проверкой подлинности (MFA) для входа в ресурсы Azure и их настройки.

Использование рабочих станций с привилегированным доступом: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Как включить MFA в Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Ведение журнала и создание оповещений по подозрительным действиям учетных записей администраторов

**Руководство**: Включите расширенную защиту от угроз для базы данных Azure для PostgreSQL, чтобы создавать оповещения при подозрительных действиях.

Кроме того, вы можете использовать управление привилегированными пользователями (PIM) в Azure Active Directory (AD) для создания журналов и оповещений при возникновении подозрительных или небезопасных действий в окружении.

Используйте обнаружение рисков Azure AD для просмотра предупреждений и отчетов об опасном поведении пользователя.

Настройка расширенной защиты от угроз для Базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Развертывание Azure AD Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Об обнаружении рисков Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Управление ресурсами Azure только из утвержденных расположений

**Руководство**: Используйте именованные расположения с условным доступом, чтобы разрешить порталу и Azure Resource Manager доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

Как настроить именованные расположения: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Использование Azure Active Directory

**Руководство**: Используйте Azure Active Directory как центральную систему проверки подлинности и авторизации. Azure AD защищает данные с помощью надежного шифрования для хранимых и транзитных данных. Кроме того, в Azure AD используются salt-записи, хэши и безопасное хранение учетных данных пользователей.

Для входа в базу данных Azure для PostgreSQL рекомендуется использовать Azure AD и маркер Azure AD для подключения. При использовании токена Azure AD поддерживаются различные методы, например пользователь Azure AD, Группа Azure AD или приложение Azure AD, подключающееся к базе данных.

Учетные данные Azure AD также могут использоваться для администрирования на уровне управления (например, портал Azure), для управления учетными записями администратора PostgreSQL.

Используйте Azure Active Directory для проверки подлинности в базе данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Регулярная проверка и согласование доступа пользователей

**Руководство**: Проверьте журналы Azure Active Directory, чтобы обнаружить устаревшие учетные записи, которые могут включать в себя роли администраторов базы данных Azure для PostgreSQL. Кроме того, используйте проверку доступа удостоверений Azure для эффективного управления членством в группах, доступа к корпоративным приложениям, которые могут использоваться для доступа к базе данных Azure для PostgreSQL и назначения ролей. Доступ пользователей следует проверять регулярно, например раз в 90 дней, чтобы только у авторизованных пользователей был постоянный доступ.

Основы отчетности Azure AD https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Использование проверок доступа для идентификации Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Проверка пользователей PostgreSQL и назначенных ролей: https://www.postgresql.org/docs/current/database-roles.html

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Отслеживание попыток доступа к отключенным учетным записям

**Руководство**: Включите параметры диагностики для базы данных Azure для PostgreSQL и Azure Active Directory, отправив все журналы в рабочую область Log Analytics. Настройте необходимые оповещения (например, неудачные попытки проверки подлинности) в Log Analytics.

Настройка и предоставление доступа к Журналам сервера для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Настройка и предоставление доступа к Журналам аудита для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/concepts-audit

Как интегрировать журналы действий Azure в Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Мониторинг центра безопасности Azure**: Сейчас это недоступно

**Ответственность**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Предупреждение при подозрительном входе в учетную запись

**Руководство**: Включите расширенную защиту от угроз для базы данных Azure для PostgreSQL, чтобы создавать оповещения при подозрительных действиях.

Используйте защиту идентификации Azure Active Directory и функции обнаружения подозрительных операций, чтобы настроить автоматическое реагирование для обнаружения подозрительных действий. Вы можете включить автоматические ответы с помощью Azure Sentinel, чтобы оперативно реагировать на угрозы.

Вы также можете включить журналы в Azure Sentinel для дальнейшего изучения.

Настройка расширенной защиты от угроз для Базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Подключение защиты идентификации Azure AD: https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Как просматривать рискованные входы в Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Подключение к Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Предоставление корпорации Майкрософт доступа к соответствующим данным клиентов в рамках сценариев поддержки

**Руководство**: В настоящее время недоступно; защищенное хранилище еще не поддерживается для базы данных Azure для PostgreSQL.

Список поддерживаемых служб защищенного хранилища: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Мониторинг центра безопасности Azure**: Сейчас это недоступно

**Ответственность**: Customer

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в статье [Управление безопасностью: защита данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Инвентаризация конфиденциальных данных

**Руководство**: Используйте теги для пометки экземпляров базы данных Azure для PostgreSQL или связанных ресурсов, в которых хранятся или обрабатываются конфиденциальные данные.

Как создавать и использовать теги: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Изолирование систем, хранящих или обрабатывающих конфиденциальные данные

**Руководство**: Реализуйте отдельные подписки и группы управления для разработки, тестирования и производства. Используйте сочетание приватного канала, конечных точек службы и (или) правил брандмауэра, чтобы изолировать и ограничить сетевой доступ к экземплярам базы данных Azure для PostgreSQL.

Создание дополнительных подписок Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание групп управления: https://docs.microsoft.com/azure/governance/management-groups/create

Настройте приватный канал для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Создание правил и конечных точек службы VNet и правил VNet в базе данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Настройка правил брандмауэра сервера базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules

**Мониторинг центра безопасности Azure**: Недоступно

**Ответственность**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Руководство**: При использовании виртуальных машин Azure для доступа к экземплярам базы данных Azure PostgreSQL используйте приватные каналы, конфигурации сети PostgreSQL, группы безопасности сети и теги службы, чтобы снизить вероятность возможной утечки данных.

Корпорация Майкрософт управляет базовой инфраструктурой базы данных Azure для PostgreSQL и реализовала надежные элементы управления для предотвращения потери или раскрытия данных клиента.

Как исключить утечку данных из базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link

Общие сведения о защите данных клиентов в Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Совмещаемая блокировка

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Шифрование любой конфиденциальной информации при передаче

**Руководство**: База данных Azure для PostgreSQL поддерживает подключение сервера PostgreSQL к клиентским приложениям с помощью протокола TLS, ранее известного как SSL. Принудительное использование SSL-соединений между сервером базы данных и клиентскими приложениями помогает обеспечить защиту от атак "злоумышленник в середине" за счет шифрования потока данных между сервером и приложением. В портал Azure убедитесь, что по умолчанию включено "принудительное подключение SSL" для всех экземпляров базы данных Azure для PostgreSQL.

Сейчас версии TLS, поддерживаемые для базы данных Azure для PostgreSQL, — это TLS 1.0, TLS 1.1, TLS 1.2.

Настройте шифрование передаваемых данных для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Совмещаемая блокировка

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Использование средства активного обнаружения для распознавания конфиденциальных данных

**Руководство**: Функции идентификации, классификации и предотвращения потери данных пока недоступны для базы данных Azure для PostgreSQL. Установите сторонние решения, если это необходимо для обеспечения соответствия требованиям.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и предпринимает все возможные усилия для защиты клиентов от потери данных и раскрытия информации. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

Общие сведения о защите данных клиентов в Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: Сейчас это недоступно

**Ответственность**: Совмещаемая блокировка

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Контроль доступа к ресурсам с помощью RBAC

**Руководство**: Используйте управление доступом на основе ролей Azure (RBAC) для управления доступом к панели управления базы данных Azure для PostgreSQL (например, порталу Azure). Для доступа к уровню данных (в самой базе данных) используйте SQL-запросы для создания пользователей и настройки разрешений пользователей. RBAC не влияет на разрешения пользователей в базе данных.

Как настроить RBAC в Azure: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Настройка и предоставление прав пользователей с помощью SQL для базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-create-users

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

Корпорация Майкрософт управляет базовой инфраструктурой базы данных Azure для PostgreSQL и реализовала надежные элементы управления для предотвращения потери или раскрытия данных клиента.

Общие сведения о защите данных клиентов в Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Шифрование конфиденциальной информации при хранении

**Руководство**: В службе "База данных Azure для PostgreSQL" используется проверенный криптографический модуль FIPS 140-2 для шифрования неактивных данных. Данные, включая резервные копии, шифруются на диске (за исключением временных файлов, создаваемых при выполнении запросов). Служба использует 256-разрядный шифр AES, включенный в шифрование службы хранилища Azure. Ключами управляет система. Шифрование хранилища всегда включено, и его нельзя отключить.

Шифрование данных с помощью управляемых пользователем ключей (CMK) для отдельного сервера Базы данных Azure для PostgreSQL позволяет создавать свой собственный ключ (BYOK) для защиты неактивных данных. В настоящее время для использования этой возможности требуется запросить соответствующие права доступа. Для этого обратитесь:

AskAzureDBforPostgreSQL@service.microsoft.com.

О шифровании хранимых данных в Базе данных Azure для PostgreSQL https://docs.microsoft.com/azure/postgresql/concepts-security

Общие сведения о шифровании хранимых данных для базы данных Azure для PostgreSQL с помощью управляемых клиентом ключей: https://docs.microsoft.com/azure/postgresql/concepts-data-encryption-postgresql


**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Включение в журнал и создание оповещений по изменениям критических ресурсов Azure

**Руководство**: Используйте Azure Monitor с журналом действий Azure для создания оповещений об изменениях на коммерческих экземплярах базы данных Azure для PostgreSQL и других важных и связанных с ними ресурсах.

Создание оповещений для событий журнала действий Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="vulnerability-management"></a>Управление уязвимостями

*Дополнительные сведения см. в статье [Управление безопасностью: управление уязвимостями](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Выполнение автоматизированных средства анализа уязвимостей

**Руководство**: В настоящее время недоступно; центр безопасности Azure пока не поддерживает оценку уязвимостей для базы данных Azure для PostgreSQL.

Покрытие компонентов для служб Azure PaaS в центре безопасности Azure: https://docs.microsoft.com/azure/security-center/features-paas

**Мониторинг центра безопасности Azure**: Сейчас это недоступно

**Ответственность**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Развертывание автоматизированного решения для управления исправлениями ПО сторонних производителей

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Сравнение проверок смежных уязвимостей

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Использование процесса оценки рисков для определения приоритета в устранении обнаруженных уязвимостей

**Руководство**: Корпорация Майкрософт управляет уязвимостью в базовых системах, поддерживающих базу данных Azure для PostgreSQL.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в статье [Управление безопасностью: Инвентаризация и управление ресурсами](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Использование обнаружения ресурсов Azure

**Руководство**: Используйте граф ресурсов Azure для запроса и обнаружения всех ресурсов (включая базу данных Azure для PostgreSQL) в ваших подписках. Убедитесь, что у вас есть соответствующие разрешения (на чтение) в клиенте и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

Как создавать запросы с помощью графа ресурсов Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Как просматривать подписки Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Общие сведения об Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: Ведение метаданных активов

**Руководство**: Примените теги к базе данных Azure для экземпляров PostgreSQL и другим связанным ресурсам, предоставив метаданные для логической организации их в таксономию.

Как создавать и использовать теги: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Удаление неавторизованных ресурсов Azure

**Руководство**: При необходимости используйте теги, группы управления и отдельные подписки, чтобы упорядочить и отслеживать экземпляры базы данных Azure для PostgreSQL и связанные ресурсы. Регулярно сверяйте ресурсы, чтобы своевременно удалять неавторизованные ресурсы из подписки.

Создание дополнительных подписок Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание групп управления: https://docs.microsoft.com/azure/governance/management-groups/create

Как создавать и использовать теги: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Ведение каталога утвержденных ресурсов Azure и наименований программного обеспечения

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислений с ресурсами, а также для Azure в целом.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Отслеживание неутвержденных ресурсов Azure

**Руководство**: Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Кроме того, используйте граф ресурсов Azure для запроса или обнаружения ресурсов в подписках.

Как настроить политику SQL Azure и управлять ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как создавать запросы с помощью графа Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислений с ресурсами, а также для Azure в целом.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="68-use-only-approved-applications"></a>6.8: Использование только утвержденных приложений

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="69-use-only-approved-azure-services"></a>6.9: Использование только утвержденных служб Azure

**Руководство**: Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Как настроить политику SQL Azure и управлять ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как отказаться от определенного типа ресурса с помощью политики Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: Реализация списка утвержденных приложений

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Ограничение возможности пользователей взаимодействовать с диспетчером ресурсов Azure с помощью сценариев

**Руководство**: Используйте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "Блокировать доступ" для приложения "Управление Microsoft Azure". Это может препятствовать созданию и изменению ресурсов в среде с высоким уровнем безопасности, например экземпляров базы данных Azure для PostgreSQL, содержащих конфиденциальные сведения.

Как настроить условный доступ для блокировки доступа к Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ограничьте возможность пользователей выполнять сценарии в ресурсах вычислений

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Физическое или логическое разделение приложений с высоким риском

**Руководство**: Неприменимо; эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

## <a name="secure-configuration"></a>Настройка безопасности

*Дополнительные сведения см. в статье [Управление безопасностью: безопасная конфигурация](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Установка безопасных конфигураций для всех ресурсов Azure

**Руководство**: Определите и реализуйте стандартные конфигурации безопасности для экземпляров базы данных Azure для PostgreSQL с помощью политики Azure. Используйте псевдонимы политик Azure в пространствах имен "Microsoft.DBforPostgreSQL" для создания настраиваемых политик, предназначенных для аудита или принудительного применения конфигурации сети для базы данных Azure для экземпляров PostgreSQL. Вы также можете использовать встроенные определения политик для ваших экземпляров базы данных Azure для PostgreSQL, например:

- Для серверов баз данных PostgreSQL должно быть включено принудительное использование TLS-соединения

- На серверах баз данных PostgreSQL должны быть включены журналы подключений

Просмотр доступных псевдонимов политик Azure: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Как настроить политику SQL Azure и управлять ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Сохранение безопасных конфигураций для операционных систем

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Сохранение безопасных конфигураций для ресурсов Azure

**Руководство**: Используйте политику Azure [отказывать] и [развернуть, если не существует], чтобы обеспечить безопасность параметров в ресурсах Azure.

Как настроить политику SQL Azure и управлять ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Сведения о действии политик Azure https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Сохранение безопасных конфигураций для операционных систем

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Безопасное хранение конфигурации ресурсов Azure

**Руководство**: Если вы используете пользовательские определения политики Azure для экземпляров базы данных Azure для PostgreSQL и связанных ресурсов, используйте Azure Repos для безопасного хранения кода и управления им.

Как хранить код в Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Документация по Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Безопасное хранение пользовательских образов операционной системы

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Развертывание инструментов управления конфигурацией системы

**Руководство**: Используйте псевдонимы политик Azure в пространстве имен "Microsoft.DBforPostgreSQL" для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.

Как настроить политику SQL Azure и управлять ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Развертывание средств управления конфигурацией системы для операционных систем

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Реализация автоматизированного мониторинга конфигурации для служб Azure

**Руководство**: Используйте псевдонимы политик Azure в пространстве имен "Microsoft.DBforPostgreSQL" для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Используйте политику Azure [аудит], [запретить] и [развернуть, если не существует], чтобы автоматически применять конфигурации для экземпляров базы данных Azure для PostgreSQL и связанных ресурсов.

Как настроить политику SQL Azure и управлять ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Реализация автоматизированного мониторинга конфигурации для операционных систем

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="711-manage-azure-secrets-securely"></a>7.11: Безопасное управление секретами Azure

**Руководство**: Для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, которые используются для доступа к экземплярам базы данных Azure для PostgreSQL, используйте Управляемое удостоверение службы в сочетании с Azure Key Vault, чтобы упростить и защитить базу данных Azure для управления секретами PostgreSQL. Убедитесь, что включено обратимое удаление в Azure Key Vault.

Интеграция с управляемыми удостоверениями Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Чтобы создать хранилище ключей, сделайте следующее: https://docs.microsoft.com/azure/key-vault/quick-create-portal

Проверка подлинности в Key Vault с помощью управляемого удостоверения: https://docs.microsoft.com/azure/key-vault/managed-identity

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Безопасное и автоматическое управление удостоверениями

**Руководство**: Сервер базы данных Azure для PostgreSQL поддерживает проверку подлинности Azure Active Directory для доступа к базам данных.  При создании сервера базы данных Azure для PostgreSQL вы предоставляете учетные данные для пользователя с правами администратора. Этот администратор может использоваться для создания дополнительных пользователей БД.  

Для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, которые используются для доступа к серверу базы данных Azure для PostgreSQL, используйте Управляемое удостоверение службы в сочетании с Azure Key Vault, чтобы хранить и получать учетные данные для сервера базы данных Azure для PostgreSQL. Убедитесь, что включено обратимое удаление в Azure Key Vault.

Функция управляемых удостоверений предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory (AD). Управляемое удостоверение можно использовать для проверки подлинности в любой службе, которая поддерживает проверку подлинности Azure AD, включая Key Vault, при этом не сохраняя каких-либо учетных данных в коде.

Настройка управляемых удостоверений: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Интеграция с управляемыми удостоверениями Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Устранение непреднамеренного раскрытия учетных данных

**Руководство**: Реализуйте сканер учетных данных для обнаружения учетных данных в коде. Сканер учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault.

Настройка сканера учетных данных: https://secdevtools.azurewebsites.net/helpcredscan.html

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в статье [Управление безопасностью: Защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

Защита от вредоносных программ Майкрософт включена на базовом узле, поддерживающем службы Azure (например, служба приложений Azure), но не выполняется в содержимом клиента.


**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся вычислительными

**Руководство**: Защита от вредоносных программ Майкрософт включена на базовом узле, поддерживающем службы Azure (например, база данных Azure для PostgreSQL), но не выполняется в содержимом клиента.

Предварительно сканируйте любое содержимое, отправляемое в невычислительные ресурсы Azure, например служба приложений, хранилище Data Lake, хранилище BLOB-объектов, база данных Azure для PostgreSQL и т. д. Корпорация Майкрософт не может получить доступ к данным в этих экземплярах.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Совмещаемая блокировка

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3. Своевременное обновление программного обеспечения для защиты от вредоносных программ и подписей

**Руководство**: Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

Защита от вредоносных программ Майкрософт включена на базовом узле, поддерживающем службы Azure (например, база данных Azure для PostgreSQL), но не выполняется в содержимом клиента.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в статье [Управление безопасностью: Восстановление данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Обеспечение регулярного автоматического резервного копирования

**Руководство**: База данных Azure для PostgreSQL создает резервные копии файлов данных и журнала транзакций. В зависимости от поддерживаемого максимального размера хранилища мы будем использовать полные и разностные резервные копии (максимум 4 ТБ на сервере) или моментальные снимки (до 16 ТБ на сервере). При помощи этих резервных копий вы можете восстановить сервер до любой точки во времени в пределах заданного срока хранения резервных копий. По умолчанию срок хранения резервных копий составляет 7 дней. При необходимости вы увеличить его вплоть до 35 дней. Все резервные копии шифруются с помощью 256-битового шифрования AES.

Как создать резервную копию сервера в базе данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Сведения о начальной конфигурации базы данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Совмещаемая блокировка

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Выполнение полного резервного копирования системы и любых ключей, управляемых клиентом

**Руководство**: База данных Azure для PostgreSQL автоматически создает резервные копии для сервера и сохраняет их в локальном избыточном или геоизбыточном хранилище — по выбору пользователя. Резервные копии можно использовать для восстановления сервера до точки во времени. Резервное копирование и восстановление данных являются важной частью любой стратегии непрерывности бизнес-процессов. Таким образом данные защищаются от случайного повреждения или удаления.

Если вы используете Azure Key Vault для хранения учетных данных для экземпляров базы данных Azure для PostgreSQL, обеспечьте регулярное автоматическое резервное копирование ключей.

Как создать резервную копию сервера в базе данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Резервное копирование ключей Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Мониторинг центра безопасности Azure**: Сейчас это недоступно

**Ответственность**: Совмещаемая блокировка

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Проверка всех резервных копий, включая управляемые клиентом ключи

**Руководство**: В службе "База данных Azure для PostgreSQL" при восстановлении создается новый сервер из резервных копий исходного сервера. Есть два типа восстановления: Восстановление на определенный момент времени и географическое восстановление. Восстановление на определенный момент времени доступно для любого типа избыточности резервного копирования. При таком восстановлении новый сервер создается в том же регионе, где расположен исходный сервер. Географическое восстановление доступно, если для сервера настроено геоизбыточное хранилище, и позволяет восстановить сервер в другом регионе.

Предполагаемое время восстановления будет зависеть от нескольких факторов, включая размер базы данных, размер журнала транзакций, пропускную способность сети и общее количество баз данных, восстанавливаемых в том же регионе и в то же время. Обычно время восстановления составляет менее 12 часов.

Периодическое тестирование восстановления экземпляров базы данных Azure для PostgreSQL.

Как создать резервную копию сервера в базе данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Обеспечение защиты резервных копий и управляемых клиентом ключей

**Руководство**: Служба "База данных Azure для PostgreSQL" создает полные, разностные резервные копии и резервные копии журналов. При помощи этих резервных копий вы можете восстановить сервер до любой точки во времени в пределах заданного срока хранения резервных копий. По умолчанию срок хранения резервных копий составляет 7 дней. При необходимости вы увеличить его вплоть до 35 дней. Все резервные копии шифруются с помощью 256-битового шифрования AES.

Резервное копирование и восстановление в базе данных Azure для PostgreSQL: https://docs.microsoft.com/azure/postgresql/concepts-backup

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="incident-response"></a>реагирование на инциденты.

*Дополнительные сведения см. в статье [Управление безопасностью: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Создание руководства по реагированию на инциденты

**Руководство**: Создание руководства по реагированию на инциденты для вашей организации. Убедитесь, что имеются письменные планы реагирования на инциденты, которые определяют все действия персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.

Как настроить автоматизацию рабочих процессов в центре безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Руководство по созданию собственного процесса реагирования на инциденты безопасности: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Анатомия инцидента Центра Майкрософт по реагированию на угрозы: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Клиент может также использовать руководство по обработке инцидентов безопасности компьютера от NIST, чтобы разработать собственный план реагирования на инциденты: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Создание процедуры оценки инцидента и определения приоритетов

**Руководство**: Центр безопасности назначает каждому оповещению серьезность, которая поможет определить, какие предупреждения следует расследовать первыми. Серьезность основывается на том, насколько уверен центр безопасности в исследовании или аналитике, используемой для оповещения, а также на уровне достоверности злонамеренности события, приведшего к оповещению.

Кроме того, четко помечайте подписки (для устар. производственной, непроизводственная) и создайте систему именования, чтобы четко определить и классифицировать ресурсы Azure.

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: Проверка процедур реагирования на угрозы

**Руководство**: Выполните упражнения, чтобы периодически протестировать возможности ваших систем реагировать на угрозы. Выявите слабые точки и пробелов и пересмотрите план по мере необходимости.

См. публикацию NIST: Руководство по тестированию, обучению и выполнению программ для ИТ-планов и возможностей: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Предоставление контактных сведений и настройка уведомлений по инцидентам безопасности

**Руководство**: Корпорация Майкрософт будет использовать информацию об инциденте безопасности для связи с вами, если центр Microsoft Security Response Center (MSRC) обнаружит, что к вашим пользовательским данным был получен незаконный или несанкционированный доступ.  Проверьте инциденты после факта обращения, чтобы убедиться, что проблемы устранены.

Как задать контакт безопасности Центра безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**: Экспортируйте оповещения и рекомендации центра безопасности Azure с помощью функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Вы можете использовать соединитель данных центра безопасности Azure для потоковой передачи метки предупреждений.

Настройка непрерывного экспорта данных: https://docs.microsoft.com/azure/security-center/continuous-export

Как выполнить потоковую передачу предупреждений в Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Автоматизация реагирования на оповещения системы безопасности

**Руководство**: Используйте функцию автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" в оповещениях и рекомендациях системы безопасности.

Как настроить автоматизацию рабочего процесса и Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в статье [Управление безопасностью: тесты на проникновение и попытки нарушения безопасности "красной командой"](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Регулярно проводите тестирование на проникновение ресурсов Azure и следите за исправлением всех критических точек безопасности в течение 60 дней

**Руководство**: Следуйте правилам взаимодействия Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Дополнительные сведения о стратегии корпорации Майкрософт и реализации "красной команды", а также о тестировании на основе уязвимости для облачной инфраструктуры, служб и приложений Майкрософт см. здесь: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Мониторинг центра безопасности Azure**: Неприменимо

**Ответственность**: Совмещаемая блокировка

## <a name="next-steps"></a>Дальнейшие действия

- См. [Производительность системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Дополнительные сведения о [Базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
