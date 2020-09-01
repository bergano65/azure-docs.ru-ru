---
title: Базовые показатели безопасности Azure
description: Базовый уровень безопасности Azure для базы данных SQL Azure и Управляемый экземпляр Azure SQL
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 846d66a1cf1bb1d97f7ab9d7dfd7bbcf43d3f8d6
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231033"
---
# <a name="azure-security-baseline-for-azure-sql-database--sql-managed-instance"></a>Базовый план безопасности Azure для базы данных SQL Azure & SQL Управляемый экземпляр
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Базовый план безопасности Azure для базы данных SQL Azure содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовый план безопасности для этой службы взят из [Эталона безопасности Azure версии 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), содержащего рекомендации по обеспечению безопасности облачных решений в Azure с помощью наших практических рекомендаций.

Дополнительные сведения см. в статье [Обзор базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в статье [Управление безопасностью: безопасность сети](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1. Защита ресурсов в виртуальной сети с помощью групп безопасности сети или Брандмауэра Azure

**Руководство**. Вы можете включить функцию "Частная связь Azure", чтобы разрешить доступ к службам Azure PaaS (например, к базе данных SQL) и размещенным в Azure клиентам или партнерским службам через частную конечную точку в виртуальной сети. Трафик между виртуальной сетью и службой проходит через магистральную сеть Майкрософт, что позволяет избежать рисков общедоступного Интернета.

Чтобы трафик поступал в базу данных SQL Azure, используйте теги служб SQL, которые разрешают исходящий трафик через механизм групп безопасности сети.

Правила виртуальной сети позволяют базе данных SQL Azure принимать только подключения, отправленные из выбранных подсетей в виртуальной сети.

Как настроить частную связь для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Как использовать конечные точки службы и правила виртуальной сети для серверов:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Используйте центр безопасности Azure и исправьте рекомендации по защите сети для подсети, в которой развернута база данных SQL Azure.

Для виртуальных машин Azure, которые будут подключаться к базе данных SQL Azure, включите журналы потоков группы безопасности сети (NSG) для группы безопасности сети, защищающих эти виртуальные машины, и отправьте журналы в учетную запись хранения Azure для аудита трафика.

Вы также можете отправить журналы потоков для группы безопасности сети в рабочую область Log Analytics и использовать аналитику трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитики трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять некорректные сетевые настройки.

Включение журналов потоков NSG: 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Общие сведения о безопасности сети, предоставляемой центром безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Как включить и использовать Аналитика трафика:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Общие сведения о безопасности сети, предоставляемой центром безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3. Защита критических веб-приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для служб приложений Azure или ресурсов вычислений, в которых размещены веб-приложения.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4. Запрет взаимодействия с известными опасными IP-адресами

**Рекомендации**. Включите стандарт защиты от атак DDoS в виртуальных сетях, связанных с базой данных SQL Azure, для защиты от атак типа "отказ в обслуживании". Используйте интегрированную аналитику угроз Центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми IP-адресами Интернета.

Настройка защиты от атак DDoS: 

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Общие сведения о центре безопасности Azure, интегрированной с системой анализа угроз.

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5. Запись сетевых пакетов и журналов потоков

**Руководство**. для виртуальных машин Azure, которые будут подключаться к экземпляру базы данных SQL Azure, включите журналы потоков для группы безопасности сети (NSG) для группы безопасности сети, защищающие эти виртуальные машины, и отправьте журналы в учетную запись хранения Azure для аудита трафика. Если требуется для изучения аномальных действий, включите запись пакетов наблюдателя за сетями.

Как включить журналы потоков NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить наблюдатель за сетями:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Развертывание сетевых систем обнаружения и предотвращения вторжений (IDS/IPS)

**Руководство**. Включение расширенной защиты от угроз (ATP) для базы данных SQL Azure.  Пользователи получают оповещения о подозрительных действиях с базами данных, потенциальных уязвимостях, атаках путем внедрения кода SQL и аномальных закономерностей в доступе к базам данных и шаблонам запросов. Кроме того, с помощью центра безопасности Azure вы можете интегрировать оповещения в службу Advanced Threat protection.

Общие сведения и использование расширенной защиты от угроз для базы данных SQL Azure: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7. Управление трафиком к веб-приложениям

**Руководство**: неприменимо; Эта рекомендация предназначена для служб приложений Azure или ресурсов вычислений, в которых размещены веб-приложения.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Уменьшение сложности и дополнительных затрат на администрирование в правилах безопасности сети

**Руководство**. Использование тегов службы виртуальной сети для определения элементов управления доступом к сети для групп безопасности сети или брандмауэра Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, ApiManagement) в соответствующем исходном поле или поле назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, входящих в тег службы, и автоматически обновляет этот тег при изменении адресов.

При использовании конечных точек службы для базы данных SQL Azure требуется исходящий трафик к общедоступным IP-адресам базы данных SQL Azure. чтобы разрешить подключение, необходимо открыть группы безопасности сети (группы безопасности сети) для прав доступа к базе данных SQL Azure. Это можно сделать с помощью тегов службы NSG для базы данных SQL Azure.

Общие сведения о тегах служб с конечными точками службы для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Общие сведения и использование тегов служб:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9. Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определение и реализация конфигураций безопасности сети для базы данных SQL Azure с помощью политики Azure. Вы можете использовать пространство имен Microsoft. SQL для определения пользовательских политик или использовать любое из встроенных определений политик, предназначенных для защиты сети сервера. Пример применимой политики сетевой безопасности для сервера: "база данных SQL должна использовать конечную точку службы виртуальной сети".

Используйте схемы Azure для упрощения крупномасштабных развертываний Azure с помощью ключевых артефактов среды пакетов, таких как шаблоны управления ресурсами Azure, управление доступом на основе ролей (RBAC) и политики, в одном определении схемы. Простое применение схемы к новым подпискам и средам, а также управление точной настройкой и управление с помощью версионирования.

Настройка Политики SQL Azure и управление ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Создание схемы Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10. Документация по правилам конфигурации трафика

**Руководство**. Использование тегов для групп безопасности сети (NSG) и других ресурсов, относящихся к сетевой безопасности и потоку трафика. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и длительность (и т. д.) любых правил, которые разрешают трафик в сеть и из нее.

Используйте любое из встроенных определений политик Azure, связанных с тегами, например "требовать тег и его значение", чтобы убедиться, что все ресурсы созданы с помощью тегов и уведомлять вас о существующих ресурсах без тегов.

Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий с ресурсами на основе их тегов.

Создание и использование тегов: 

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений сетевых ресурсов, связанных с сервером. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых ресурсов.

Просмотр и получение событий журнала действий Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Создание оповещений в Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в статье [Управление безопасностью: ведение журналов и мониторинг](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1. Использование утвержденных источников синхронизации времени

**Руководство**. Майкрософт поддерживает источники времени для ресурсов Azure. Вы можете обновить синхронизацию времени для развертывания вычислений.

Как настроить синхронизацию времени для ресурсов вычислений Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2. Настройка централизованного управления журналами безопасности

**Руководство**. Включение аудита для базы данных SQL Azure для мониторинга событий базы данных и записи их в журнал аудита в учетной записи хранения Azure, log Analytics рабочей области или концентраторах событий.

Кроме того, вы можете выполнять потоковую передачу телеметрии диагностики SQL Azure в Аналитика SQL Azure, облачное решение, которое наблюдает за производительностью базы данных SQL Azure и Azure SQL Управляемый экземпляр в масштабе и в нескольких подписках. Решение поможет собирать и визуализировать показатели производительности Базы данных SQL Azure, а также обладает встроенными интеллектуальными возможностями для устранения неполадок производительности.

Как настроить аудит для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Как получить журналы и метрики платформы с помощью Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Потоковая передача диагностики в Аналитика SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Включение журналов аудита для ресурсов Azure

**Руководство**. Включите аудит на сервере и выберите место хранения журналов аудита (служба хранилища Azure, log Analytics или концентратор событий).

Как включить аудит для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Сбор журналов безопасности из операционных систем

**Руководство**: неприменимо; Этот тест производительности предназначен для расчетных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="25-configure-security-log-storage-retention"></a>2.5. Настройка хранения журнала безопасности

**Руководство**. при хранении журналов базы данных SQL Azure в log Analytics рабочей области задайте срок хранения журнала согласно нормативным требованиям Организации.

Как задать параметры хранения журнала:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6. Мониторинг и просмотр журналов

**Руководство**. анализ и мониторинг журналов для аномальных поведений и регулярная проверка результатов. Используйте расширенную защиту от угроз в центре безопасности Azure для оповещения о необычных действиях, связанных с экземпляром базы данных SQL Azure. Кроме того, можно настроить оповещения на основе значений метрик или записей журнала действий Azure, связанных с экземплярами базы данных SQL Azure.

Общие сведения о расширенной защите от угроз и предупреждениях для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Как настроить пользовательские предупреждения для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7. Включение оповещений об аномальных действиях

**Руководство**. Использование расширенной защиты от угроз Azure Security Center для базы данных SQL Azure для мониторинга и оповещения о аномальных действиях. Включите расширенную защиту данных для баз данных SQL. Расширенная защита данных включает в себя функции для обнаружения и классификации конфиденциальных данных, отображая и устранения потенциальных уязвимостей баз данных, а также обнаружения аномальных действий, которые могут указывать на угрозу для базы данных.

Общие сведения о расширенной защите от угроз и предупреждениях для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Как включить расширенную защиту данных для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Управление оповещениями в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8. Централизованное ведение журнала защиты от вредоносных программ

**Руководство**: неприменимо; для базы данных SQL Azure решение для защиты от вредоносных программ управляется корпорацией Майкрософт на базовой платформе.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="29-enable-dns-query-logging"></a>2.9. Включение ведения журнала запросов DNS

**Руководство**: неприменимо; Ведение журнала DNS неприменимо к базе данных SQL Azure.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="210-enable-command-line-audit-logging"></a>2.10. Включение ведения журнала аудита для командной строки

**Руководство**: неприменимо; Аудит командной строки не применяется к базе данных SQL Azure.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Дополнительные сведения см. в статье [Управление безопасностью: идентификаторы и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Инвентаризация учетных записей администраторов

**Руководство**. Azure Active Directory (AAD) имеет встроенные роли, которые должны быть явно назначены и доступны для запросов. Используйте модуль AAD PowerShell для выполнения специальных запросов для обнаружения учетных записей, входящих в группы администраторов.

Как получить роль каталога в Azure AD с помощью PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Как получить членов роли каталога в Azure AD с помощью PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2. Изменение паролей по умолчанию, где применимо

**Рекомендации**. Azure Active Directory не имеет концепции паролей по умолчанию. При подготовке экземпляра базы данных SQL Azure рекомендуется интегрировать проверку подлинности с Azure Active Directory.

Как настроить проверку подлинности Azure Active Directory и управлять ею с помощью Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3. Применение выделенных административных учетных записей

**Руководство**. Создание политик и процедур, связанных с использованием выделенных административных учетных записей. Используйте учетную запись Центра безопасности Azure и управление доступом для мониторинга количества административных учетных записей.

Общие сведения об идентификации и доступе в Центре безопасности Azure: 

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4. Использование единого входа с Azure Active Directory

**Руководство**: неприменимо; Хотя вы можете настроить проверку подлинности Azure Active Directory для интеграции с базой данных SQL Azure, единый вход не поддерживается.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5. Использование многофакторной проверки подлинности для любого доступа на основе Azure Active Directory

**Руководство**. Включение многофакторной идентификации (MFA) Azure Active Directory (AAD) и следование рекомендациям по управлению удостоверениями и доступом в центре безопасности Azure.

Как включить MFA в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Мониторинг удостоверений и доступа в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Использование выделенных компьютеров (рабочих станций с привилегированным доступом) для всех административных задач

**Руководство**. Использование рабочей станции с привилегированным доступом (привилегированным доступом) с MFA, настроенным для входа и настройки ресурсов Azure.

Дополнительные сведения о рабочих станциях с привилегированным доступом:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Как включить MFA в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7. Ведение журнала и создание оповещений по подозрительным действиям учетных записей администраторов

**Рекомендации**. Используйте Azure Active Directory отчеты о безопасности для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде.

Используйте расширенную защиту от угроз для базы данных SQL Azure, чтобы обнаружить аномальные действия, указывающие на необычные и потенциально опасные попытки доступа к базам данных или их использования.

Определение пользователей Azure AD, помеченных для рискованных действий:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Как отслеживать действия пользователей и доступа в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Ознакомьтесь с дополнительными возможностями защиты от угроз и потенциальными оповещениями:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8. Управление ресурсами Azure только из утвержденных расположений

**Руководство**. Используйте условный доступ с именованными расположениями, чтобы разрешить доступ к порталу и управлению ресурсами Azure только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

Настройка именованных расположений в Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="39-use-azure-active-directory"></a>3.9. Использование Azure Active Directory

**Руководство**. создание администратора Azure Active Directory (AAD) для сервера.

Как настроить проверку подлинности Azure Active Directory и управлять ею с помощью Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Создание и настройка экземпляра AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10. Регулярная проверка и согласование доступа пользователей

**Руководство**. Azure Active Directory (AAD) предоставляет журналы для облегчения поиска устаревших учетных записей. Кроме того, используйте проверку доступа удостоверений Azure, чтобы эффективно управлять членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователей можно регулярно проверять, чтобы убедиться, что доступ к ним имеют только нужные пользователи.

Использование проверок доступа для идентификации Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11. Отслеживание попыток доступа к отключенным учетным записям

**Руководство**. Настройка проверки подлинности Azure Active Directory (AAD) с помощью Azure SQL и создание параметров диагностики для Azure Active Directory учетных записей пользователей, отправка журналов аудита и журналов входа в рабочую область log Analytics. Настройте нужные оповещения в Log Analytics рабочей области.

Как настроить проверку подлинности Azure Active Directory и управлять ею с помощью Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Как интегрировать журналы действий Azure в Azure Monitor: 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12. Предупреждение при подозрительном входе в учетную запись

**Руководство**. Использование Azure Active Directory (AAD) для защиты идентификации и обнаружения рисков для настройки автоматических ответов на обнаруженные подозрительные действия, связанные с удостоверениями пользователей. Кроме того, вы можете принимать данные в метку Azure для дальнейшего изучения.

Как просмотреть события входа в систему с рисками Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Настройка и включение политик риска для защиты идентификации.

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13. Предоставление корпорации Майкрософт доступа к соответствующим данным клиентов в рамках сценариев поддержки

**Руководство**. в сценариях поддержки, в которых корпорации Майкрософт требуется доступ к данным клиентов, защищенное хранилище Azure предоставляет пользователям интерфейс для просмотра и утверждения или отклонения запросов на доступ к данным клиентов.

Общие сведения о защищенное хранилище:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в статье [Управление безопасностью: защита данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Инвентаризация конфиденциальных данных

**Руководство**. Используйте теги для пометки ресурсов Azure, в которых хранятся или обрабатываются конфиденциальные данные.

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2. Изолирование систем, хранящих или обрабатывающих конфиденциальные данные

**Руководство**. Реализуйте отдельные подписки и группы управления для разработки, тестирования и производства. Ресурсы должны быть разделены виртуальной сетью или подсетью, помечены соответствующим образом и защищены в брандмауэре NSG или Azure. Ресурсы, которые хранят или обрабатывают конфиденциальные данные, должны быть изолированы. Использовать частную ссылку; Развертывание базы данных SQL Azure в виртуальной сети и подключение к частным конечным точкам в частном порядке.

Как создать дополнительные подписки Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание Группы управления:

https://docs.microsoft.com/azure/governance/management-groups/create

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Как настроить частную связь для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Руководство**. для баз данных в базе данных SQL Azure хранение или обработка конфиденциальной информации пометьте базу данных и связанные ресурсы как конфиденциальные с помощью тегов. Настройте закрытую связь в сочетании с тегами службы групп безопасности сети в экземплярах базы данных SQL Azure, чтобы предотвратить утечка конфиденциальной информации.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и предпринимает все возможные усилия для защиты клиентов от потери данных и раскрытия информации. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

Настройка частных ссылок и группы безопасности сети для предотвращения утечка данных в экземплярах базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Общие сведения о защите данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Шифрование любой конфиденциальной информации при передаче

**Руководство**. база данных SQL Azure обеспечивает защиту данных путем шифрования данных при перемещении с защитой транспортного уровня. База данных SQL в любое время обеспечивает шифрование (SSL/TLS) для всех подключений. Это гарантирует, что все данные шифруются "при передаче" между клиентом и сервером независимо от значения параметра Encrypt или TrustServerCertificate в строке подключения.

Общие сведения о шифровании SQL Azure при передаче:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Использование средства активного обнаружения для распознавания конфиденциальных данных

**Руководство**. Использование функции обнаружения и классификации данных в базе данных SQL Azure. Обнаружение и классификация данных предоставляет расширенные возможности, встроенные в базу данных SQL Azure, для обнаружения, классификации и пометки &amp; защиты конфиденциальных данных в базах данных.

Как использовать обнаружение и классификацию данных для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6. Контроль доступа к ресурсам с помощью RBAC

**Руководство**. Использование Azure Active Directory (AAD) для проверки подлинности и управления доступом к экземплярам базы данных SQL Azure.

Как интегрировать базу данных SQL Azure с Azure Active Directory для проверки подлинности:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Как управлять доступом в базе данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**. Корпорация Майкрософт управляет базовой инфраструктурой базы данных SQL Azure и реализует четкие элементы управления для предотвращения потери или раскрытия данных клиента.

Общие сведения о защите данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Неприменимо

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Шифрование конфиденциальной информации при хранении

**Руководство**. прозрачное шифрование данных (TDE) помогает защитить базу данных SQL Azure, azure SQL управляемый экземпляр и хранилище данных Azure от угроз вредоносной автономной активности, шифруя неактивных данных. Выполняется шифрование и расшифровка базы данных, связанных резервных копий и неактивных файлов журналов транзакций в реальном времени без необходимости изменения приложения. По умолчанию TDE включается для всех вновь развернутых баз данных SQL Server и SQL Управляемый экземпляр. Ключ шифрования TDE может управляться как корпорацией Майкрософт, так и клиентом.

Как управлять прозрачным шифрованием данных и использовать собственные ключи шифрования:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Включение в журнал и создание оповещений по изменениям критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналом действий Azure для создания оповещений о том, когда изменения выполняются в рабочих экземплярах базы данных SQL Azure и других критических или связанных ресурсах.

Создание оповещений для событий журнала действий Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="vulnerability-management"></a>Управление уязвимостями

*Дополнительные сведения см. в статье [Управление безопасностью: управление уязвимостями](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Выполнение автоматизированных средства анализа уязвимостей

**Рекомендации**. включите расширенную защиту данных для базы данных SQL Azure и следуйте рекомендациям центра безопасности Azure по выполнению оценки уязвимостей на серверах.

Как запустить оценку уязвимостей в базе данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Включение расширенной защиты данных.

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Как реализовать рекомендации по оценке уязвимостей центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Развертывание автоматизированного решения для управления исправлениями ПО сторонних производителей

**Руководство**: неприменимо; Этот тест производительности предназначен для расчетных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Сравнение проверок смежных уязвимостей

**Руководство**. Включение периодических повторяющихся проверок для экземпляров базы данных SQL Azure; будет настроена Оценка уязвимостей для автоматического выполнения проверки базы данных в течение недели. Сводка результатов отправляется ​​на указанный адрес электронной почты. Сравните результаты, чтобы убедиться, что уязвимости исправлены.

Как экспортировать отчет об оценке уязвимостей в центре безопасности Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Использование процесса оценки рисков для определения приоритета в устранении обнаруженных уязвимостей

**Руководство**. Используйте оценки рисков по умолчанию (Оценка безопасности), предоставляемые центром безопасности Azure.

Оценка безопасности центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в статье [Управление безопасностью: инвентаризация и управление ресурсами](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1. Использование обнаружения ресурсов Azure

**Руководство**. Использование графа ресурсов Azure для запроса и обнаружения всех ресурсов (включая базу данных SQL Azure) в ваших подписках.  Убедитесь, что у вас есть соответствующие разрешения (на чтение) в клиенте и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

Хотя классические ресурсы Azure можно обнаружить через Resource Graph, настоятельно рекомендуется в дальнейшем создавать и использовать ресурсы Azure Resource Manager.

Создание запросов с помощью Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Просмотр подписок Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Общие сведения об Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2. Ведение метаданных активов

**Руководство**. Применяйте к ресурсам Azure теги, чтобы логически классифицировать их на основе метаданных.

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Удаление неавторизованных ресурсов Azure

**Руководство**. Использование тегов, групп управления и отдельных подписок (при необходимости) для Организации и мониторинга ресурсов. Регулярно сверяйте ресурсы, чтобы своевременно удалять неавторизованные ресурсы из подписки.

Создание дополнительных подписок Azure: 

https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание Группы управления:

https://docs.microsoft.com/azure/governance/management-groups/create

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4. Ведение каталога утвержденных ресурсов Azure и наименований программного обеспечения

**Руководство**. Определение списка утвержденных ресурсов Azure и утвержденного программного обеспечения для ресурсов вычислений

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5. Отслеживание неутвержденных ресурсов Azure

**Рекомендации**. Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Используйте граф ресурсов Azure для запроса или обнаружения ресурсов в ваших подписках. Убедитесь в том, что все ресурсы Azure, представленные в среде, утверждены.

Настройка Политики SQL Azure и управление ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Создание запросов с помощью Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6. Отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="68-use-only-approved-applications"></a>6.8: Использование только утвержденных приложений

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="69-use-only-approved-azure-services"></a>6.9: Использование только утвержденных служб Azure

**Руководство**. Использование политики Azure для размещения ограничений на тип ресурсов, которые могут быть созданы в подписках клиента с помощью следующих встроенных определений политик:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Используйте граф ресурсов Azure для запроса или обнаружения ресурсов в ваших подписках. Убедитесь в том, что все ресурсы Azure, представленные в среде, утверждены.

Настройка Политики SQL Azure и управление ею: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Отказ от определенного типа ресурса с помощью Политики Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="610-implement-approved-application-list"></a>6.10. Реализация списка утвержденных приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для приложений, выполняющихся на ресурсах вычислений.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Ограничение возможности пользователей взаимодействовать с диспетчером ресурсов Azure с помощью сценариев

**Руководство**. Используйте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "Блокировать доступ" для приложения "Управление Microsoft Azure".

Настройка условного доступа для блокировки доступа к Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12. Ограничение возможности пользователей выполнять сценарии в ресурсах вычислений

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13. Физическое или логическое разделение приложений с высоким риском

**Руководство**: неприменимо; Эта рекомендация предназначена для служб приложений или ресурсов вычислений, в которых размещаются настольные системы или веб-приложения.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

## <a name="secure-configuration"></a>Настройка безопасности

*Дополнительные сведения см. в статье [Управление безопасностью: безопасная конфигурация](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Установка безопасных конфигураций для всех ресурсов Azure

**Руководство**. Используйте рекомендации по использованию политики Azure или центра безопасности Azure для базы данных SQL Azure, чтобы поддерживать конфигурации безопасности для всех ресурсов Azure.

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Сохранение безопасных конфигураций для операционных систем

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Сохранение безопасных конфигураций для ресурсов Azure

**Рекомендации**. Используйте Политику Azure [отказывать] и [развернуть, если не существует], чтобы обеспечить безопасность параметров в ресурсах Azure.

Настройка Политики SQL Azure и управление ею: 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Общие сведения о влиянии политики Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4. Сохранение безопасных конфигураций для операционных систем

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Безопасное хранение конфигурации ресурсов Azure

**Руководство**. Если вы используете пользовательские определения политики Azure, используйте Azure DevOps или Azure Repos для безопасного хранения кода и управления им.

Хранение кода в Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Документация по Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6. Безопасное хранение пользовательских образов операционной системы

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Развертывание инструментов управления конфигурацией системы

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. SQL для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.

Настройка Политики SQL Azure и управление ею: 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8. Развертывание средств управления конфигурацией системы для операционных систем

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Реализация автоматизированного мониторинга конфигурации для служб Azure

**Руководство**. Использование центра безопасности Azure для выполнения проверок базовых показателей для базы данных SQL Azure.

Как исправить рекомендации в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10. Реализация автоматизированного мониторинга конфигурации для операционных систем

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="711-manage-azure-secrets-securely"></a>7.11: Безопасное управление секретами Azure

**Руководство**. Использование Azure Key Vault для хранения ключей шифрования для прозрачное шифрование данных базы данных SQL Azure (TDE).

Как защитить конфиденциальные данные, хранящиеся в базе данных SQL Azure, и хранить ключи шифрования в Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12. Безопасное и автоматическое управление удостоверениями

**Руководство**. Использование управляемых удостоверений для предоставления служб Azure с автоматически управляемым удостоверением в Azure Active Directory (AAD). Управляемые удостоверения позволяют проходить проверку подлинности в любой службе, поддерживающей проверку подлинности AAD, включая Azure Key Vault без каких бы то ни было учетных данных в коде.

Учебник. использование управляемого удостоверения, назначенного системой виртуальной машины Windows, для доступа к Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Настройка управляемых удостоверений: 

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13. Устранение непреднамеренного раскрытия учетных данных

**Руководство**. Реализация средства проверки учетных данных для указания учетных данных в коде. Сканер учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault.

Настройка сканера учетных данных: https://secdevtools.azurewebsites.net/helpcredscan.html

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в статье [Управление безопасностью: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов. Корпорация Майкрософт обрабатывает Антивредоносные программы для базовой платформы.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2. Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся вычислительными

**Руководство**. Защита от вредоносных программ Майкрософт включена на базовом узле, поддерживающем службы Azure (например, в службе приложений Azure), но не выполняется в содержимом клиента.

Предварительное сканирование любого содержимого, отправляемого в Нерасчетные ресурсы Azure, например служба приложений, Data Lake Storage, хранилище BLOB-объектов, база данных SQL Azure и т. д. Корпорация Майкрософт не может получить доступ к данным в этих экземплярах.

Сведения о антивредоносном по Майкрософт для облачных служб и виртуальных машин Azure: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3. Своевременное обновление программного обеспечения для защиты от вредоносных программ и подписей

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов. Корпорация Майкрософт обрабатывает Антивредоносные программы для базовой платформы.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в статье [Управление безопасностью: восстановление данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1. Обеспечение регулярного автоматического резервного копирования

**Рекомендации**. чтобы защитить бизнес от потери данных, база данных SQL Azure автоматически создает полные резервные копии баз данных еженедельно, разностные резервные копии каждые 12 часов, а резервные копии журналов транзакций — каждые 5-10 минут. Резервные копии хранятся в хранилище RA-GRS по крайней мере за 7 дней для всех уровней служб. Все уровни служб, за исключением поддержка Basic настраиваемого срока хранения резервных копий для восстановления до точки во времени, до 35 дней.

Для удовлетворения различных требований соответствия можно выбрать разные периоды хранения для еженедельных, ежемесячных и (или) ежегодных резервных копий. Использование хранилища зависит от частоты резервного копирования и периода хранения.

Общие сведения о резервном копировании и непрерывности бизнес-процессов с помощью базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Совмещаемая блокировка

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Выполнение полного резервного копирования системы и любых ключей, управляемых клиентом

**Руководство**. база данных SQL Azure автоматически создает резервные копии базы данных, которые хранятся в интервале от 7 до 35 дней, и использует геоизбыточное хранилище с доступом на чтение Azure (RA-GRS), чтобы гарантировать их сохранность, даже если центр обработки данных недоступен. Эти резервные копии создаются автоматически. При необходимости включите долгосрочные геоизбыточные резервные копии для баз данных SQL Azure.

Если для прозрачное шифрование данных используются управляемые клиентом ключи, убедитесь, что резервное копирование ключей выполняется.

Общие сведения о резервном копировании в базе данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Как создать резервную копию ключей хранилища ключей в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3. Проверка всех резервных копий, включая управляемые клиентом ключи

**Руководство**. обеспечение возможности периодически выполнять восстановление данных в Azure Backup. При необходимости протестируйте содержимое для восстановления изолированной виртуальной ЛС. Проверка восстановления резервных копий ключей, управляемых клиентом.

Как восстановить ключи хранилища ключей в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Как восстановить резервные копии базы данных SQL Azure с помощью восстановления до точки во времени:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Обеспечение защиты резервных копий и управляемых клиентом ключей

**Руководство**. Включение обратимого удаления в Azure Key Vault для защиты ключей от случайного или вредоносного удаления.

Включение обратимого удаления в Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="incident-response"></a>реагирование на инциденты.

*Дополнительные сведения см. в статье [Управление безопасностью: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Создание руководства по реагированию на инциденты

**Руководство**. Убедитесь, что существуют планы реагирования на инциденты, определяющие роли персонала, а также этапы обработки инцидентов и управления ими.

Настройка автоматизации рабочих процессов в Центре безопасности Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Создание процедуры оценки инцидента и определения приоритетов

**Руководство**. Центр безопасности назначает серьезность предупреждениям, чтобы помочь вам определить порядок, в котором вы задаете каждое оповещение, чтобы при компрометации ресурса вы могли сразу перейти к нему. Серьезность основывается на том, насколько уверен Центр безопасности в исследовании или аналитике, используемой для оповещения, а также на уровне достоверности злонамеренности события, приведшего к оповещению.

Оповещения системы безопасности в центре безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="103-test-security-response-procedures"></a>10.3. Проверка процедур реагирования на угрозы

**Рекомендации**. Выполните упражнения, чтобы периодически протестировать возможности ваших систем реагировать на угрозы. Выявите слабые точки и пробелов и пересмотрите план по мере необходимости.

Вы можете обратиться к публикации NIST: руководство по тестированию, обучению и выполнению программ для ИТ-планов и возможностей.

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Предоставление контактных сведений и настройка уведомлений по инцидентам безопасности

**Руководство**. контактные данные инцидентов безопасности будут использоваться корпорацией Майкрософт для связи с вами, если Microsoft Security Response Center (MSRC) обнаруживает, что доступ к данным был получен незаконные или неавторизованной стороной.

Как задать контакт безопасности центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Включение оповещений системы безопасности в систему реагирования на инциденты

**Рекомендации**. Экспортируйте оповещения и рекомендации центра безопасности Azure с помощью функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Вы можете использовать соединитель данных центра безопасности Azure для потоковой передачи оповещений в метку.

Как настроить непрерывный экспорт:

https://docs.microsoft.com/azure/security-center/continuous-export

Как выполнить потоковую передачу предупреждений в Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6. Автоматизация реагирования на оповещения системы безопасности

**Рекомендации**. Используйте функцию автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" в оповещениях и рекомендациях системы безопасности.

Как настроить автоматизацию рабочего процесса и Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в статье [Управление безопасностью: тесты на проникновение и попытки нарушения безопасности "красной командой"](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1. Регулярное тестирование на проникновение ресурсов Azure и отслеживание исправлений всех критических точек безопасности в течение 60 дней

**Рекомендации**. Следуйте правилам взаимодействия Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Дополнительные сведения о стратегии корпорации Майкрософт и ее выполнении, а также о тестировании на основе уязвимости для управляемой облачной инфраструктуры, служб и приложений Майкрософт см. здесь: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Совмещаемая блокировка

## <a name="next-steps"></a>Дальнейшие действия

- См. [Тесты производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview).
- Узнайте больше о [Базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).
