---
title: Базовый план безопасности Azure для Key Vault
description: Базовый план безопасности Azure для Key Vault
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 03b2312b554743d83eff268876be1f20be595290
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78267523"
---
# <a name="azure-security-baseline-for-key-vault"></a>Базовый план безопасности Azure для Key Vault

Базовый план безопасности Azure для Key Vault содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовые показатели для этих служб взяты из [теста безопасности Azure версии 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), который содержит рекомендации по защите облачных решений в Azure с помощью наших рекомендаций.

Дополнительные сведения см. в статье [Общие сведения о базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в разделе Security [Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Защитите ресурсы с помощью групп безопасности сети или брандмауэра Azure в виртуальной сети.

**Руководство**. Интеграция Azure Key Vault с помощью частной ссылки Azure. Служба "Частная связь Azure" позволяет получить доступ к службам Azure (например, Azure Key Vault) и службам клиента или партнера Azure, размещенным в частной конечной точке в виртуальной сети.
Частная конечная точка Azure — это сетевой интерфейс, который защищенно и надежно подключается к службе через Приватный канал Azure. Частная конечная точка использует частный IP-адрес из виртуальной сети, по сути перемещая службу в виртуальную сеть. Весь трафик к службе может маршрутизироваться через частную конечную точку, поэтому шлюзы, устройства преобразования сетевых адресов (NAT), подключения ExpressRoute и VPN, а также общедоступные IP-адреса не требуются. Трафик между виртуальной сетью и службой проходит через магистральную сеть Майкрософт, что позволяет избежать рисков общедоступного Интернета. Вы можете подключиться к экземпляру ресурса Azure, обеспечивая наивысшую степень детализации в управлении доступом.

Как интегрировать Key Vault с помощью частной ссылки Azure: https://docs.microsoft.com/azure/key-vault/private-link-service

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Используйте центр безопасности Azure и следуйте рекомендациям по защите сети для защиты ресурсов, настроенных Key Vault в Azure. 


Дополнительные сведения о безопасности сети, предоставляемой центром безопасности Azure: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="13-protect-critical-web-applications"></a>1,3: защита критически важных веб-приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: запретите обмен данными с известными вредоносными IP-адресами

**Руководство**. Включение стандарта защиты Azure от атак DDoS в виртуальных сетях Azure, связанных с экземплярами Key Vault, для защиты от атак типа "отказ в обслуживании". Используйте интегрированную аналитику угроз центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми IP-адресами Интернета.

 
Управление Azure от атак DDoS Protection Standard с помощью портал Azure: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection обнаружение угроз для уровня служб Azure в центре безопасности Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: запись сетевых пакетов и журналов потоков

**Руководство**. Azure Key Vault не использует группы безопасности сети (NSG) и журналы потоков для Azure Key Vault не фиксируются. Вместо этого используйте частную ссылку Azure для защиты экземпляров Azure Key Vault и включите параметры диагностики для записи метрик и событий аудита.
Интеграция Key Vault с помощью частной ссылки Azure: https://docs.microsoft.com/azure/key-vault/private-link-service

Ведение журнала Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание систем обнаружения вторжений на основе сети и предотвращения вторжений (ИДЕНТИФИКАТОРы и IP-адреса)

**Рекомендации**. это требование можно выполнить, настроив Advanced Threat protection (ATP) для Azure Key Vault. ATP предоставляет дополнительный уровень аналитики безопасности. Это средство обнаруживает потенциально опасные попытки доступа или использования учетных записей Azure Key Vault.


Когда центр безопасности Azure обнаруживает аномальные действия, он отображает оповещения. Он также отправляет администратору подписки сведения о подозрительной активности и рекомендации по исследованию и исправлению обнаруженных угроз.


Настройка расширенной защиты от угроз для Azure Key Vault: https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault


**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="17-manage-traffic-to-web-applications"></a>1,7. Управление трафиком к веб-приложениям

**Руководство**: неприменимо; Эта рекомендация предназначена для веб-приложений, работающих в службе приложений Azure или в ресурсах вычислений.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: сократите сложность и издержки администрирования правил безопасности сети

**Руководство**. для ресурсов, которым требуется доступ к экземплярам Azure Key Vault, используйте теги службы Azure для Azure Key Vault, чтобы определить элементы управления доступом к сети для групп безопасности сети или брандмауэра Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, ApiManagement) в соответствующем поле источника или назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, включенными в тег службы, и автоматически обновляет тег службы при изменении адресов.


Общие сведения о тегах служб Azure: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определение и реализация стандартных конфигураций безопасности для сетевых ресурсов, связанных с экземплярами Azure Key Vault, с помощью политики Azure. Используйте псевдонимы политик Azure в пространствах имен Microsoft. KeyVault и Microsoft. Network, чтобы создать настраиваемые политики для аудита или принудительного применения конфигурации сети для экземпляров Azure Key Vault. Вы также можете использовать встроенные определения политик, связанные с Azure Key Vault, например:


Key Vault должны использовать конечную точку службы виртуальной сети


Руководство. Создание политик для обеспечения соответствия и управление ими:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Примеры политики Azure:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Краткое руководство. Определение и назначение схемы на портале.

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="110-document-traffic-configuration-rules"></a>1,10: правила конфигурации трафика документов

**Руководство**. Используйте теги для ресурсов, связанных с сетевой безопасностью и потоком трафика для экземпляров Azure Key Vault, чтобы предоставить метаданные и логическую организацию.


Используйте любое из встроенных определений политик Azure, связанных с тегами, например "требовать тег и его значение", чтобы убедиться, что все ресурсы созданы с помощью тегов и уведомлять вас о существующих ресурсах без тегов.


Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий с ресурсами на основе их тегов.


Используйте теги для Организации ресурсов Azure:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11. использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений сетевых ресурсов, связанных с экземплярами Azure Key Vault. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых ресурсов.

Просмотр и получение событий журнала действий Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Создание, просмотр и Управление оповещениями журнала действий с помощью Azure Monitor.

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в разделе [Security Control: logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: используйте источники синхронизации утвержденного времени

**Руководство**: неприменимо; Корпорация Майкрософт поддерживает источник времени, используемый для ресурсов Azure, например Azure Key Vault, для меток времени в журналах.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="22-configure-central-security-log-management"></a>2,2: Настройка централизованного управления журналом безопасности

**Руководство**. прием журналов с помощью Azure Monitor для агрегирования данных безопасности, создаваемых Azure Key Vault. В Azure Monitor используйте рабочую область Azure Log Analytics для запроса и выполнения анализа и используйте учетные записи хранения Azure для долгосрочного и архивного хранения. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM. 

Ведение журнала Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Краткое руководство. как настроить метку Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Включите ведение журнала аудита для ресурсов Azure.

**Рекомендации**. Включите параметры диагностики для экземпляров Azure Key Vault, чтобы получить доступ к журналам аудита, безопасности и диагностики. Журналы действий, которые автоматически доступны, включают источник событий, дату, пользователя, метку времени, исходные адреса, адреса назначения и другие полезные элементы.

Ведение журнала Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: получение журналов безопасности из операционных систем

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="25-configure-security-log-storage-retention"></a>2,5: Настройка хранения журнала безопасности

**Руководство**. в Azure Monitor для рабочей области log Analytics, используемой для хранения журналов Azure Key Vault, задайте срок хранения согласно нормативным требованиям Организации. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения.

Изменение срока хранения данных: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="26-monitor-and-review-logs"></a>2,6: мониторинг и просмотр журналов

**Руководство**. анализ и мониторинг журналов для аномального поведения и регулярная проверка результатов для ресурсов, защищенных Azure Key Vault. Используйте рабочую область Log Analytics Azure Monitor, чтобы просматривать журналы и выполнять запросы к данным журнала. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM. 


Краткое руководство. Встроенный Sentinel Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard


Начало работы с Log Analytics в Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Начало работы с запросами журналов в Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Включение оповещений для аномальных действий

**Руководство**. в центре безопасности Azure включите Advanced Threat protection (ATP) для Key Vault. Включите параметры диагностики в Azure Key Vault и отправьте журналы в рабочую область Log Analytics. Подключите рабочую область Log Analytics к Azure Sentinel, так как она предоставляет решение автоматического реагирования на системы безопасности (ВЗЛЕТЕЛ). Это позволяет создавать и использовать модули PlayBook (автоматизированные решения) для устранения проблем безопасности.

Краткое руководство. закрывающий метку Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Управление оповещениями безопасности в центре безопасности Azure и реагирование на них:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts


Реагирование на события с помощью оповещений Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="28-centralize-anti-malware-logging"></a>2,8: централизованное ведение журнала защиты от вредоносных программ

**Руководство**: неприменимо; Azure Key Vault не обрабатывает и не создает журналы, связанные с защитой от вредоносных программ.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="29-enable-dns-query-logging"></a>2,9: Включение ведения журнала запросов DNS

**Руководство**: неприменимо; Azure Key Vault не обрабатывает и не создает журналы, связанные с DNS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="210-enable-command-line-audit-logging"></a>2,10: Включение ведения журнала аудита в командной строке

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Дополнительные сведения см. в разделе [Управление безопасностью: идентификация и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: ведение инвентаризации учетных записей администраторов

**Руководство**. обеспечение инвентаризации зарегистрированных Azure Active Directory приложений, а также любых учетных записей пользователей, имеющих доступ к ключам Azure Key Vault, секретам и сертификатам. Для запроса и согласования Key Vault доступа можно использовать либо портал Azure, либо PowerShell. Чтобы просмотреть доступ в PowerShell, используйте следующую команду:


(Get-Азресаурце-ResourceId [Кэйваултресаурцеид]). Properties. AccessPolicies

Регистрация приложения с помощью Azure Active Directory: https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Безопасный доступ к хранилищу ключей: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Измените пароли по умолчанию, если это применимо

**Руководство**: неприменимо; Azure Key Vault не имеет концепции паролей по умолчанию, так как проверка подлинности обеспечивается Active Directory и защищена с помощью управления доступом на основе ролей.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: используйте выделенные учетные записи администратора

**Руководство**. Создание стандартных рабочих процедур, связанных с использованием выделенных административных учетных записей, имеющих доступ к экземплярам Azure Key Vault. Используйте центр безопасности Azure управление удостоверениями и доступом (сейчас в предварительной версии) для отслеживания количества активных административных учетных записей.


Мониторинг удостоверений и доступа (Предварительная версия):

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4. Использование единого входа (SSO) с Azure Active Directory

**Руководство**. Используйте субъект-службу Azure совместно с AppID, TenantID и ClientSecret, чтобы легко проверить подлинность приложения и получить маркер, который будет использоваться для доступа к Azure Key Vault секретам.


Проверка подлинности между службами для Azure Key Vault с помощью .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: используйте многофакторную проверку подлинности для всех Azure Active Directoryного доступа

**Рекомендации**. Включите многофакторную проверку подлинности Azure Active Directory и следуйте рекомендациям центра безопасности Azure по управлению удостоверениями и доступом (в настоящее время в предварительной версии) для защиты ресурсов, включенных в концентратор событий.


Планирование развертывания многофакторной идентификации Azure на основе облачных служб:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Мониторинг удостоверений и доступа (Предварительная версия):

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: используйте выделенные компьютеры (рабочие станции с привилегированным доступом) для всех административных задач

**Руководство**. Используйте рабочую станцию привилегированного доступа (привилегированным доступом) с поддержкой многофакторной идентификации Azure (MFA), настроенной для входа в систему и настройки ресурсов KEY Vault. 

Рабочие станции привилегированного доступа: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Планирование развертывания многофакторной идентификации Azure на основе облачных служб: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: регистрация и оповещение о подозрительных действиях из учетных записей администраторов

**Руководство**. Использование Azure Active Directory (AAD) Управление привилегированными пользователями (PIM) для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде. Используйте обнаружение рисков AAD для просмотра предупреждений и отчетов о поведении опасного пользователя. Для дополнительного ведения журнала Отправьте оповещения об обнаружении рисков центра безопасности Azure в Azure Monitor и настройте пользовательские оповещения и уведомления с помощью групп действий.

Включите расширенную защиту от угроз (ATP) для Azure Key Vault, чтобы создать оповещения для подозрительных действий.

Развертывание Azure AD Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Настройка расширенной защиты от угроз для Azure Key Vault (Предварительная версия): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Оповещения для Azure Key Vault (Предварительная версия): https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory обнаружения рисков: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Создание групп действий и управление ими в портал Azure: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups


**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: управление ресурсами Azure из только утвержденных расположений

**Руководство**. Настройка условия расположения политики условного доступа и Управление именованными расположениями. С помощью именованных расположений можно создавать логические группы диапазонов IP-адресов или стран и регионов. Вы можете ограничить доступ к конфиденциальным ресурсам, таким как Key Vault секреты, в настроенные именованные расположения.

Каково условие расположения в Azure Active Directory условном доступе?: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="39-use-azure-active-directory"></a>3,9: используйте Azure Active Directory

**Руководство**. Использование Azure Active Directory (AAD) в качестве централизованной системы проверки подлинности и авторизации для ресурсов Azure, таких как key Vault. Это позволяет управлять доступом на основе ролей (RBAC) для администрирования конфиденциальных ресурсов.

 

Краткое руководство. Создание нового клиента в Azure Active Directory: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: регулярно просматривайте и выверяйте доступ пользователей

**Рекомендации**. проверьте журналы Azure Active Directory (AAD), чтобы упростить обнаружение устаревших учетных записей с помощью Azure Key Vault административных ролей. Кроме того, используйте проверки доступа AAD для эффективного управления членством в группах, доступом к корпоративным приложениям, которые могут использоваться для доступа к Azure Key Vault и назначения ролей. Доступ пользователей следует проверять регулярно, например каждые 90 дней, чтобы убедиться, что доступ к ним имеют только нужные пользователи.


Azure Active Directory отчеты и документация по мониторингу:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/


Что такое проверки доступа Azure AD?

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: монитор пытается получить доступ к отключенным учетным записям

**Руководство**. Включение параметров диагностики для Azure Key Vault и Azure Active Directory, отправка всех журналов в log Analytics рабочую область. Настройте нужные оповещения (например, попытки доступа к отключенным секретам) в Log Analytics.

Интеграция журналов Azure AD с журналами Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Миграция из старого решения Key Vault: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: предупреждение об отклонении режима входа учетной записи

**Руководство**. Использование функций защиты идентификации и обнаружения рисков Azure Active Directory для настройки автоматических ответов на обнаруженные подозрительные действия, связанные с защищенными ресурсами Azure Key Vault. Вы должны включить автоматические ответы через метку Azure, чтобы реализовать ответы на безопасность вашей организации. 

Отчет о событиях входа с рисками на Azure Active Directory портале: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Как настроить и включить политики риска: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Как подключить метку Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: предоставьте корпорации Майкрософт доступ к соответствующим пользовательским данным во время сценариев поддержки

**Руководство**: неприменимо; Защищенное хранилище не поддерживается для Azure Key Vault.

Поддерживаемые службы и сценарии в общем доступе: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в разделе [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: поддержание инвентаризации конфиденциальной информации

**Руководство**. Использование тегов для отслеживания ресурсов Azure, в которых хранятся или обрабатываются конфиденциальные сведения о ресурсах с поддержкой Azure Key Vault. 

Использование тегов для Организации ресурсов Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Изолируйте системы, хранящие или обрабатывающие конфиденциальные данные

**Руководство**. Вы можете защитить доступ к Azure Key Vault, используя конечные точки службы виртуальной сети, настроенные для ограничения доступа к определенным подсетям.

После того как правила брандмауэра вступают в действие, можно выполнять только Azure Key Vault операции с плоскостью данных, если запрос исходит из разрешенных подсетей или диапазонов IP-адресов. Это также относится к Azure Key Vaultному доступу в портал Azure. Хотя вы можете перейти к хранилищу ключей из портал Azure, вы не сможете перечислить ключи, секреты или сертификаты, если ваш клиентский компьютер не находится в списке разрешенных. Это также влияет на средство выбора Azure Key Vault и другие службы Azure. Вы можете просмотреть списки хранилищ ключей, но не список ключей, если правила брандмауэра не позволяют компьютеру клиента сделать это.

Настройка Azure Key Vault брандмауэров и виртуальных сетей: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Конечные точки службы виртуальной сети для Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints


**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Отслеживайте и блокируйте несанкционированную пересылку конфиденциальной информации

**Руководство**. все данные, хранящиеся в Azure Key Vault, считаются конфиденциальными. Используйте Azure Key Vault элементы управления доступом к плоскости данных, чтобы управлять доступом к Azure Key Vault секретам. Вы также можете использовать встроенный брандмауэр Key Vault для управления доступом на уровне сети. Чтобы отслеживать доступ к Azure Key Vault, включите Key Vault параметры диагностики и отправьте журналы в учетную запись хранения Azure или Log Analytics рабочую область.

Безопасный доступ к хранилищу ключей: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Настройка Azure Key Vault брандмауэров и виртуальных сетей: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Ведение журнала Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: шифрование всех конфиденциальных данных во время передачи

**Руководство**. весь трафик, который Azure Key Vault для проверки подлинности, управления и доступа к плоскости данных, шифруется и проходит через HTTPS: порт 443. (Тем не менее, для списка отзыва сертификатов иногда будет использоваться трафик HTTP [порт 80]). 

Доступ к Azure Key Vault за брандмауэром: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: использование активного средства обнаружения для распознавания конфиденциальных данных

**Руководство**: неприменимо; все данные в Azure Key Vault (секреты, ключи и сертификаты) считаются конфиденциальными.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="46-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,6: использование активного средства обнаружения для распознавания конфиденциальных данных

**Руководство**. Защита доступа к плоскости управления и данных экземпляров Azure Key Vault.


Безопасный доступ к хранилищу ключей:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**. Корпорация Майкрософт управляет базовой инфраструктурой для Azure Key Vault и реализует четкие элементы управления для предотвращения потери или раскрытия данных клиента.


Что такое Azure Key Vault? https://docs.microsoft.com/azure/key-vault/key-vault-overview

Защита данных клиентов Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: шифрование конфиденциальной информации при хранении

**Руководство**. все управляемые объекты (ключ, сертификаты и секреты) шифруются при хранении в Azure Key Vault.


Средства управления безопасностью для Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-security-controls


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: журналы и оповещения об изменениях критических ресурсов Azure

**Руководство**. использование решения Azure Key Vault Analytics в Azure Monitor для просмотра журналов событий аудита Azure Key Vault.

Решение Azure Key Vault Analytics в Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="vulnerability-management"></a>Управление уязвимостями

*Дополнительные сведения см. в разделе [Security Control: управлением уязвимостей](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: Запуск автоматизированных средств проверки уязвимостей

**Руководство**. Корпорация Майкрософт выполняет Управление уязвимостью в базовых системах, поддерживающих Azure Key Vault.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: н/д; Корпорация Майкрософт выполняет управление исправлениями в базовых системах, поддерживающих Key Vault.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Развертывание автоматизированного решения по управлению исправлениями сторонних производителей

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: сравнение проверок уязвимостей "назад-назад"

**Руководство**. Корпорация Майкрософт выполняет Управление уязвимостью в базовых системах, поддерживающих Key Vault.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5. Использование процесса оценки рисков для определения приоритета устранения обнаруженных уязвимостей

**Руководство**. Используйте оценки рисков по умолчанию (Оценка безопасности), предоставляемые центром безопасности Azure.

Улучшение оценки безопасности в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в разделе [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1. Использование обнаружения ресурсов Azure

**Руководство**. Использование графа ресурсов Azure для запроса и обнаружения всех ресурсов (включая Azure Key Vault экземпляры) в подписке. Убедитесь, что у вас есть соответствующие разрешения (на чтение) в клиенте и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

Краткое руководство. Запуск первого запроса графа ресурсов с помощью Azure Resource Graph Explorer: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Получение подписок, к которым может получить доступ текущая учетная запись.: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Что такое управление доступом на основе ролей (RBAC) для ресурсов Azure? https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="62-maintain-asset-metadata"></a>6,2: сохранение метаданных активов

**Руководство**. применение тегов к Azure Key Vault ресурсам, предоставляя метаданные для логической организации их в таксономию.


Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: удалите неавторизованные ресурсы Azure

**Руководство**. Использование тегов, групп управления и отдельных подписок (при необходимости) для упорядочения и отслеживания Azure Key Vault экземпляров и связанных ресурсов. Регулярно выверка инвентаризации и своевременное удаление неавторизованных ресурсов из подписки.


Создайте дополнительную подписку Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription


Создание групп управления для Организации ресурсов и управления ими:

https://docs.microsoft.com/azure/governance/management-groups/create


Использование тегов для Организации ресурсов Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: ведение инвентаризации утвержденных ресурсов Azure и наименований программного обеспечения

**Руководство**. Определение списка утвержденных ресурсов Azure и утвержденного программного обеспечения для ресурсов вычислений

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: отслеживание неутвержденных ресурсов Azure

**Руководство**. Использование политик Azure для ограничения типа ресурсов, которые могут быть созданы в подписках клиентов, с помощью следующих встроенных определений политик:

- Недопустимые типы ресурсов
- Допустимые типы ресурсов

Кроме того, используйте граф ресурсов Azure для запроса или обнаружения ресурсов в подписках.

Руководство. Создание политик для обеспечения соответствия требованиям и управление ими: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Краткое руководство. Запуск первого запроса графа ресурсов с помощью Azure Resource Graph Explorer: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для Azure как к целому, так и к расчетным ресурсам.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="68-use-only-approved-applications"></a>6,8: Используйте только утвержденные приложения

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="69-use-only-approved-azure-services"></a>6,9: Используйте только утвержденные службы Azure

**Руководство**. Использование политик Azure для ограничения типа ресурсов, которые могут быть созданы в подписках клиентов, с помощью следующих встроенных определений политик:

- Недопустимые типы ресурсов
- Допустимые типы ресурсов

Руководство. Создание политик для обеспечения соответствия требованиям и управление ими: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Примеры политики Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="610-implement-approved-application-list"></a>6,10: реализация списка утвержденных приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: ограничьте возможность пользователей взаимодействовать с Азурересаурцес Manager с помощью сценариев

**Руководство**. Использование условного доступа Azure для ограничения возможности пользователей взаимодействовать с Azure Resource Manager (ARM) путем настройки "блокировать доступ" для приложения "Управление Microsoft Azure". Это может препятствовать созданию и изменению ресурсов в среде с высоким уровнем безопасности, например при использовании конфигурации Key Vault.


Управление доступом к управлению Azure с помощью условного доступа: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. KeyVault для создания настраиваемых политик для аудита или принудительного применения конфигурации экземпляров Azure Key Vault. Вы также можете использовать встроенные определения политик Azure для Azure Key Vault, например:


Объекты Key Vault должны быть восстанавливаемыми

Развертывание параметров диагностики для Key Vault в Log Analytics рабочей области

Журналы диагностики в Key Vault должны быть включены

Key Vault должны использовать конечную точку службы виртуальной сети

Развертывание параметров диагностики для Key Vault в концентратор событий


Используйте рекомендации из центра безопасности Azure в качестве защищенного шаблона базовой конфигурации для экземпляров Azure Key Vault.


Просмотр доступных псевдонимов политик Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Руководство. Создание политик для обеспечения соответствия и управление ими:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Установка безопасных конфигураций операционной системы

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: поддержание защищенных конфигураций ресурсов Azure

**Руководство**. Использование политики Azure [Deny] и [развертывание если не существует] для обеспечения безопасности параметров в ресурсах с поддержкой Azure Key Vault. 


Руководство. Создание политик для обеспечения соответствия и управление ими:

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

**Руководство**. Если вы используете пользовательские определения политики Azure для ресурсов с поддержкой Azure Key Vault, используйте Azure Repos для безопасного хранения кода и управления им.


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

**Руководство**. Использование псевдонимов политик Azure в пространстве имен Microsoft. KeyVault для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.



Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией системы для операционных систем

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9. Реализация автоматического мониторинга конфигурации для служб Azure

**Руководство**. Использование центра безопасности Azure для выполнения проверок базовых данных для ресурсов, защищенных Azure Key Vault 

  

Как исправить рекомендации в центре безопасности Azure: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10. Реализация автоматического мониторинга конфигурации для операционных систем

**Руководство**: неприменимо; Этот тест производительности предназначен для расчетных ресурсов.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="711-manage-azure-secrets-securely"></a>7,11: безопасное управление секретами Azure

**Рекомендации**. Используйте управляемое удостоверение службы в сочетании с Azure Key Vault, чтобы упростить и защитить управление секретами для облачных приложений. Убедитесь, что Azure Key Vault обратимое удаление включено.


Как интегрироваться с управляемыми удостоверениями Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity


Создание Key Vault.

https://docs.microsoft.com/azure/key-vault/quick-create-portal


Как обеспечить проверку подлинности Key Vault с помощью управляемого удостоверения: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: безопасно управляйте удостоверениями и автоматически

**Рекомендации**. Используйте управляемое удостоверение службы в сочетании с Azure Key Vault, чтобы упростить и защитить управление секретами для облачных приложений. 

  

Как интегрироваться с управляемыми удостоверениями Azure: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  



Создание Key Vault. 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

Как обеспечить проверку подлинности Key Vault с помощью управляемого удостоверения:  
https://docs.microsoft.com/azure/key-vault/managed-identity

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

**Руководство**. на базовом узле, поддерживающем службы Azure (например, Azure Key Vault), включено антивредоносное по Майкрософт, однако оно не выполняется в содержимом клиента.


Предварительное сканирование любого содержимого, отправляемого или отправленного в Нерасчетные ресурсы Azure, например Azure Key Vault. Корпорация Майкрософт не может получить доступ к данным в этих экземплярах.


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

**Рекомендации**: регулярное автоматическое резервное копирование сертификатов Key Vault, ключей, управляемых учетных записей хранения и секретов с помощью следующих команд PowerShell:

- Backup-Азкэйваултцертификате
- Backup-Азкэйваулткэй
- Backup-Азкэйваултманажедсторажеаккаунт
- Backup-Азкэйваултсекрет

При необходимости резервные копии Key Vault можно хранить в Azure Backup.

Резервное копирование Key Vault сертификатов: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Резервное копирование Key Vault ключей: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Резервное копирование Key Vault управляемых учетных записей хранения: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Как создать резервную копию Key Vault секреты: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Включение Azure Backup: https://docs.microsoft.com/azure/backup


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом.

**Руководство**. Резервное копирование сертификатов Key Vault, ключей, управляемых учетных записей хранения и секретов с помощью следующих команд PowerShell:

- Backup-Азкэйваултцертификате
- Backup-Азкэйваулткэй
- Backup-Азкэйваултманажедсторажеаккаунт
- Backup-Азкэйваултсекрет

При необходимости резервные копии Key Vault можно хранить в Azure Backup.

Резервное копирование Key Vault сертификатов: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Резервное копирование Key Vault ключей: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Резервное копирование Key Vault управляемых учетных записей хранения: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Как создать резервную копию Key Vault секреты: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Включение Azure Backup: https://docs.microsoft.com/azure/backup

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Рекомендации**. периодически выполняется восстановление данных сертификатов Key Vault, ключей, управляемых учетных записей хранения и секретов с помощью следующих команд PowerShell:

- Restore-Азкэйваултцертификате
- Restore-Азкэйваулткэй
- Restore-Азкэйваултманажедсторажеаккаунт
- Restore-Азкэйваултсекрет

Восстановление сертификатов Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Восстановление ключей Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Как восстановить Key Vault управляемые учетные записи хранения: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Как восстановить Key Vault секреты: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и управляемых клиентом ключей

**Рекомендации**. Убедитесь, что для Azure Key Vault включено обратимое удаление. Обратимое удаление позволяет восстанавливать удаленные хранилища ключей и объекты хранилища, такие как ключи, секреты и сертификаты. 


Использование обратимого удаления Azure Key Vault: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

## <a name="incident-response"></a>реагирование на инциденты.

*Дополнительные сведения см. в разделе [контроль безопасности: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: создайте руководство по реагированию на инциденты

**Руководство**. Создание руководства по реагированию на инциденты для вашей организации. Убедитесь, что имеются письменные планы реагирования на инциденты, которые определяют все роли персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента. Эти процессы должны сосредоточиться на защите конфиденциальных систем, например, с помощью Key Vault секретов.



Как настроить автоматизацию рабочих процессов в центре безопасности Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   



Руководство по созданию собственного процесса реагирования на инциденты безопасности:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/



Анатомия инцидента Microsoft Security Response Center:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   



Клиент может также использовать руководство по обработке инцидентов безопасности компьютера NIST, чтобы помочь в создании собственного плана реагирования на инциденты: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: создание оценки инцидента и процедуры определения приоритетов

**Руководство**. Центр безопасности назначает серьезность каждому оповещению, чтобы вы могли определить, какие предупреждения следует изучить первыми. Серьезность основывается на том, насколько надежным является центр безопасности в поиске или аналитике, используемой для выдаче оповещения, а также об уровне достоверности, который был вредоносной задачей, вызвавшей оповещение. Кроме того, четко помечать подписки (например, Рабочая, не производственная) и создайте систему именования для четкого обнаружения и классификации ресурсов Azure, особенно для обработки конфиденциальных данных, таких как Azure Key Vault секреты.

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="103-test-security-response-procedures"></a>10,3: тестирование процедур реагирования на безопасность

**Руководство**. проведение упражнений по тестированию возможностей реагирования на инциденты системы с регулярным интервалом для защиты экземпляров Azure Key Vault и связанных ресурсов. Выявление слабых точек и пробелов и пересмотр плана по мере необходимости.


Обратитесь к публикации NIST: руководство по тестированию, обучению и упражнениям для ИТ-планов и возможностей. 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: укажите контактные данные инцидента безопасности и настройте уведомления о событиях для инцидентов безопасности.

**Руководство**. контактные данные инцидентов безопасности будут использоваться корпорацией Майкрософт для связи с вами, если Microsoft Security Response Center (MSRC) обнаруживает, что доступ к данным был получен незаконные или неавторизованной стороной.  Проверьте инциденты после факта, чтобы убедиться, что проблемы устранены.



Как задать контакт безопасности центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**. Экспорт оповещений и рекомендаций центра безопасности Azure с помощью функции непрерывного экспорта для выявления рисков для ресурсов с поддержкой Azure Key Vault. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме.  Вы можете использовать соединитель данных центра безопасности Azure для потоковой передачи оповещений в Azure Sentinel. 

 

Как настроить непрерывный экспорт: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Как выполнить потоковую передачу предупреждений в Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Автоматизируйте ответ на оповещения системы безопасности

**Руководство**. Использование функции автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" в оповещениях системы безопасности и рекомендациях по защите ресурсов, защищенных Azure Key Vault. 

 

Как настроить автоматизацию рабочего процесса и Logic Apps: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в разделе [контроль безопасности: тесты на проникновение и примеры для красных команд](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критических обновлений безопасности в течение 60 дней.

**Руководство**. Вы не выполняете прямое тестирование пера непосредственно в службе Azure Key Vault, однако рекомендуется протестировать ресурсы Azure, использующие Key Vault, чтобы обеспечить безопасность секретов.


Чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт, необходимо соблюдать правила корпорации Майкрософт.

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1


Дополнительные сведения о стратегии корпорации Майкрософт и ее выполнении, а также о тестировании на основе уязвимости для облачной инфраструктуры, служб и приложений Майкрософт см. здесь: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

## <a name="next-steps"></a>Следующие шаги

- Ознакомьтесь со статьей о [производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Дополнительные сведения о [базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
