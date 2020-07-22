---
title: Базовая конфигурация безопасности для базы данных Azure для MySQL (отдельный сервер)
description: Базовая конфигурация безопасности для базы данных Azure для MySQL (отдельный сервер)
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 02050745e7c8a9aa05d3f2de63a4bc5f1ebf8318
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654776"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Базовая конфигурация безопасности для базы данных Azure для MySQL (отдельный сервер)

Базовая конфигурация безопасности для базы данных Azure для MySQL содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовая конфигурация безопасности для этой службы взята из [теста производительности системы Azure версии 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), содержащего рекомендации по обеспечению безопасности облачных решений в Azure с помощью наших практических рекомендаций.

Дополнительные сведения см. в статье [Обзор базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в статье [Управление безопасностью: безопасность сети](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1. Защита ресурсов в виртуальной сети с помощью групп безопасности сети или Брандмауэра Azure

**Рекомендации**. Настройте приватный канал для базы данных Azure для MySQL с частными конечными точками. Приватный канал позволяет подключаться к различным службам PaaS в Azure через частную конечную точку. Приватный канал Azure, по сути, предоставляет службы Azure в частной виртуальной сети (VNet). Трафик между виртуальной сетью и экземпляром MySQL проходит через магистральную сеть Майкрософт.

Кроме того, вы можете использовать конечные точки службы виртуальной сети для защиты и ограничения сетевого доступа к реализациям базы данных Azure для MySQL. Правила виртуальной сети — это одна из функций безопасности брандмауэра, обеспечивающая управление приемом сервером базы данных Azure для MySQL подключений из определенных подсетей в виртуальных сетях.

Вы можете обезопасить сервер базы данных Azure для MySQL правилами брандмауэра. Брандмауэр сервера запрещает любой доступ к серверу базы данных, пока вы не укажете компьютеры, у которых есть разрешение на доступ. Для настройки брандмауэра можно создать правила брандмауэра, которые указывают диапазон допустимых IP-адресов. Правила брандмауэра можно создавать на уровне сервера.

Настройка приватного канала для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Настройка правил и конечных точек службы VNet и правил VNet в базе данных Azure для MySQL и управление ими: https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Настройка правил брандмауэра базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal

**Мониторинг Центра безопасности Azure**: Недоступно

**Ответственность**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Рекомендации**. Если экземпляр базы данных Azure для MySQL защищен частной конечной точкой, вы можете развернуть виртуальные машины в той же виртуальной сети. Чтобы снизить риск утечки данных, можно использовать группу безопасности сети (NSG). Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков NSG в рабочую область Log Analytics и использовать аналитику трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитики трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять некорректные сетевые настройки.

Настройка приватного канала для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Включение журналов потоков NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Включение и использование Аналитики трафика: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3. Защита критических веб-приложений

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4. Запрет взаимодействия с известными опасными IP-адресами

**Рекомендации**. Используйте Расширенную защиту от угроз для базы данных Azure для MySQL. Расширенная защита от угроз позволяет выявить подозрительную активность, указывающую на необычные и потенциально опасные попытки получить доступ к базам данных или воспользоваться ими.

Включите стандарт защиты от атак DDoS в виртуальных сетях, связанных с экземплярами базы данных Azure для MySQL, чтобы защититься от атак типа DDoS. Используйте интегрированную аналитику угроз Центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми IP-адресами Интернета.

Настройка Расширенной защиты от угроз для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Настройка защиты от атак DDoS: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5. Запись сетевых пакетов и журналов потоков

**Рекомендации**. Если экземпляр базы данных Azure для MySQL защищен частной конечной точкой, вы можете развернуть виртуальные машины в той же виртуальной сети. Чтобы снизить риск утечки данных, можно настроить группу безопасности сети (NSG). Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков NSG в рабочую область Log Analytics и использовать аналитику трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитики трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять некорректные сетевые настройки.

Включение журналов потоков NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Включение и использование Аналитики трафика: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Развертывание сетевых систем обнаружения и предотвращения вторжений (IDS/IPS)

**Рекомендации**. Используйте Расширенную защиту от угроз для базы данных Azure для MySQL. Расширенная защита от угроз позволяет выявить подозрительную активность, указывающую на необычные и потенциально опасные попытки получить доступ к базам данных или воспользоваться ими.

Настройка Расширенной защиты от угроз для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7. Управление трафиком к веб-приложениям

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Уменьшение сложности и дополнительных затрат на администрирование в правилах безопасности сети

**Рекомендации**. Для ресурсов, которым требуется доступ к экземплярам базы данных Azure для MySQL, используйте теги службы виртуальной сети для определения элементов управления доступом к сети в группах безопасности сети или брандмауэре Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, SQL.WestUs) в соответствующем исходном поле или поле назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, входящих в тег службы, и автоматически обновляет этот тег при изменении адресов.

Примечание. База данных Azure для MySQL использует тег службы "Microsoft.Sql".

Дополнительные сведения об использовании тегов служб: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Общие сведения об использовании тегов службы для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9. Поддержание стандартных конфигураций безопасности для сетевых устройств

**Рекомендации**. Определите и реализуйте стандартные конфигурации безопасности для сетевых настроек и сетевых ресурсов, связанных с вашими экземплярами базы данных Azure для MySQL, с помощью политики Azure. Используйте псевдонимы политик Azure в пространствах имен "Microsoft.DBforMySQL" и "Microsoft. Network" для создания настраиваемых политик, предназначенных для аудита или принудительного применения конфигурации сети для экземпляров базы данных Azure для MySQL. Вы также можете использовать встроенные определения политик, связанные с сетью, или экземпляры базы данных Azure для MySQL, например:

- Стандарт Защиты от атак DDoS должен быть включен.

- Для серверов баз данных MySQL должно быть включено принудительное использование SSL-соединения

Настройка Политики SQL Azure и управление ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Примеры Политики Azure для работы с сетью: https://docs.microsoft.com/azure/governance/policy/samples/

Создание схемы Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10. Документация по правилам конфигурации трафика

**Рекомендации**. Используйте теги для ресурсов, связанных с сетевой безопасностью и потоком трафика, для экземпляров базы данных Azure для MySQL, чтобы предоставить метаданные и логическую организацию.

Используйте любые встроенные определения политик Azure, связанные с разметкой, например "требовать тег и его значение", чтобы обеспечить создание всех ресурсов с помощью тегов и уведомления о существующих непомеченных ресурсах.

Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий с ресурсами на основе их тегов.

Создание и использование тегов: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Рекомендации**. Используйте журнал действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений для сетевых ресурсов, связанных с экземплярами базы данных Azure для MySQL. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых ресурсов.

Просмотр и извлечение событий журнала действий Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Создание оповещений в службе Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в статье [Управление безопасностью: ведение журналов и мониторинг](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1. Использование утвержденных источников синхронизации времени

**Рекомендации**. Корпорация Майкрософт поддерживает источники времени для ресурсов Azure, например базу данных Azure для MySQL для меток времени в журналах.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2. Настройка централизованного управления журналами безопасности

**Рекомендации**. Включите параметры диагностики и журналы сервера и включайте журналы для агрегирования данных безопасности, создаваемых экземплярами базы данных Azure для MySQL. В Azure Monitor используйте рабочие области Log Analytics для запроса и выполнения анализа, а учетные записи хранения Azure — для долгосрочного и архивного хранения. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

Общие сведения о журналах сервера в службе "База данных Azure для MySQL": https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Подключение Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг Центра безопасности Azure**: Недоступно

**Ответственность**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Включение журналов аудита для ресурсов Azure

**Рекомендации**. Включите параметры диагностики в экземплярах базы данных Azure для MySQL, чтобы получить доступ к журналам аудита, безопасности и ресурсов. Убедитесь, что вы включили именно журнал аудита MySQL. Автоматически доступны журналы действий, включающие источник событий, дату, пользователя, метку времени, исходные адреса, адреса назначения и другие полезные элементы. Вы также можете включить параметры диагностики журнала действий Azure и отправить журналы в ту же рабочую область Log Analytics или учетную запись хранения.

Общие сведения о журналах сервера в службе "База данных Azure для MySQL": https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Настройка и предоставление доступа к журналам медленных запросов для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Настройка и предоставление доступа к журналам аудита для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Настройка параметров диагностики для журнала действий Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Мониторинг Центра безопасности Azure**: Недоступно

**Ответственность**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Сбор журналов безопасности из операционных систем

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="25-configure-security-log-storage-retention"></a>2.5. Настройка хранения журнала безопасности

**Рекомендации**. В Azure Monitor для рабочей области Log Analytics, используемой для хранения журналов базы данных Azure для MySQL, задайте срок хранения согласно нормативным требованиям вашей организации. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения.

Задание параметров хранения журнала для рабочих областей Log Analytics: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Хранение журналов ресурсов в учетной записи хранения Azure: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6. Мониторинг и просмотр журналов

**Рекомендации**. Анализируйте и отслеживайте журналы экземпляров базы данных Azure для MySQL на предмет аномального поведения. Используйте аналитику журналов Azure Monitor для просмотра журналов и выполнения запросов к данным журнала. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

Подключение Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Дополнительные сведения об аналитике журналов: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Выполнение пользовательских запросов в Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7. Включение оповещений об аномальных действиях

**Рекомендации**. Включите Расширенную защиту от угроз для базы данных Azure для MySQL. Расширенная защита от угроз позволяет выявить подозрительную активность, указывающую на необычные и потенциально опасные попытки получить доступ к базам данных или воспользоваться ими.

Кроме того, вы можете включить журналы сервера и параметры диагностики для MySQL и отправить журналы в рабочую область Log Analytics. Подключите рабочую область Log Analytics к Azure Sentinel, так как она предоставляет решение для оркестрации событий безопасности и автоматического реагирования (SOAR). Это позволяет создавать и использовать сборники схем (автоматизированные решения) для устранения проблем безопасности.

Включение Расширенной защиты от угроз для базы данных Azure для MySQL (предварительная версия): https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Общие сведения о журналах сервера в службе "База данных Azure для MySQL": https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Настройка и предоставление доступа к журналам медленных запросов для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Настройка и предоставление доступа к журналам аудита для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Настройка параметров диагностики для журнала действий Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Подключение Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8. Централизованное ведение журнала защиты от вредоносных программ

**Рекомендации**. Неприменимо. База данных Azure для MySQL не обрабатывает и не создает журналы, связанные с защитой от вредоносных программ.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="29-enable-dns-query-logging"></a>2.9. Включение ведения журнала запросов DNS

**Рекомендации**. Неприменимо. База данных Azure для MySQL не обрабатывает и не создает журналы, связанные с DNS.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="210-enable-command-line-audit-logging"></a>2.10. Включение ведения журнала аудита для командной строки

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Дополнительные сведения см. в статье [Управление безопасностью: идентификаторы и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Инвентаризация учетных записей администраторов

**Рекомендации**. Настройте инвентаризацию учетных записей пользователей, имеющих административный доступ к уровню управления (например, порталу Azure) для экземпляров базы данных Azure для MySQL. Кроме того, поддерживайте инвентаризацию учетных записей администраторов, имеющих доступ к уровню данных (в самой базе данных) для экземпляров базы данных Azure для MySQL. (При создании сервера MySQL вы предоставляете учетные данные для пользователя с правами администратора. Этот администратор может использоваться для создания дополнительных пользователей MySQL.)

База данных Azure для MySQL не поддерживает встроенные функции управления доступом на основе ролей, но можно создавать пользовательские роли на основе конкретных операций с поставщиками ресурсов.

Общие сведения о пользовательских ролях для подписки Azure: https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Общие сведения об операциях поставщика ресурсов базы данных Azure для MySQL: https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql

Общие сведения об управлении доступом для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/concepts-security#access-management

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2. Изменение паролей по умолчанию, где применимо

**Рекомендации**. В Azure AD нет концепции паролей по умолчанию.

После создания самого ресурса базы данных Azure для MySQL Azure заставляет создать пользователя с правами администратора с надежным паролем. Однако после создания экземпляра MySQL можно использовать учетную запись первого администратора сервера, предназначенную для создания дополнительных пользователей и предоставления им административного доступа. При создании этих учетных записей убедитесь, что для каждой учетной записи настроен отличающийся надежный пароль.

Создание дополнительных учетных записей для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

Обновление пароля администратора: https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3. Применение выделенных административных учетных записей

**Рекомендации**. Создайте стандартные операционные процедуры для использования выделенных административных учетных записей, имеющих доступ к вашим экземплярам базы данных Azure MySQL. Используйте учетную запись Центра безопасности Azure и управление доступом для мониторинга количества административных учетных записей.

Общие сведения об идентификации и доступе в Центре безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

Общие сведения о создании администраторов в базе данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4. Использование единого входа с Azure Active Directory

**Рекомендации**. Вход в базу данных Azure для MySQL поддерживается как с помощью имени пользователя/пароля, настроенных непосредственно в базе данных, так и с помощью идентификации Azure Active Directory (AD) и использования токена Azure AD для подключения. При использовании токена Azure AD поддерживаются различные методы, например пользователь Azure AD, Группа Azure AD или приложение Azure AD, подключающееся к базе данных.

Отдельный доступ к плоскости управления для MySQL доступен через REST API и поддерживает единый вход. Чтобы выполнить проверку подлинности, установите JSON Web Token, полученный из Azure Active Directory (AAD), в качестве заголовка авторизации.

Использование Azure Active Directory для проверки подлинности в базе данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Общие сведения о REST API базы данных Azure для MySQL: https://docs.microsoft.com/rest/api/mysql/

Общие сведения о едином входе в Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5. Использование многофакторной проверки подлинности для любого доступа на основе Azure Active Directory

**Рекомендации**. Включите многофакторную идентификацию (MFA) в Azure Active Directory и следуйте рекомендациям по управлению идентификацией и доступом в Центре безопасности Azure. При использовании маркеров Azure AD для входа в базу данных возможна многофакторная проверка подлинности для входа в базу данных.

Включение MFA в Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Использование Azure Active Directory для проверки подлинности в базе данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Мониторинг идентификации и доступа в Центре безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Использование выделенных компьютеров (рабочих станций с привилегированным доступом) для всех административных задач

**Рекомендации**. Используйте рабочие станции привилегированного доступа (PAW) с настроенной многофакторной идентификацией (MFA) для входа в ресурсы Azure и их настройки.

Сведения о рабочих станциях с привилегированным доступом: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Включение MFA в Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7. Ведение журнала и создание оповещений по подозрительным действиям учетных записей администраторов

**Рекомендации**. Включите Расширенную защиту от угроз для базы данных Azure для MySQL, чтобы создавать оповещения при подозрительных действиях.

Кроме того, вы можете использовать управление привилегированными пользователями (PIM) в Azure AD для создания журналов и оповещений при возникновении подозрительных или небезопасных действий в окружении.

Используйте обнаружение рисков Azure AD для просмотра предупреждений и отчетов об опасном поведении пользователя.

Настройка Расширенной защиты от угроз для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Развертывание Azure AD Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Общие сведения об обнаружении рисков Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8. Управление ресурсами Azure только из утвержденных расположений

**Рекомендации**. Используйте именованные расположения с условным доступом, чтобы разрешить порталу и Azure Resource Manager доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

Настройка именованных расположений в Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="39-use-azure-active-directory"></a>3.9. Использование Azure Active Directory

**Рекомендации**. Используйте Azure Active Directory как центральную систему проверки подлинности и авторизации. Azure AD защищает данные с помощью надежного шифрования для хранимых и транзитных данных. Кроме того, в Azure AD используются salt-записи, хэши и безопасное хранение учетных данных пользователей.

Для входа в базу данных Azure для MySQL рекомендуется использовать Azure AD и токен Azure AD для подключения. При использовании токена Azure AD поддерживаются различные методы, например пользователь Azure AD, Группа Azure AD или приложение Azure AD, подключающееся к базе данных. 

Учетные данные Azure AD также могут использоваться для администрирования на уровне управления (например, портал Azure), для управления учетными записями администратора MySQL.

Использование Azure Active Directory для проверки подлинности в базе данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10. Регулярная проверка и согласование доступа пользователей

**Рекомендации**. Проверьте журналы Azure Active Directory, чтобы обнаружить устаревшие учетные записи, которые могут включать в себя роли администраторов базы данных Azure для MySQL. Кроме того, используйте проверку доступа удостоверений Azure для эффективного управления членством в группах, доступа к корпоративным приложениям, которые могут использоваться для доступа к базе данных Azure для MySQL и назначения ролей. Доступ пользователей следует проверять регулярно, например раз в 90 дней, чтобы только у авторизованных пользователей был постоянный доступ.

Основы отчетности Azure AD https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Использование проверок доступа для идентификации Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11. Отслеживание попыток доступа к отключенным учетным записям

**Рекомендации**. Включите параметры диагностики для базы данных Azure для MySQL и Azure Active Directory, отправив все журналы в рабочую область Log Analytics. Настройте необходимые оповещения (например, неудачные попытки проверки подлинности) в Log Analytics.

Настройка и предоставление доступа к журналам медленных запросов для базы данных Azure для MySQL: https://docs.microsoft.com/Azure/mysql/howto-configure-server-logs-in-portal

Настройка и предоставление доступа к журналам аудита для базы данных Azure для MySQL: https://docs.microsoft.com/Azure/mysql/howto-configure-audit-logs-portal

Интеграция журналов действий Azure в Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Мониторинг Центра безопасности Azure**: Недоступно

**Ответственность**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12. Предупреждение при подозрительном входе в учетную запись

**Рекомендации**. Включите Расширенную защиту от угроз для базы данных Azure для MySQL, чтобы создавать оповещения при подозрительных действиях.

Используйте защиту идентификации Azure Active Directory и функции обнаружения подозрительных операций, чтобы настроить автоматическое реагирование для обнаружения подозрительных действий. Вы можете включить автоматические ответы с помощью Azure Sentinel, чтобы оперативно реагировать на угрозы.

Вы также можете включить журналы в Azure Sentinel для дальнейшего изучения.

Настройка Расширенной защиты от угроз для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Общие сведения о Защите идентификации Azure AD: https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Просмотр рискованных входов в Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Подключение Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг Центра безопасности Azure**: Недоступно

**Ответственность**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13. Предоставление корпорации Майкрософт доступа к соответствующим данным клиентов в рамках сценариев поддержки

**Рекомендации**. Недоступно. Защищенное хранилище еще не поддерживается для базы данных Azure для MySQL.

Список поддерживаемых служб защищенного хранилища: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в статье [Управление безопасностью: защита данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Инвентаризация конфиденциальных данных

**Рекомендации**. Используйте теги для пометки экземпляров базы данных Azure для MySQL или связанных ресурсов, в которых хранятся или обрабатываются конфиденциальные данные.

Создание и использование тегов: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2. Изолирование систем, хранящих или обрабатывающих конфиденциальные данные

**Рекомендации**. Реализуйте отдельные подписки и группы управления для разработки, тестирования и производства. Используйте сочетание приватного канала, конечных точек службы и (или) правил брандмауэра, чтобы изолировать и ограничить сетевой доступ к экземплярам базы данных Azure для MySQL.

Создание дополнительных подписок Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание групп управления: https://docs.microsoft.com/azure/governance/management-groups/create

Настройка приватного канала для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link

Настройка правил и конечных точек службы VNet и правил VNet в базе данных Azure для MySQL и управление ими: https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Настройка правил брандмауэра базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/concepts-firewall-rules


**Мониторинг Центра безопасности Azure**: Недоступно

**Ответственность**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Рекомендации**. При использовании виртуальных машин Azure для доступа к экземплярам базы данных Azure MySQL используйте приватные каналы, конфигурации сети MySQL, группы безопасности сети и теги службы, чтобы снизить вероятность возможной утечки данных.

Корпорация Майкрософт управляет базовой инфраструктурой базы данных Azure для MySQL и реализовала надежные элементы управления для предотвращения потери или раскрытия данных клиента.

Устранение утечки данных из базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention

Общие сведения о защите данных клиентов в Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Шифрование любой конфиденциальной информации при передаче

**Рекомендации**. База данных Azure для MySQL поддерживает подключение сервера MySQL к клиентским приложениям с помощью SSL (Secure Sockets Layer). Применение SSL-соединений между сервером базы данных и клиентскими приложениями обеспечивает защиту от атак "злоумышленник в середине" за счет шифрования потока данных между сервером и приложением. На портале Azure убедитесь, что по умолчанию включено "принудительное подключение SSL" для всех экземпляров базы данных Azure для MySQL.

Сейчас для базы данных Azure для MySQL поддерживаются следующие версии TLS: TLS 1.0, TLS 1.1, TLS 1.2.

Настройка шифрования передаваемых данных для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security

**Мониторинг Центра безопасности Azure**: Недоступно

**Ответственность**: Совмещаемая блокировка

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Использование средства активного обнаружения для распознавания конфиденциальных данных

**Рекомендации**. Функции идентификации, классификации и предотвращения потери данных пока недоступны для базы данных Azure для MySQL. Установите сторонние решения, если это необходимо для обеспечения соответствия требованиям.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и предпринимает все возможные усилия для защиты клиентов от потери данных и раскрытия информации. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

Общие сведения о защите данных клиентов в Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure**: Недоступно

**Ответственность**: Совмещаемая блокировка

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6. Контроль доступа к ресурсам с помощью RBAC

**Рекомендации**. Используйте управление доступом на основе ролей Azure (RBAC) для управления доступом к уровню управления базы данных Azure для MySQL (например, порталу Azure). Для доступа к уровню данных (в самой базе данных) используйте SQL-запросы для создания пользователей и настройки разрешений пользователей. RBAC не влияет на разрешения пользователей в базе данных.

Настройка RBAC в Azure: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Настройка доступа пользователей с помощью SQL для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

Корпорация Майкрософт управляет базовой инфраструктурой базы данных Azure для MySQL и реализовала надежные элементы управления для предотвращения потери или раскрытия данных клиента.

Общие сведения о защите данных клиентов в Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Шифрование конфиденциальной информации при хранении

**Рекомендации**. В Базе данных Azure для MySQL используется проверенный криптографический модуль FIPS 140-2 для шифрования неактивных данных. Данные, включая резервные копии, шифруются на диске (за исключением временных файлов, создаваемых при выполнении запросов). Служба использует 256-разрядный шифр AES, включенный в шифрование службы хранилища Azure. Ключами управляет система. Шифрование хранилища всегда включено, и его нельзя отключить.

Шифрование данных с помощью управляемых пользователем ключей для Базы данных Azure для MySQL позволяет создавать собственный ключ (BYOK) для защиты неактивных данных. В настоящее время для использования этой возможности требуется запросить соответствующие права доступа. Для этого обратитесь:

AskAzureDBforMySQL@service.microsoft.com

Общие сведения о шифровании хранимых данных в базе данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/concepts-security

Настройка управляемых клиентом ключей для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-encryption-mysql

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Включение в журнал и создание оповещений по изменениям критических ресурсов Azure

**Рекомендации**. Используйте Azure Monitor с журналом действий Azure для создания оповещений об изменениях на коммерческих экземплярах базы данных Azure для MySQL и других важных и связанных с ними ресурсах.

Создание оповещений для событий журнала действий Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="vulnerability-management"></a>Управление уязвимостями

*Дополнительные сведения см. в статье [Управление безопасностью: управление уязвимостями](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Выполнение автоматизированных средства анализа уязвимостей

**Рекомендации**. В настоящее время недоступно. Центр безопасности Azure пока не поддерживает оценку уязвимостей для базы данных Azure для MySQL.

Покрытие компонентов для служб Azure PaaS в Центре безопасности Azure: https://docs.microsoft.com/azure/security-center/features-paas

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Развертывание автоматизированного решения для управления исправлениями операционной системы

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3. Развертывание автоматизированного решения для управления исправлениями ПО сторонних производителей

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4. Сравнение проверок смежных уязвимостей

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Использование процесса оценки рисков для определения приоритета в устранении обнаруженных уязвимостей

**Рекомендации**. Корпорация Майкрософт управляет уязвимостью в базовых системах, поддерживающих базу данных Azure для MySQL.


**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в статье [Управление безопасностью: инвентаризация и управление ресурсами](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1. Использование обнаружения ресурсов Azure

**Рекомендации**. Используйте Azure Resource Graph для запроса и обнаружения всех ресурсов (включая экземпляры базы данных Azure для MySQL) в ваших подписках. Убедитесь, что у вас есть соответствующие разрешения (на чтение) в клиенте и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

Создание запросов с помощью Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Просмотр подписок Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Общие сведения об Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2. Ведение метаданных активов

**Рекомендации**. Примените теги к экземплярам базы данных Azure для MySQL и другим связанным ресурсам, предоставив метаданные для логической организации их в таксономию.

Создание и использование тегов: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Удаление неавторизованных ресурсов Azure

**Рекомендации**. При необходимости используйте теги, группы управления и отдельные подписки, чтобы упорядочить и отслеживать экземпляры базы данных Azure для MySQL и связанные ресурсы. Регулярно сверяйте ресурсы, чтобы своевременно удалять неавторизованные ресурсы из подписки.

Создание дополнительных подписок Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание групп управления: https://docs.microsoft.com/azure/governance/management-groups/create

Создание и использование тегов: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4. Ведение каталога утвержденных ресурсов Azure и наименований программного обеспечения

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислений с ресурсами, а также для Azure в целом.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5. Отслеживание неутвержденных ресурсов Azure

**Рекомендации**. Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Кроме того, используйте Azure Resource Graph для запроса или обнаружения ресурсов в подписках.

Настройка Политики SQL Azure и управление ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Создание запросов с помощью Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6. Отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Удаление неутвержденных ресурсов Azure и программных приложений

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислений с ресурсами, а также для Azure в целом.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="68-use-only-approved-applications"></a>6.8. Использование только утвержденных приложений

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="69-use-only-approved-azure-services"></a>6.9. Использование только утвержденных служб Azure

**Рекомендации**. Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Настройка Политики SQL Azure и управление ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Отказ от определенного типа ресурса с помощью Политики Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="610-implement-approved-application-list"></a>6.10. Реализация списка утвержденных приложений

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11. Ограничение возможности пользователей взаимодействовать с Azure Resources Manager с помощью сценариев

**Рекомендации**. Используйте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "Блокировать доступ" для приложения "Управление Microsoft Azure". Это может препятствовать созданию и изменению ресурсов в среде с высоким уровнем безопасности, например экземпляров базы данных Azure для MySQL, содержащих конфиденциальные сведения.

Настройка условного доступа для блокировки доступа к Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12. Ограничение возможности пользователей выполнять сценарии в ресурсах вычислений

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13. Физическое или логическое разделение приложений с высоким риском

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

## <a name="secure-configuration"></a>Настройка безопасности

*Дополнительные сведения см. в статье [Управление безопасностью: безопасная конфигурация](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Установка безопасных конфигураций для всех ресурсов Azure

**Рекомендации**. Определите и реализуйте стандартные конфигурации безопасности для экземпляров базы данных Azure для MySQL с помощью Политики Azure. Используйте псевдонимы политик Azure в пространствах имен "Microsoft.DBforMySQL" для создания настраиваемых политик, предназначенных для аудита или принудительного применения конфигурации сети для экземпляров базы данных Azure для MySQL. Вы также можете использовать встроенные определения политик для ваших экземпляров базы данных Azure для MySQL, например:

Для серверов баз данных MySQL должно быть включено принудительное использование SSL-соединения

Просмотр доступных псевдонимов политик Azure: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Настройка Политики SQL Azure и управление ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Сохранение безопасных конфигураций для операционных систем

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3. Сохранение безопасных конфигураций для ресурсов Azure

**Рекомендации**. Используйте Политику Azure [отказывать] и [развернуть, если не существует], чтобы обеспечить безопасность параметров в ресурсах Azure.

Настройка Политики SQL Azure и управление ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Сведения о действии Политики Azure: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4. Сохранение безопасных конфигураций для операционных систем

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5. Безопасное хранение конфигурации ресурсов Azure

**Рекомендации**. Если вы используете пользовательские определения Политики Azure для экземпляров базы данных Azure для MySQL и связанных ресурсов, используйте Azure Repos для безопасного хранения кода и управления им.

Хранение кода в Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Документация по Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6. Безопасное хранение пользовательских образов операционной системы

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="77-deploy-system-configuration-management-tools"></a>7.7. Развертывание инструментов управления конфигурацией системы

**Рекомендации**. Используйте псевдонимы политик Azure в пространстве имен "Microsoft.DBforMySQL" для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.

Настройка Политики SQL Azure и управление ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8. Развертывание средств управления конфигурацией системы для операционных систем

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9. Реализация автоматизированного мониторинга конфигурации для служб Azure

**Рекомендации**. Используйте псевдонимы политик Azure в пространстве имен "Microsoft.DBforMySQL" для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Используйте Политику Azure [аудит], [запретить] и [развернуть, если не существует], чтобы автоматически применять конфигурации для экземпляров базы данных Azure для MySQL и связанных ресурсов.

Настройка Политики SQL Azure и управление ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10. Реализация автоматизированного мониторинга конфигурации для операционных систем

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="711-manage-azure-secrets-securely"></a>7.11. Безопасное управление секретами Azure

**Рекомендации**. Для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, которые используются для доступа к экземплярам базы данных Azure для MySQL, используйте Управляемое удостоверение службы в сочетании с Azure Key Vault, чтобы упростить и защитить управление секретами базы данных Azure для MySQL. Убедитесь, что включено обратимое удаление в Azure Key Vault.

Интеграция с управляемыми удостоверениями Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Создание Key Vault: https://docs.microsoft.com/azure/key-vault/quick-create-portal

Проверка подлинности в Key Vault с помощью управляемого удостоверения: https://docs.microsoft.com/azure/key-vault/managed-identity

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12. Безопасное и автоматическое управление удостоверениями

**Рекомендации**. Экземпляр базы данных Azure для MySQL поддерживает проверку подлинности Azure Active Directory для доступа к базам данных.  При создании экземпляра базы данных Azure для MySQL вы предоставляете учетные данные для пользователя с правами администратора. Этот администратор может использоваться для создания дополнительных пользователей БД.  

Для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, которые используются для доступа к экземплярам базы данных Azure для MySQL, используйте Управляемое удостоверение службы в сочетании с Azure Key Vault, чтобы хранить и получать учетные данные для экземпляра базы данных Azure для MySQL. Убедитесь, что включено обратимое удаление в Azure Key Vault.

Функция управляемых удостоверений предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory (AD). Управляемое удостоверение можно использовать для проверки подлинности в любой службе, которая поддерживает проверку подлинности Azure AD, включая Key Vault, при этом не сохраняя каких-либо учетных данных в коде.

Настройка управляемых удостоверений: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Интеграция с управляемыми удостоверениями Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13. Устранение непреднамеренного раскрытия учетных данных

**Рекомендации**. Реализуйте сканер учетных данных для обнаружения учетных данных в коде. Сканер учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault.

Настройка сканера учетных данных: https://secdevtools.azurewebsites.net/helpcredscan.html

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в статье [Управление безопасностью: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

Защита от вредоносных программ Майкрософт включена на базовом узле, поддерживающем службы Azure (например, база данных Azure для SQL), но не выполняется в содержимом клиента.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2. Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся вычислительными

**Рекомендации**. Защита от вредоносных программ Майкрософт включена на базовом узле, поддерживающем службы Azure (например, база данных Azure для MySQL), но не выполняется в содержимом клиента.

Предварительно сканируйте любое содержимое, отправляемое в невычислительные ресурсы Azure, например служба приложений, хранилище Data Lake, хранилище BLOB-объектов, база данных Azure для MySQL и т. д. Корпорация Майкрософт не может получить доступ к данным в этих экземплярах.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Совмещаемая блокировка

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3. Своевременное обновление программного обеспечения для защиты от вредоносных программ и подписей

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

Защита от вредоносных программ Майкрософт включена на базовом узле, поддерживающем службы Azure (например, база данных Azure для MySQL), но не выполняется в содержимом клиента.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Недоступно

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в статье [Управление безопасностью: восстановление данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1. Обеспечение регулярного автоматического резервного копирования

**Рекомендации**. База данных Azure для MySQL создает резервные копии файлов данных и журнала транзакций. В зависимости от поддерживаемого максимального размера хранилища мы будем использовать полные и разностные резервные копии (максимум 4 ТБ на сервере) или моментальные снимки (до 16 ТБ на сервере). При помощи этих резервных копий вы можете восстановить сервер до любой точки во времени в пределах заданного срока хранения резервных копий. По умолчанию срок хранения резервных копий составляет 7 дней. При необходимости вы увеличить его вплоть до 35 дней. Все резервные копии шифруются с помощью 256-битового шифрования AES.

Общие сведения о резервных копиях для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

Общие сведения о начальной конфигурации базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/tutorial-design-database-using-portal

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Совмещаемая блокировка

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Выполнение полного резервного копирования системы и любых ключей, управляемых клиентом

**Рекомендации**. База данных Azure для MySQL автоматически создает резервные копии для сервера и сохраняет их в локальном избыточном или геоизбыточном хранилище — по выбору пользователя. Резервные копии можно использовать для восстановления сервера до точки во времени. Резервное копирование и восстановление данных являются важной частью любой стратегии непрерывности бизнес-процессов. Таким образом данные защищаются от случайного повреждения или удаления. 

Если вы используете Azure Key Vault для хранения учетных данных для экземпляров базы данных Azure для MySQL, обеспечьте регулярное автоматическое резервное копирование ключей. 

Общие сведения о резервных копиях для базы данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/howto-restore-server-portal 

Резервное копирование ключей Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Совмещаемая блокировка

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3. Проверка всех резервных копий, включая управляемые клиентом ключи

**Рекомендации**. При восстановлении в службе "База данных Azure для MySQL" из резервных копий исходного сервера создается новый. Есть два типа восстановления: Восстановление на определенный момент времени и географическое восстановление. Восстановление на определенный момент времени доступно для любого типа избыточности резервного копирования. При таком восстановлении новый сервер создается в том же регионе, где расположен исходный сервер. Географическое восстановление доступно, если для сервера настроено геоизбыточное хранилище, и позволяет восстановить сервер в другом регионе.

Предполагаемое время восстановления будет зависеть от нескольких факторов, включая размер базы данных, размер журнала транзакций, пропускную способность сети и общее количество баз данных, восстанавливаемых в том же регионе и в то же время. Обычно время восстановления составляет менее 12 часов.

Периодическое тестирование восстановления экземпляров базы данных Azure для MySQL.

Общие сведения о резервном копировании и восстановление в базе данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Обеспечение защиты резервных копий и управляемых клиентом ключей

**Рекомендации**. В службе "База данных Azure для MySQL" создаются полные и разностные резервные копии, а также резервные копии журналов. При помощи этих резервных копий вы можете восстановить сервер до любой точки во времени в пределах заданного срока хранения резервных копий. По умолчанию срок хранения резервных копий составляет 7 дней. При необходимости вы увеличить его вплоть до 35 дней. Все резервные копии шифруются с помощью 256-битового шифрования AES. Убедитесь, что включено обратимое удаление в Azure Key Vault.

Общие сведения о резервном копировании и восстановление в базе данных Azure для MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="incident-response"></a>реагирование на инциденты.

*Дополнительные сведения см. в статье [Управление безопасностью: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Создание руководства по реагированию на инциденты

**Рекомендации**. Создайте руководство по реагированию на инциденты для вашей организации. Убедитесь, что имеются письменные планы реагирования на инциденты, которые определяют все действия персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.

Настройка автоматизации рабочих процессов в Центре безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Руководство по созданию собственного процесса реагирования на инциденты безопасности: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Анатомия инцидента Центра Майкрософт по реагированию на угрозы: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Клиент может также использовать руководство по обработке инцидентов безопасности компьютера от NIST, чтобы разработать собственный план реагирования на инциденты: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Создание процедуры оценки инцидента и определения приоритетов

**Рекомендации**. Центр безопасности назначает каждому оповещению серьезность, которая поможет определить, какие предупреждения следует расследовать первыми. Серьезность основывается на том, насколько уверен центр безопасности в исследовании или аналитике, используемой для оповещения, а также на уровне достоверности злонамеренности события, приведшего к оповещению.

Кроме того, четко помечайте подписки (для устар. производственной, непроизводственной) и создайте систему именования, чтобы четко определить и классифицировать ресурсы Azure.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="103-test-security-response-procedures"></a>10.3. Проверка процедур реагирования на угрозы

**Рекомендации**. Выполните упражнения, чтобы периодически протестировать возможности ваших систем реагировать на угрозы. Выявите слабые точки и пробелы и пересмотрите план по мере необходимости.

См. публикацию NIST: Руководство по тестированию, обучению и выполнению программ для ИТ-планов и возможностей: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Предоставление контактных сведений и настройка уведомлений по инцидентам безопасности

**Рекомендации**. Корпорация Майкрософт будет использовать информацию об инциденте безопасности для связи с вами, если центр Microsoft Security Response Center (MSRC) обнаружит, что к вашим пользовательским данным был получен незаконный или несанкционированный доступ.  Проверьте инциденты после факта обращения, чтобы убедиться, что проблемы устранены.

Задание контакта безопасности Центра безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Включение оповещений системы безопасности в систему реагирования на инциденты

**Рекомендации**. Экспортируйте оповещения и рекомендации центра безопасности Azure с помощью функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Вы можете использовать соединитель данных центра безопасности Azure для потоковой передачи метки предупреждений.

Настройка непрерывного экспорта данных: https://docs.microsoft.com/azure/security-center/continuous-export

Выполнение потоковой передачи предупреждений в Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6. Автоматизация реагирования на оповещения системы безопасности

**Рекомендации**. Используйте функцию автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" в оповещениях и рекомендациях системы безопасности.

Настройка автоматизации рабочего процесса и Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в статье [Управление безопасностью: тесты на проникновение и попытки нарушения безопасности "красной командой"](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1. Регулярное тестирование на проникновение ресурсов Azure и отслеживание исправлений всех критических точек безопасности в течение 60 дней

**Рекомендации**. Следуйте правилам взаимодействия Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Дополнительные сведения о стратегии корпорации Майкрософт и реализации "красной команды", а также о тестировании на основе уязвимости для облачной инфраструктуры, служб и приложений Майкрософт см. здесь: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Совмещаемая блокировка

## <a name="next-steps"></a>Дальнейшие действия

- См. [Тесты производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview).
- Узнайте больше о [Базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).
