---
title: Data Lake Analytics базового уровня безопасности для производительности системы безопасности Azure
description: Базовый план безопасности Data Lake Analytics содержит практические руководства и ресурсы для реализации рекомендаций по безопасности, указанных в статье о производительности системы безопасности Azure.
author: msmbaldwin
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 5e6d5beaad5080bf3081d5f68722623fb62d6c8a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532088"
---
# <a name="data-lake-analytics-security-baseline-for-azure-security-benchmark"></a>Data Lake Analytics базового уровня безопасности для производительности системы безопасности Azure

Базовый план безопасности Azure для Data Lake Analytics содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовый план безопасности для этой службы взят из [Эталона безопасности Azure версии 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), содержащего рекомендации по обеспечению безопасности облачных решений в Azure с помощью наших практических рекомендаций.

Дополнительные сведения см. в статье [Обзор базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в статье [Управление безопасностью: безопасность сети](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: защита ресурсов Azure в виртуальных сетях

**Руководство**. Используйте параметры брандмауэра для Data Lake Analytics ограничения внешних диапазонов IP-адресов, чтобы разрешить доступ с локальных клиентов и сторонних служб. Настройка параметров брандмауэра доступна через портал, API-интерфейсы или PowerShell.

* [Правила брандмауэра](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Управление аналитикой озера данных Azure с помощью Azure PowerShell](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**. Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых интерфейсов

**Руководство**: неприменимо; Azure Data Lake Analytics не выполняется в виртуальной сети и при использовании федеративных запросов исходящие вызовы не могут быть настроены для маршрутизации через виртуальную сеть клиента.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="13-protect-critical-web-applications"></a>1.3. Защита критических веб-приложений

**Руководство**: неприменимо; Этот элемент управления предназначен для веб-приложений, выполняющихся в службе приложений Azure или экземплярах IaaS.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: запретите обмен данными с известными вредоносными IP-адресами

**Руководство**. Используйте параметры брандмауэра для Data Lake Analytics ограничения внешних диапазонов IP-адресов, чтобы разрешить доступ с локальных клиентов и сторонних служб. Настройка параметров брандмауэра доступна через портал, API-интерфейсы или PowerShell.

* [Правила брандмауэра](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Управление аналитикой озера данных Azure с помощью Azure PowerShell](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="15-record-network-packets"></a>1,5: запись сетевых пакетов

**Руководство**: неприменимо; Data Lake Analytics не выполняется в виртуальных сетях клиентов и не может использовать группы безопасности сети (группы безопасности сети) для записи журналов сетевых потоков.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание системы обнаружения вторжений на основе сети и предотвращения вторжения (ИДЕНТИФИКАТОРы и IP-адреса)

**Руководство**: неприменимо; Data Lake Analytics — это предложение PaaS, которое не развертывается в сетях клиентов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="17-manage-traffic-to-web-applications"></a>1.7. Управление трафиком к веб-приложениям

**Руководство**: неприменимо; Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Неприменимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Уменьшение сложности и дополнительных затрат на администрирование в правилах безопасности сети

**Руководство**: неприменимо; Data Lake Analytics не выполняется в виртуальных сетях клиентов и не может использовать группы безопасности сети (группы безопасности сети).

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9. Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**: неприменимо; Data Lake Analytics не выполняется в виртуальных сетях клиентов и не может использовать группы безопасности сети (группы безопасности сети).

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="110-document-traffic-configuration-rules"></a>1.10. Документация по правилам конфигурации трафика

**Руководство**: неприменимо; Data Lake Analytics не выполняется в виртуальных сетях клиентов и не может использовать группы безопасности сети (группы безопасности сети).

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**: неприменимо; Data Lake Analytics не выполняется в виртуальных сетях клиентов и не может использовать группы безопасности сети (группы безопасности сети).

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в статье [Управление безопасностью: ведение журналов и мониторинг](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1. Использование утвержденных источников синхронизации времени

**Руководство**: неприменимо; Корпорация Майкрософт поддерживает источник времени для Data Lake Analytics.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2. Настройка централизованного управления журналами безопасности

**Руководство**. прием журналов с помощью Azure Monitor для агрегирования данных безопасности, таких как Data Lake Analytics "Audit" и "Диагностика запросов". В Azure Monitor используйте рабочую область Log Analytics для запроса и выполнения анализа и используйте учетные записи хранения Azure для долгосрочного и архивного хранения, при необходимости используя такие функции безопасности, как неизменяемое хранилище и принудительное хранение.

Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

* [Доступ к журналам диагностики для Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Как получить журналы и метрики платформы с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Как получить журналы внутреннего узла виртуальной машины Azure с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [Начало работы с Azure Monitor и интеграция SIEM стороннего производителя](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Включение журналов аудита для ресурсов Azure

**Руководство**. Включение параметров диагностики для Data Lake Analytics доступа к журналам аудита и запросов. К ним относятся такие данные, как источник событий, Дата, пользователь, отметка времени и другие полезные элементы.

* [Как получить журналы и метрики платформы с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Общие сведения о ведении журналов и различных типах журналов в Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**. Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Сбор журналов безопасности из операционных систем

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="25-configure-security-log-storage-retention"></a>2.5. Настройка хранения журнала безопасности

**Рекомендации**. в Azure Monitor задайте срок хранения log Analytics рабочей области в соответствии с нормативными требованиями Организации. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения.

* [Изменение срока хранения данных в Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Настройка политики хранения для журналов учетных записей хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**. Customer

### <a name="26-monitor-and-review-logs"></a>2,6: мониторинг и просмотр журналов

**Руководство**. анализ и мониторинг журналов для аномального поведения и регулярная проверка результатов для Data Lake Analyticsных ресурсов. Используйте рабочую область Log Analytics Azure Monitor, чтобы просматривать журналы и выполнять запросы к данным журнала. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Дополнительные сведения о рабочей области Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Выполнение пользовательских запросов в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**. Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Включение оповещений для аномальных действий

**Руководство**. Включение параметров диагностики для Data Lake Analytics и отправка журналов в рабочую область log Analytics. Подключите рабочую область Log Analytics к Azure Sentinel, так как она предоставляет решение для оркестрации событий безопасности и автоматического реагирования (SOAR). Это позволяет создавать и использовать сборники схем (автоматизированные решения) для устранения проблем безопасности.

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Как оповещать данные журнала Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

* [Доступ к журналам диагностики для Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8. Централизованное ведение журнала защиты от вредоносных программ

**Руководство**: неприменимо; Data Lake Analytics не обрабатывает и не создает журналы, связанные с защитой от вредоносных программ.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="29-enable-dns-query-logging"></a>2.9. Включение ведения журнала запросов DNS

**Руководство**. Внедрение решения стороннего производителя из Azure Marketplace для решения регистрации DNS в соответствии с вашими организациями.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="210-enable-command-line-audit-logging"></a>2.10. Включение ведения журнала аудита для командной строки

**Руководство**: неприменимо; Этот элемент управления предназначен для расчетных ресурсов, в которых у клиента есть доступ к базовой операционной системе.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

## <a name="identity-and-access-control"></a>Идентификаторы и управление доступом

*Дополнительные сведения см. в статье [Управление безопасностью: идентификаторы и управление доступом](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Инвентаризация учетных записей администраторов

**Руководство**. в Azure AD есть встроенные роли, которые должны быть явно назначены и доступны для запросов. Используйте модуль Azure AD PowerShell для выполнения нерегламентированных запросов для обнаружения учетных записей, входящих в группы администраторов.

* [Как получить роль каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Как получить членов роли каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2. Изменение паролей по умолчанию, где применимо

**Рекомендации**. Data Lake Analytics не имеет концепции паролей по умолчанию, так как проверка подлинности обеспечивается Azure Active Directory и защищена с помощью управления доступом на основе РОЛЕЙ (RBAC).

* [Обзор Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-overview)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3. Применение выделенных административных учетных записей

**Руководство**. Создайте стандартные операционные процедуры для использования выделенных административных учетных записей.

Вы также можете включить JIT-доступ с помощью Azure AD Privileged Identity Management и Azure Resource Manager.

* [Дополнительные сведения о управление привилегированными пользователями](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4. Использование единого входа Azure Active Directory (SSO)

**Рекомендации**. везде, где это возможно, используйте Azure Active Directory SSO вместо настройки отдельных автономных учетных данных для каждой службы. Использование удостоверений центра безопасности Azure и рекомендаций по доступу.

* [Общие сведения об использовании единого входа в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: используйте многофакторную проверку подлинности для всех Azure Active Directory доступа

**Рекомендации**. Включите Azure Active Directory многофакторную идентификацию (MFA) и следуйте рекомендациям по управлению удостоверениями и доступом в центре безопасности Azure, чтобы помочь защитить ресурсы Data Lake Analytics.

* [Включение MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Мониторинг идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: используйте защищенные рабочие станции, управляемые Azure, для административных задач

**Руководство**. используйте защищенную, управляемую Azure рабочую станцию (также называемую рабочей станцией привилегированного доступа или привилегированным доступом) для административных задач, требующих повышенных привилегий.

* [Общие сведения о защищенных рабочих станциях под управлением Azure](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-managed-workstation)

* [Как включить Azure AD MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: журналы и оповещения о подозрительных действиях учетных записей администраторов

**Рекомендации**. Используйте Azure Active Directory отчеты о безопасности для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде. Используйте Центр безопасности Azure для мониторинга действий идентификации и доступа.

* [Как определить пользователей Azure AD, помеченных для события риска](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Мониторинг пользовательских действий идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8. Управление ресурсами Azure только из утвержденных расположений

**Руководство**. Использование именованных расположений Azure AD для разрешения доступа только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

* [Настройка именованных расположений Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="39-use-azure-active-directory"></a>3.9. Использование Azure Active Directory

**Руководство**. Использование Azure Active Directory (Azure AD) в качестве централизованной системы проверки подлинности и авторизации. Azure AD предоставляет управление доступом на основе ролей (RBAC) для точного управления доступом клиента к ресурсам Data Lake Analytics.

* [Создание и настройка экземпляра Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10. Регулярная проверка и согласование доступа пользователей

**Руководство**. Azure AD предоставляет журналы для облегчения поиска устаревших учетных записей. Кроме того, используйте проверки подлинности и доступа Azure AD для эффективного управления членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователей можно проверить регулярно, чтобы убедиться, что доступ к ним имеют только нужные пользователи.

* [Общие сведения об отчетах Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Как использовать проверки подлинности и доступа Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: монитор пытается получить доступ к отключенным учетным данным

**Руководство**. Включение параметров диагностики для Data Lake Analytics и Azure Active Directory, отправка всех журналов в log Analytics рабочую область. Настройте нужные оповещения (например, попытки доступа к отключенным секретам) в Log Analytics.

* [Интеграция журналов Azure AD с Azure Monitor журналами](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: предупреждение об отклонении при входе в учетную запись

**Руководство**. Использование функций защиты Azure Active Directory и безопасности удостоверений для настройки автоматических ответов на обнаруженные подозрительные действия, связанные с ресурсами Data Lake Analytics. Вы должны включить автоматические ответы через метку Azure, чтобы реализовать ответы на безопасность вашей организации.

* [Просмотр рискованных входов в Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Как настроить и включить политики рисков с помощью защиты идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13. Предоставление корпорации Майкрософт доступа к соответствующим данным клиентов в рамках сценариев поддержки

**Руководство**: неприменимо; Защищенное хранилище не поддерживается для Azure Data Lake Analytics.

* [Поддерживаемые службы и сценарии в общем доступе](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в статье [Управление безопасностью: защита данных](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Инвентаризация конфиденциальных данных

**Руководство**. Использование тегов для отслеживания Data Lake Analytics ресурсов, в которых хранятся или обрабатываются конфиденциальные данные.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2. Изолирование систем, хранящих или обрабатывающих конфиденциальные данные

**Руководство**. Реализация изоляции с помощью отдельных подписок, групп управления для отдельных доменов безопасности, таких как среда, чувствительность данных. Вы можете ограничить Data Lake Analytics, чтобы управлять уровнем доступа к ресурсам Data Lake Analytics, которые требуются приложениям и корпоративным средам. Когда правила брандмауэра настроены, доступ к ресурсам Data Lake Analytics могут получить только приложения, запрашивающие данные через указанный набор сетей. Вы можете управлять доступом к Azure Data Lake Analytics через Azure AD RBAC.

* [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Управление управлением доступом на основе ролей](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-role-based-access-control)

* [Правила брандмауэра](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Управление аналитикой озера данных Azure с помощью Azure PowerShell](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Руководство**. функции защиты от потери данных еще не доступны для Azure Data Lake Analyticsных ресурсов. Установите сторонние решения, если это необходимо для обеспечения соответствия требованиям.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и защищенным от потери и доступности данных клиентов. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

* [Как защитить учетные записи хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Шифрование любой конфиденциальной информации при передаче

**Руководство**. Microsoft Azure ресурсы будут согласовывать TLS 1,2 по умолчанию. Убедитесь, что все клиенты, подключающиеся к Data Lake Analytics, могут согласовываться с помощью TLS 1,2 или более поздней версии.

* [Пример списка операций](https://docs.microsoft.com/rest/api/datalakeanalytics/operations/list)

**Мониторинг центра безопасности Azure**: Сейчас это недоступно

**Ответственность**: Совмещаемая блокировка

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Использование средства активного обнаружения для распознавания конфиденциальных данных

**Руководство**. функции идентификации данных еще недоступны для Azure Data Lake Analyticsных ресурсов. Установите сторонние решения, если это необходимо для обеспечения соответствия требованиям.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6. Использование управления доступом на основе ролей для управления доступом к ресурсам

**Руководство**. Использование управления доступом на основе РОЛЕЙ (RBAC) для управления взаимодействием пользователей со службой.

* [Управление управлением доступом на основе ролей](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-role-based-access-control)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**. Неприменимо; эта рекомендация предназначена для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Неприменимо

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Шифрование конфиденциальной информации при хранении

**Руководство**. данные хранятся в учетной записи Data Lake Storage 1-го поколения по умолчанию. Data Lake Storage 1-го поколения поддерживает прозрачное шифрование неактивных данных, включенное по умолчанию.

* [Шифрование данных в Azure Data Lake Storage 1-го поколения](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption)

**Мониторинг центра безопасности Azure**: Сейчас это недоступно

**Ответственность**: Совмещаемая блокировка

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Включение в журнал и создание оповещений по изменениям критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналом действий Azure для создания оповещений о том, когда изменения выполняются в рабочих экземплярах Azure Data Lake Analyticsных ресурсов.

* [Создание оповещений для событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="vulnerability-management"></a>управление уязвимостями;

*Дополнительные сведения см. в статье [Управление безопасностью: управление уязвимостями](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Выполнение автоматизированных средства анализа уязвимостей

**Рекомендации**. Следуйте рекомендациям центра безопасности Azure по защите ресурсов Azure Data Lake Analytics.

Корпорация Майкрософт выполняет Управление уязвимостью в базовых системах, поддерживающих Azure Data Lake Analytics.

* [Общие сведения о рекомендациях центра безопасности Azure](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Развертывание автоматизированного решения по управлению исправлениями для программ сторонних разработчиков

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Сравнение проверок смежных уязвимостей

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Использование процесса оценки рисков для определения приоритета в устранении обнаруженных уязвимостей

**Руководство**. Используйте общую программу оценки рисков (например, общую систему оценки уязвимостей) или оценки рисков по умолчанию, предоставляемые сторонним средством сканирования.

* [Публикация NIST — общая система оценки уязвимости](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в статье [Управление безопасностью: инвентаризация и управление ресурсами](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1. Использование автоматизированного решения для обнаружения ресурсов

**Руководство**. Использование графа ресурсов Azure для запроса и обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов и протоколов и т. д.) в подписках. Обеспечьте соответствующие разрешения (чтение) в клиенте и перечислите все подписки Azure, а также ресурсы в подписках.

Хотя классические ресурсы Azure могут быть обнаружены с помощью обозревателя графа ресурсов Azure, настоятельно рекомендуется создавать и использовать Azure Resource Manager ресурсов.

* [Создание запросов с помощью обозревателя графа ресурсов Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Просмотр подписок Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Общие сведения об Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2. Ведение метаданных активов

**Руководство**. Применяйте к ресурсам Azure теги, чтобы логически классифицировать их на основе метаданных.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**. Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Удаление неавторизованных ресурсов Azure

**Руководство**. Использование тегов, групп управления и отдельных подписок (при необходимости) для Организации и мониторинга Azure Data Lake Analyticsных ресурсов. Регулярно сверяйте ресурсы, чтобы своевременно удалять неавторизованные ресурсы из подписки.

Кроме того, используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в клиентских подписках, используя следующие встроенные определения политик:
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

* [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: определение и обслуживание инвентаризации утвержденных ресурсов Azure

**Руководство**. Создание инвентаризации утвержденных ресурсов Azure и утвержденного программного обеспечения для ресурсов вычислений в соответствии с потребностями Организации.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5. Отслеживание неутвержденных ресурсов Azure

**Руководство**. Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик.
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

Кроме того, используйте граф ресурсов Azure для запроса или обнаружения ресурсов в подписках.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Как создавать запросы с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6. Отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="68-use-only-approved-applications"></a>6.8: Использование только утвержденных приложений

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="69-use-only-approved-azure-services"></a>6.9: Использование только утвержденных служб Azure

**Руководство**. Используйте Политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик.
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Как отказаться от определенного типа ресурса с помощью Политики Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: ведение инвентаризации утвержденных наименований программного обеспечения

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: ограничьте возможность пользователей работать с Azure Resource Manager

**Руководство**. Настройте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "Блокировать доступ" для приложения "Управление Microsoft Azure".

* [Настройка условного доступа для блокировки доступа к ARM](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12. Ограничение возможности пользователей выполнять сценарии в ресурсах вычислений

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13. Физическое или логическое разделение приложений с высоким риском

**Руководство**. Неприменимо; эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Неприменимо

## <a name="secure-configuration"></a>Безопасная конфигурация

*Дополнительные сведения см. в статье [Управление безопасностью: безопасная конфигурация](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Установка безопасных конфигураций для всех ресурсов Azure

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. Data Lake Analytics для создания настраиваемых политик для аудита или принудительного применения конфигурации Azure Data Lake Analytics. Вы также можете использовать встроенные определения политик, связанные с Azure Data Lake Analytics, например:
- В Data Lake Analytics должны быть включены журналы диагностики.

* [Просмотр доступных псевдонимов политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Сохранение безопасных конфигураций для операционных систем

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Сохранение безопасных конфигураций для ресурсов Azure

**Руководство**. Использование политики Azure [Deny] и [развертывание если не существует] для обеспечения безопасности параметров в ресурсах Azure.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Сведения о действии Политик Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4. Сохранение безопасных конфигураций для операционных систем

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Безопасное хранение конфигурации ресурсов Azure

**Руководство**. Использование Azure Repos для безопасного хранения и управления кодом, например пользовательскими политиками Azure, Azure Resource Manager шаблонами, сценариями настройки требуемого состояния и т. д. Чтобы получить доступ к ресурсам, которыми вы управляете в Azure DevOps, вы можете предоставить или отклонить разрешения для определенных пользователей, встроенных групп безопасности или групп, определенных в Azure Active Directory (Azure AD), если они интегрированы с Azure DevOps, или Active Directory, если они интегрированы с TFS.

* [Как хранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [О разрешениях и группах в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6. Безопасное хранение пользовательских образов операционной системы

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: развертывание средств управления конфигурацией для ресурсов Azure

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией для операционных систем

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9. Реализация автоматического мониторинга конфигурации для ресурсов Azure

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. Data Lake Analytics для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Используйте политику Azure [аудит], [запретить] и [развернуть, если не существует], чтобы автоматически применять конфигурации для ресурсов Azure Data Lake Analytics.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10. Реализация автоматизированного мониторинга конфигурации для операционных систем

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="711-manage-azure-secrets-securely"></a>7.11: Безопасное управление секретами Azure

**Руководство**: неприменимо; Data Lake Analytics не предоставляет никаких секретов, которыми должен управлять клиент.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="712-manage-identities-securely-and-automatically"></a>7.12. Безопасное и автоматическое управление удостоверениями

**Руководство**: неприменимо; Data Lake Analytics не поддерживает управляемые удостоверения Azure.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13. Устранение непреднамеренного раскрытия учетных данных

**Руководство**. Реализуйте сканер учетных данных для обнаружения учетных данных в коде. Сканер учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault.

* [Как настроить сканер учетных данных](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в статье [Управление безопасностью: защита от вредоносных программ](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2. Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся вычислительными

**Руководство**. антивредоносное по Майкрософт включено на базовом узле, поддерживающем службы Azure (например, Azure Data Lake Analytics), но не выполняется в содержимом клиента.

Предварительное сканирование любого содержимого, отправляемого в ресурсы Azure, таких как служба приложений, Data Lake Analytics, хранилище BLOB-объектов и т. д. Корпорация Майкрософт не может получить доступ к данным в этих экземплярах.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3. Своевременное обновление программного обеспечения для защиты от вредоносных программ и подписей

**Руководство**: неприменимо; Этот элемент управления предназначен для вычислений-ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в статье [Управление безопасностью: восстановление данных](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Обеспечьте регулярное автоматическое резервное копирование

**Руководство**. Data Lake Analytics журналы заданий и выходные данные хранятся в базовой службе Data Lake Storage 1-го поколения. Вы можете использовать различные методы для копирования данных, включая ADLCopy, Azure PowerShell или фабрику данных Azure. Службу автоматизации Azure также можно использовать для автоматической архивации данных на регулярной основе.

* [Управление ресурсами Azure Data Lake Storage 1-го поколения с помощью обозревателя службы хранилища](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)

* [Копирование данных из больших двоичных объектов службы хранилища Azure в Azure Data Lake Storage 1-го поколения](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)

* [Обзор службы автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-intro)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом

**Руководство**. Data Lake Analytics журналы заданий и выходные данные хранятся в базовой службе Data Lake Storage 1-го поколения. Вы можете использовать различные методы для копирования данных, включая ADLCopy, Azure PowerShell или фабрику данных Azure.

* [Управление ресурсами Azure Data Lake Storage 1-го поколения с помощью обозревателя службы хранилища](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)

* [Копирование данных из больших двоичных объектов службы хранилища Azure в Azure Data Lake Storage 1-го поколения](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Руководство**. периодически выполняется восстановление данных резервной копии для проверки целостности данных.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и ключей, управляемых клиентом

**Руководство**. Data Lake Analytics резервные копии, хранящиеся в Data Lake Storage 1-го поколения или хранилище Azure, поддерживают шифрование по умолчанию и не могут быть отключены. Резервные копии следует рассматривать как конфиденциальные данные и применять соответствующие элементы управления доступом и защиты данных в рамках этого базового плана.

* [Обеспечение безопасности в Azure Data Lake Storage 1-го поколения](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data)

* [Авторизация доступа к данным в службе хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

## <a name="incident-response"></a>Реагирование на инциденты

*Дополнительные сведения см. в статье [Управление безопасностью: реагирование на инциденты](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Создание руководства по реагированию на инциденты

**Руководство**. Создайте руководство по реагированию на инциденты для вашей организации. Убедитесь в том, что имеются письменные планы реагирования на инциденты, которые определяют все действия персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.

* [Руководство по созданию собственного процесса реагирования на инциденты безопасности](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Анатомия инцидента Центра Майкрософт по реагированию на угрозы](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Клиент также может воспользоваться руководством по обработке инцидентов безопасности компьютера NIST, чтобы помочь в создании собственного плана реагирования на инциденты.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Создание процедуры оценки инцидента и определения приоритетов

**Руководство**. Центр безопасности назначает каждому оповещению серьезность, которая поможет определить, какие предупреждения следует расследовать первыми. Серьезность основывается на том, насколько уверен Центр безопасности в исследовании или аналитике, используемой для оповещения, а также на уровне достоверности злонамеренности события, приведшего к оповещению.

Кроме того, четко помечайте подписки тегами (например, рабочие, нерабочие) и создайте систему именования, чтобы четко определить и классифицировать ресурсы Azure, особенно обрабатывающие конфиденциальные данные. Вы несете ответственность за назначение приоритета оповещениям, требующим действий по исправлению, в зависимости от важности ресурсов Azure и среды, в которой произошел инцидент.

* [Оповещения безопасности в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [использование тегов для упорядочения ресурсов в Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="103-test-security-response-procedures"></a>10.3. Проверка процедур реагирования на угрозы

**Руководство**. проведение упражнений по тестированию возможностей реагирования на инциденты систем с регулярным интервалом для защиты ресурсов Azure. Выявление слабых точек и пропусков, а затем при необходимости пересмотрите свой план ответа.

* [Публикация NIST — руководства по тестированию, обучению и выполнению упражнений для ИТ-планов и возможностей](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Предоставление контактных сведений и настройка уведомлений по инцидентам безопасности

**Руководство**. Корпорация Майкрософт будет использовать информацию об инциденте безопасности для связи с вами, если центр Microsoft Security Response Center (MSRC) обнаружит, что к вашим данным был получен незаконный или несанкционированный доступ. Проверьте инциденты после факта обращения, чтобы убедиться в том, что проблемы устранены.

* [Как задать контакт безопасности Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Мониторинг Центра безопасности Azure**: Неприменимо

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

*Дополнительные сведения см. в статье [Управление безопасностью: тесты на проникновение и попытки нарушения безопасности "красной командой"](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критических результатов безопасности.

**Рекомендации**. Следуйте правилам тестирования уязвимости Microsoft Cloud, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт. Используйте стратегию и исполнение Microsoft, а затем протестировать проникновение в реальном времени для управляемой корпорацией Майкрософт облачной инфраструктуры, служб и приложений.

* [Правила тестирования уязвимости Задействований](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud красное объединение](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Совмещаемая блокировка

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со статьей [Тесты производительности системы безопасности Azure](/azure/security/benchmarks/overview).
- Дополнительные сведения о [базовой конфигурации безопасности Azure](/azure/security/benchmarks/security-baselines-overview).
