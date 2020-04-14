---
title: Базовый универсаль безопасности Azure для базы данных Azure для MyS'L
description: Базовый универсаль безопасности Azure для базы данных Azure для MyS'L
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: cffefb294aaeeb4d66358ceae72c98becd04cc58
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261964"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Базовый универсаль безопасности Azure для базы данных Azure для MyS'L

Базовый универсаль безопасности Azure для базы данных Azure для MyS'L содержит рекомендации, которые помогут вам улучшить безопасность развертывания.

Базовая версия этой службы взята из [версии 1.0 Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)которая содержит рекомендации о том, как можно обезопасить свои облачные решения в Azure с нашими рекомендациями по передовым практикам.

Для получения дополнительной информации смотрите [обзор базовых линий безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Защита ресурсов, использующих группы сетевой безопасности или брандмауэр Azure в виртуальной сети

**Руководство**: Наконкажировать частную ссылку для базы данных Azure для MyS'L с частными конечными точками. Приватный канал позволяет подключаться к различным службам PaaS в Azure через частную конечную точку. Azure Private Link по существу приносит услуги Azure в вашей частной виртуальной сети (VNet). Трафик между виртуальной сетью и экземпляром MyS'L перемещается по магистральной сети Майкрософт.

Кроме того, вы можете использовать конечные точки виртуального сетевого обслуживания для защиты и ограничения доступа к базе данных Azure для реализаций MyS'L. Правила виртуальной сети — это одна из функций безопасности брандмауэра, обеспечивающая управление приемом сервером базы данных Azure для MySQL подключений из определенных подсетей в виртуальных сетях.

Вы также можете обеспечить защиту базы данных Azure для сервера MyS'L с правилами брандмауэра. Брандмауэр сервера предотвращает доступ к серверу базы данных до тех пор, пока вы не укажете, какие компьютеры имеют разрешение. Для настройки брандмауэра можно создать правила брандмауэра, которые указывают диапазон допустимых IP-адресов. Правила брандмауэра можно создавать на уровне сервера.

Как настроить частную ссылку для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Как создавать и управлять конечными точками службы VNet и правилами VNet в базе данных Azure для MyS'L:https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Как настроить базу данных Azure для правил брандмауэра MyS'L:https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal

**Мониторинг Центра безопасности Azure**: Недоступен

**Ответственность**: Клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Мониторинг и регистрация конфигурации и трафика Vnets, Subnets и NICs

**Руководство**: Когда база данных Azure для экземпляра MyS'L защищена в частной конечном пункте, вы можете развернуть виртуальные машины в той же виртуальной сети. Можно использовать группу сетевой безопасности (NSG) для снижения риска эксфильтрации данных. Включите журналы потока NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправлять журналы потоков NSG в рабочее пространство Log Analytics и использовать аналитику трафика для получения информации о движении в облаке Azure. Некоторые преимущества Traffic Analytics — это возможность визуализации сетевой активности и выявления горячих точек, выявления угроз безопасности, понимания моделей движения и неправильной настройки сети.

Как настроить частную ссылку для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Как включить журналы потока NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить и использовать аналитику трафика:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="13-protect-critical-web-applications"></a>1.3: Защита критически важных веб-приложений

**Руководство**: Не применимо; эта рекомендация предназначена для веб-приложений, работающих на Azure App Service или для вычисления ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Запретить связь с известными вредоносными IP-адресами

**Руководство**: Используйте расширенную защиту угроз для базы данных Azure для MyS'L. Расширенная защита от угроз обнаруживает аномальные действия, указывающие на необычные и потенциально опасные попытки получить доступ к базам данных или эксплуатировать их.

Включите стандарт DDoS-защиты для виртуальных сетей, связанных с базой данных Azure для экземпляров MyS'L, для защиты от DDoS-атак. Используйте Azure Security Center Integrated Threat Intelligence, чтобы отказать в сообщении с известными вредоносными или неиспользованными IP-адресами в Интернете.

Как настроить расширенную защиту угроз для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Как настроить DDoS-защиту:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Запись сетевых пакетов и журналов потока

**Руководство**: Когда база данных Azure для экземпляра MyS'L защищена в частной конечном пункте, вы можете развернуть виртуальные машины в той же виртуальной сети. Затем можно настроить группу сетевой безопасности (NSG) для снижения риска эксфильтрации данных. Включите журналы потока NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправлять журналы потоков NSG в рабочее пространство Log Analytics и использовать аналитику трафика для получения информации о движении в облаке Azure. Некоторые преимущества Traffic Analytics — это возможность визуализации сетевой активности и выявления горячих точек, выявления угроз безопасности, понимания моделей движения и неправильной настройки сети.

Как включить журналы потока NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить и использовать аналитику трафика:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Развертывание сетевых систем обнаружения/предотвращения вторжений (IDS/IPS)

**Руководство**: Используйте расширенную защиту угроз для базы данных Azure для MyS'L. Расширенная защита от угроз обнаруживает аномальные действия, указывающие на необычные и потенциально опасные попытки получить доступ к базам данных или эксплуатировать их.

Как настроить расширенную защиту угроз для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Управление трафиком для веб-приложений

**Руководство**: Не применимо; эта рекомендация предназначена для веб-приложений, работающих на Azure App Service или для вычисления ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Минимизация сложности и административных накладных расходов на правила сетевой безопасности

**Руководство:** Для ресурсов, которым необходим доступ к базе данных Azure для экземпляров MyS'L, используйте теги виртуальных сетевых служб для определения элементов управления доступом к сети в группах сетевой безопасности или брандмауэре Azure Firewall. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указывая имя тега службы (например, S'L. WestUs) в соответствующем источнике или поле назначения правила, вы можете разрешить или отказать трафик для соответствующей службы. Корпорация Майкрософт управляет адресными префиксами, охватываемыми тегом службы, и автоматически обновляет тег службы при изменении адресов.

