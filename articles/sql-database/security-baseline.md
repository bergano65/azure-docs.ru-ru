---
title: Базовый план безопасности Azure для базы данных SQL Azure
description: Базовый план безопасности Azure для базы данных SQL Azure
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d9cf78f3cc5399a9e974cd9b4f0b3f24c80dacce
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214125"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Базовый план безопасности Azure для базы данных SQL Azure

Базовый план безопасности Azure для базы данных SQL Azure содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовые показатели для этих служб взяты из [теста безопасности Azure версии 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), который содержит рекомендации по защите облачных решений в Azure с помощью наших рекомендаций.

Дополнительные сведения см. в статье [Общие сведения о базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в разделе Security [Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Защитите ресурсы с помощью групп безопасности сети или брандмауэра Azure в виртуальной сети.

**Руководство**. Вы можете включить функцию "Частная связь Azure", чтобы разрешить доступ к службам Azure PaaS (например, к базе данных SQL) и размещенным в Azure клиентам или партнерским службам через частную конечную точку в виртуальной сети. Трафик между виртуальной сетью и службой проходит через магистральную сеть Майкрософт, что позволяет избежать рисков общедоступного Интернета. Чтобы разрешить трафик для доступа к базе данных SQL Azure, используйте теги службы SQL, чтобы разрешить исходящий трафик через группы безопасности сети.


Правила виртуальной сети позволяют базе данных SQL Azure принимать только подключения, отправленные из выбранных подсетей в виртуальной сети.


Как настроить частную связь для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database


Как использовать конечные точки службы и правила виртуальной сети для серверов баз данных:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Используйте центр безопасности Azure и исправьте рекомендации по защите сети для подсети, в которой развернут сервер базы данных SQL Azure. Для виртуальных машин Azure, которые будут подключаться к экземпляру сервера базы данных SQL Azure, включите журналы потоков группы безопасности сети (NSG) для группы безопасности сети, защищающие эти виртуальные машины, и отправьте журналы в учетную запись хранения Azure для аудита трафика. Вы также можете отправить журналы потоков NSG в рабочую область Log Analytics и использовать Аналитика трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитика трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять несетевые настройки.


Как включить журналы потоков NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Общие сведения о безопасности сети, предоставляемой центром безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


Как включить и использовать Аналитика трафика:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Общие сведения о безопасности сети, предоставляемой центром безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="13-protect-critical-web-applications"></a>1,3: защита критически важных веб-приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для служб приложений Azure или ресурсов вычислений, в которых размещены веб-приложения.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: запретите обмен данными с известными вредоносными IP-адресами

**Руководство**. Включение стандарта защиты от атак DDoS в виртуальных сетях, связанных с экземплярами SQL Server, для защиты от атак типа "отказ в обслуживании". Используйте интегрированную аналитику угроз центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми IP-адресами Интернета.


Настройка защиты от атак DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Общие сведения о центре безопасности Azure, интегрированной с системой анализа угроз.

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: запись сетевых пакетов и журналов потоков

**Руководство**. для виртуальных машин Azure, которые будут подключаться к экземпляру базы данных SQL Azure, включите журналы потоков для группы безопасности сети (NSG) для группы безопасности сети, защищающие эти виртуальные машины, и отправьте журналы в учетную запись хранения Azure для аудита трафика. Если требуется для изучения аномальных действий, включите запись пакетов наблюдателя за сетями.


Как включить журналы потоков NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Как включить наблюдатель за сетями:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание систем обнаружения вторжений на основе сети и предотвращения вторжений (ИДЕНТИФИКАТОРы и IP-адреса)

**Руководство**. Включение расширенной защиты от угроз (ATP) для базы данных SQL Azure.  Пользователи получают оповещения о подозрительных действиях с базами данных, потенциальных уязвимостях, атаках путем внедрения кода SQL и аномальных закономерностей в доступе к базам данных и шаблонам запросов. Кроме того, с помощью центра безопасности Azure вы можете интегрировать оповещения в службу Advanced Threat protection.

Общие сведения и использование расширенной защиты от угроз для базы данных SQL Azure: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="17-manage-traffic-to-web-applications"></a>1,7. Управление трафиком к веб-приложениям

**Руководство**: неприменимо; Эта рекомендация предназначена для служб приложений Azure или ресурсов вычислений, в которых размещены веб-приложения.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: сократите сложность и издержки администрирования правил безопасности сети

**Руководство**. Использование тегов службы виртуальной сети для определения элементов управления доступом к сети для групп безопасности сети или брандмауэра Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, ApiManagement) в соответствующем поле источника или назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, включенными в тег службы, и автоматически обновляет тег службы при изменении адресов.


При использовании конечных точек службы для базы данных SQL Azure требуется исходящий трафик к общедоступным IP-адресам базы данных SQL Azure. чтобы разрешить подключение, необходимо открыть группы безопасности сети (группы безопасности сети) для прав доступа к базе данных SQL Azure. Это можно сделать с помощью тегов службы NSG для базы данных SQL Azure.


Общие сведения о тегах служб с конечными точками службы для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations


Общие сведения и использование тегов служб:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определение и реализация конфигураций безопасности сети для экземпляров сервера базы данных SQL Azure с помощью политики Azure. Вы можете использовать пространство имен Microsoft. SQL для определения пользовательских политик или использовать любое из встроенных определений политик, предназначенных для защиты сети сервера базы данных SQL Azure. Пример применимой встроенной политики безопасности сети для сервера базы данных SQL Azure: "SQL Server должен использовать конечную точку службы виртуальной сети".
 

Используйте схемы Azure для упрощения крупномасштабных развертываний Azure с помощью ключевых артефактов среды пакетов, таких как шаблоны управления ресурсами Azure, управление доступом на основе ролей (RBAC) и политики, в одном определении схемы. Простое применение схемы к новым подпискам и средам, а также тонкая настройка управления и управления с помощью управления версиями.


Как настроить политику Azure и управлять ей: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Создание Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="110-document-traffic-configuration-rules"></a>1,10: правила конфигурации трафика документов

**Руководство**. Использование тегов для групп безопасности сети (NSG) и других ресурсов, относящихся к сетевой безопасности и потоку трафика. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и (или) Длительность (т. д.) для любых правил, которые разрешают трафик из сети в сеть.


Используйте любое из встроенных определений политик Azure, связанных с тегами, например "требовать тег и его значение", чтобы убедиться, что все ресурсы созданы с помощью тегов и уведомлять вас о существующих ресурсах без тегов.


Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий с ресурсами на основе их тегов.


Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11. использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений сетевых ресурсов, связанных с экземплярами сервера базы данных SQL Azure. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых ресурсов.


Просмотр и получение событий журнала действий Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Создание оповещений в Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в разделе [Security Control: logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: используйте источники синхронизации утвержденного времени

**Руководство**. Майкрософт поддерживает источники времени для ресурсов Azure. Вы можете обновить синхронизацию времени для развертывания вычислений.



Как настроить синхронизацию времени для ресурсов вычислений Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="22-configure-central-security-log-management"></a>2,2: Настройка централизованного управления журналом безопасности

**Руководство**. Включение аудита для базы данных SQL Azure для мониторинга событий базы данных и записи их в журнал аудита в учетной записи хранения Azure, log Analytics рабочей области или концентраторах событий.


Кроме того, вы можете передавать данные телеметрии диагностики SQL Azure в Аналитика SQL Azure, облачное решение, которое отслеживает производительность баз данных SQL Azure, эластичных пулов и управляемых экземпляров в масштабе и нескольких подписках. Решение поможет собирать и визуализировать показатели производительности Базы данных SQL Azure, а также обладает встроенными интеллектуальными возможностями для устранения неполадок производительности.

Как настроить аудит для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing


Как получить метрики и журналы ресурсов с помощью Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging


Потоковая передача диагностики в Аналитика SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-diagnostic-telemetry-into-sql-analytics

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Включите ведение журнала аудита для ресурсов Azure.

**Руководство**. Включите аудит на экземпляре сервера базы данных SQL Azure и выберите место хранения журналов аудита (служба хранилища Azure, log Analytics или концентратор событий).


Как включить аудит для SQL Server Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: получение журналов безопасности из операционных систем

**Руководство**: неприменимо; Этот тест производительности предназначен для расчетных ресурсов.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="25-configure-security-log-storage-retention"></a>2,5: Настройка хранения журнала безопасности

**Руководство**. при хранении журналов базы данных SQL Azure в log Analytics рабочей области задайте срок хранения журнала согласно нормативным требованиям Организации.



Как задать параметры хранения журнала:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="26-monitor-and-review-logs"></a>2,6: мониторинг и просмотр журналов

**Руководство**. анализ и мониторинг журналов для аномальных поведений и регулярная проверка результатов. Используйте расширенную защиту от угроз в центре безопасности Azure для оповещения о необычных действиях, связанных с экземпляром базы данных SQL Azure. Кроме того, можно настроить оповещения на основе значений метрик или записей журнала действий Azure, связанных с экземплярами базы данных SQL Azure.


Общие сведения о расширенной защите от угроз и предупреждениях для SQL Server Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


Как настроить пользовательские предупреждения для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Включение оповещений для аномальных действий

**Руководство**. Использование Advanced Threat Protection в центре безопасности Azure для баз данных SQL Azure для мониторинга и оповещения о аномальных действиях. Включите расширенную защиту данных для баз данных SQL. Расширенная защита данных включает в себя функции для обнаружения и классификации конфиденциальных данных, отображая и устранения потенциальных уязвимостей баз данных, а также обнаружения аномальных действий, которые могут указывать на угрозу для базы данных.



Общие сведения о расширенной защите от угроз и предупреждениях для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview



Как включить расширенную защиту данных для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Управление оповещениями в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="28-centralize-anti-malware-logging"></a>2,8: централизованное ведение журнала защиты от вредоносных программ

**Руководство**: неприменимо; для SQL Server Azure решение для защиты от вредоносных программ управляется корпорацией Майкрософт на базовой платформе.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="29-enable-dns-query-logging"></a>2,9: Включение ведения журнала запросов DNS

**Руководство**: неприменимо; Ведение журнала DNS неприменимо к Azure SQL Server.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="210-enable-command-line-audit-logging"></a>2,10: Включение ведения журнала аудита в командной строке

**Руководство**: неприменимо; Аудит командной строки неприменим к Azure SQL Server.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Дополнительные сведения см. в разделе [Управление безопасностью: идентификация и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: ведение инвентаризации учетных записей администраторов

**Руководство**. Azure Active Directory (AAD) имеет встроенные роли, которые должны быть явно назначены и доступны для запросов. Используйте модуль AAD PowerShell для выполнения специальных запросов для обнаружения учетных записей, входящих в группы администраторов.


Как получить роль каталога в Azure AD с помощью PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Как получить членов роли каталога в Azure AD с помощью PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Измените пароли по умолчанию, если это применимо

**Рекомендации**. Azure Active Directory не имеет концепции паролей по умолчанию. При подготовке экземпляра базы данных SQL Azure рекомендуется интегрировать проверку подлинности с Azure Active Directory.


Как настроить проверку подлинности Azure Active Directory и управлять ею с помощью Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: используйте выделенные учетные записи администратора

**Руководство**. Создание политик и процедур, связанных с использованием выделенных административных учетных записей. Используйте управление удостоверениями и доступом центра безопасности Azure для мониторинга количества административных учетных записей.



Сведения об удостоверении и доступе центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4. Использование единого входа (SSO) с Azure Active Directory

**Руководство**: неприменимо; Хотя вы можете настроить проверку подлинности Azure Active Directory для интеграции с Azure SQL Server, единый вход не поддерживается.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: используйте многофакторную проверку подлинности для всех Azure Active Directoryного доступа

**Руководство**. Включение многофакторной идентификации (MFA) Azure Active Directory (AAD) и следование рекомендациям по управлению удостоверениями и доступом в центре безопасности Azure.


Как включить MFA в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Мониторинг удостоверений и доступа в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: используйте выделенные компьютеры (рабочие станции с привилегированным доступом) для всех административных задач

**Руководство**. Использование рабочей станции с привилегированным доступом (привилегированным доступом) с MFA, настроенным для входа и настройки ресурсов Azure.


Дополнительные сведения о рабочих станциях с привилегированным доступом:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Как включить MFA в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: регистрация и оповещение о подозрительных действиях из учетных записей администраторов

**Рекомендации**. Используйте Azure Active Directory отчеты о безопасности для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде.



Используйте расширенную защиту от угроз для базы данных SQL Azure, чтобы обнаружить аномальные действия, указывающие на необычные и потенциально опасные попытки доступа к базам данных или их использования.



Определение пользователей Azure AD, помеченных для рискованных действий:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk



Как отслеживать действия пользователей и доступа в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access



Ознакомьтесь с дополнительными возможностями защиты от угроз и потенциальными оповещениями:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: управление ресурсами Azure из только утвержденных расположений

**Руководство**. Используйте условный доступ с именованными расположениями, чтобы разрешить доступ к порталу и управлению ресурсами Azure только из конкретных логических групп диапазонов IP-адресов или стран и регионов.


Как настроить именованные расположения в Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="39-use-azure-active-directory"></a>3,9: используйте Azure Active Directory

**Руководство**. создание администратора Azure Active Directory (AAD) для экземпляров сервера базы данных SQL Azure.


Как настроить проверку подлинности Azure Active Directory и управлять ею с помощью Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Создание и настройка экземпляра AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: регулярно просматривайте и выверяйте доступ пользователей

**Руководство**. Azure Active Directory (AAD) предоставляет журналы для облегчения поиска устаревших учетных записей. Кроме того, используйте проверку доступа удостоверений Azure, чтобы эффективно управлять членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователей можно регулярно проверять, чтобы убедиться, что доступ к ним имеют только нужные пользователи.


Использование проверок доступа для идентификации Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: монитор пытается получить доступ к отключенным учетным записям

**Руководство**. Настройка проверки подлинности Azure Active Directory (AAD) с помощью Azure SQL и создание параметров диагностики для Azure Active Directory учетных записей пользователей, отправка журналов аудита и журналов входа в рабочую область log Analytics. Настройте нужные оповещения в Log Analytics рабочей области.


Как настроить проверку подлинности Azure Active Directory и управлять ею с помощью Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Как интегрировать журналы действий Azure в Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: предупреждение об отклонении режима входа учетной записи

**Руководство**. Использование Azure Active Directory (AAD) для защиты идентификации и обнаружения рисков для настройки автоматических ответов на обнаруженные подозрительные действия, связанные с удостоверениями пользователей. Кроме того, вы можете принимать данные в метку Azure для дальнейшего изучения.


Как просмотреть события входа в систему с рисками Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Настройка и включение политик риска для защиты идентификации.

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: предоставьте корпорации Майкрософт доступ к соответствующим пользовательским данным во время сценариев поддержки

**Руководство**. в сценариях поддержки, в которых корпорации Майкрософт требуется доступ к данным клиентов, защищенное хранилище Azure предоставляет пользователям интерфейс для просмотра и утверждения или отклонения запросов на доступ к данным клиентов.


Общие сведения о защищенное хранилище:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в разделе [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: поддержание инвентаризации конфиденциальной информации

**Руководство**. Использование тегов для отслеживания ресурсов Azure, в которых хранятся или обрабатываются конфиденциальные данные.


Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Изолируйте системы, хранящие или обрабатывающие конфиденциальные данные

**Руководство**. Реализация отдельных подписок и групп управления для разработки, тестирования и производства. Ресурсы должны быть разделены виртуальной сетью или подсетью, помечены соответствующим образом и защищены в брандмауэре NSG или Azure. Ресурсы, которые хранят или обрабатывают конфиденциальные данные, должны быть изолированы. Использовать частную ссылку; развертывание SQL Server Azure в виртуальной сети и подключение к частным конечным точкам в частном порядке.



Как создать дополнительные подписки Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Создание Группы управления:

https://docs.microsoft.com/azure/governance/management-groups/create



Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



Как настроить частную связь для базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Отслеживайте и блокируйте несанкционированную пересылку конфиденциальной информации

**Руководство**. для баз данных SQL Azure, в которых хранятся или обрабатываются конфиденциальные данные, пометьте базу данных и связанные ресурсы как конфиденциальные с помощью тегов. Настройте закрытую связь в сочетании с тегами службы групп безопасности сети в экземплярах базы данных SQL Azure, чтобы предотвратить утечка конфиденциальной информации.



Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и имеет большую длину для защиты от потери данных и доступности клиентов. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.



Настройка частных ссылок и группы безопасности сети для предотвращения утечка данных в экземплярах базы данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview



Общие сведения о защите данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: шифрование всех конфиденциальных данных во время передачи

**Руководство**. база данных SQL Azure обеспечивает защиту данных путем шифрования данных при перемещении с защитой транспортного уровня. Для всех подключений SQL Server принудительно применяет шифрование SSL/TLS. Это гарантирует, что все данные шифруются "при передаче" между клиентом и сервером независимо от значения параметра Encrypt или TrustServerCertificate в строке подключения.



Общие сведения о шифровании SQL Azure при передаче:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: использование активного средства обнаружения для распознавания конфиденциальных данных

**Руководство**. Использование функции обнаружения и классификации данных в базе данных SQL Azure. Обнаружение и классификация данных предоставляет расширенные возможности, встроенные в базу данных SQL Azure для обнаружения, классификации, маркировки &amp; защиты конфиденциальных данных в базах данных.



Как использовать обнаружение и классификацию данных для SQL Server Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6. Использование Azure RBAC для управления доступом к ресурсам

**Руководство**. Использование Azure Active Directory (AAD) для проверки подлинности и управления доступом к экземплярам базы данных SQL Azure.


Как интегрировать Azure SQL Server с Azure Active Directory для проверки подлинности:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication


Как управлять доступом в Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**. Корпорация Майкрософт управляет базовой инфраструктурой базы данных SQL Azure и реализует четкие элементы управления для предотвращения потери или раскрытия данных клиента.

Общие сведения о защите данных клиентов в Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: шифрование конфиденциальной информации при хранении

**Руководство**. прозрачное шифрование данных (TDE) помогает защитить базу данных SQL Azure, управляемый экземпляр SQL Azure и хранилище данных Azure от угроз вредоносной автономной активности путем шифрования неактивных данных. Выполняется шифрование и расшифровка базы данных, связанных резервных копий и неактивных файлов журналов транзакций в реальном времени без необходимости изменения приложения. По умолчанию TDE включено для всех вновь развертываемых баз данных SQL Azure. Ключ шифрования TDE может управляться как корпорацией Майкрософт, так и клиентом.


Как управлять прозрачным шифрованием данных и использовать собственные ключи шифрования:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: журналы и оповещения об изменениях критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналом действий Azure для создания оповещений о том, когда изменения выполняются в рабочих экземплярах баз данных SQL Azure и других критических или связанных ресурсах.


Создание оповещений для событий журнала действий Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

## <a name="vulnerability-management"></a>Управление уязвимостями

*Дополнительные сведения см. в разделе [Security Control: управлением уязвимостей](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: Запуск автоматизированных средств проверки уязвимостей

**Рекомендации**. включите расширенную защиту данных для базы данных SQL Azure и следуйте рекомендациям центра безопасности Azure по выполнению оценки уязвимостей на серверах Azure SQL.



Как выполнять оценку уязвимостей в базах данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment



Включение расширенной защиты данных.

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Как реализовать рекомендации по оценке уязвимостей центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Развертывание автоматизированного решения по управлению исправлениями сторонних производителей

**Руководство**: неприменимо; Этот тест производительности предназначен для расчетных ресурсов.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: сравнение проверок уязвимостей "назад-назад"

**Руководство**. Включение периодических повторяющихся проверок для экземпляров базы данных SQL Azure; будет настроена Оценка уязвимостей для автоматического выполнения проверки базы данных в течение недели. Сводка результатов отправляется ​​на указанный адрес электронной почты. Сравните результаты, чтобы убедиться, что уязвимости исправлены.



Как экспортировать отчет об оценке уязвимостей в центре безопасности Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5. Использование процесса оценки рисков для определения приоритета устранения обнаруженных уязвимостей

**Руководство**. Используйте оценки рисков по умолчанию (Оценка безопасности), предоставляемые центром безопасности Azure.

Оценка безопасности центра безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в разделе [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1. Использование обнаружения ресурсов Azure

**Руководство**. Использование графа ресурсов Azure для запроса и обнаружения всех ресурсов (включая экземпляры SQL Server Azure) в ваших подписках.  Убедитесь, что у вас есть соответствующие разрешения (на чтение) в клиенте и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.


Хотя классические ресурсы Azure могут быть обнаружены с помощью графа ресурсов Azure, настоятельно рекомендуется создавать и использовать Azure Resource Manager ресурсы, идущие вперед.


Создание запросов с помощью графа ресурсов Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Просмотр подписок Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Общие сведения об Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="62-maintain-asset-metadata"></a>6,2: сохранение метаданных активов

**Руководство**. применение тегов к ресурсам Azure с помощью метаданных для логической организации их в таксономию.



Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: удалите неавторизованные ресурсы Azure

**Руководство**. Использование тегов, групп управления и отдельных подписок (при необходимости) для Организации и мониторинга ресурсов. Регулярно выверка инвентаризации и своевременное удаление неавторизованных ресурсов из подписки.



Как создать дополнительные подписки Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Создание Группы управления:

https://docs.microsoft.com/azure/governance/management-groups/create



Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: ведение инвентаризации утвержденных ресурсов Azure и наименований программного обеспечения

**Руководство**. Определение списка утвержденных ресурсов Azure и утвержденного программного обеспечения для ресурсов вычислений

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: отслеживание неутвержденных ресурсов Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в подписках клиентов, с помощью следующих встроенных определений политик:

- Недопустимые типы ресурсов
- Допустимые типы ресурсов

Используйте граф ресурсов Azure для запроса или обнаружения ресурсов в ваших подписках. Убедитесь, что все ресурсы Azure, представленные в окружении, утверждены.

Как настроить политику Azure и управлять ей: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Создание запросов с помощью графа ресурсов Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="68-use-only-approved-applications"></a>6,8: Используйте только утвержденные приложения

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="69-use-only-approved-azure-services"></a>6,9: Используйте только утвержденные службы Azure

**Руководство**. Использование политики Azure для размещения ограничений на тип ресурсов, которые могут быть созданы в подписках клиента с помощью следующих встроенных определений политик:

- Недопустимые типы ресурсов
- Допустимые типы ресурсов

Используйте граф ресурсов Azure для запроса или обнаружения ресурсов в ваших подписках. Убедитесь, что все ресурсы Azure, представленные в окружении, утверждены.

Как настроить политику Azure и управлять ей: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как отказаться от определенного типа ресурса с помощью политики Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="610-implement-approved-application-list"></a>6,10: реализация списка утвержденных приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для приложений, выполняющихся на ресурсах вычислений.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: ограничьте возможность пользователей взаимодействовать с диспетчером ресурсов Azure с помощью сценариев

**Руководство**. Использование условного доступа Azure для ограничения возможности пользователей взаимодействовать с Azure Resource Manager путем настройки "блокировать доступ" для приложения "Управление Microsoft Azure".


Настройка условного доступа для блокировки доступа к Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: ограничьте возможность пользователей выполнять сценарии в ресурсах вычислений

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: физическое или логическое разделение приложений с высоким риском

**Руководство**: неприменимо; Эта рекомендация предназначена для служб приложений или ресурсов вычислений, в которых размещаются настольные системы или веб-приложения.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="secure-configuration"></a>Настройка безопасности

*Дополнительные сведения см. в разделе [Security Control: Secure Configuration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Установите безопасные конфигурации для всех ресурсов Azure.

**Руководство**. Используйте рекомендации по использованию политики Azure или центра безопасности Azure для серверов и баз данных SQL Azure, чтобы поддерживать конфигурации безопасности для всех ресурсов Azure.


Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Установка безопасных конфигураций операционной системы

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: поддержание защищенных конфигураций ресурсов Azure

**Руководство**. Использование политики Azure [Deny] и [развертывание если не существует] для обеспечения безопасности параметров в ресурсах Azure.



Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



Общие сведения о влиянии политики Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: поддержание защищенных конфигураций операционных систем

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: безопасное хранение конфигурации ресурсов Azure

**Руководство**. Если вы используете пользовательские определения политики Azure, используйте Azure DevOps или Azure Repos для безопасного хранения кода и управления им.



Как сохранить код в Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Документация по Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: безопасное хранение пользовательских образов операционной системы

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: развертывание средств управления конфигурацией системы

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. SQL для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.



Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией системы для операционных систем

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9. Реализация автоматического мониторинга конфигурации для служб Azure

**Руководство**. Использование центра безопасности Azure для выполнения проверок базовых показателей для серверов и баз данных SQL Azure.



Как исправить рекомендации в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10. Реализация автоматического мониторинга конфигурации для операционных систем

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="711-manage-azure-secrets-securely"></a>7,11: безопасное управление секретами Azure

**Руководство**. Использование Azure Key Vault для хранения ключей шифрования для прозрачное шифрование данных базы данных SQL Azure (TDE).



Как защитить конфиденциальные данные, хранящиеся в SQL Server Azure, и хранить ключи шифрования в Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: безопасно управляйте удостоверениями и автоматически

**Руководство**. Использование управляемых удостоверений для предоставления служб Azure с автоматически управляемым удостоверением в Azure Active Directory (AAD). Управляемые удостоверения позволяют проходить проверку подлинности в любой службе, поддерживающей проверку подлинности AAD, включая Azure Key Vault без каких бы то ни было учетных данных в коде.


Учебник. использование управляемого удостоверения, назначенного системой виртуальной машины Windows, для доступа к Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql


Как настроить управляемые удостоверения:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: устранение непреднамеренной раскрытия учетных данных

**Руководство**. Реализация средства проверки учетных данных для указания учетных данных в коде. Средство проверки учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault. 

Настройка средства проверки учетных данных: https://secdevtools.azurewebsites.net/helpcredscan.html

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в разделе [Security Control: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами. Корпорация Майкрософт обрабатывает Антивредоносные программы для базовой платформы.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся средами вычислений

**Руководство**. Защита от вредоносных программ Майкрософт включена на базовом узле, поддерживающем службы Azure (например, в службе приложений Azure), но не выполняется в содержимом клиента.


Предварительное сканирование любого содержимого, отправляемого в Нерасчетные ресурсы Azure, например служба приложений, Data Lake Storage, хранилище BLOB-объектов, SQL Server Azure и т. д. Корпорация Майкрософт не может получить доступ к данным в этих экземплярах.


Сведения о антивредоносном по Майкрософт для облачных служб и виртуальных машин Azure: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Убедитесь, что программное обеспечение и подписи для защиты от вредоносных программ обновлены

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами. Корпорация Майкрософт обрабатывает Антивредоносные программы для базовой платформы.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в разделе [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Обеспечьте регулярную автоматическую архивацию

**Рекомендации**. чтобы защитить бизнес от потери данных, база данных SQL Azure автоматически создает полные резервные копии баз данных еженедельно, разностные резервные копии каждые 12 часов, а резервные копии журналов транзакций — каждые 5-10 минут. Резервные копии хранятся в хранилище RA-GRS по крайней мере за 7 дней для всех уровней служб. Все уровни служб, за исключением поддержка Basic настраиваемого срока хранения резервных копий для восстановления до точки во времени, до 35 дней.


Для удовлетворения различных требований соответствия можно выбрать разные периоды хранения для еженедельных, ежемесячных и (или) ежегодных резервных копий. Использование хранилища зависит от частоты резервного копирования и периода хранения.


Общие сведения о резервном копировании и непрерывности бизнес-процессов с помощью Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Общая

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом.

**Руководство**. база данных SQL Azure автоматически создает резервные копии базы данных, которые хранятся в интервале от 7 до 35 дней, и использует геоизбыточное хранилище с доступом на чтение Azure (RA-GRS), чтобы гарантировать их сохранность, даже если центр обработки данных недоступен. Эти резервные копии создаются автоматически. При необходимости включите долгосрочные геоизбыточные резервные копии для баз данных SQL Azure.


Если для прозрачное шифрование данных используются управляемые клиентом ключи, убедитесь, что резервное копирование ключей выполняется.


Общие сведения о резервном копировании в Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database


Как создать резервную копию ключей хранилища ключей в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Руководство**. обеспечение возможности периодически выполнять восстановление данных в Azure Backup. При необходимости протестируйте содержимое для восстановления изолированной виртуальной ЛС. Проверка восстановления резервных копий ключей, управляемых клиентом.


Как восстановить ключи хранилища ключей в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


Как восстановить резервные копии базы данных SQL Azure с помощью восстановления до точки во времени:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и управляемых клиентом ключей

**Руководство**. Включение обратимого удаления в Azure Key Vault для защиты ключей от случайного или вредоносного удаления.


Включение обратимого удаления в Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

## <a name="incident-response"></a>реагирование на инциденты.

*Дополнительные сведения см. в разделе [контроль безопасности: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: создайте руководство по реагированию на инциденты

**Руководство**. Убедитесь, что существуют планы реагирования на инциденты, определяющие роли персонала, а также этапы обработки инцидентов и управления ими.



Как настроить автоматизацию рабочих процессов в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: создание оценки инцидента и процедуры определения приоритетов

**Руководство**. Центр безопасности назначает серьезность предупреждениям, чтобы помочь вам определить порядок, в котором вы задаете каждое оповещение, чтобы при компрометации ресурса вы могли сразу перейти к нему. Серьезность основывается на том, насколько надежным является центр безопасности в поиске или аналитике, используемой для выдаче оповещения, а также об уровне достоверности, который был вредоносной задачей, вызвавшей оповещение.
Оповещения системы безопасности в центре безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview


**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="103-test-security-response-procedures"></a>10,3: тестирование процедур реагирования на безопасность

**Руководство**. проведение упражнений по тестированию возможностей реагирования на инциденты систем на регулярной ритмичности. Выявление слабых точек и пробелов и пересмотр плана по мере необходимости.



Вы можете обратиться к публикации NIST: руководство по тестированию, обучению и выполнению программ для ИТ-планов и возможностей.

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: укажите контактные данные инцидента безопасности и настройте уведомления о событиях для инцидентов безопасности.

**Руководство**. контактные данные инцидентов безопасности будут использоваться корпорацией Майкрософт для связи с вами, если Microsoft Security Response Center (MSRC) обнаруживает, что доступ к данным был получен незаконные или неавторизованной стороной.



Как задать контакт безопасности центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**. Экспорт оповещений и рекомендаций центра безопасности Azure с помощью функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Вы можете использовать соединитель данных центра безопасности Azure для потоковой передачи оповещений в метку.


Как настроить непрерывный экспорт:

https://docs.microsoft.com/azure/security-center/continuous-export


Как выполнить потоковую передачу предупреждений в Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Автоматизируйте ответ на оповещения системы безопасности

**Руководство**. Использование функции автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" в оповещениях и рекомендациях системы безопасности.



Как настроить автоматизацию рабочего процесса и Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в разделе [контроль безопасности: тесты на проникновение и примеры для красных команд](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критических обновлений безопасности в течение 60 дней.

**Рекомендации**. Следуйте правилам взаимодействия Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



Дополнительные сведения о стратегии корпорации Майкрософт и ее выполнении, а также о тестировании на основе уязвимости для управляемой облачной инфраструктуры, служб и приложений Майкрософт можно найти здесь: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со статьей о [производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Дополнительные сведения о [базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
