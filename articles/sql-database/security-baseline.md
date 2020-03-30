---
title: Базовый универсаль безопасности Azure для базы данных Azure S'L
description: Базовый универсаль безопасности Azure для базы данных Azure S'L
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 35d4d44f0b9f1b210f38a034575b589c7211d55c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246676"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Базовый универсаль безопасности Azure для базы данных Azure S'L

Базовая база данных Azure Security для базы данных Azure S'L содержит рекомендации, которые помогут вам улучшить безопасность развертывания.

Базовый узел для этих служб взят из [версии 1.0 Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)которая содержит рекомендации о том, как можно обезопасить свои облачные решения в Azure с нашими рекомендациями по передовым практикам.

Для получения дополнительной информации смотрите [обзор базовых линий безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Защита ресурсов, использующих группы сетевой безопасности или брандмауэр Azure в виртуальной сети

**Руководство**: Вы можете включить частную ссылку Azure, чтобы разрешить доступ к службам Azure PaaS (например, база данных S'L) и Azure размещает услуги клиентов/партнеров по частной конечной точке в вашей виртуальной сети. Трафик между виртуальной сетью и службой проходит через магистральную сеть Майкрософт, что позволяет избежать рисков общедоступного Интернета. Чтобы трафик поступал в базу данных SQL Azure, используйте теги служб SQL, которые разрешают исходящий трафик через механизм групп безопасности сети.


Правила виртуальной сети позволяют базе данных Azure S'L принимать только сообщения, которые отправляются из выбранных подсетей внутри виртуальной сети.


Как настроить частную ссылку для базы данных Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database


Как использовать конечные точки виртуального сетевого обслуживания и правила для серверов баз данных:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Мониторинг и регистрация конфигурации и трафика Vnets, Subnets и NICs

**Руководство**: Использование Центра безопасности Azure и рекомендации по защите сети для подсети, в развертываемый сервер базы данных Azure S'L. Для виртуальных машин Azure (VM), которые будут подключаться к экземпляру Azure S'L Database Server, включите журналы потоков группы сетевой безопасности (NSG) для НСГ, защищающих эти виртуальные технологии, и отправьте журналы в учетную запись хранения Azure для аудита трафика. Вы также можете отправлять журналы потоков NSG в рабочее пространство Log Analytics и использовать аналитику трафика для получения информации о движении в облаке Azure. Некоторые преимущества Traffic Analytics — это возможность визуализации сетевой активности и выявления горячих точек, выявления угроз безопасности, понимания моделей движения и неправильной настройки сети.


Как включить журналы потока NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Понимание сетевой безопасности, предоставляемой Центром безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


Как включить и использовать аналитику трафика:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Понимание сетевой безопасности, предоставляемой Центром безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="13-protect-critical-web-applications"></a>1.3: Защита критически важных веб-приложений

**Руководство**: Не применимо; эта рекомендация предназначена для Службы приложений Azure или для вычисления ресурсов, худавив веб-приложений.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Запретить связь с известными вредоносными IP-адресами

**Руководство**: Включите стандарт Защиты DDoS для виртуальных сетей, связанных с экземплярами сервера S'L, для защиты от распределенных атак типа «отказ в обслуживании». Используйте Azure Security Center Integrated Threat Intelligence, чтобы отказать в сообщении с известными вредоносными или неиспользованными IP-адресами в Интернете.


Как настроить DDoS-защиту:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Поймите Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Запись сетевых пакетов и журналов потока

**Руководство:** Для виртуальных машин Azure (VMs), которые будут подключаться к экземпляру базы данных Azure S'L, включить журналы потоков группы сетевой безопасности (NSG) для НГУ, защищающие эти виртуальные технологии, и отправлять журналы в учетную запись хранения Azure для аудита трафика. При необходимости исследования аномальной активности включите пакет пакетnetwork-наблюдатель Network Watcher.


Как включить журналы потока NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Как включить сетевой наблюдатель:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Развертывание сетевых систем обнаружения/предотвращения вторжений (IDS/IPS)

**Руководство**: Включить расширенную защиту от угроз (ATP) для базы данных Azure S'L.  Пользователи получают оповещения о подозрительных действиях с базами данных, потенциальных уязвимостях, атаках путем внедрения кода SQL и аномальных закономерностей в доступе к базам данных и шаблонам запросов. Расширенная защита от угроз также интегрирует оповещения с Центром безопасности Azure.

Понимание и использование расширенной защиты от угроз для базы данных Azure S'L:https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Управление трафиком для веб-приложений

**Руководство**: Не применимо; эта рекомендация предназначена для Службы приложений Azure или для вычисления ресурсов, худавив веб-приложений.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Минимизация сложности и административных накладных расходов на правила сетевой безопасности

**Руководство**: Используйте виртуальные теги сетевого обслуживания для определения элементов управления доступом к сети в группах сетевой безопасности или Брандмауэре Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указывая имя тега службы (например, ApiManagement) в соответствующем поле источника или назначения правила, можно разрешить или отклонить трафик для соответствующей службы. Корпорация Майкрософт управляет адресными префиксами, охватываемыми тегом службы, и автоматически обновляет тег службы при изменении адресов.


При использовании конечных точек обслуживания для базы данных Azure S'L требуется выход на публичные IP-адреса базы данных Azure S'L: группы сетевой безопасности (НСГ) должны быть открыты для IP-адресов базы данных Azure S'L Database, чтобы обеспечить подключение. Это можно сделать, используя теги служб Ы NSG для базы данных Azure S'L.


Понимание тегов служб с конечными точками обслуживания для базы данных Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations


Понимание и использование тегов службы:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство:** Определение и реализация конфигураций сетевой безопасности для экземпляров сервера базы данных Azure S'L с помощью политики Azure. Для определения определений пользовательских политик или для определения пользовательских определений политики можно использовать пространство имен «Microsoft.Sql» или использовать любое из встроенных определений политики, предназначенных для защиты серверной сети серверов базы данных Azure S'L. Примером применимой встроенной сетевой политики безопасности для сервера базы данных Azure S'L может быть: «Сервер S'L должен использовать конечную точку виртуального сетевого обслуживания».
 

Используйте шаблоны Azure для упрощения крупномасштабных развертываний Azure путем упаковки ключевых артефактов среды, таких как шаблоны управления ресурсами Azure, управление доступом на основе ролей (RBAC) и политики, в единое определение чертежа. Легко применять план к новым подпискам и средам, а также тонкой настройки управления и управления с помощью версий.


Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Как создать план Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="110-document-traffic-configuration-rules"></a>1.10: Правила конфигурации трафика документов

**Руководство**: Использование тегов для групп сетевой безопасности (NSG) и других ресурсов, связанных с сетевой безопасностью и потоком трафика. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и/или продолжительность (и т.д.) для любых правил, разрешающие трафик в/из сети.


Используйте любое из встроенных определений политики Azure, связанных с пометкой, например "Требуемый тег и его значение", чтобы гарантировать, что все ресурсы создаются с тегами и уведомлять вас о существующих немаркированных ресурсах.


Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий на ресурсах, основанных на их тегах.


Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Используйте автоматизированные инструменты для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**: Используйте журнал действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений для сетевых ресурсов, связанных с экземплярами серверов базы данных Azure S'L. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критически важных сетевых ресурсов.


Как просмотреть и получить события журнала активности Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Как создать оповещения в Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Используйте утвержденные источники синхронизации времени

**Руководство**: Корпорация Майкрософт поддерживает источники времени для ресурсов Azure. Можно обновить синхронизацию времени для развертывания вычислений.



Как настроить синхронизацию времени для вычислительных ресурсов Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="22-configure-central-security-log-management"></a>2.2: Настройка централизованного управления журналами безопасности

**Руководство**: Включить аудит базы данных Azure S'L для отслеживания событий базы данных и записи их в журнал аудита в учетной записи хранения Azure, рабочей области аналитики журналов или концентрах событий.


Кроме того, можно передавать телеметрию диагностики Azure S'L в Azure S'L Analytics, облачное решение, которое отслеживает производительность баз данных Azure S'L, эластичных пулов и управляемых экземпляров в масштабе и в нескольких подписках. Решение поможет собирать и визуализировать показатели производительности Базы данных SQL Azure, а также обладает встроенными интеллектуальными возможностями для устранения неполадок производительности.

Как настроить аудит для базы данных Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing


Как собирать метрики и журналы ресурсов с помощью Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging


Как передать диагностику в аналитику Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#configure-the-streaming-export-of-diagnostic-telemetry

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Включить журнал аудита для ресурсов Azure

**Руководство:** Возможность аудита на экземпляре сервера базы данных Azure S'L и выбрать место хранения для журналов аудита (Azure Storage, Log Analytics или Концентратор событий).


Как включить аудит для сервера Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Сбор журналов безопасности из операционных систем

**Руководство**: Не применимо; этот бенчмарк предназначен для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="25-configure-security-log-storage-retention"></a>2.5: Настройка хранения журналов безопасности

**Руководство**: При хранении журналов базы данных Azure S'L в рабочем пространстве аналитики журналов установите период хранения журналов в соответствии с правилами соответствия организации.



Как установить параметры удержания журнала:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="26-monitor-and-review-logs"></a>2.6: Мониторинг и обзор журналы

**Руководство**: Анализ и мониторинг журналов для аномального поведения и регулярно просматривать результаты. Используйте расширенную защиту от угроз Центра безопасности Azure, чтобы предупредить о необычных действиях, связанных с экземпляром базы данных Azure S'L. Кроме того, назначьте оповещения на основе метеорных значений или записей журнала о деятельности Azure, связанных с экземплярами базы данных Azure S'L.


Поймите расширенную защиту угроз и оповещения для сервера Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


Как настроить пользовательские оповещения для базы данных Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Включить оповещения об аномальной активности

**Руководство**: Используйте Расширенную защиту от угроз Azure для баз данных Azure S'L для мониторинга и оповещения об аномальных действиях. Включите расширенную безопасность данных для ваших баз данных S'L. Расширенная безопасность данных включает в себя функциональность для обнаружения и классификации конфиденциальных данных, всплывающих и смягчающих потенциальные уязвимости базы данных, а также обнаружения аномальных действий, которые могут указывать на угрозу для вашей базы данных.



Понять расширенную защиту угроз и оповещение для базы данных Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview



Как включить расширенную безопасность данных для базы данных Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Как управлять оповещениями в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="28-centralize-anti-malware-logging"></a>2.8: Централизованное заготовку от вредоносных программ

**Руководство**: Не применимо; Для сервера Azure S'L Server антивредоносное решение управляется корпорацией Майкрософт на базовой платформе.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="29-enable-dns-query-logging"></a>2.9: Включить журнал запросов DNS

**Руководство**: Не применимо; Запись DNS не применяется к серверу Azure S'L Server.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="210-enable-command-line-audit-logging"></a>2.10: Включить журнал аудита командной строки

**Руководство**: Не применимо; аудит командной строки не применяется к серверу Azure S'L Server.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Ведение перечня административных счетов

**Руководство**: Активный каталог Azure (AAD) имеет встроенные роли, которые должны быть четко назначены и задаются запросом. Используйте модуль AAD PowerShell для выполнения специальных запросов для обнаружения учетных записей, входят в состав административных групп.


Как получить роль каталога в Azure AD с PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Как получить роль участников каталога в Azure AD с PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Изменение паролей по умолчанию, где это применимо

**Руководство**: Активный каталог Azure не имеет понятия паролей по умолчанию. При подготовке экземпляра базы данных Azure S'L рекомендуется интегрировать аутентификацию в Active Directory Azure.


Как настроить и управлять аутентификацией активных каталогов Azure с помощью Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Использование выделенных административных учетных записей

**Руководство**: Создание политик и процедур, окружающих использование выделенных административных учетных записей. Используйте identity identity и access Management Центра безопасности Azure для мониторинга количества административных учетных записей.



Поймите идентификацию и доступ центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Используйте одиночный вписаться (SSO) с активным каталогом Azure

**Руководство**: Не применимо; в то время как вы можете настроить активную проверку подлинности каталогов Azure для интеграции с Azure S'L Server, единая регистрация не поддерживается.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Используйте многофакторную аутентификацию для всего доступа на основе активного каталога Azure

**Руководство:** Включите активный каталог Azure (AAD) Multi-Factor Authentication (MFA) и следуйте рекомендациям Центра идентификации и управления доступом Azure.


Как включить МИД в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Как контролировать личность и доступ в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Используйте выделенные машины (Привилегированные рабочие станции доступа) для всех административных задач

**Руководство**: Используйте привилегированное рабочее место доступа (PAW) с мультифакторной аутентификацией МИД, настроенной для входа в ресурсы Azure и настройки.


Узнайте о рабочих станциях привилегированном доступе:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Как включить МИД в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Войти и предупредить о подозрительной активности с административных счетов

**Руководство**: Используйте отчеты безопасности Azure Active Directory для генерации журналов и предупреждений, когда в среде происходит подозрительная или небезопасная деятельность.



Используйте Расширенную защиту угроз для базы данных Azure S'L для обнаружения аномальных действий, указывающих на необычные и потенциально опасные попытки получить доступ к базам данных или эксплуатировать их.



Как определить пользователей Azure AD, помеченных для рискованных действий:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk



Как отслеживать идентификацию пользователей и доступ к деятельности в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access



Обзор Расширенной защиты от угроз и потенциальных предупреждений:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Управление ресурсами Azure только из утвержденных мест

**Руководство**: Используйте условные места доступа, чтобы обеспечить доступ к portal и Azure Resource Management только из конкретных логических групп диапазонов IP-адресов или стран/регионов.


Как настроить именованные места в Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="39-use-azure-active-directory"></a>3.9: Используйте активный каталог Azure

**Руководство:** Создайте администратора active Directory (AAD) Azure для экземпляров серверов базы данных Azure S'L.


Как настроить и управлять аутентификацией активных каталогов Azure с помощью Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Как создать и настроить экземпляр AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Регулярно проверяйте и согласовывая доступ пользователей

**Руководство**: Активный каталог Azure (AAD) предоставляет журналы, помогая обнаружить устаревшие учетные записи. Кроме того, используйте обзоры доступа КИОт Azure для эффективного управления членством в группах, доступа к корпоративным приложениям и ролевых назначений. Доступ пользователей может быть рассмотрен на регулярной основе, чтобы убедиться, что только правильные пользователи имеют постоянный доступ.


Как использовать обзоры доступа к идентификационным данным Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Попытка monitor получить доступ к деактивированным учетным записям

**Руководство:** Настройка функциональной каталога Azure Active Directory (AAD) с помощью Azure S'L и создание диагностических настроек для учетных записей пользователей Azure Active Directory, отправка журналов аудита и журналов входа в рабочее пространство Log Analytics. Налаживайте желаемые оповещения в рабочей области Log Analytics.


Как настроить и управлять аутентификацией активных каталогов Azure с помощью Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Как интегрировать журналы активности Azure в Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Оповещение об отклонении поведения входа в учетную запись

**Руководство:** Используйте Azure Active Directory (AAD) Identity Protection и обнаружения рисков для настройки автоматизированных ответов на обнаруженные подозрительные действия, связанные с идентификаторами пользователей. Кроме того, можно проемить данные в Azure Sentinel для дальнейшего изучения.


Как просмотреть вхливые в систему Azure AD риски риска:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Как настроить и включить политики защиты от идентификации:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Предоставить корпорации Майкрософт доступ к соответствующим данным клиентов во время сценариев поддержки

**Руководство**: В сценариях поддержки, в которых корпорации Майкрософт необходим доступ к данным клиентов, Azure Customer Lockbox предоставляет клиентам интерфейс для рассмотрения и утверждения или отклонения запросов на доступ к данным клиентов.


Понять клиентскую блокировку:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="data-protection"></a>Защита данных

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Ведение перечня конфиденциальной информации

**Руководство**: Используйте теги для отслеживания ресурсов Azure, которые хранят или обрабатывают конфиденциальную информацию.


Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Изолировать системы хранения или обработки конфиденциальной информации

**Руководство**: Реализация отдельных подписок и/или групп управления для разработки, тестирования и производства. Ресурсы должны быть разделены Vnet/Subnet, помечены соответствующим образом и защищены в NSG или Azure Firewall. Ресурсы, хранимые или обрабатывающие конфиденциальные данные, должны быть изолированы. Используйте частную ссылку; развертывание сервера Azure S'L Server в вашем Vnet и подключение в частном порядке с помощью частных конечных точек.



Как создать дополнительные подписки Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Как создать группы управления:

https://docs.microsoft.com/azure/governance/management-groups/create



Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



Как настроить частную ссылку для базы данных Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Руководство:** Для баз данных Azure S'L, хранящихся или обрабатывающих конфиденциальную информацию, отметьте базу данных и связанные с ней ресурсы как конфиденциальные с помощью тегов. Настройте Private Link совместно с тегами группы сетевой безопасности в экземплярах базы данных Azure S'L, чтобы предотвратить эксфильтрацию конфиденциальной информации.



Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт рассматривает все содержимое клиента как конфиденциальный и делает все усилия для защиты от потери данных клиентов и их воздействия. Для обеспечения безопасности данных клиентов в Azure корпорация Майкрософт внедрила и поддерживает набор надежных средств управления и возможностей защиты данных.



Как настроить частную ссылку и НСК, чтобы предотвратить эксфильтрацию данных в экземплярах базы данных Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview



Понимание защиты данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Шифрование всей конфиденциальной информации в пути

**Руководство**: База данных Azure S'L защищает ваши данные, шифруя данные в движении с помощью безопасности уровня транспорта. Сервер S'L обеспечивает шифрование (SSL/TLS) в любое время для всех подключений. Это гарантирует, что все данные зашифрованы "в пути" между клиентом и сервером, независимо от настройки шифрования или TrustServerCertificate в строке соединения.



Поймите шифрование Azure S'L в транзите:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Используйте инструмент активного обнаружения для идентификации конфиденциальных данных

**Руководство**: Используйте функцию обнаружения и классификации данных базы данных Azure S'L. Обнаружение и классификация данных обеспечивают расширенные возможности, встроенные &amp; в базу данных Azure S'L для обнаружения, классификации, маркировки конфиденциальных данных в базах данных.



Как использовать обнаружение данных и классификацию для сервера Azure S'L Server:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Используйте Azure RBAC для управления доступом к ресурсам

**Руководство**: Используйте активный каталог Azure (AAD) для проверки подлинности и контроля доступа к экземплярам базы данных Azure S'L.


Как интегрировать Azure S'L Server с active-каталогом Azure для проверки подлинности:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication


Как управлять доступом в Azure S'L Server:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Используйте предотвращение потери данных на основе хоста для обеспечения контроля доступа

**Руководство**: Корпорация Майкрософт управляет базовой инфраструктурой для базы данных Azure s'L и внедрила строгие средства контроля для предотвращения потери или выдержки данных клиентов.

Понимание защиты данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Шифрование конфиденциальной информации в состоянии покоя

**Руководство:** Прозрачное шифрование данных (TDE) помогает защитить базу данных Azure S'L, управляемый экземпляр Azure S'L и хранилище данных Azure от угрозы вредоносной деятельности в автономном режиме путем шифрования данных в состоянии покоя. Выполняется шифрование и расшифровка базы данных, связанных резервных копий и неактивных файлов журналов транзакций в реальном времени без необходимости изменения приложения. По умолчанию TDE включено для всех вновь развертываемых баз данных SQL Azure. Ключом шифрования TDE может управлять корпорация Майкрософт или заказчик.


Как управлять прозрачным шифрованием данных и использовать собственные ключи шифрования:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Войти и предупредить об изменениях в критических ресурсах Azure

**Руководство**: Используйте azure Monitor с журналом действий Azure для создания оповещений о случаях внесения изменений в производственные экземпляры баз данных Azure S-L и другие критически важные или связанные с ними ресурсы.


Как создать оповещения для событий журнала активности Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

## <a name="vulnerability-management"></a>Управление уязвимостями

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Запуск автоматизированных инструментов сканирования уязвимостей

**Руководство**: Включите расширенную безопасность данных для базы данных Azure S'L и следуйте рекомендациям Центра безопасности Azure по проведению оценки уязвимостей на серверах Azure S'L.



Как провести оценку уязвимости в базах данных Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment



Как обеспечить расширенную безопасность данных:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Как реализовать рекомендации по оценке уязвимостей Центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Развертывание автоматизированного решения для управления патчами сторонних программ

**Руководство**: Не применимо; этот бенчмарк предназначен для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Сравните сканирование уязвимости спина к спине

**Руководство**: Включить периодические периодические сканирование для экземпляров базы данных Azure S'L; это настраивает оценку уязвимости для автоматического выполнения сканирования в базе данных один раз в неделю. Сводка результатов отправляется ​​на указанный адрес электронной почты. Сравните результаты, чтобы убедиться, что уязвимости были исправлены.



Как экспортировать отчет об оценке уязвимости в Центре безопасности Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implement-vulnerability-assessment

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Используйте процесс оценки рисков, чтобы определить приоритеты в устранении обнаруженных уязвимостей

**Руководство**: Используйте оценки риска по умолчанию (Secure Score), предоставляемые Центром безопасности Azure.

Поймите безопасный счет центра безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Используйте обнаружение активов Azure

**Руководство**: Используйте график ресурсов Azure для запроса и обнаружения всех ресурсов (включая экземпляры Azure S'L Server) в подписке(ы).  Убедитесь, что у вас есть соответствующие (читай) разрешения в арендаторе и можете перечислить все подписки Azure, а также ресурсы в рамках подписки.


Хотя классические ресурсы Azure могут быть обнаружены с помощью графика ресурсов Azure, настоятельно рекомендуется создавать и использовать ресурсы Azure Resource Manager в будущем.


Как создавать запросы с помощью графика ресурсов Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Как просмотреть подписку на Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Поймите Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг Центра безопасности Azure:** не применимо

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

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Мониторинг неодобренных ресурсов Azure

**Руководство**: Используйте политику Azure для ограничения типа ресурсов, которые могут быть созданы в подписке клиента (ы) с использованием следующих встроенных определений политики:

- Недопустимые типы ресурсов
- Допустимые типы ресурсов

Используйте график ресурсов Azure для запроса/обнаружения ресурсов в подписке(s). Убедитесь, что все ресурсы Azure, присутствующие в среде, утверждены.

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как создавать запросы с помощью графика ресурсов Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Мониторинг неодобренных программных приложений в вычислительных ресурсах

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Удалить неодобренные ресурсы и программные приложения Azure

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="68-use-only-approved-applications"></a>6.8: Используйте только одобренные приложения

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="69-use-only-approved-azure-services"></a>6.9: Используйте только утвержденные службы Azure

**Руководство**: Используйте политику Azure для ограничения типа ресурсов, которые могут быть созданы в подписке клиента (ы) с использованием следующих встроенных определений политики:

- Недопустимые типы ресурсов
- Допустимые типы ресурсов

Используйте график ресурсов Azure для запроса/обнаружения ресурсов в подписке(s). Убедитесь, что все ресурсы Azure, присутствующие в среде, утверждены.

Как настроить и управлять политикой Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как отказать в определенном типе ресурсов с помощью политики Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="610-implement-approved-application-list"></a>6.10: Реализация утвержденного списка заявок

**Руководство**: Не применимо; эта рекомендация предназначена для приложений, работающих на вычислительных ресурсах.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Ограничьте возможности пользователей взаимодействовать с менеджером ресурсов Azure с помощью скриптов

**Руководство**: Используйте Azure Conditional Access, чтобы ограничить возможности пользователей взаимодействовать с менеджером ресурсов Azure, нанастройки "Блок-доступ" для приложения "Управление Microsoft Azure".


Как настроить условный доступ, чтобы заблокировать доступ к менеджеру ресурсов Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ограничьте возможности пользователей по выполнению скриптов в вычислительных ресурсах

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Физически или логически сегрегировать приложения высокого риска

**Руководство**: Не применимо; эта рекомендация предназначена для Службы приложений или вычислений ресурсов, худавав настольных или веб-приложений.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

## <a name="secure-configuration"></a>Настройка безопасности

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Создание безопасных конфигураций для всех ресурсов Azure

**Руководство**: Используйте рекомендации Azure Policy или Azure Security Center для серверов/баз данных Azure s'L для поддержания конфигураций безопасности для всех ресурсов Azure.


Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Создание безопасных конфигураций операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычисления ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Поддержание безопасных конфигураций ресурсов Azure

**Руководство**: Используйте политику Azure «отрицать» и «развертывать, если не существует» для обеспечения безопасных настроек в ресурсах Azure.



Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



Понять эффекты политики Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Поддержание безопасных конфигураций операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Безопасное хранение конфигурации ресурсов Azure

**Руководство:** При использовании пользовательских определений политики Azure используйте Azure DevOps или Azure Repos для безопасного хранения и управления кодом.



Как хранить код в Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Документация Репос Azure:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Безопасно хранить пользовательские изображения операционной системы

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Инструменты управления конфигурацией конфигурации системы развертывание

**Руководство**: Используйте псевдонимы Azure Policy в пространстве имен «Microsoft.S'L» для создания пользовательских политик для оповещения, аудита и обеспечения безопасности конфигураций систем. Кроме того, разработайте процесс и конвейер для управления исключениями политики.



Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Развертывать инструменты управления конфигурацией системы для операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Внедрение автоматизированного мониторинга конфигурации для служб Azure

**Руководство**: Использование Центра безопасности Azure для выполнения базовых сканирований для серверов и баз данных Azure S-L.



Как уделать рекомендации в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Внедрение автоматизированного мониторинга конфигурации для операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="711-manage-azure-secrets-securely"></a>7.11: Надежное управление секретами Azure

**Руководство**: Используйте Azure Key Vault для хранения ключей шифрования для прозрачного шифрования данных Лазурного средства (TDE).



Как защитить конфиденциальные данные, хранящиеся в Azure S'L Server, и хранить ключи шифрования в Хранилище ключей Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Управление идентификаторами безопасно и автоматически

**Руководство:** Используйте управляемые идентификаторы для предоставления службам Azure автоматического управления идентификатором в Active Directory Azure (AAD). Управляемые идентификаторы позволяют аутентифицировать проверку любой службы, поддерживающей аутентификацию AAD, включая Azure Key Vault, без каких-либо учетных данных в коде.


Учебник: Используйте систему Windows VM, назначенную управляемой интизией, чтобы получить доступ к Azure S'L:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql


Как настроить управляемые идентификаторы:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

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

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов. Корпорация Майкрософт обрабатывает антивредоносные программы для базовой платформы.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Предварительное сканирование файлов, которые будут загружены на некомпьютерные ресурсы Azure

**Руководство**: Антивредоносное початек Майкрософт включено на базовом узеле, поддерживающем службы Azure (например, Служба приложений Azure), однако оно не выполняется на контенте клиента.


Предварительное сканирование любого содержимого, загруженного на некомпьютерные ресурсы Azure, такие как Служба приложений, Хранение озер данных, Хранилище blob, Azure S'L Server и т.д. Корпорация Майкрософт не может получить доступ к вашим данным в этих случаях.


Поймите Microsoft Antimalware для облачных служб Azure и виртуальных машин:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Обеспечить обновление антивредоносного программного обеспечения и подписей

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов. Корпорация Майкрософт обрабатывает антивредоносные программы для базовой платформы.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

## <a name="data-recovery"></a>Восстановление данных

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Обеспечить регулярные автоматизированные резервные истектоли

**Руководство**: Для защиты вашего бизнеса от потери данных база данных Azure S'L автоматически создает полные резервные копии базы данных еженедельно, резервные копии дифференциальных баз данных каждые 12 часов и резервные копии журналов транзакций каждые 5 - 10 минут. Резервные копии хранятся в хранилище RA-GRS не менее 7 дней для всех уровней обслуживания. Все уровни обслуживания, за исключением базовой поддержки настраиваемый период хранения резервного копирования для точки в срок восстановления, до 35 дней.


Чтобы соответствовать различным требованиям соответствия, можно выбрать различные периоды хранения для еженедельного, ежемесячного и/или ежегодного резервного копирования. Использование хранилища зависит от частоты резервного копирования и периода хранения.


Понять резервные копирования и непрерывность бизнеса с Azure S'L Server:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Общие сведения

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Выполните полное резервное копирование системы и резервное копирование любых ключей, управляемых клиентом

**Руководство**: База данных Azure S'L автоматически создает резервные копии базы данных, которые хранятся от 7 до 35 дней, и использует гео-избыточное хранилище считываемых данных Azure (RA-GRS), чтобы гарантировать их сохранение, даже если центр обработки данных недоступен. Эти резервные копирования создаются автоматически. При необходимости включите долгосрочные георезервные резервные копирования для баз данных Azure S'L.


При использовании управляемых клиентом ключей для шифрования прозрачных данных убедитесь, что ваши ключи резервируются.


Понять резервные отсталые средства в сервере Azure S'L:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database


Как создать резервную связь с ключами хранилища в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Проверка всех резервных ups, включая управляемые ключей клиента

**Руководство**: Обеспечьте возможность периодически выполнять восстановление содержимого в резервном копировании Azure. При необходимости протест восстановите содержимое изолированного VLAN. Тест-восстановление резервных ключей, управляемых клиентом.


Как восстановить ключи хранилища в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


Как восстановить резервные данные базы данных Azure S'L с помощью восстановления point-in-time:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Обеспечить защиту резервных и управляемых ключей клиента

**Руководство**: Включить мягкое удаление в Azure Key Vault для защиты ключей от случайного или вредоносного удаления.


Как включить мягкое удаление в Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Мониторинг Центра безопасности Azure:** Да

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
Оповещения о безопасности в Центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-overview


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="103-test-security-response-procedures"></a>10.3: Процедуры реагирования на тестовую безопасность

**Руководство**: Проведение упражнений для проверки возможностей реагирования на инциденты систем на регулярной каденции. Выявление слабых мест и пробелов и пересмотр плана по мере необходимости.



Вы можете обратиться к публикации NIST: Руководство по тестированию, обучению и осуществлению программ для ИТ-планов и возможностей:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Предоставьте контактные данные об инцидентах безопасности и назначьте уведомления о поместью для инцидентов безопасности

**Руководство**: Контактная информация об инцидентах безопасности будет использоваться корпорацией Майкрософт для связи с вами, если Центр реагирования на безопасность Майкрософт (MSRC) обнаружит, что доступ к вашим данным был получен незаконной или несанкционированной стороной.



Как установить контакт с безопасностью Центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Включите оповещения о безопасности в систему реагирования на инциденты

**Руководство**: Экспорт оповещений и рекомендаций Центра безопасности Azure с использованием функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать оповещения и рекомендации либо вручную, либо в постоянном, непрерывном режиме. Для потоковой передачи оповещений в Sentinel можно использовать разъем данных Центра безопасности Azure.


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

## <a name="next-steps"></a>Дальнейшие действия

- Посмотреть [тест безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Узнайте больше об [базовых линиях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
