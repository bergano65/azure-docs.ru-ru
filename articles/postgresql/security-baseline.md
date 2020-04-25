---
title: Базовый уровень безопасности Azure для сервера базы данных Azure для PostgreSQL
description: Базовый уровень безопасности Azure для сервера базы данных Azure для PostgreSQL
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: a38bb12577fd646b2e7abf773dbb2a3f138f58fe
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127552"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql-single-server"></a>Базовый уровень безопасности Azure для сервера базы данных Azure для PostgreSQL

Базовый план безопасности Azure для сервера базы данных Azure для PostgreSQL содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовая информация для этой службы взята из [теста безопасности Azure версии 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), который содержит рекомендации по защите облачных решений в Azure с помощью наших рекомендаций.

Дополнительные сведения см. в статье [Общие сведения о базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в разделе Security [Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Защитите ресурсы с помощью групп безопасности сети или брандмауэра Azure в виртуальной сети.

**Руководство**. Настройка частного канала для базы данных Azure для PostgreSQL с частными конечными точками. Приватный канал позволяет подключаться к различным службам PaaS в Azure через частную конечную точку. Частная связь Azure, по сути, предоставляет службы Azure в частной виртуальной сети (VNet). Трафик между виртуальной сетью и экземпляром PostgreSQL перемещается в магистральную сеть Майкрософт.

Кроме того, вы можете использовать конечные точки службы виртуальной сети для защиты и ограничения сетевого доступа к базе данных Azure для реализации PostgreSQL. Правила виртуальной сети — это одна из функций безопасности брандмауэра, обеспечивающая управление приемом сервером Базы данных Azure для PostgreSQL подключений из определенных подсетей в виртуальных сетях.

Вы также можете защитить базу данных Azure для сервера PostgreSQL с помощью правил брандмауэра. Брандмауэр сервера предотвращает доступ к серверу базы данных, пока вы не укажете, какие компьютеры имеют разрешение. Для настройки брандмауэра можно создать правила брандмауэра, которые указывают диапазон допустимых IP-адресов. Правила брандмауэра можно создавать на уровне сервера.

Как настроить частный канал для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Как создать конечные точки службы виртуальной сети и правила виртуальной сети в базе данных Azure для PostgreSQL и управлять ими:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Как настроить правила брандмауэра базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-manage-firewall-using-portal

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Если база данных Azure для экземпляра PostgreSQL защищена частной конечной точкой, можно развернуть виртуальные машины в той же виртуальной сети. Чтобы снизить риск утечка данных, можно использовать группу безопасности сети (NSG). Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков NSG в рабочую область Log Analytics и использовать Аналитика трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитика трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять несетевые настройки.

Как настроить частный канал для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Как включить журналы потоков NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить и использовать Аналитика трафика:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="13-protect-critical-web-applications"></a>1,3: защита критически важных веб-приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: запретите обмен данными с известными вредоносными IP-адресами

**Руководство**. Использование Advanced Threat Protection для базы данных Azure для PostgreSQL. Расширенная защита от угроз обнаруживает аномальные действия, указывающие на необычные и потенциально опасные попытки доступа к базам данных или их использования.

Включите стандарт защиты от атак DDoS в виртуальных сетях, связанных с экземплярами базы данных Azure для PostgreSQL, чтобы защититься от атак от атак DDoS. Используйте интегрированную аналитику угроз центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми IP-адресами Интернета.

Как настроить расширенную защиту от угроз для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Настройка защиты от атак DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: запись сетевых пакетов и журналов потоков

**Руководство**. Если база данных Azure для экземпляра PostgreSQL защищена частной конечной точкой, можно развернуть виртуальные машины в той же виртуальной сети. Затем можно настроить группу безопасности сети (NSG), чтобы снизить риск утечка данных. Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков NSG в рабочую область Log Analytics и использовать Аналитика трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитика трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять несетевые настройки.

Как включить журналы потоков NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить и использовать Аналитика трафика:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание систем обнаружения вторжений на основе сети и предотвращения вторжений (ИДЕНТИФИКАТОРы и IP-адреса)

**Руководство**. Использование Advanced Threat Protection для базы данных Azure для PostgreSQL. Расширенная защита от угроз обнаруживает аномальные действия, указывающие на необычные и потенциально опасные попытки доступа к базам данных или их использования.

Как настроить расширенную защиту от угроз для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="17-manage-traffic-to-web-applications"></a>1,7. Управление трафиком к веб-приложениям

**Руководство**: неприменимо; Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: сократите сложность и издержки администрирования правил безопасности сети

**Руководство**. для ресурсов, которым требуется доступ к базе данных Azure для экземпляров PostgreSQL, используйте теги службы виртуальной сети для определения элементов управления доступом к сети в группах безопасности сети или брандмауэре Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, SQL). WestUs) в соответствующем поле источника или назначения правила можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, включенными в тег службы, и автоматически обновляет тег службы при изменении адресов.