Примечание: База данных Azure для MyS'L использует теги службы Microsoft.Sql.

Для получения дополнительной информации об использовании сервисных тегов:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Понимание использования тагой службы для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**: Определение и реализация стандартных конфигураций безопасности для настроек сети и сетевых ресурсов, связанных с базой данных Azure для экземпляров MyS'L с azure Policy. Используйте псевдонимы Azure Policy в пространствах имен "Microsoft.DBforMyS'L" и "Microsoft.Network" для создания пользовательских политик для аудита или обеспечения сетевой конфигурации базы данных Azure для экземпляров MyS'L. Можно также использовать встроенные определения политики, связанные с сетью, или базу данных Azure для экземпляров MyS'L, такие как:

- Стандарт Защиты от атак DDoS должен быть включен.

- Для серверов баз данных MySQL должно быть включено принудительное использование SSL-соединения

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Образцы политики Azure для сетей:https://docs.microsoft.com/azure/governance/policy/samples/

Как создать план Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="110-document-traffic-configuration-rules"></a>1.10: Правила конфигурации трафика документов

**Руководство**: Используйте теги для ресурсов, связанных с сетевой безопасностью и потоком трафика, для вашей базы данных Azure для экземпляров MyS'L для предоставления метаданных и логической организации.

Используйте любое из встроенных определений политики Azure, связанных с пометкой, например, "Требуемый тег и его значение", чтобы гарантировать, что все ресурсы создаются с тегами и уведомлять вас о существующих немаркированных ресурсах.

Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий на ресурсах, основанных на их тегах.

Как создавать и использовать теги:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Используйте автоматизированные инструменты для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**: Используйте журнал действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений для сетевых ресурсов, связанных с базой данных Azure для экземпляров MyS'L. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критически важных сетевых ресурсов.

Как просмотреть и получить события журнала активности Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Как создать оповещения в Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Используйте утвержденные источники синхронизации времени

**Руководство**: Корпорация Майкрософт поддерживает источник времени, используемый для ресурсов Azure, например базу данных Azure для MyS'L для меток времени в журналах.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="22-configure-central-security-log-management"></a>2.2: Настройка централизованного управления журналами безопасности

**Руководство:** Включить диагностические настройки и журналы серверов и глотать журналы для агрегирования данных безопасности, генерируемых базой данных Azure для экземпляров MyS'L. В Azure Monitor используйте рабочее пространство журналов Analytics Workspace (ы) для запроса и выполнения аналитики, а также используйте учетные записи хранения Azure для долгосрочного/архивного хранения. Кроме того, вы можете включить и на борт данных Azure Sentinel или сторонних SIEM.

Поймите журналы серверов для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Как на борту Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг Центра безопасности Azure**: Недоступен

**Ответственность**: Клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Включить журнал аудита для ресурсов Azure

**Руководство:** Включить диагностические настройки в базе данных Azure для экземпляров MyS'L для доступа к аудиту, медленному запросу и журналам метрик MyS'L. Убедитесь, что вы специально включить журнал аудита MyS'L. В журналы активности, которые автоматически доступны, входят источник событий, дата, пользователь, метка времени, адреса источников, адреса назначения и другие полезные элементы. Вы также можете включить диагностические настройки журналов активности Azure и отправить журналы в ту же рабочую область анализа журналов или учетную запись хранения.

Поймите журналы серверов для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Как настроить и получить доступ к медленным журналам запросов для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Как настроить и получить доступ к журналам аудита для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Как настроить диагностические настройки для журнала активности Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Мониторинг Центра безопасности Azure**: Недоступен

**Ответственность**: Клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Сбор журналов безопасности из операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="25-configure-security-log-storage-retention"></a>2.5: Настройка хранения журналов безопасности

**Руководство**: В Azure Monitor для рабочего пространства Log Analytics, используемого для хранения базы данных Azure для журналов MyS'L, установите период хранения в соответствии с правилами соответствия организации. Используйте учетные записи хранения Azure для долгосрочного/архивного хранения.

