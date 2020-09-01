---
title: Базовый план безопасности Azure для Stream Analytics
description: Базовый план безопасности Azure для Stream Analytics
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 72a2b24ad8e05943f975f0819d8b2a872b0ab829
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229010"
---
# <a name="azure-security-baseline-for-stream-analytics"></a>Базовый план безопасности Azure для Stream Analytics

Базовый план безопасности Azure для Stream Analytics содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовый план безопасности для этой службы взят из [Эталона безопасности Azure версии 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), содержащего рекомендации по обеспечению безопасности облачных решений в Azure с помощью наших практических рекомендаций.

Дополнительные сведения см. в статье [Базовые показатели безопасности для Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в статье [Управление безопасностью: безопасность сети](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: защита ресурсов Azure в виртуальных сетях

**Руководство**. Azure Stream Analytics не поддерживает использование групп безопасности сети (NSG) и брандмауэра Azure.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Azure Stream Analytics не поддерживает использование виртуальных сетей и подсетей.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3. Защита критических веб-приложений

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4. Запрет взаимодействия с известными опасными IP-адресами

**Руководство**. Использование защиты от угроз центра безопасности Azure для обнаружения и оповещения о взаимодействии с известными вредоносными или неиспользуемыми IP-адресами Интернета.

* [Защита от угроз для уровня служб Azure в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/threat-protection)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="15-record-network-packets"></a>1,5: запись сетевых пакетов

**Руководство**. Azure Stream Analytics не использует группы безопасности сети, а журналы потоков для Azure Key Vault не фиксируются.

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание системы обнаружения вторжений на основе сети и предотвращения вторжения (ИДЕНТИФИКАТОРы и IP-адреса)

**Руководство**. Использование защиты от угроз в центре безопасности Azure для обнаружения необычных или потенциально опасных операций в среде подписки Azure.

* [Защита от угроз для уровня служб Azure в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/threat-protection)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7. Управление трафиком к веб-приложениям

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Уменьшение сложности и дополнительных затрат на администрирование в правилах безопасности сети

**Руководство**. Azure Stream Analytics не поддерживает использование виртуальных сетей и правил сети.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9. Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Azure Stream Analytics не поддерживает использование виртуальных сетей и сетевых устройств.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10. Документация по правилам конфигурации трафика

**Руководство**. Azure Stream Analytics не поддерживает использование виртуальных сетей и правил настройки трафика.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для мониторинга конфигураций ресурсов и обнаружения изменений для ресурсов Stream Analytics. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических ресурсов.

* [Как просматривать и извлекать события журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Как создать оповещения в службе Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в статье [Управление безопасностью: ведение журналов и мониторинг](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1. Использование утвержденных источников синхронизации времени

**Руководство**. Корпорация Майкрософт поддерживает источник времени, используемый для ресурсов Azure, например Stream Analytics.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2. Настройка централизованного управления журналами безопасности

**Рекомендации**. прием журналов с помощью Azure Monitor для агрегирования данных безопасности, таких как события аудита и запросы. В рамках Azure Monitor использовать рабочие области Log Analytics для запроса и выполнения анализа, а также использовать хранилище Azure аккаунтифор долгосрочного или архивного хранилища, при необходимости используя такие функции безопасности, как неизменяемое хранилище и принудительное хранение.

* [Как получить журналы и метрики платформы с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Включение журналов аудита для ресурсов Azure

**Рекомендации**. Включите параметры диагностики на Azure Stream Analytics для доступа к журналам администрирования, безопасности и диагностики. Вы также можете включить параметры диагностики журнала действий Azure и отправить журналы в ту же Log Analytics рабочую область или учетную запись хранения.

* [Azure Stream Analytics предоставляет журналы диагностики и данные о действиях для проверки](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Сбор журналов безопасности из операционных систем

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5. Настройка хранения журнала безопасности

**Рекомендации**. при хранении журналов событий безопасности в учетной записи хранения Azure или log Analytics рабочей области вы можете настроить политику хранения в соответствии с требованиями вашей организации.

* [Azure Stream Analytics предоставляет журналы диагностики и данные о действиях для проверки](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

* [Настройка политики хранения для журналов учетных записей хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

* [Изменение срока хранения данных в Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6. Мониторинг и просмотр журналов

**Руководство**. анализ и мониторинг журналов для аномального поведения и регулярное рассмотрение результатов для Stream Analyticsных ресурсов. Используйте рабочую область Log Analytics Azure Monitor, чтобы просматривать журналы и выполнять запросы к данным журнала. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Дополнительные сведения о рабочей области Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Выполнение пользовательских запросов в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Включение оповещений для аномальных действий

**Руководство**. Включение параметров диагностики для Stream Analytics и отправка журналов в рабочую область log Analytics. Подключите рабочую область Log Analytics к Azure Sentinel, так как она предоставляет решение для оркестрации событий безопасности и автоматического реагирования (SOAR). Это позволяет создавать и использовать сборники схем (автоматизированные решения) для устранения проблем безопасности.

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Как оповещать данные журнала Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

* [Azure Stream Analytics предоставляет журналы диагностики и данные о действиях для проверки](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8. Централизованное ведение журнала защиты от вредоносных программ

**Руководство**: неприменимо; Stream Analytics не обрабатывает и не создает журналы, связанные с защитой от вредоносных программ.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9. Включение ведения журнала запросов DNS

**Руководство**. решение Azure DNS Analytics (Preview) в Azure Monitor собирает информацию о инфраструктуре DNS в области безопасности, производительности и эксплуатации. В настоящее время это не поддерживает Azure Stream Analytics однако можно использовать сторонние решения для ведения журнала DNS.

* [Сбор подробных сведений о инфраструктуре DNS с помощью предварительной версии решения Аналитика DNS](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10. Включение ведения журнала аудита для командной строки

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="identity-and-access-control"></a>Идентификаторы и управление доступом

*Дополнительные сведения см. в статье [Управление безопасностью: идентификаторы и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Инвентаризация учетных записей администраторов

**Руководство**. в Azure AD есть встроенные роли, которые должны быть явно назначены. Роли можно запрашивать для обнаружения членства. Используйте модуль Azure AD PowerShell для выполнения нерегламентированных запросов для обнаружения учетных записей, входящих в группы администраторов.

* [Как получить роль каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Как получить членов роли каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2. Изменение паролей по умолчанию, где применимо

**Руководство**. Stream Analytics не имеет концепции паролей по умолчанию, так как проверка подлинности предоставляется Azure Active Directory и защищена с помощью управления доступом на основе РОЛЕЙ (RBAC) для управления службой. В зависимости от служб и выходных данных внедрения, необходимо повернуть учетные данные, настроенные в заданиях.

* [Смена учетных данных входа для ввода и вывода задания Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-login-credentials-inputs-outputs)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3. Применение выделенных административных учетных записей

**Руководство**. Создание плана безопасности для управления удостоверениями и ролей, соблюдение рекомендаций, включая принцип минимального привилегированного доступа для ролей администратора. Используйте Azure управление привилегированными пользователями (PIM), чтобы предоставить доступ к ресурсам Azure AD и Azure только в режиме доступа. Используйте оповещения и журнал аудита PIM Azure для отслеживания активности административных учетных записей. Используйте отчеты безопасности Azure AD, чтобы определить учетные записи администратора, которые могли быть скомпрометированы.

* [Дополнительные сведения](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4. Использование единого входа с Azure Active Directory

**Рекомендации**. везде, где это возможно, используйте Azure Active Directory SSO вместо настройки автономных учетных данных для каждой службы. Реализуйте рекомендации по доступу удостоверений центра безопасности Azure &amp; .

* [Общие сведения об использовании единого входа в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5. Использование многофакторной проверки подлинности для любого доступа на основе Azure Active Directory

**Рекомендации**. Включите Azure Active Directory многофакторную идентификацию (MFA) и следуйте рекомендациям по управлению удостоверениями и доступом в центре безопасности Azure, чтобы помочь защитить ресурсы Stream Analytics.

* [Включение MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Мониторинг идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Использование выделенных компьютеров (рабочих станций с привилегированным доступом) для всех административных задач

**Руководство**. Используйте лапы (рабочие станции с привилегированным доступом) с многофакторной идентификацией (MFA), настроенной для входа в и настройки ресурсов Stream Analytics.

* [Использование рабочих станций с привилегированным доступом](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Включение MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: журналы и оповещения о подозрительных действиях учетных записей администраторов

**Рекомендации**. Используйте Azure Active Directory отчеты о безопасности для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде. Используйте Центр безопасности Azure для мониторинга действий идентификации и доступа.

* [Как определить пользователей Azure AD, помеченных для события риска](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Мониторинг пользовательских действий идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8. Управление ресурсами Azure только из утвержденных расположений

**Руководство**. Используйте условный доступ с именованными расположениями, чтобы разрешить доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

* [Настройка именованных расположений в Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="39-use-azure-active-directory"></a>3.9. Использование Azure Active Directory

**Руководство**. Использование Azure Active Directory (Azure AD) в качестве централизованной системы проверки подлинности и авторизации. Azure AD предоставляет управление доступом на основе ролей (RBAC) для точного управления доступом клиента к ресурсам Stream Analytics.

* [Создание и настройка экземпляра Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10. Регулярная проверка и согласование доступа пользователей

**Руководство**. проверьте журналы Azure Active Directory, чтобы упростить обнаружение устаревших учетных записей, которые могут включать в себя роли администраторов учетной записи хранения. Кроме того, используйте проверку доступа удостоверений Azure, чтобы эффективно управлять членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователей следует регулярно проверять, чтобы обеспечить доступ к ним только тем пользователям, которые имеют право доступа.

* [Общие сведения об отчетах Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Использование проверок доступа для идентификации Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: монитор пытается получить доступ к отключенным учетным данным

**Руководство**. Включение параметров диагностики для Azure Stream Analytics и Azure Active Directory, отправка всех журналов в log Analytics рабочую область. Настройте нужные оповещения (например, попытки доступа к отключенным секретам) в Log Analytics.

* [Интеграция журналов Azure AD с Azure Monitor журналами](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12. Предупреждение при подозрительном входе в учетную запись

**Руководство**. Использование функций защиты Azure Active Directory и безопасности удостоверений для настройки автоматических ответов на обнаруженные подозрительные действия, связанные с ресурсами Stream Analytics. Вы должны включить автоматические ответы через метку Azure, чтобы реализовать ответы на безопасность вашей организации.

* [Просмотр рискованных входов в Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Как настроить и включить политики рисков с помощью защиты идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг Центра безопасности Azure**. Сейчас это недоступно.

**Ответственность**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13. Предоставление корпорации Майкрософт доступа к соответствующим данным клиентов в рамках сценариев поддержки

**Руководство**: неприменимо; Защищенное хранилище не поддерживается для Azure Stream Analytics.

* [Поддерживаемые службы и сценарии в общем доступе](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в статье [Управление безопасностью: защита данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Инвентаризация конфиденциальных данных

**Руководство**. Использование тегов для отслеживания Stream Analytics ресурсов, в которых хранятся или обрабатываются конфиденциальные данные.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2. Изолирование систем, хранящих или обрабатывающих конфиденциальные данные

**Руководство**. изоляция заданий Stream Analytics путем размещения входных и выходных данных, а также учетных записей хранения в одной подписке. Вы можете ограничить Stream Analytics, чтобы управлять уровнем доступа к ресурсам Stream Analytics, которые требуются приложениям и корпоративным средам. Вы можете управлять доступом к Azure Stream Analytics через Azure AD RBAC.

* [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Общие сведения о входных данных Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-add-inputs)

* [Описание выходных данных из Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Руководство**. функции защиты от потери данных еще не доступны для Azure Stream Analyticsных ресурсов. Установите сторонние решения, если это необходимо для обеспечения соответствия требованиям.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и защищенным от потери и доступности данных клиентов. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

* [Как защитить учетные записи хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Шифрование любой конфиденциальной информации при передаче

**Руководство**. Azure Stream Analytics шифрует все входящие и исходящие подключения и поддерживает TLS 1,2. Встроенные контрольные точки также шифруются.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Использование средства активного обнаружения для распознавания конфиденциальных данных

**Руководство**. функции идентификации данных еще недоступны для Azure Stream Analyticsных ресурсов. Установите сторонние решения, если это необходимо для обеспечения соответствия требованиям.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6. Контроль доступа к ресурсам с помощью RBAC

**Руководство**. Использование управления доступом на основе ролей Azure (Azure RBAC) для управления взаимодействием пользователей со службой.

* [Настройка Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Шифрование конфиденциальной информации при хранении

**Руководство**. Stream Analytics не сохраняет входящие данные, так как вся обработка выполняется в памяти. Любые закрытые данные, в том числе запросы и функции, которые должны сохраняться Stream Analytics, хранятся в настроенной учетной записи хранения. Используйте ключи, управляемые клиентом (CMK), для шифрования неактивных выходных данных в учетных записях хранения. Даже без CMK, Stream Analytics автоматически использует оптимальные стандарты шифрования в своей инфраструктуре для шифрования и защиты данных.

* [Защита данных в Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Включение в журнал и создание оповещений по изменениям критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналом действий Azure для создания оповещений о том, когда изменения выполняются в рабочих экземплярах Azure Stream Analyticsных ресурсов.

* [Создание оповещений для событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="vulnerability-management"></a>управление уязвимостями;

*Дополнительные сведения см. в статье [Управление безопасностью: управление уязвимостями](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Выполнение автоматизированных средства анализа уязвимостей

**Рекомендации**. Следуйте рекомендациям центра безопасности Azure по защите ресурсов Azure Stream Analytics.

Корпорация Майкрософт выполняет Управление уязвимостью в базовых системах, поддерживающих Azure Stream Analytics.

* [Общие сведения о рекомендациях центра безопасности Azure](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Развертывание автоматизированного решения по управлению исправлениями для программ сторонних разработчиков

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4. Сравнение проверок смежных уязвимостей

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Использование процесса оценки рисков для определения приоритета в устранении обнаруженных уязвимостей

**Руководство**. Используйте оценки рисков по умолчанию (Оценка безопасности), предоставляемые центром безопасности Azure.

* [Оценка безопасности центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в статье [Управление безопасностью: инвентаризация и управление ресурсами](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1. Использование автоматизированного решения для обнаружения ресурсов

**Руководство**. Используйте Azure Resource Graph для запроса и обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов, протоколов и т. д.) в ваших подписках. Убедитесь в том, что в вашем клиенте есть соответствующие разрешения (на чтение) и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

Хотя классические ресурсы Azure можно обнаружить через Resource Graph, настоятельно рекомендуется в дальнейшем создавать и использовать ресурсы Azure Resource Manager.

* [Как создавать запросы с помощью Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Как просматривать подписки Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Общие сведения об Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2. Ведение метаданных активов

**Руководство**. Применяйте к ресурсам Azure теги, чтобы логически классифицировать их на основе метаданных.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Удаление неавторизованных ресурсов Azure

**Руководство**. Использование тегов, групп управления и отдельных подписок (при необходимости) для Организации и мониторинга Azure Stream Analyticsных ресурсов. Регулярно сверяйте ресурсы, чтобы своевременно удалять неавторизованные ресурсы из подписки.

Кроме того, используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик:
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

* [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: определение и обслуживание инвентаризации утвержденных ресурсов Azure

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислений с ресурсами, а также для Azure в целом.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5. Отслеживание неутвержденных ресурсов Azure

**Рекомендации**. Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик:
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

Кроме того, используйте граф ресурсов Azure для запроса или обнаружения ресурсов в подписках.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Как создавать запросы с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6. Отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Удаление неутвержденных ресурсов Azure и программных приложений

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислений с ресурсами, а также для Azure в целом.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="68-use-only-approved-applications"></a>6.8. Использование только утвержденных приложений

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9. Использование только утвержденных служб Azure

**Руководство**. Используйте Политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик.
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Как отказаться от определенного типа ресурса с помощью Политики Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: ведение инвентаризации утвержденных наименований программного обеспечения

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: ограничьте возможность пользователей работать с Azure Resource Manager

**Руководство**. Настройте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "Блокировать доступ" для приложения "Управление Microsoft Azure".

* [Как настроить условный доступ для блокировки доступа к Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12. Ограничение возможности пользователей выполнять сценарии в ресурсах вычислений

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13. Физическое или логическое разделение приложений с высоким риском

**Руководство**. Неприменимо; эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="secure-configuration"></a>Безопасная конфигурация

*Дополнительные сведения см. в статье [Управление безопасностью: безопасная конфигурация](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Установка безопасных конфигураций для всех ресурсов Azure

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. StreamAnalytics для создания настраиваемых политик для аудита или принудительного применения конфигурации Azure Stream Analytics. Вы также можете использовать встроенные определения политик, связанные с Azure Stream Analytics, например:-журналы диагностики в Azure Stream Analytics должны быть включены

* [Просмотр доступных псевдонимов политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Определения встроенных политик в Политике Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies)

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Сохранение безопасных конфигураций для операционных систем

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3. Сохранение безопасных конфигураций для ресурсов Azure

**Рекомендации**. Используйте Политику Azure [отказывать] и [развернуть, если не существует], чтобы обеспечить безопасность параметров в ресурсах Azure.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Сведения о действии Политик Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4. Сохранение безопасных конфигураций для операционных систем

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5. Безопасное хранение конфигурации ресурсов Azure

**Руководство**. Используйте Azure Repos для безопасного хранения кода и управления им, включая пользовательские политики Azure, шаблоны Azure Resource Manager, скрипты настройки требуемого состояния, пользовательские функции, запросы. Чтобы получить доступ к ресурсам, которыми вы управляете в Azure DevOps, вы можете предоставить или отклонить разрешения для определенных пользователей, встроенных групп безопасности или групп, определенных в Azure Active Directory (Azure AD), если они интегрированы с Azure DevOps, или Active Directory, если они интегрированы с TFS.

* [Как хранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [О разрешениях и группах в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6. Безопасное хранение пользовательских образов операционной системы

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: развертывание средств управления конфигурацией для ресурсов Azure

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. StreamAnalytics для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией для операционных систем

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9. Реализация автоматического мониторинга конфигурации для ресурсов Azure

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. StreamAnalytics для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Используйте политику Azure [аудит], [запретить] и [развернуть, если не существует], чтобы автоматически применять конфигурации для ресурсов Azure Stream Analytics.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10. Реализация автоматизированного мониторинга конфигурации для операционных систем

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11. Безопасное управление секретами Azure

**Руководство**. сведения о подключении входных и выходных ресурсов, которые используются в Stream Analytics задании, хранятся в настроенной учетной записи хранения. Зашифруйте учетную запись хранения, чтобы защитить все ваши данные. Кроме того, регулярно вращайте учетные данные для ввода или вывода задания Stream Analytics.

* [Защита данных в Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection)

* [Смена учетных данных для источников входных данных и мест назначения выходных данных задания Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-login-credentials-inputs-outputs)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12. Безопасное и автоматическое управление удостоверениями

**Руководство**. Аутентификация с управляемым удостоверением для выходных данных предоставляет Stream Analytics заданиям прямой доступ к службе, включая Power BI, учетную запись хранения, вместо использования строки подключения.

* [Проверка подлинности Stream Analytics для Azure Data Lake Storage 1-го поколения с помощью управляемых удостоверений](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls)

* [Использование управляемого удостоверения для проверки подлинности задания Azure Stream Analytics в выходных данных хранилища BLOB-объектов Azure](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)

* [Используйте управляемое удостоверение для проверки подлинности задания Azure Stream Analytics для Power BI](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13. Устранение непреднамеренного раскрытия учетных данных

**Руководство**. Реализуйте сканер учетных данных для обнаружения учетных данных в коде. Сканер учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault.

* [Как настроить сканер учетных данных](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в статье [Управление безопасностью: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2. Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся вычислительными

**Руководство**. антивредоносное по Майкрософт включено на базовом узле, поддерживающем службы Azure (например, Azure Stream Analytics), но не выполняется в содержимом клиента.

Предварительное сканирование любого содержимого, отправляемого в ресурсы Azure, таких как служба приложений, Stream Analytics, хранилище BLOB-объектов и т. д. Корпорация Майкрософт не может получить доступ к данным в этих экземплярах.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3. Своевременное обновление программного обеспечения для защиты от вредоносных программ и подписей

**Рекомендации**. Неприменимо. Эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в статье [Управление безопасностью: восстановление данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1. Обеспечение регулярного автоматического резервного копирования

**Руководство**. зависит от типа выбранной службы вывода. Вы можете выполнять автоматическое резервное копирование выходных данных в соответствии с рекомендациями для службы вывода. Внутренние данные, включая определяемые пользователем функции, запросы, моментальные снимки данных, хранятся в настроенной учетной записи хранения, которую можно архивировать на регулярной основе.

Данные в учетной записи хранения Microsoft Azure всегда реплицируются автоматически, чтобы обеспечить устойчивость и высокий уровень доступности. Служба хранилища Azure копируют данные для защиты от запланированных и незапланированных событий, включая временные сбои оборудования, сети или энергоснабжения, масштабные стихийные бедствия и т. д. Вы можете реплицировать данные в пределах одного центра обработки данных, между зональными центрами обработки данных в одном регионе или между географически разделенными регионами.

Функцию управления жизненным циклом можно также использовать для резервного копирования данных на уровень архива. Кроме того, включите обратимое удаление для резервных копий, хранящихся в учетной записи хранения.

* [Защита данных в Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [Общие сведения о избыточности службы хранилища Azure и соглашениях об уровне обслуживания](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

* [Управление жизненным циклом хранилища BLOB-объектов Azure (предварительная версия)](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)

Обратимое удаление для больших двоичных объектов службы хранилища Azure: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом

**Руководство**. внутренние данные, включая определяемые пользователем функции, запросы, моментальные снимки данных, хранятся в настроенной учетной записи хранения, которую можно регулярно архивировать.

Для резервного копирования данных из поддерживаемых служб учетной записи хранения доступны несколько методов, включая использование azcopy или сторонних средств. Функция хранения данных в неизменяемом виде хранилища BLOB-объектов Azure дает пользователям возможность хранить критически важные для бизнеса объекты данных в состоянии WORM. Это состояние делает их нестираемыми и неизменяемыми в течение определенного пользователем интервала времени.

* [Защита данных в Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [Начало работы с AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

* [Настройка и администрирование политик неизменяемости для хранилища BLOB-объектов](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

Управляемые клиентом ключи могут быть обеспечены в Azure Key Vault с помощью Azure CLI или PowerShell.

* [Как создать резервную копию ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Руководство**. периодически выполняется восстановление данных резервной копии для проверки целостности данных.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и ключей, управляемых клиентом

**Руководство**. Stream Analytics резервные копии, хранящиеся в хранилище Azure, поддерживают шифрование по умолчанию и не могут быть отключены. Резервные копии следует рассматривать как конфиденциальные данные и применять соответствующие элементы управления доступом и защиты данных в рамках этого базового плана.

* [Защита данных в Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [Авторизация доступа к данным в службе хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="incident-response"></a>Реагирование на инциденты

*Дополнительные сведения см. в статье [Управление безопасностью: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Создание руководства по реагированию на инциденты

**Руководство**. Создайте руководство по реагированию на инциденты для вашей организации. Убедитесь в том, что имеются письменные планы реагирования на инциденты, которые определяют все действия персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.

* [Руководство по созданию собственного процесса реагирования на инциденты безопасности](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Анатомия инцидента Центра Майкрософт по реагированию на угрозы](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Клиент также может воспользоваться руководством по обработке инцидентов безопасности компьютера NIST, чтобы помочь в создании собственного плана реагирования на инциденты.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Создание процедуры оценки инцидента и определения приоритетов

**Руководство**. Центр безопасности назначает каждому оповещению серьезность, которая поможет определить, какие предупреждения следует расследовать первыми. Серьезность основывается на том, насколько уверен Центр безопасности в исследовании или аналитике, используемой для оповещения, а также на уровне достоверности злонамеренности события, приведшего к оповещению.

Кроме того, четко помечайте подписки тегами (например, рабочие, нерабочие) и создайте систему именования, чтобы четко определить и классифицировать ресурсы Azure, особенно обрабатывающие конфиденциальные данные. Вы несете ответственность за назначение приоритета оповещениям, требующим действий по исправлению, в зависимости от важности ресурсов Azure и среды, в которой произошел инцидент.

* [Оповещения безопасности в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [использование тегов для упорядочения ресурсов в Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="103-test-security-response-procedures"></a>10.3. Проверка процедур реагирования на угрозы

**Руководство**. Выполняйте упражнения, чтобы периодически тестировать возможности ваших систем реагировать на угрозы для защиты ресурсов Azure. Выявите слабые точки и пробелов и пересмотрите план по мере необходимости.

* [См. публикацию NIST: руководство по тестированию, обучению и выполнению программ для ИТ-планов и возможностей](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Предоставление контактных сведений и настройка уведомлений по инцидентам безопасности

**Руководство**. Корпорация Майкрософт будет использовать информацию об инциденте безопасности для связи с вами, если центр Microsoft Security Response Center (MSRC) обнаружит, что к вашим данным был получен незаконный или несанкционированный доступ. Проверьте инциденты после факта обращения, чтобы убедиться в том, что проблемы устранены.

* [Как задать контакт безопасности Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**. Экспортируйте оповещения и рекомендации Центра безопасности Azure с помощью функции непрерывного экспорта с целью выявления рисков для ресурсов Azure. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Вы можете использовать соединитель данных Центра безопасности Azure для потоковой передачи оповещений в Azure Sentinel.

* [Настройка непрерывного экспорта данных](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Как выполнить потоковую передачу оповещений в Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6. Автоматизация реагирования на оповещения системы безопасности

**Руководство**. Использование функции автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" в оповещениях системы безопасности и рекомендациях по защите ресурсов Azure.

* [Как настроить автоматизацию рабочего процесса и Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в статье [Управление безопасностью: тесты на проникновение и попытки нарушения безопасности "красной командой"](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критических результатов безопасности.

**Руководство**. 

* [Следуйте правилам взаимодействия Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Дополнительные сведения о стратегии корпорации Майкрософт и реализации "красной команды", а также о тестировании на основе уязвимости для облачной инфраструктуры, служб и приложений Майкрософт см. здесь](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Совмещаемая блокировка

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со статьей [Тесты производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview).
- Дополнительные сведения о [базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).