Примечание. база данных Azure для PostgreSQL использует тег службы Microsoft. SQL.

Дополнительные сведения об использовании тегов служб:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Общие сведения об использовании тегов службы для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet#terminology-and-description

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определение и реализация стандартных конфигураций безопасности для сетевых параметров и сетевых ресурсов, связанных с экземплярами базы данных Azure для PostgreSQL, с помощью политики Azure. Используйте псевдонимы политик Azure в пространствах имен "Microsoft. Дбфорпостгрескл" и "Microsoft. Network" для создания настраиваемых политик для аудита или принудительного применения конфигурации сети для экземпляров базы данных Azure для PostgreSQL. Вы также можете использовать встроенные определения политик, связанные с сетью, или базу данных Azure для PostgreSQL экземпляров, например:

- Стандарт Защиты от атак DDoS должен быть включен.

- Для серверов баз данных PostgreSQL должно быть включено принудительное использование SSL-соединения

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Примеры политики Azure для работы в сети:https://docs.microsoft.com/azure/governance/policy/samples/

Создание Azure Blueprint.https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="110-document-traffic-configuration-rules"></a>1,10: правила конфигурации трафика документов

**Руководство**. Используйте теги для ресурсов, относящихся к сетевой безопасности и потоку трафика для экземпляров базы данных Azure для PostgreSQL, чтобы предоставить метаданные и логическую организацию.

Используйте любые встроенные определения политик Azure, связанные с разметкой, например "требовать тег и его значение", чтобы обеспечить создание всех ресурсов с помощью тегов и уведомления о существующих непомеченных ресурсах.

Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий с ресурсами на основе их тегов.

Создание и использование тегов:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11. использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений сетевых ресурсов, связанных с экземплярами базы данных Azure для PostgreSQL. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых ресурсов.