Как установить параметры удержания журнала для рабочих областей анализа журналов:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Хранение журналов ресурсов в учетной записи хранения Azure:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="26-monitor-and-review-logs"></a>2.6: Мониторинг и обзор журналы

**Руководство**: Анализ и мониторинг журналов из базы данных Azure для экземпляров MyS'L для аномального поведения. Используйте аналитику журналов Azure Monitor для просмотра журналов и выполнения запросов по данным журнала. Кроме того, вы можете включить и на борт данных Azure Sentinel или третьей стороной SIEM.

Как на борту Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Для получения дополнительной информации о журнале Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Как выполнять пользовательские запросы в Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Включить оповещения об аномальной активности

**Руководство**: Включите расширенную защиту угроз для базы данных Azure для MyS'L. Расширенная защита от угроз обнаруживает аномальные действия, указывающие на необычные и потенциально опасные попытки получить доступ к базам данных или эксплуатировать их.

Кроме того, можно включить журналы серверов и диагностические настройки для MyS'L и отправить журналы в рабочее пространство Log Analytics. На борту рабочего пространства журнала Analytics для Azure Sentinel, поскольку оно обеспечивает систему автоматического реагирования на систему безопасности (SOAR). Это позволяет создавать и использовать игровые книги (автоматизированные решения) для устранения проблем безопасности.

Как включить расширенную защиту угроз для базы данных Azure для MyS'L (Предварительный просмотр):https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Поймите журналы серверов для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Как настроить и получить доступ к медленным журналам запросов для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Как настроить и получить доступ к журналам аудита для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Как настроить диагностические настройки для журнала активности Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Как на борту Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="28-centralize-anti-malware-logging"></a>2.8: Централизованное заготовку от вредоносных программ

**Руководство**: Не применимо; База данных Azure для MyS'L не обрабатывает и не производит журналы, связанные с вредоносными программами.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="29-enable-dns-query-logging"></a>2.9: Включить журнал запросов DNS

**Руководство**: Не применимо; База данных Azure для MyS'L не обрабатывает и не производит журналы, связанные с DNS.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="210-enable-command-line-audit-logging"></a>2.10: Включить журнал аудита командной строки

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Ведение перечня административных счетов

**Руководство**: Ведение инвентаризации учетных записей пользователей, которые имеют административный доступ к плоскости управления (например, портал Azure) базы данных Azure для MyS'Linstances. Кроме того, ведется инвентаризация административных учетных записей, которые имеют доступ к плоскости данных (в самой базе данных) базы данных Azure для экземпляров MyS'L. (При создании сервера MyS'L вы предоставляете учетные данные для пользователя администратора. Этот администратор может быть использован для создания дополнительных пользователей MyS'L.)

База данных Azure для MyS'L не поддерживает встроенный элемент управления доступом на основе ролей, но можно создавать пользовательские роли на основе определенных вариантов поставщика ресурсов.

Понять пользовательские роли для подписки Azure:https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Поймите базу данных Azure для операций поставщика ресурсов MyS'L:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql

Понять управление доступом для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-security#access-management

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Изменение паролей по умолчанию, где это применимо

**Руководство**: Azure AD не имеет понятия паролей по умолчанию.

После создания базы данных Azure для самого ресурса MyS'L Azure заставляет создать административного пользователя с сильным паролем. Однако после создания экземпляра MyS'L можно использовать созданную вами первую учетную запись администратора сервера, чтобы создать дополнительных пользователей и предоставить к ним административный доступ. При создании этих учетных записей убедитесь, что вы настраиваете другой, надежный пароль для каждой учетной записи.

Как создать дополнительные учетные записи для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-create-users

Как обновить пароль админ:https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Использование выделенных административных учетных записей

**Руководство**: Создание стандартных операционных процедур вокруг использования выделенных административных учетных записей, которые имеют доступ к базе данных Azure для экземпляров MyS'L. Используйте identity и доступ к центру безопасности Azure для мониторинга количества административных учетных записей.

Поймите идентификацию и доступ центра безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Понять, как создавать пользователей администрирования в базе данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-create-users

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Используйте одиночный вписаться (SSO) с активным каталогом Azure

**Руководство**: Подписание в базу данных Azure для MyS'L поддерживается как с использованием имени пользователя/пароля, настроенного непосредственно в базе данных, так и с помощью идентификатора Azure Active Directory (AD) и использования токена Azure AD для подключения. При использовании маркера Azure AD поддерживаются различные методы, такие как пользователь Azure AD, группа Azure AD или приложение Azure AD, соединяющееся с базой данных.

Отдельно доступ к плоскости управления для MyS'L доступен через REST API и поддерживает SSO. Чтобы проверить подлинность, установите заголовок авторизации для запросов в маркер JSON Web Token, полученный в каталоге Active Azure.

Используйте Active Directory Azure для проверки подлинности с помощью базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Поймите базу данных Azure для MyS'L REST API:https://docs.microsoft.com/rest/api/mysql/

Поймите SSO с Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Используйте многофакторную аутентификацию для всего доступа на основе активного каталога Azure

**Руководство**: Включите многофакторную аутентификацию Azure Active Directory (MFA) и следуйте рекомендациям Центра идентификации и управления доступом Azure. При использовании токенов Azure AD для вхинга в базу данных это позволяет требовать многофакторной аутентификации для входов в базу данных.

Как включить МИД в Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Используйте Active Directory Azure для проверки подлинности с помощью базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Как контролировать личность и доступ в Центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Используйте выделенные машины (Привилегированные рабочие станции доступа) для всех административных задач

**Руководство**: Использование привилегированных рабочих станций доступа (PAW) с Multi-Фактораута (MFA), настроенной для входа в ресурсы Azure и настройки.

Узнайте о рабочих станциях привилегированном доступе:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Как включить МИД в Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Войти и предупредить о подозрительной активности с административных счетов

**Руководство**: Включите расширенную защиту угроз для базы данных Azure для MyS'L для создания оповещений о подозрительной активности.

Кроме того, можно использовать Azure AD Privileged Identity Management (PIM) для генерации журналов и оповещений при возникновении подозрительной или небезопасной активности в среде.

Используйте обнаружения рисков Azure AD для просмотра предупреждений и отчетов о рискованном поведении пользователей.

Как настроить расширенную защиту угроз для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Как развернуть Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Поймите обнаружения рисков Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Управление ресурсами Azure только из утвержденных мест

**Руководство**: Используйте условные места доступа, чтобы обеспечить доступ к порталу и менеджеру ресурсов Azure только из конкретных логических групп диапазонов IP-адресов или стран/регионов.

Как настроить именованные места в Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="39-use-azure-active-directory"></a>3.9: Используйте активный каталог Azure

**Руководство**: Использование активного каталога Azure (AD) в качестве центральной системы аутентификации и авторизации. Azure AD защищает данные, используя надежное шифрование данных в состоянии покоя и в пути. Azure AD также солит, хэширует и надежно хранит учетные данные пользователей.

Для вхена в базу данных Azure для MyS'L рекомендуется использовать Azure AD и использовать токен Azure AD для подключения. При использовании маркера Azure AD поддерживаются различные методы, такие как пользователь Azure AD, группа Azure AD или приложение Azure AD, соединяющееся с базой данных. 

Учетные данные Azure AD также могут использоваться для администрирования на уровне плоскости управления (например, порталAz) для управления учетными записями администрирования MyS'L.

Используйте Active Directory Azure для проверки подлинности с помощью базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Регулярно проверяйте и согласовывая доступ пользователей

**Руководство**: Просмотрите журналы Active Directory Azure, чтобы помочь обнаружить устаревшие учетные записи, которые могут включать учетные записи с базой данных Azure для административных ролей MyS'L. Кроме того, используйте Azure Identity Access Reviews для эффективного управления членством в группах, доступа к корпоративным приложениям, которые могут быть использованы для доступа к базе данных Azure для MyS'L, и ролевых назначений. Доступ пользователей должен регулярно пересматриваться, например, каждые 90 дней, чтобы убедиться, что только нужные пользователи имеют постоянный доступ.

Понимание отчетов ad Azurehttps://docs.microsoft.com/azure/active-directory/reports-monitoring/

Как использовать обзоры доступа к идентификационным данным Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Попытка monitor получить доступ к деактивированным учетным записям

**Руководство:** Включить диагностические настройки для базы данных Azure для myS'L и активный каталог Azure, отправив все журналы в рабочее пространство analytics журнала. Налаживание желаемых оповещений (например, неудачные попытки проверки подлинности) в журнале Analytics.

Как настроить и получить доступ к медленным журналам запросов для базы данных Azure для MyS'L:https://docs.microsoft.com/Azure/mysql/howto-configure-server-logs-in-portal

Как настроить и получить доступ к журналам аудита для базы данных Azure для MyS'L:https://docs.microsoft.com/Azure/mysql/howto-configure-audit-logs-portal

Как интегрировать журналы активности Azure в Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Мониторинг Центра безопасности Azure**: Недоступен

**Ответственность**: Клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Оповещение об отклонении поведения входа в учетную запись

**Руководство**: Включите расширенную защиту угроз для базы данных Azure для MyS'L для создания оповещений о подозрительной активности.

Используйте функции Azure Active Directory Identity Protection и обнаружения рисков для настройки автоматизированных ответов на обнаруженные подозрительные действия. Можно включить автоматические ответы через Azure Sentinel для реализации ответов на безопасность вашей организации.

Вы также можете глотать журналы в Azure Sentinel для дальнейшего изучения.