Просмотр и получение событий журнала действий Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Создание оповещений в Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в разделе [Security Control: logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: используйте источники синхронизации утвержденного времени

**Руководство**. Корпорация Майкрософт поддерживает источник времени, используемый для ресурсов Azure, например базу данных Azure для PostgreSQL для меток времени в журналах.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="22-configure-central-security-log-management"></a>2,2: Настройка централизованного управления журналом безопасности

**Руководство**. Включение параметров диагностики и журналов сервера и прием журналов для агрегирования данных безопасности, создаваемых экземплярами базы данных Azure для PostgreSQL. В Azure Monitor используйте рабочие области Log Analytics для запроса и выполнения анализа и используйте учетные записи хранения Azure для долгосрочного и архивного хранения. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

Как настроить и получить доступ к журналам сервера для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Как настроить и получить доступ к журналам аудита для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Как подключить метку Azure:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Включите ведение журнала аудита для ресурсов Azure.

**Руководство**. Включение параметров диагностики в базе данных Azure для экземпляров PostgreSQL для доступа к журналам аудита, безопасности и ресурсов. Убедитесь, что вы специально включили журнал аудита PostgreSQL. Журналы действий, которые автоматически доступны, включают источник событий, дату, пользователя, метку времени, исходные адреса, адреса назначения и другие полезные элементы. Вы также можете включить параметры диагностики журнала действий Azure и отправить журналы в ту же Log Analytics рабочую область или учетную запись хранения.

Как настроить и получить доступ к журналам сервера для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Как настроить и получить доступ к журналам аудита для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Как настроить параметры диагностики для журнала действий Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: получение журналов безопасности из операционных систем

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="25-configure-security-log-storage-retention"></a>2,5: Настройка хранения журнала безопасности

**Руководство**. в Azure Monitor для рабочей области log Analytics, используемой для хранения журналов базы данных Azure для PostgreSQL, задайте срок хранения согласно нормативным требованиям Организации. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения.

Как задать параметры хранения журнала для рабочих областей Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Хранение журналов ресурсов в учетной записи хранения Azure:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="26-monitor-and-review-logs"></a>2,6: мониторинг и просмотр журналов

**Руководство**. анализ и мониторинг журналов из базы данных Azure для экземпляров PostgreSQL для аномального поведения. Используйте Log Analytics Azure Monitor для просмотра журналов и выполнения запросов к данным журнала. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

Как подключить метку Azure:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Дополнительные сведения о Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Как выполнять пользовательские запросы в Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Включение оповещений для аномальных действий

**Руководство**по включению расширенной защиты от угроз для службы "база данных Azure для PostgreSQL". Расширенная защита от угроз обнаруживает аномальные действия, указывающие на необычные и потенциально опасные попытки доступа к базам данных или их использования.

Кроме того, вы можете включить журналы сервера и параметры диагностики для PostgreSQL и отправить журналы в рабочую область Log Analytics. Подключите рабочую область Log Analytics к Azure Sentinel, так как она предоставляет решение автоматического реагирования на системы безопасности (ВЗЛЕТЕЛ). Это позволяет создавать и использовать модули PlayBook (автоматизированные решения) для устранения проблем безопасности.

Как включить расширенную защиту от угроз для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Как настроить и получить доступ к журналам сервера для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Как настроить и получить доступ к журналам аудита для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Как настроить параметры диагностики для журнала действий Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Как подключить метку Azure:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="28-centralize-anti-malware-logging"></a>2,8: централизованное ведение журнала защиты от вредоносных программ

**Руководство**: неприменимо; База данных Azure для PostgreSQL не обрабатывает и не создает журналы, связанные с защитой от вредоносных программ.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="29-enable-dns-query-logging"></a>2,9: Включение ведения журнала запросов DNS

**Руководство**: неприменимо; База данных Azure для PostgreSQL не обрабатывает и не создает журналы, связанные с DNS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="210-enable-command-line-audit-logging"></a>2,10: Включение ведения журнала аудита в командной строке

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Дополнительные сведения см. в разделе [Управление безопасностью: идентификация и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: ведение инвентаризации учетных записей администраторов

**Руководство**. поддержание инвентаризации учетных записей пользователей, имеющих административный доступ к плоскости управления (например, портал Azure) для экземпляров базы данных Azure для PostgreSQL. Кроме того, необходимо поддерживать инвентаризацию учетных записей администраторов, имеющих доступ к плоскости данных (в самой базе данных) для экземпляров базы данных Azure для PostgreSQL. (При создании сервера PostgreSQL Вы предоставляете учетные данные для пользователя с правами администратора. Этот администратор может использовать для создания дополнительных пользователей PostgreSQL.)

База данных Azure для PostgreSQL не поддерживает встроенные функции управления доступом на основе ролей, но можно создавать пользовательские роли на основе конкретных операций с поставщиками ресурсов.

Общие сведения о пользовательских ролях для подписки Azure:https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Общие сведения об операциях поставщика ресурсов базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql 

Общие сведения об управлении доступом для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-security#access-management

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Измените пароли по умолчанию, если это применимо

**Руководство**. Azure Active Directory и база данных Azure для PostgreSQL не имеют концепции паролей по умолчанию.

После создания самого ресурса базы данных Azure для PostgreSQL Azure принудительно создает для пользователя с правами администратора надежный пароль. Однако после создания экземпляра PostgreSQL можно использовать учетную запись первого администратора сервера, созданную для создания дополнительных пользователей и предоставления административного доступа к ним. При создании этих учетных записей убедитесь, что для каждой учетной записи настроен другой надежный пароль.

Как создать дополнительные учетные записи для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users

Обновление пароля администратора:https://docs.microsoft.com/azure/postgresql/howto-create-manage-server-portal#update-admin-password

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: используйте выделенные учетные записи администратора

**Руководство**. Создание стандартных рабочих процедур, связанных с использованием выделенных административных учетных записей, имеющих доступ к экземплярам базы данных Azure для PostgreSQL. Используйте управление удостоверениями и доступом центра безопасности Azure для мониторинга количества административных учетных записей. 

Сведения об удостоверении и доступе центра безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access 

Сведения о создании пользователей с правами администратора в базе данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4. Использование единого входа (SSO) с Azure Active Directory

**Руководство**. вход в базу данных Azure для PostgreSQL поддерживается как с помощью имени пользователя, так и пароля, настроенного непосредственно в базе данных, а также с использованием удостоверения Azure Active Directory (AD) и использования маркера Azure AD для подключения. При использовании маркера Azure AD поддерживаются различные методы, например пользователь Azure AD, Группа Azure AD или приложение Azure AD, подключающиеся к базе данных.

По отдельности доступ к плоскости управления для PostgreSQL доступен через REST API и поддерживает единый вход. Для проверки подлинности задайте заголовок авторизации для запросов к JSON Web Token, полученному из Azure Active Directory.

Используйте Azure Active Directory для проверки подлинности в базе данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Общие сведения о базе данных Azure для PostgreSQL REST API:https://docs.microsoft.com/rest/api/postgresql/

Общие сведения об использовании единого входа в Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: используйте многофакторную проверку подлинности для всех Azure Active Directoryного доступа

**Руководство**. Включение многофакторной проверки подлинности Azure Active Directory (MFA) и следование рекомендациям по управлению удостоверениями и доступом в центре безопасности Azure. При использовании маркеров Azure AD для входа в базу данных это позволяет требовать многофакторную проверку подлинности для входа в базу данных.

Как включить MFA в Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Используйте Azure Active Directory для проверки подлинности в базе данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Мониторинг удостоверений и доступа в центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: используйте выделенные компьютеры (рабочие станции с привилегированным доступом) для всех административных задач

**Руководство**. Использование рабочих станций привилегированного доступа (лапы) с настроенной многофакторной проверкой подлинности (MFA) для входа и настройки ресурсов Azure.

Дополнительные сведения о рабочих станциях с привилегированным доступом:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Как включить MFA в Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: регистрация и оповещение о подозрительных действиях из учетных записей администраторов

**Руководство**по включению расширенной защиты от угроз для службы "база данных Azure для PostgreSQL" для создания оповещений о подозрительных действиях.

Кроме того, вы можете использовать Azure Active Directory (AD) управление привилегированными пользователями (PIM) для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде.

Используйте обнаружение рисков Azure AD для просмотра предупреждений и отчетов о поведении опасного пользователя.

Как настроить расширенную защиту от угроз для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Развертывание управление привилегированными пользователями (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Общие сведения об обнаружении рисков Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: управление ресурсами Azure из только утвержденных расположений

**Руководство**. Используйте условный доступ с именованными расположениями, чтобы разрешить порталу и Azure Resource Manager доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

Как настроить именованные расположения в Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="39-use-azure-active-directory"></a>3,9: используйте Azure Active Directory

**Руководство**. Использование Azure Active Directory (AD) в качестве централизованной системы проверки подлинности и авторизации. Azure AD защищает данные с помощью надежного шифрования для неактивных и транзитных данных. Кроме того, в Azure AD имеются Salt-записи, хэши и безопасно хранят учетные данные пользователя.

Для входа в базу данных Azure для PostgreSQL рекомендуется использовать Azure AD и использовать маркер Azure AD для подключения. При использовании маркера Azure AD поддерживаются различные методы, например пользователь Azure AD, Группа Azure AD или приложение Azure AD, подключающиеся к базе данных.

Учетные данные Azure AD также могут использоваться для администрирования на уровне плоскости управления (например, портал Azure) для управления учетными записями администратора PostgreSQL.

Используйте Azure Active Directory для проверки подлинности в базе данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: регулярно просматривайте и выверяйте доступ пользователей

**Руководство**. Изучите журналы Azure Active Directory, чтобы упростить поиск устаревших учетных записей, которые могут включать в себя роли администраторов базы данных Azure для PostgreSQL. Кроме того, используйте проверку доступа удостоверений Azure для эффективного управления членством в группах, доступа к корпоративным приложениям, которые могут использоваться для доступа к базе данных Azure для PostgreSQL, и назначения ролей. Доступ пользователей следует проверять регулярно, например каждые 90 дней, чтобы убедиться, что доступ к ним имеют только нужные пользователи.

Общие сведения об отчетах Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Использование проверок доступа для идентификации Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Проверка пользователей PostgreSQL и назначенных ролей:https://www.postgresql.org/docs/current/database-roles.html

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: монитор пытается получить доступ к отключенным учетным записям

**Руководство**. Включение параметров диагностики для базы данных Azure для PostgreSQL и Azure Active Directory, отправка всех журналов в log Analytics рабочую область. Настройте необходимые оповещения (например, неудачные попытки проверки подлинности) в Log Analytics.

Как настроить и получить доступ к журналам сервера для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Как настроить и получить доступ к журналам аудита для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Как интегрировать журналы действий Azure в Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: предупреждение об отклонении режима входа учетной записи

**Руководство**по включению расширенной защиты от угроз для службы "база данных Azure для PostgreSQL" для создания оповещений о подозрительных действиях.

Используйте функции защиты идентификации и обнаружения рисков Azure Active Directory, чтобы настроить автоматические ответы на обнаруженные подозрительные действия. Вы можете включить автоматические ответы с помощью Azure Sentinel, чтобы реализовать реагирование на безопасность вашей организации.

Вы также можете принять журналы в Azure Sentinel для дальнейшего изучения.

Как настроить расширенную защиту от угроз для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Общие сведения о защита идентификации Azure AD:https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Как просмотреть рискованные входы в Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Как подключить метку Azure:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: предоставьте корпорации Майкрософт доступ к соответствующим пользовательским данным во время сценариев поддержки

**Руководство**: Сейчас недоступно; Защищенное хранилище еще не поддерживается для базы данных Azure для PostgreSQL.

Список поддерживаемых защищенное хранилище служб:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в разделе [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: поддержание инвентаризации конфиденциальной информации

**Руководство**. Использование тегов для отслеживания экземпляров базы данных Azure для PostgreSQL или связанных ресурсов, в которых хранятся или обрабатываются конфиденциальные данные.

Создание и использование тегов:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Изолируйте системы, хранящие или обрабатывающие конфиденциальные данные

**Руководство**. Реализация отдельных подписок и групп управления для разработки, тестирования и производства. Используйте сочетание частной связи, конечных точек службы и (или) правил брандмауэра, чтобы изолировать и ограничить сетевой доступ к базе данных Azure для экземпляров PostgreSQL.

Как создать дополнительные подписки Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание Группы управления:https://docs.microsoft.com/azure/governance/management-groups/create

Как настроить частный канал для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Как создать конечные точки службы виртуальной сети и правила виртуальной сети в базе данных Azure для PostgreSQL и управлять ими:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Как настроить правила брандмауэра базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules

**Мониторинг центра безопасности Azure**: недоступен

**Ответственность**: клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Отслеживайте и блокируйте несанкционированную пересылку конфиденциальной информации

**Рекомендации**. при использовании виртуальных машин Azure для доступа к базе данных Azure для PostgreSQL используйте частные ссылки, конфигурации сети PostgreSQL, группы безопасности сети и теги службы, чтобы уменьшить вероятность возможного использования утечка данных.

Корпорация Майкрософт управляет базовой инфраструктурой базы данных Azure для PostgreSQL и реализовала четкие элементы управления для предотвращения потери или раскрытия данных клиента.

Как устранить утечка данных для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link

Общие сведения о защите данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Общая

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: шифрование всех конфиденциальных данных во время передачи

**Руководство**. база данных Azure для PostgreSQL поддерживает подключение сервера PostgreSQL к клиентским приложениям с помощью SSL (SSL). Применение SSL-соединений между сервером базы данных и клиентскими приложениями обеспечивает защиту от атак "злоумышленник в середине" за счет шифрования потока данных между сервером и приложением. В портал Azure убедитесь, что по умолчанию включено "принудительное подключение SSL" для всех экземпляров базы данных Azure для PostgreSQL.

Сейчас версия TLS, поддерживаемая для базы данных Azure для PostgreSQL, — это TLS 1,0, TLS 1,1, TLS 1,2.

Как настроить шифрование при передаче для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Общая

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: использование активного средства обнаружения для распознавания конфиденциальных данных

**Руководство**. функции для идентификации данных, классификации и предотвращения потерь еще не доступны для базы данных Azure для PostgreSQL. Реализуйте сторонние решения, если это необходимо для обеспечения соответствия требованиям.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и имеет большую длину для защиты от потери данных и доступности клиентов. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

Общие сведения о защите данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: Общая

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6. Использование Azure RBAC для управления доступом к ресурсам

**Руководство**. Использование управления доступом на основе РОЛЕЙ (RBAC) Azure для контроля доступа к плоскости управления базы данных Azure для PostgreSQL (например, портал Azure). Для доступа к плоскости данных (в самой базе данных) используйте SQL-запросы для создания пользователей и настройки разрешений пользователей. RBAC не влияет на разрешения пользователей в базе данных.

Как настроить RBAC в Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Как настроить доступ пользователей с помощью SQL для базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

Корпорация Майкрософт управляет базовой инфраструктурой базы данных Azure для PostgreSQL и реализовала четкие элементы управления для предотвращения потери или раскрытия данных клиента.

Общие сведения о защите данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: шифрование конфиденциальной информации при хранении

**Руководство**. Служба базы данных Azure для PostgreSQL использует проверенный криптографический модуль FIPS 140-2 для шифрования неактивных данных. Данные, включая резервные копии, шифруются на диске, за исключением временных файлов, созданных во время выполнения запросов. Служба использует 256-разрядный шифр AES, включенный в шифрование службы хранилища Azure. Ключами управляет система. Шифрование хранилища всегда включено, и его нельзя отключить.

Шифрование данных с помощью ключей, управляемых клиентом (CMK) для базы данных Azure для PostgreSQL Single Server, позволяет создавать собственный ключ (BYOK) для защиты данных. В настоящее время необходимо запросить доступ, чтобы использовать эту возможность. Для этого обратитесь к:

AskAzureDBforPostgreSQL@service.microsoft.com.

Общие сведения о шифровании неактивных данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-security

Общие сведения о шифровании неактивных данных Azure для PostgreSQL с помощью управляемых клиентом ключей:https://docs.microsoft.com/azure/postgresql/concepts-data-encryption-postgresql


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: журналы и оповещения об изменениях критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналом действий Azure для создания оповещений о том, когда изменения выполняются в рабочих экземплярах базы данных Azure для PostgreSQL и других критических или связанных ресурсах.

Создание оповещений для событий журнала действий Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="vulnerability-management"></a>Управление уязвимостями

*Дополнительные сведения см. в разделе [Security Control: управлением уязвимостей](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: Запуск автоматизированных средств проверки уязвимостей

**Руководство**: Сейчас недоступно; Центр безопасности Azure пока не поддерживает оценку уязвимостей для базы данных Azure для PostgreSQL.

Покрытие компонентов для служб Azure PaaS в центре безопасности Azure:https://docs.microsoft.com/azure/security-center/features-paas

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Развертывание автоматизированного решения по управлению исправлениями сторонних производителей

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: сравнение проверок уязвимостей "назад-назад"

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5. Использование процесса оценки рисков для определения приоритета устранения обнаруженных уязвимостей

**Руководство**. Корпорация Майкрософт выполняет Управление уязвимостью в базовых системах, поддерживающих базу данных Azure для PostgreSQL.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в разделе [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1. Использование обнаружения ресурсов Azure

**Руководство**. Использование графа ресурсов Azure для запроса и обнаружения всех ресурсов (включая базу данных Azure для PostgreSQL) в ваших подписках. Убедитесь, что у вас есть соответствующие разрешения (на чтение) в клиенте и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

Как создавать запросы с помощью графа ресурсов Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Как просмотреть подписки Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Общие сведения об Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="62-maintain-asset-metadata"></a>6,2: сохранение метаданных активов

**Руководство**. применение тегов к базе данных Azure для экземпляров PostgreSQL и другим связанным ресурсам, предоставляя метаданные для логической организации их в таксономию.

Создание и использование тегов:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: удалите неавторизованные ресурсы Azure

**Руководство**. Использование тегов, групп управления и отдельных подписок (при необходимости) для Организации и мониторинга базы данных Azure для экземпляров PostgreSQL и связанных ресурсов. Регулярно выверка инвентаризации и своевременное удаление неавторизованных ресурсов из подписки.

Как создать дополнительные подписки Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание Группы управления:https://docs.microsoft.com/azure/governance/management-groups/create

Создание и использование тегов:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: ведение инвентаризации утвержденных ресурсов Azure и наименований программного обеспечения

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами и Azure в целом.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

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

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами и Azure в целом.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="68-use-only-approved-applications"></a>6,8: Используйте только утвержденные приложения

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

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

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: ограничьте возможность пользователей взаимодействовать с диспетчером ресурсов Azure с помощью сценариев

**Руководство**. Используйте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "блокировать доступ" для приложения "Управление Microsoft Azure". Это может препятствовать созданию и изменению ресурсов в среде с высоким уровнем безопасности, например экземплярами базы данных Azure для PostgreSQL, содержащими конфиденциальные сведения.

Как настроить условный доступ для блокировки доступа к Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: ограничьте возможность пользователей выполнять сценарии в ресурсах вычислений

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: физическое или логическое разделение приложений с высоким риском

**Руководство**: неприменимо; Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="secure-configuration"></a>Настройка безопасности

*Дополнительные сведения см. в разделе [Security Control: Secure Configuration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Установите безопасные конфигурации для всех ресурсов Azure.

**Руководство**. Определение и реализация стандартных конфигураций безопасности для экземпляров базы данных Azure для PostgreSQL с помощью политики Azure. Используйте псевдонимы политик Azure в пространстве имен Microsoft. Дбфорпостгрескл, чтобы создать настраиваемые политики для аудита или принудительно применить конфигурацию сети для экземпляров базы данных Azure для PostgreSQL. Вы также можете использовать встроенные определения политик, связанные с экземплярами базы данных Azure для PostgreSQL, например:

- Для серверов баз данных PostgreSQL должно быть включено принудительное использование SSL-соединения

- На серверах баз данных PostgreSQL должны быть включены журналы подключений

Просмотр доступных псевдонимов политик Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Установка безопасных конфигураций операционной системы

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: поддержание защищенных конфигураций ресурсов Azure

**Руководство**. Использование политики Azure [Deny] и [развертывание если не существует] для обеспечения безопасности параметров в ресурсах Azure.

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Общие сведения о влиянии политики Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: поддержание защищенных конфигураций операционных систем

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: безопасное хранение конфигурации ресурсов Azure

**Руководство**. Если вы используете пользовательские определения политики Azure для экземпляров PostgreSQL и связанных ресурсов, используйте Azure Repos для безопасного хранения кода и управления им.

Как сохранить код в Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Документация по Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: безопасное хранение пользовательских образов операционной системы

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: развертывание средств управления конфигурацией системы

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. дбфорпостгрескл для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией системы для операционных систем

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9. Реализация автоматического мониторинга конфигурации для служб Azure

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. дбфорпостгрескл для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Используйте политику Azure [аудит], [запретить] и [развернуть, если не существует], чтобы автоматически применять конфигурации для экземпляров базы данных Azure для PostgreSQL и связанных ресурсов.

Как настроить политику Azure и управлять ей:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10. Реализация автоматического мониторинга конфигурации для операционных систем

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="711-manage-azure-secrets-securely"></a>7,11: безопасное управление секретами Azure

**Рекомендации**. для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, которые используются для доступа к экземплярам базы данных Azure для PostgreSQL, используйте управляемое удостоверение службы в сочетании с Azure Key Vault, чтобы упростить и защитить базу данных Azure для управления секретами PostgreSQL. Убедитесь, что Key Vault обратимое удаление включено.

Как интегрироваться с управляемыми удостоверениями Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Создание Key Vault.https://docs.microsoft.com/azure/key-vault/quick-create-portal

Как обеспечить проверку подлинности Key Vault с помощью управляемого удостоверения:https://docs.microsoft.com/azure/key-vault/managed-identity

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: безопасно управляйте удостоверениями и автоматически

**Руководство**. сервер базы данных Azure для PostgreSQL поддерживает проверку подлинности Azure Active Directory (Предварительная версия) для доступа к базам данных.  При создании базы данных Azure для сервера PostgreSQL Вы предоставляете учетные данные для пользователя с правами администратора. Этот администратор может использоваться для создания дополнительных пользователей базы данных.  

Для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, которые используются для доступа к серверу базы данных Azure для PostgreSQL, используйте Управляемое удостоверение службы в сочетании с Azure Key Vault для хранения и извлечения учетных данных для сервера базы данных Azure для PostgreSQL. Убедитесь, что Key Vault обратимое удаление включено.

Используйте управляемые удостоверения для предоставления служб Azure с автоматически управляемым удостоверением в Azure Active Directory (AD). Управляемые удостоверения позволяют проходить проверку подлинности в любой службе, поддерживающей проверку подлинности Azure AD, включая Key Vault без каких бы то ни было учетных данных в коде.

Как настроить управляемые удостоверения:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Как интегрироваться с управляемыми удостоверениями Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: устранение непреднамеренной раскрытия учетных данных

**Руководство**. Реализация средства проверки учетных данных для указания учетных данных в коде. Средство проверки учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault.

Настройка средства проверки учетных данных:https://secdevtools.azurewebsites.net/helpcredscan.html

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в разделе [Security Control: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

Защита от вредоносных программ Майкрософт включена на базовом узле, поддерживающем службы Azure (например, служба приложений Azure), но не выполняется в содержимом клиента.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся средами вычислений

**Руководство**. Защита от вредоносных программ Майкрософт включена на базовом узле, поддерживающем службы Azure (например, база данных Azure для PostgreSQL), но не выполняется в содержимом клиента.

Предварительное сканирование любого содержимого, отправляемого в Нерасчетные ресурсы Azure, например служба приложений, Data Lake Storage, хранилище BLOB-объектов, база данных Azure для PostgreSQL и т. д. Корпорация Майкрософт не может получить доступ к данным в этих экземплярах.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Убедитесь, что программное обеспечение и подписи для защиты от вредоносных программ обновлены

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

Защита от вредоносных программ Майкрософт включена на базовом узле, поддерживающем службы Azure (например, база данных Azure для PostgreSQL), но не выполняется в содержимом клиента.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в разделе [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Обеспечьте регулярную автоматическую архивацию

**Руководство**. база данных Azure для PostgreSQL создает резервные копии файлов данных и журнала транзакций. В зависимости от поддерживаемого максимального размера хранилища мы будем использовать полные и разностные резервные копии (максимум 4 ТБ серверов хранения) или резервные копии моментальных снимков (до 16 ТБ серверов хранилища). При помощи этих резервных копий вы можете восстановить сервер до любой точки во времени в пределах заданного срока хранения резервных копий. По умолчанию срок хранения резервных копий составляет 7 дней. При необходимости вы увеличить его вплоть до 35 дней. Все резервные копии шифруются с помощью 256-битового шифрования AES.

Как создать резервную копию сервера в базе данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Изучите начальную конфигурацию базы данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом.

**Руководство**. база данных Azure для PostgreSQL автоматически создает резервные копии сервера и сохраняет их в локально избыточном или геоизбыточном хранилище в соответствии с выбором пользователя. Резервные копии можно использовать для восстановления сервера до точки во времени. Резервное копирование и восстановление данных являются важной частью любой стратегии непрерывности бизнес-процессов. Таким образом данные защищаются от случайного повреждения или удаления.

Если вы используете Azure Key Vault для хранения учетных данных для экземпляров базы данных Azure для PostgreSQL, обеспечьте регулярное автоматическое резервное копирование ключей.

Как создать резервную копию сервера в базе данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Резервное копирование Key Vault ключей:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: Общая

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Руководство**. в базе данных Azure для PostgreSQL при восстановлении создается новый сервер из резервных копий исходного сервера. Доступно два типа восстановления: восстановление до точки во времени и геовосстановление. Восстановление до точки во времени доступно для любого типа избыточности резервного копирования. При таком восстановлении новый сервер создается в том же регионе, где расположен исходный сервер. Географическое восстановление доступно, если для сервера настроено геоизбыточное хранилище, и позволяет восстановить сервер в другом регионе.

Предполагаемое время восстановления будет зависеть от нескольких факторов, включая размер базы данных, размер журнала транзакций, пропускную способность сети и общее количество баз данных, восстанавливаемых в том же регионе и в то же время. Обычно время восстановления составляет менее 12 часов.

Периодическое тестирование восстановления базы данных Azure для экземпляров PostgreSQL.

Как создать резервную копию сервера в базе данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и управляемых клиентом ключей

**Руководство**. база данных Azure для PostgreSQL принимает полные, разностные резервные копии и журналы транзакций. При помощи этих резервных копий вы можете восстановить сервер до любой точки во времени в пределах заданного срока хранения резервных копий. По умолчанию срок хранения резервных копий составляет 7 дней. При необходимости вы увеличить его вплоть до 35 дней. Все резервные копии шифруются с помощью 256-битового шифрования AES.

Общие сведения о резервном копировании и восстановлении в базе данных Azure для PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-backup

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

## <a name="incident-response"></a>реагирование на инциденты.

*Дополнительные сведения см. в разделе [контроль безопасности: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: создайте руководство по реагированию на инциденты

**Руководство**. Создание руководства по реагированию на инциденты для вашей организации. Убедитесь, что имеются письменные планы реагирования на инциденты, которые определяют все роли персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.

Как настроить автоматизацию рабочих процессов в центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Руководство по созданию собственного процесса реагирования на инциденты безопасности:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Анатомия инцидента Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Клиент может также использовать руководство по обработке инцидентов безопасности компьютера NIST, чтобы помочь в создании собственного плана реагирования на инциденты:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: создание оценки инцидента и процедуры определения приоритетов

**Руководство**. Центр безопасности назначает серьезность каждому оповещению, чтобы вы могли определить, какие предупреждения следует изучить первыми. Серьезность основывается на том, насколько надежным является центр безопасности в поиске или аналитике, используемой для выдаче оповещения, а также об уровне достоверности, который был вредоносной задачей, вызвавшей оповещение.

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

## <a name="next-steps"></a>Следующие шаги

- Ознакомьтесь со статьей о [производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Дополнительные сведения о [базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