Как настроить расширенную защиту угроз для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Обзор защиты идентификационных данных Azure AD:https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Как посмотреть рискованные ввоза Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Как на борту Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг Центра безопасности Azure**: Недоступен

**Ответственность**: Клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Предоставить корпорации Майкрософт доступ к соответствующим данным клиентов во время сценариев поддержки

**Руководство**: Не применимо; Локтора клиента пока не поддерживается для базы данных Azure для MyS'L.

Список поддерживаемых служб блокировки клиентов:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

## <a name="data-protection"></a>Защита данных

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Ведение перечня конфиденциальной информации

**Руководство**: Используйте теги для отслеживания базы данных Azure для экземпляров MyS'L или связанных с ними ресурсов, которые хранят или обрабатывают конфиденциальную информацию.

Как создавать и использовать теги:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Изолировать системы хранения или обработки конфиденциальной информации

**Руководство**: Реализация отдельных подписок и/или групп управления для разработки, тестирования и производства. Используйте комбинацию частных ссылок, конечных точек обслуживания и/или правил брандмауэра, чтобы изолировать и ограничить доступ к базе данных Azure для экземпляров MyS'L.

Как создать дополнительные подписки Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Как создать группы управления:https://docs.microsoft.com/azure/governance/management-groups/create

Как настроить частную ссылку для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link

Как создавать и управлять конечными точками службы VNet и правилами VNet в базе данных Azure для MyS'L:https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Как настроить базу данных Azure для правил брандмауэра MyS'L:https://docs.microsoft.com/azure/mysql/concepts-firewall-rules


**Мониторинг Центра безопасности Azure**: Недоступен

**Ответственность**: Клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Руководство**: При использовании Azure VMs для доступа к базе данных Azure для экземпляров MyS'L, используйте конфигурации сети Private Link, MyS'L, группы сетевой безопасности и теги служб, чтобы уменьшить возможность эксфильтрации данных.

Корпорация Майкрософт управляет базовой инфраструктурой для базы данных Azure для MyS'L и внедрила строгие средства контроля для предотвращения потери или выдержки данных клиентов.

Как смягчить эксфильтрацию данных для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention

Понимание защиты данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Шифрование всей конфиденциальной информации в пути

**Руководство**: База данных Azure для MyS'L поддерживает подключение сервера MyS'L к клиентским приложениям с использованием безопасных sockets Layer (SSL). Применение SSL-соединений между сервером базы данных и клиентскими приложениями обеспечивает защиту от атак "злоумышленник в середине" за счет шифрования потока данных между сервером и приложением. На портале Azure убедитесь, что «Принудительное подключение SSL» включено для всех ваших баз данных Azure для экземпляров MyS'L по умолчанию.

В настоящее время версия TLS, поддерживаемая для базы данных Azure для MyS'L, является TLS 1.0, TLS 1.1, TLS 1.2.

Как настроить шифрование в транзите для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security

**Мониторинг Центра безопасности Azure**: Недоступен

**Ответственность**: Общие сведения

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Используйте инструмент активного обнаружения для идентификации конфиденциальных данных

**Руководство**: Функции идентификации, классификации и предотвращения потерь пока не доступны для базы данных Azure для MyS'L. Внедрить стороннее решение, если это необходимо для целей соответствия.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт рассматривает все содержимое клиента как конфиденциальный и делает все усилия для защиты от потери данных клиентов и их воздействия. Для обеспечения безопасности данных клиентов в Azure корпорация Майкрософт внедрила и поддерживает набор надежных средств управления и возможностей защиты данных.

Понимание защиты данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure**: Недоступен

**Ответственность**: Общие сведения

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Используйте Azure RBAC для управления доступом к ресурсам

**Руководство**: Используйте элемент управления доступом на основе ролей Azure (RBAC) для управления доступом к базе данных Azure для плоскости управления MyS'L (например, портал Azure). Для доступа к плоскости данных (в самой базе данных) используйте запросы s-L для создания пользователей и настройки пользовательских разрешений. RBAC не влияет на пользовательские разрешения в базе данных.

Как настроить RBAC в Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Как настроить пользовательский доступ с помощью S'L для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-create-users

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Используйте предотвращение потери данных на основе хоста для обеспечения контроля доступа

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

Корпорация Майкрософт управляет базовой инфраструктурой для базы данных Azure для MyS'L и внедрила строгие средства контроля для предотвращения потери или выдержки данных клиентов.

Понимание защиты данных клиентов в Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Шифрование конфиденциальной информации в состоянии покоя

**Руководство**: База данных Azure для сервиса MyS'L использует проверенный криптографический модуль FIPS 140-2 для шифрования данных на отдыхе. Данные, включая резервные, шифруются на диске, за исключением временных файлов, созданных во время выполнения запросов. Служба использует 256-разрядный шифр AES, включенный в шифрование службы хранилища Azure. Ключами управляет система. Шифрование хранилища всегда включено, и его нельзя отключить.

Шифрование данных с ключами, управляемыми клиентами для базы данных Azure для MyS'L, позволяет использовать свой собственный ключ (BYOK) для защиты данных в состоянии покоя. В это время необходимо запросить доступ к этой возможности. Для этого свяжитесь:

AskAzureDBforMySQL@service.microsoft.com

Понять шифрование на отдых для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-security

Как настроить управляемые ключи клиента для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-data-encryption-mysql

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Войти и предупредить об изменениях в критических ресурсах Azure

**Руководство**: Используйте azure Monitor с журналом действий Azure для создания оповещений о случаях, когда происходят изменения в производственных экземплярах базы данных Azure для MyS'L и других критически важных или связанных с ними ресурсов.

Как создать оповещения для событий журнала активности Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="vulnerability-management"></a>Управление уязвимостями

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Запуск автоматизированных инструментов сканирования уязвимостей

**Руководство**: В настоящее время недоступно; Центр безопасности Azure пока не поддерживает оценку уязвимости для базы данных Azure для MyS'L.

Покрытие функций для служб Azure PaaS в Центре безопасности Azure:https://docs.microsoft.com/azure/security-center/features-paas

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Развертывание автоматизированного решения для управления патчами сторонних программ

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Сравните сканирование уязвимости спина к спине

**Руководство**: Не применимо; это руководство предназначено для вычислений ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Используйте процесс оценки рисков, чтобы определить приоритеты в устранении обнаруженных уязвимостей

**Руководство**: Корпорация Майкрософт выполняет управление уязвимостями в базовых системах, поддерживающих базу данных Azure для MyS'L.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Используйте обнаружение активов Azure

**Руководство**: Используйте график ресурсов Azure для запроса и обнаружения всех ресурсов (включая базу данных Azure для экземпляров MyS'L) в вашей подписке(ы). Убедитесь, что у вас есть соответствующие (читай) разрешения в арендаторе и можете перечислить все подписки Azure, а также ресурсы в рамках подписки.

Как создавать запросы с помощью Графика Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Как просмотреть подписку на Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Поймите Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="62-maintain-asset-metadata"></a>6.2: Поддержание метаданных активов

**Руководство**: Приложить теги к базе данных Azure для экземпляров MyS'L и другим связанным с ними ресурсам, дающим метаданные, чтобы логически организовать их в таксономию.

Как создавать и использовать теги:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Удалить несанкционированные ресурсы Azure

**Руководство**: Используйте пометки, группы управления и отдельные подписки, где это уместно, для организации и отслеживания базы данных Azure для экземпляров MyS'L и связанных с ними ресурсов. Регулярно согласовывать инвентаризацию и своевременно исключать несанкционированные ресурсы из подписки.

Как создать дополнительные подписки Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Как создать группы управления:https://docs.microsoft.com/azure/governance/management-groups/create

Как создавать и использовать теги:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Ведение перечня утвержденных ресурсов Azure и названий программного обеспечения

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов и Azure в целом.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

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

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Удалить неодобренные ресурсы и программные приложения Azure

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов и Azure в целом.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="68-use-only-approved-applications"></a>6.8: Используйте только одобренные приложения

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="69-use-only-approved-azure-services"></a>6.9: Используйте только утвержденные службы Azure

**Руководство**: Используйте политику Azure для ограничения типа ресурсов, которые могут быть созданы в подписке клиента (ы) с использованием следующих встроенных определений политики:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как отказать в определенном типе ресурсов с помощью политики Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="610-implement-approved-application-list"></a>6.10: Реализация утвержденного списка заявок

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Ограничьте возможности пользователей взаимодействовать с менеджером ресурсов Azure с помощью скриптов

**Руководство**: Используйте условный доступ Azure, чтобы ограничить возможности пользователей взаимодействовать с менеджером ресурсов Azure, нанастройки "Блок-доступ" для приложения "Управление Microsoft Azure". Это может предотвратить создание и изменение ресурсов в среде с высоким уровнем безопасности, таких как экземпляры базы данных Azure для MyS'L, содержащей конфиденциальную информацию.

Как настроить условный доступ, чтобы заблокировать доступ к менеджеру ресурсов Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ограничьте возможности пользователей по выполнению скриптов в вычислительных ресурсах

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Физически или логически сегрегировать приложения высокого риска

**Руководство**: Не применимо; эта рекомендация предназначена для веб-приложений, работающих на Azure App Service или для вычисления ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

## <a name="secure-configuration"></a>Настройка безопасности

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Создание безопасных конфигураций для всех ресурсов Azure

**Руководство**: Определите и внедрить стандартные конфигурации безопасности для вашей базы данных Azure для экземпляров MyS'L с политикой Azure. Используйте псевдонимы Azure Policy в пространстве имен «Microsoft.DBforMyS'L» для создания пользовательских политик для аудита или обеспечения сетевой конфигурации базы данных Azure для экземпляров MyS'L. Можно также использовать встроенные определения политики, связанные с базой данных Azure, для экземпляров MyS'L, такие как:

Для серверов баз данных MySQL должно быть включено принудительное использование SSL-соединения

Как просмотреть доступные Aliases Azure Policy:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Создание безопасных конфигураций операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Поддержание безопасных конфигураций ресурсов Azure

**Руководство**: Используйте политику Azure «отрицать» и «развертывать, если не существует» для обеспечения безопасных настроек в ресурсах Azure.

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Понять эффекты политики Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Поддержание безопасных конфигураций операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Безопасное хранение конфигурации ресурсов Azure

**Руководство:** При использовании пользовательских определений политики Azure для базы данных Azure для экземпляров MyS'L и связанных с ними ресурсов используйте Azure Repos для безопасного хранения и управления кодом.

Как хранить код в Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Документация Репос Azure:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Безопасно хранить пользовательские изображения операционной системы

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Инструменты управления конфигурацией конфигурации системы развертывание

**Руководство**: Используйте псевдонимы Azure Policy в пространстве имен «Microsoft.DBforMyS'L» для создания пользовательских политик для оповещения, аудита и обеспечения безопасности конфигураций систем. Кроме того, разработайте процесс и конвейер для управления исключениями политики.

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Развертывать инструменты управления конфигурацией системы для операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Внедрение автоматизированного мониторинга конфигурации для служб Azure

**Руководство**: Используйте псевдонимы Azure Policy в пространстве имен «Microsoft.DBforMyS'L» для создания пользовательских политик для оповещения, аудита и обеспечения безопасности конфигураций систем. Используйте политику Azure «аудит», «отрицать» и «развернуть, если не существует», чтобы автоматически принудить конфигурации для базы данных Azure для экземпляров MyS'L и связанных с ними ресурсов.

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Внедрение автоматизированного мониторинга конфигурации для операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="711-manage-azure-secrets-securely"></a>7.11: Надежное управление секретами Azure

**Руководство:** Для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, используемых для доступа к базе данных Azure для экземпляров MyS'L, используйте идентификатор управляемой службы совместно с Azure Key Vault для упрощения и защиты базы данных Azure для секретного управления MyS'L. Убедитесь, что Key Vault Soft Delete включен.

Как интегрироваться с управляемыми идентификаторами Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Как создать Убежище ключей:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Как обеспечить аутентификацию Key Vault с управляемой идентификацией:https://docs.microsoft.com/azure/key-vault/managed-identity

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Управление идентификаторами безопасно и автоматически

**Руководство**: База данных Azure для экземпляра MyS'L поддерживает аутентификацию активного каталога Azure (в предварительном просмотре) для доступа к базам данных.  При создании базы данных Azure для экземпляра MyS'L вы предоставляете учетные данные для пользователя администратора. Этот администратор может быть использован для создания дополнительных пользователей баз данных.  

Для виртуальных машин Azure или веб-приложений, работающих в службе приложений Azure, используемых для доступа к базе данных Azure для экземпляров MyS'L, используйте идентификатор управляемой службы совместно с Azure Key Vault для хранения и получения учетных данных для базы данных Azure для экземпляра MyS'L. Убедитесь, что Key Vault Soft Delete включен.

Используйте управляемые идентификаторы для предоставления службам Azure автоматического управляемого идентификатора в Active Directory (AD). Управляемые идентификаторы позволяют аутентифицировать проверку любой службы, которая поддерживает аутентификацию Azure AD, включая Key Vault, без каких-либо учетных данных в коде.

Как настроить управляемые идентификаторы:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Как интегрироваться с управляемыми идентификаторами Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Устранить непреднамеренное воздействие учетных данных

**Руководство**: Реализация сканера учетных данных для идентификации учетных данных в коде. Сканер учетных данных также будет способствовать перемещению обнаруженных учетных данных в более безопасные места, такие как Убежище ключей Azure.

Как настроить сканер учетных данных:https://secdevtools.azurewebsites.net/helpcredscan.html

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="malware-defense"></a>Защита от вредоносных программ

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Используйте централизованно управляемое программное обеспечение для борьбы с вредоносным ПО

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

Антивредоносное початек Microsoft включено на базовом узеле, поддерживающем службы Azure (например, база данных Azure для S'L), однако оно не выполняется на контенте клиента.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Предварительное сканирование файлов, которые будут загружены на некомпьютерные ресурсы Azure

**Руководство**: Антивредоносное початек Майкрософт включено на базовом узеле, поддерживающем службы Azure (например, база данных Azure для MyS'L), однако оно не работает на контенте клиента.

Предварительное сканирование любого содержимого, загруженного на некомпьютерные ресурсы Azure, такие как Служба приложений, Хранение озер данных, хранилище blob, база данных Azure для MyS'L и т.д. Корпорация Майкрософт не может получить доступ к вашим данным в этих случаях.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Общие сведения

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Обеспечить обновление антивредоносного программного обеспечения и подписей

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

Антивредоносное початек Microsoft включено на базовом узеле, поддерживающем службы Azure (например, база данных Azure для MyS'L), однако оно не работает на контенте клиента.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: N/A

## <a name="data-recovery"></a>Восстановление данных

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Обеспечить регулярные автоматизированные резервные истектоли

**Руководство**: База данных Azure для MyS'L занимает резервное копирование файлов данных и журнал транзакций. В зависимости от поддерживаемого максимального размера хранилища мы либо берем полные и дифференциальные резервные копии (4 сервера хранения с максимальным объемом хранения ТБ), либо резервные копии моментального снимка (до 16-ТБ максимальных серверов хранения). При помощи этих резервных копий вы можете восстановить сервер до любой точки во времени в пределах заданного срока хранения резервных копий. По умолчанию срок хранения резервных копий составляет 7 дней. При необходимости вы увеличить его вплоть до 35 дней. Все резервные копии шифруются с помощью 256-битового шифрования AES.

Понять резервные копирования для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-backup

Поймите базу данных Azure для начальной конфигурации MyS'L:https://docs.microsoft.com/azure/mysql/tutorial-design-database-using-portal

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Общие сведения

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Выполните полное резервное копирование системы и резервное копирование любых ключей, управляемых клиентом

**Руководство**: База данных Azure для MyS'L автоматически создает резервные копии серверов и хранит их в локально-избыточном или гео-избыточном хранилище, в соответствии с выбором пользователя. Резервные копии можно использовать для восстановления сервера до точки во времени. Резервное копирование и восстановление данных являются важной частью любой стратегии непрерывности бизнес-процессов. Таким образом данные защищаются от случайного повреждения или удаления. 

При использовании Azure Key Vault для хранения учетных данных для базы данных Azure для экземпляров MyS'L убедитесь, что автоматические резервные копирования ключей. 

Понять резервные копирования для базы данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/howto-restore-server-portal 

Как создать резервную резервную резервную часть ключей Убежища:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Общие сведения

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Проверка всех резервных ups, включая управляемые ключей клиента

**Руководство**: В базе данных Azure для MyS'L выполнение восстановления создает новый сервер из резервных данных исходного сервера. Доступны два типа восстановления: восстановление point-in-time и Geo-restore. Восстановление до точки во времени доступно для любого типа избыточности резервного копирования. При таком восстановлении новый сервер создается в том же регионе, где расположен исходный сервер. Географическое восстановление доступно, если для сервера настроено геоизбыточное хранилище, и позволяет восстановить сервер в другом регионе.

Предполагаемое время восстановления будет зависеть от нескольких факторов, включая размер базы данных, размер журнала транзакций, пропускную способность сети и общее количество баз данных, восстанавливаемых в том же регионе и в то же время. Обычно время восстановления составляет менее 12 часов.

Периодически тестируйте восстановление базы данных Azure для экземпляров MyS'L.

Понимание резервного копирования и восстановление в базе данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-backup

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Обеспечить защиту резервных и управляемых ключей клиента

**Руководство**: База данных Azure для MyS'L занимает полное, дифференциальное и резервное копирование журналов транзакций. При помощи этих резервных копий вы можете восстановить сервер до любой точки во времени в пределах заданного срока хранения резервных копий. По умолчанию срок хранения резервных копий составляет 7 дней. При необходимости вы увеличить его вплоть до 35 дней. Все резервные копии шифруются с помощью 256-битового шифрования AES. Убедитесь, что Key Vault Soft Delete включен.

Понимание резервного копирования и восстановление в базе данных Azure для MyS'L:https://docs.microsoft.com/azure/mysql/concepts-backup

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

## <a name="incident-response"></a>реагирование на инциденты.

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)*

### <a name="101-create-an-incident-response-guide"></a>10.1: Создать руководство по реагированию на инциденты

**Руководство**: Создайте руководство по реагированию на инциденты для вашей организации. Убедитесь, что существуют письменные планы реагирования на инциденты, определяющие все роли персонала, а также этапы обработки/управления инцидентами от обнаружения до обзора после инцидента.

Как настроить автоматизацию рабочих процессов в Центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Руководство по созданию собственного процесса реагирования на инциденты безопасности:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Анатомия инцидента в Центре реагирования на инцидент:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Клиент может также использовать NIST в компьютерной безопасности инцидентов Руководство по обработке помощи в создании своего собственного плана реагирования на инциденты:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Создание процедуры оценки и приоритетов инцидентов

**Руководство**: Центр безопасности присваивает серьезность каждому оповещению, чтобы помочь вам определить приоритеты, какие предупреждения должны быть исследованы в первую очередь. Тяжесть основана на том, насколько уверен центр безопасности в поиске или аналитический используется для выдачи оповещения, а также уровень уверенности, что был злой умысел за деятельность, которая привела к оповещению.

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
