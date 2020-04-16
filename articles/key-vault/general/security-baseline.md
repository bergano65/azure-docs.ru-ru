---
title: Базовый унизел безопасности Azure для Ключевого Убежища
description: Базовый унизел безопасности Azure для Ключевого Убежища
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: cb908c4b105324e0866d53463ecd3a8a59e7e651
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429893"
---
# <a name="azure-security-baseline-for-key-vault"></a>Базовый унизел безопасности Azure для Ключевого Убежища

Базовый униза безопасности Azure для Key Vault содержит рекомендации, которые помогут вам улучшить безопасность развертывания.

Базовая версия этой службы взята из [версии 1.0 Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)которая содержит рекомендации о том, как можно обезопасить свои облачные решения в Azure с нашими рекомендациями по передовым практикам.

Для получения дополнительной информации смотрите [обзор базовых линий безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Защита ресурсов, использующих группы сетевой безопасности или брандмауэр Azure в виртуальной сети

**Руководство**: Интеграция Aure Key Vault с частной ссылкой Azure. 

Служба частных ссылок Azure позволяет получить доступ к службам Azure (например, Azure Key Vault) и Azure размещает клиентские/партнерские службы через частную конечную точку в виртуальной сети.

Частная конечная точка Azure — это сетевой интерфейс, который защищенно и надежно подключается к службе через Приватный канал Azure. Частная конечная точка использует частный IP-адрес из виртуальной сети, по сути перемещая службу в виртуальную сеть. Весь трафик к службе может маршрутизироваться через частную конечную точку, поэтому шлюзы, устройства преобразования сетевых адресов (NAT), подключения ExpressRoute и VPN, а также общедоступные IP-адреса не требуются. Трафик между виртуальной сетью и службой проходит через магистральную сеть Майкрософт, что позволяет избежать рисков общедоступного Интернета. Вы можете подключиться к экземпляру ресурса Azure, обеспечивая наивысшую степень детализации в управлении доступом.

Как интегрировать Key Vault с azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Мониторинг и регистрация конфигурации и трафика Vnets, Subnets и NICs

**Руководство**: Используйте Центр безопасности Azure и следуйте рекомендациям по защите сети, чтобы помочь обезопасить настроенные ресурсы в Azure. 

Для получения дополнительной информации о сетевой безопасности, предоставленной Центром безопасности Azure: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="13-protect-critical-web-applications"></a>1.3: Защита критически важных веб-приложений

**Руководство**: Не применимо; эта рекомендация предназначена для веб-приложений, работающих на Azure App Service или для вычисления ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Запретить связь с известными вредоносными IP-адресами

**Руководство:** Включить стандарт DDoS-защиты Azure в виртуальных сетях Azure, связанных с экземплярами Key Vault, для защиты от распределенных атак типа «отказ в обслуживании». Используйте Azure Security Center Integrated Threat Intelligence, чтобы отказать в сообщении с известными вредоносными или неиспользованными IP-адресами в Интернете.

 
Управление стандартом DDoS Azure с помощью портала Azure:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Обнаружение угроз для слоя службы Azure в Центре безопасности Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Запись сетевых пакетов и журналов потока

**Руководство**: Azure Key Vault не использует группы сетевой безопасности (NSG), а журналы потоков для Azure Key Vault не фиксируются. Вместо этого используйте частную ссылку Azure для защиты экземпляров Azure Key Vault и обеспечения диагностических параметров для записи метрик и событий аудита.

Интеграция Key Vault с частной ссылкой Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service

Регистрация в Хранилище ключей Azure:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Развертывание сетевых систем обнаружения/предотвращения вторжений (IDS/IPS)

**Руководство**: Это требование может быть выполнено путем настройки расширенной защиты от угроз (ATP) для Azure Key Vault. ATP обеспечивает дополнительный уровень безопасности разведки. Этот инструмент обнаруживает потенциально опасные попытки получить доступ к учетным записям Ключ-убежище Azure или использовать их.

Когда Центр безопасности Azure обнаруживает аномальную активность, он отображает оповещения. Он также по электронной почте администратор подписки с подробной информацией о подозрительной деятельности и рекомендации о том, как исследовать и устранять выявленные угрозы.

Настройка расширенной защиты от угроз для Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Управление трафиком для веб-приложений

**Руководство**: Не применимо; эта рекомендация предназначена для веб-приложений, работающих на Azure App Service или для вычисления ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Минимизация сложности и административных накладных расходов на правила сетевой безопасности

**Руководство**: Для ресурсов, которым необходим доступ к экземплярам Azure Key Vault, используйте теги службAzов для хранилища ключей Azure для определения элементов управления доступом к сети в группах сетевой безопасности или Брандмауэре Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указывая имя тега службы (например, ApiManagement) в соответствующем поле источника или назначения правила, можно разрешить или отклонить трафик для соответствующей службы. Корпорация Майкрософт управляет адресными префиксами, охватываемыми тегом службы, и автоматически обновляет тег службы при изменении адресов.

Обзор тегов службы Azure:https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**: Определите и внедрить стандартные конфигурации безопасности для сетевых ресурсов, связанных с экземплярами Azure Key Vault с политикой Azure. Используйте псевдонимы Azure Policy в пространствах имен "Microsoft.KeyVault" и "Microsoft.Network" для создания пользовательских политик для аудита или обеспечения безопасности сетевой конфигурации экземпляров Azure Key Vault. Можно также использовать встроенные определения политики, связанные с Azure Key Vault, такие как:

Служба Key Vault должна использовать конечную точку службы виртуальной сети

Учебник: Создание и управление политиками для обеспечения соответствия требованиям:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Образцы политики Azure:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Быстрый запуск: Определите и назначайте чертеж на портале:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="110-document-traffic-configuration-rules"></a>1.10: Правила конфигурации трафика документов

**Руководство**: Используйте теги для ресурсов, связанных с сетевой безопасностью и потоком трафика, для экземпляров Azure Key Vault для предоставления метаданных и логической организации.

Используйте любое из встроенных определений политики Azure, связанных с пометкой, например "Требуемый тег и его значение", чтобы гарантировать, что все ресурсы создаются с тегами и уведомлять вас о существующих немаркированных ресурсах.

Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий на ресурсах, основанных на их тегах.

Используйте теги для организации ресурсов Azure:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Используйте автоматизированные инструменты для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**: Используйте журнал действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений для сетевых ресурсов, связанных с экземплярами Azure Key Vault. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критически важных сетевых ресурсов.

Просмотр и извлечение событий журнала активности Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Создавайте, просматривайте и управляйте оповещениями о журналах активности с помощью Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Используйте утвержденные источники синхронизации времени

**Руководство**: Не применимо; Корпорация Майкрософт поддерживает источник времени, используемый для ресурсов Azure, таких как Убежище ключей Azure, для меток времени в журналах.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="22-configure-central-security-log-management"></a>2.2: Настройка централизованного управления журналами безопасности

**Руководство**: Ingest журналы через Azure Monitor для агрегирования данных безопасности, генерируемых Azure Key Vault. В Azure Monitor используйте рабочее пространство Azure Log Analytics для запроса и выполнения аналитики, а также для долгосрочного хранения данных.- Кроме того, вы можете включить и на борт данных Azure Sentinel или сторонних SIEM. 

Регистрация в Хранилище ключей Azure:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Быстрый запуск: Как на борту Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Включить журнал аудита для ресурсов Azure

**Руководство**: Включите диагностические настройки в экземплярах Azure Key Vault для доступа к журналам аудита, безопасности и диагностики. В журналы активности, которые автоматически доступны, входят источник событий, дата, пользователь, метка времени, адреса источников, адреса назначения и другие полезные элементы.

Регистрация в Хранилище ключей Azure:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Сбор журналов безопасности из операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="25-configure-security-log-storage-retention"></a>2.5: Настройка хранения журналов безопасности

**Руководство**: В Azure Monitor для рабочего пространства log Analytics, используемого для хранения журналов Azure Key Vault, установите период хранения в соответствии с правилами соответствия организации. Используйте учетные записи хранения Azure для долгосрочного/архивного хранения.

Измените период хранения данных:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="26-monitor-and-review-logs"></a>2.6: Мониторинг и обзор журналы

**Руководство**: Анализ и мониторинг журналов для аномального поведения и регулярно просматривать результаты для ресурсов, защищенных Azure Key Vault. Используйте рабочее пространство аналитики журналов Azure Monitor для просмотра журналов и выполнения запросов на данных журнала. Кроме того, вы можете включить и на борт данных Azure Sentinel или третьей стороной SIEM. 

Быстрый запуск: На борту Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Начатые с аналитики журналов в Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Начатые с запросов журнала в Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Включить оповещения об аномальной активности

**Руководство**: В Центре безопасности Azure включите расширенную защиту угроз (ATP) для Key Vault. Включите диагностические настройки в Хранилище ключей Azure и отправьте журналы в рабочее пространство log Analytics. На борту рабочего пространства журнала Analytics для Azure Sentinel, поскольку оно обеспечивает систему автоматического реагирования на систему безопасности (SOAR). Это позволяет создавать и использовать игровые книги (автоматизированные решения) для устранения проблем безопасности.

Быстрый запуск: На борту Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Управление и реагирование на оповещения безопасности в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Реагировать на события с помощью предупреждений Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="28-centralize-anti-malware-logging"></a>2.8: Централизованное заготовку от вредоносных программ

**Руководство**: Не применимо; Azure Key Vault не обрабатывает и не производит журналы, связанные с вредоносным ПО.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="29-enable-dns-query-logging"></a>2.9: Включить журнал запросов DNS

**Руководство**: Не применимо; Azure Key Vault не обрабатывает и не производит журналы, связанные с DNS.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="210-enable-command-line-audit-logging"></a>2.10: Включить журнал аудита командной строки

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Ведение перечня административных счетов

**Руководство**: Ведение инвентаризации ваших приложений, зарегистрированных в Azure Active Directory, а также любых учетных записей пользователей, которые имеют доступ к ключам, секретам и сертификатам Azure Key Vault. Вы можете использовать портал Azure или PowerShell для запроса и согласования доступа Key Vault. Чтобы просмотреть доступ в PowerShell, используйте следующую команду:

(Get-AzResource -ResourceId (KeyVaultResourceID). Свойства.ДоступПолитика

Регистрация приложения в Active Directory Azure:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Безопасный доступ к хранилищу ключей:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Изменение паролей по умолчанию, где это применимо

**Руководство**: Не применимо; Azure Key Vault не имеет концепции паролей по умолчанию, так как аутентификация обеспечивается Active Directory и защищена управлением доступом на основе ролей.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Использование выделенных административных учетных записей

**Руководство**: Создание стандартных операционных процедур вокруг использования выделенных административных учетных записей, которые имеют доступ к экземплярам Azure Key Vault. Используйте центр идентификации и управления доступом Azure Security (в настоящее время находится в предварительном режиме) для мониторинга количества активных административных учетных записей.

Мониторинг личности и доступа (предварительный просмотр):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Используйте одиночный вписаться (SSO) с активным каталогом Azure

**Руководство**: Используйте основную службу Azure совместно с AppId, TenantID и ClientSecret, чтобы беспрепятственно аутентифицировать ваше приложение и получить токен, который будет использоваться для доступа к секретам Убежища ключей Azure.

Аутентификация от службы к обслуживанию в Azure Key Vault с помощью .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Используйте многофакторную аутентификацию для всего доступа на основе активного каталога Azure

**Руководство:** Включите многофакторную аутентификацию Azure Active Directory multi-Factor Authentication и следуйте рекомендациям Центра идентификации и управления доступом Azure (в настоящее время в предварительном просмотре), чтобы защитить ресурсы с поддержкой концентратора.

Планирование облачного развертывания Azure Multi-Factor Authentication:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Мониторинг личности и доступа (предварительный просмотр):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Используйте выделенные машины (Привилегированные рабочие станции доступа) для всех административных задач

**Руководство**: Используйте привилегированное рабочее место доступа (PAW) с Azure Multi-Фактор аутентификации (MFA) настроен для входа в систему и настройки ресурсов, включенных в Key Vault. 

Привилегированные рабочие станции доступа:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Планирование облачного развертывания Azure Multi-Factor Authentication:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Войти и предупредить о подозрительной активности с административных счетов

**Руководство**: Используйте Azure Active Directory (AAD) Privileged Identity Management (PIM) для генерации журналов и оповещений, когда в среде происходит подозрительная или небезопасная активность. Используйте обнаружение рисков AAD для просмотра предупреждений и отчетов о рискованном поведении пользователей. Для дополнительных журналов отправьте оповещения о обнаружении рисков Центра безопасности Azure в Azure Monitor и настройте пользовательские оповещения/уведомления с помощью групп действий.

Включите расширенную защиту угроз (ATP) для Azure Key Vault для создания оповещений о подозрительной активности.

Развертывание azure AD Привилегированное управление идентификацией (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Настройка расширенной защиты от угроз для Azure Key Vault (предварительный просмотр):https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Оповещения для Убежища ключей Azure (Предварительный просмотр):https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Обнаружение рисков активных каталогов Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Создавайте и управляйте группами действий на портале Azure:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Управление ресурсами Azure только из утвержденных мест

**Руководство**: Наверсти состояние местоположения политики условного доступа и управление указанными местоположениями. С названными местоположениями можно создавать логические группирования диапазонов IP-адресов или стран и регионов. Можно ограничить доступ к конфиденциальным ресурсам, таким как секреты Key Vault, к настроенным местоположений.

Каково состояние местоположения в условном доступе к условному каталогу Azure Active Directory?:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="39-use-azure-active-directory"></a>3.9: Используйте активный каталог Azure

**Руководство**: Используйте Активный каталог Azure (AAD) в качестве центральной системы аутентификации и авторизации для ресурсов Azure, таких как Key Vault. Это позволяет элементу управления доступом на основе ролей (RBAC) управлять конфиденциальными ресурсами.

 

Быстрый запуск: Создайте нового арендатора в Active Directory Azure:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Регулярно проверяйте и согласовывая доступ пользователей

**Руководство**: Просмотр журналов Active Directory Azure (AAD), чтобы обнаружить устаревшие учетные записи с административными ролями Azure Key Vault. Кроме того, используйте обзоры доступа AAD для эффективного управления членством в группах, доступа к корпоративным приложениям, которые могут быть использованы для доступа к Убежище ключей Azure, и ролевых назначений. Доступ пользователей должен регулярно пересматриваться, например, каждые 90 дней, чтобы убедиться, что только нужные пользователи имеют постоянный доступ.

Отчеты об активном каталоге Azure и документация мониторинга:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Что такое обзоры доступа Azure AD?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Попытка monitor получить доступ к деактивированным учетным записям

**Руководство:** Включить диагностические настройки для Azure Key Vault и Active Directory Azure, отправив все журналы в рабочее пространство анализа журналов. Налаживание желаемых оповещений (например, попытки получить доступ к секретам с ограниченными возможностями) в журнале Analytics.

Интегрируйте журналы Azure AD с журналами Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Миграция из старого решения Key Vault:https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Оповещение об отклонении поведения входа в учетную запись

**Руководство**: Используйте функции защиты идентификации и обнаружения рисков Azure Active Directory для настройки автоматизированных ответов на обнаруженные подозрительные действия, связанные с защищенными ресурсами Azure Key Vault. Для реализации ответов на безопасность в организации необходимо включить автоматические ответы через Azure Sentinel. 

Отчет о рискованных входе в систему на портале Active Directory Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Как: Настроить и включить политики риска:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Как на борту Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Предоставить корпорации Майкрософт доступ к соответствующим данным клиентов во время сценариев поддержки

**Руководство**: Не применимо; Блокировка клиента не поддерживается для Azure Key Vault.

Поддерживаемые службы и сценарии в общей доступности:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

## <a name="data-protection"></a>Защита данных

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Ведение перечня конфиденциальной информации

**Руководство**: Используйте теги для отслеживания ресурсов Azure, которые хранят или обрабатывают конфиденциальную информацию в ресурсах с поддержкой Azure Key Vault. 

Используйте теги для организации ресурсов Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Изолировать системы хранения или обработки конфиденциальной информации

**Руководство**: Вы можете обеспечить доступ к Azure Key Vault, спомощьи виртуального конечных точек сетевого обслуживания, настроенных для ограничения доступа к определенным подсетям.

После вступления в силу правил брандмауэра можно выполнять операции плоскости данных Azure Key Vault только в том случае, если ваш запрос исходит из разрешенных подсетей или диапазонов IP-адресов. Это также относится к доступу к Доступу к ключу Убежища Azure на портале Azure. Несмотря на то, что вы можете просматривать хранилище ключей с портала Azure, вы не сможете перечислить ключи, секреты или сертификаты, если ваша клиентская машина не находится в разрешенном списке. Это также влияет на сборщик ключей Azure Vault и другие службы Azure. Вы можете видеть списки ключей Vaults, но не список ключей, если правила брандмауэра не позволяют вашей клиентской машине сделать это.

Настроят брандмауэры Azure Key Vault и виртуальные сети:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Виртуальные конечные точки сетевого обслуживания для Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Руководство**: Все данные, хранящиеся в Хранилище ключей Azure, считаются конфиденциальными. Используйте элементы управления доступом к плоскостям данных Azure Key Vault для управления доступом к секретам Azure Key Vault. Вы также можете использовать встроенный брандмауэр Key Vault для управления доступом на сетевом слое. Чтобы контролировать доступ к Убежище ключей Azure, включите диагностические настройки Key Vault и отправьте журналы в учетную запись хранения Azure или рабочую область анализа журналов.

Безопасный доступ к хранилищу ключей:https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Настроят брандмауэры Azure Key Vault и виртуальные сети:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Регистрация в Хранилище ключей Azure:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Шифрование всей конфиденциальной информации в пути

**Руководство**: Весь трафик в Azure Key Vault для проверки подлинности, управления и доступа к плоскости данных зашифрован и проходит через HTTPS: port 443. (Однако, иногда будет http (порт 80) трафик для CRL.) 

Доступ к Убежище ключа Azure за брандмауэром:https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Используйте инструмент активного обнаружения для идентификации конфиденциальных данных

**Руководство**: Не применимо; все данные в Azure Key Vault (секреты, ключи и сертификаты) считаются конфиденциальными.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Используйте Azure RBAC для управления доступом к ресурсам

**Руководство**: Безопасный доступ к плоскости управления и данным и безопасности экземпляров Azure Key Vault.

Безопасный доступ к хранилищу ключей:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Мониторинг Центра безопасности Azure:** неприменим

**Ответственность**: Клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Используйте предотвращение потери данных на основе хоста для обеспечения контроля доступа

**Руководство**: Корпорация Майкрософт управляет базовой инфраструктурой для Azure Key Vault и внедрила строгие средства управления для предотвращения потери или выдержки данных клиентов.

Что такое хранилище ключей Azure?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Защита данных клиентов Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Шифрование конфиденциальной информации в состоянии покоя

**Руководство**: Все управляемые объекты (ключ, сертификаты и секреты) шифруются в покоя в Azure Key Vault.

Вспомогательная документация:

- [Модель шифрования и таблица управления ключами](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Войти и предупредить об изменениях в критических ресурсах Azure

**Руководство**: Используйте решение Azure Key Vault Analytics в Azure Monitor для просмотра журналов событий аудита Azure Key Vault.

Решение Azure Key Vault Analytics в Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="vulnerability-management"></a>Управление уязвимостями

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Запуск автоматизированных инструментов сканирования уязвимостей

**Руководство**: Корпорация Майкрософт выполняет управление уязвимостями в базовых системах, поддерживающих Azure Key Vault.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: N/A; Корпорация Майкрософт выполняет управление исправлениями в базовых системах, поддерживающих Key Vault.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Развертывание автоматизированного решения для управления патчами сторонних программ

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Сравните сканирование уязвимости спина к спине

**Руководство**: Корпорация Майкрософт выполняет управление уязвимостями на базовых системах, поддерживающих Key Vault.

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Корпорация Майкрософт

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Используйте процесс оценки рисков, чтобы определить приоритеты в устранении обнаруженных уязвимостей

**Руководство**: Используйте оценки риска по умолчанию (Secure Score), предоставляемые Центром безопасности Azure.

Улучшайте свой безопасный результат в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Используйте обнаружение активов Azure

**Руководство**: Используйте график ресурсов Azure для запроса и обнаружения всех ресурсов (включая экземпляры Azure Key Vault) в подписке. Убедитесь, что у вас есть соответствующие (читай) разрешения в арендаторе и можете перечислить все подписки Azure, а также ресурсы в рамках подписки.

Быстрый запуск: Запустите свой первый запрос Resource Graph, используя Azure Resource Graph Explorer:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Получите подписки, к которым может получить доступ текущий счет.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Общие сведения об управлении доступом на основе ролей (RBAC) для ресурсов Azure

https://docs.microsoft.com/azure/role-based-access-control/overview

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="62-maintain-asset-metadata"></a>6.2: Поддержание метаданных активов

**Руководство**: Применяйте теги к ресурсам Azure Key Vault, давая метаданные, чтобы логически организовать их в таксономию.

Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Удалить несанкционированные ресурсы Azure

**Руководство**: Используйте пометки, группы управления и отдельные подписки, где это уместно, для организации и отслеживания экземпляров Azure Key Vault и связанных с ними ресурсов. Регулярно согласовывать инвентаризацию и своевременно исключать несанкционированные ресурсы из подписки.

Создайте дополнительную подписку Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание групп управления для организации и управления ресурсами:

https://docs.microsoft.com/azure/governance/management-groups/create

Используйте теги для организации ресурсов Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Ведение перечня утвержденных ресурсов Azure и названий программного обеспечения

**Руководство**: Определите список утвержденных ресурсов Azure и утвержденного программного обеспечения для ваших вычислительных ресурсов

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Мониторинг неодобренных ресурсов Azure

**Руководство**: Используйте политики Azure для ограничения типа ресурсов, которые могут быть созданы в подписке клиента (ы) с использованием следующих встроенных определений политики:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Кроме того, используйте график ресурсов Azure для запроса/обнаружения ресурсов в подписке(ы).

Учебник: Создание и управление политиками для обеспечения соответствия требованиям:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Быстрый запуск: Запустите свой первый запрос Resource Graph, используя Azure Resource Graph Explorer:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Мониторинг неодобренных программных приложений в вычислительных ресурсах

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Удалить неодобренные ресурсы и программные приложения Azure

**Руководство**: Не применимо; эта рекомендация предназначена для Azure в целом, а также для вычисления ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="68-use-only-approved-applications"></a>6.8: Используйте только одобренные приложения

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="69-use-only-approved-azure-services"></a>6.9: Используйте только утвержденные службы Azure

**Руководство**: Используйте политики Azure для ограничения типа ресурсов, которые могут быть созданы в подписке клиента (ы) с использованием следующих встроенных определений политики:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Учебник: Создание и управление политиками для обеспечения соответствия требованиям:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Образцы политики Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="610-implement-approved-application-list"></a>6.10: Реализация утвержденного списка заявок

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Ограничьте возможности пользователей взаимодействовать с менеджером AzureResources с помощью скриптов

**Руководство**: Используйте условный доступ Azure, чтобы ограничить возможности пользователей взаимодействовать с менеджером ресурсов Azure (ARM) путем настройки приложения "Блок-доступ" для приложения "Microsoft Azure Management". Это может предотвратить создание и изменение ресурсов в среде с высоким уровнем безопасности, например, с конфигурацией Key Vault.

Управление доступом к управлению Azure с помощью условия:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ограничьте возможности пользователей по выполнению скриптов в вычислительных ресурсах

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Физически или логически сегрегировать приложения высокого риска

**Руководство**: Не применимо; эта рекомендация предназначена для веб-приложений, работающих на Azure App Service или для вычисления ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

## <a name="secure-configuration"></a>Настройка безопасности

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Создание безопасных конфигураций для всех ресурсов Azure

**Руководство:** Используйте псевдонимы Azure Policy в пространстве имен "Microsoft.KeyVault" для создания пользовательских политик для аудита или обеспечения соблюдения конфигурации экземпляров Azure Key Vault. Можно также использовать встроенные определения политики Azure для Azure Key Vault, такие как:

Объекты Key Vault должны быть восстанавливаемыми

Развертывание параметров диагностики Key Vault в рабочей области Log Analytics

Журналы диагностики в Key Vault должны быть включены

Служба Key Vault должна использовать конечную точку службы виртуальной сети

Развернуть параметры диагностики для Key Vault в концентраторе событий

Используйте рекомендации Центра безопасности Azure в качестве надежной базовой настройки для экземпляров Azure Key Vault.

Как просмотреть доступные Aliases Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Учебник: Создание и управление политиками для обеспечения соответствия требованиям:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Создание безопасных конфигураций операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Поддержание безопасных конфигураций ресурсов Azure

**Руководство**: Используйте политику Azure «отрицать» и «развертывать, если не существует» для обеспечения безопасных настроек в ресурсах с поддержкой Azure Key Vault. 

Учебник: Создание и управление политиками для обеспечения соответствия требованиям:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Понимание эффектов политики Azure: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Поддержание безопасных конфигураций операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Безопасное хранение конфигурации ресурсов Azure

**Руководство**: При использовании пользовательских определений политики Azure для ресурсов с поддержкой Azure Key Vault используйте Azure Repos для безопасного хранения и управления кодом.

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

**Руководство**: Используйте псевдонимы Azure Policy в пространстве имен "Microsoft.KeyVault" для создания пользовательских политик для оповещения, аудита и обеспечения безопасности конфигураций систем. Кроме того, разработайте процесс и конвейер для управления исключениями политики.

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Развертывать инструменты управления конфигурацией системы для операционных систем

**Руководство**: Не применимо; эта рекомендация предназначена для вычислительных ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Внедрение автоматизированного мониторинга конфигурации для служб Azure

**Руководство**: Используйте Центр безопасности Azure для выполнения базовых сканирований для защищенных ресурсами, защищенными azure Key Vault 

  

Как уделать рекомендации в Центре безопасности Azure: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Внедрение автоматизированного мониторинга конфигурации для операционных систем

**Руководство**: Не применимо; этот бенчмарк предназначен для вычислительных ресурсов.


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Не применимо

### <a name="711-manage-azure-secrets-securely"></a>7.11: Надежное управление секретами Azure

**Руководство**: Используйте идентификационную систему управляемых служб совместно с Azure Key Vault для упрощения и обеспечения секретного управления для облачных приложений. Убедитесь, что мягкое удаление Azure Key Vault включено.

Как интегрироваться с управляемыми идентификаторами Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Как создать Убежище ключей:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Как обеспечить аутентификацию Key Vault с управляемой идентификацией: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Управление идентификаторами безопасно и автоматически

**Руководство**: Используйте идентификационную систему управляемых служб совместно с Azure Key Vault для упрощения и обеспечения секретного управления для облачных приложений. 

  

Как интегрироваться с управляемыми идентификаторами Azure: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

Как создать Убежище ключей: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

Как обеспечить аутентификацию Key Vault с управляемой идентификацией:  
https://docs.microsoft.com/azure/key-vault/managed-identity

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

**Руководство**: Антивредоносное початок Майкрософт включено на базовом узеле, поддерживающем службы Azure (например, Azure Key Vault), однако оно не выполняется на контенте клиента.

Предварительно просканировать любой контент, загруженный или отправляемый на некомпьютерные ресурсы Azure, такие как Azure Key Vault. Корпорация Майкрософт не может получить доступ к вашим данным в этих случаях.

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

**Руководство**: Обеспечить регулярное автоматическое резервное копирование сертификатов, ключей, управляемых учетных записей хранения и секретов со следующими командами PowerShell:

- Резервное копирование-AzKeyVaultCertificate

- Резервное копирование-AzKeyVaultKey

- Резервное копирование-AzKeyVaultManagedStorageStorageAccount

- Резервное копирование-AzKeyVaultSecret

В качестве опционов можно хранить резервные данные Key Vault в резервной базе Azure.

Как создать резервную резервную часть сертификатов Key Vault:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Как создать резервную резервную резервную часть ключей Убежища:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Как создать резервную копию ключевых учетных записей хранилища убежища:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Как создать резервную резервную часть секретов Убежища:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Как включить резервное копирование Azure:https://docs.microsoft.com/azure/backup



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Выполните полное резервное копирование системы и резервное копирование любых ключей, управляемых клиентом

**Руководство**: Выполните резервные копии сертификатов, ключей, управляемых учетных записей хранения и секретов со следующими командами PowerShell:

- Резервное копирование-AzKeyVaultCertificate

- Резервное копирование-AzKeyVaultKey

- Резервное копирование-AzKeyVaultManagedStorageStorageAccount

- Резервное копирование-AzKeyVaultSecret

В качестве опционов можно хранить резервные данные Key Vault в резервной базе Azure.

Как создать резервную резервную часть сертификатов Key Vault:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Как создать резервную резервную резервную часть ключей Убежища:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Как создать резервную копию ключевых учетных записей хранилища убежища:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Как создать резервную резервную часть секретов Убежища:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Как включить резервное копирование Azure:https://docs.microsoft.com/azure/backup



**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Проверка всех резервных ups, включая управляемые ключей клиента

**Руководство**: Периодически выполнять восстановление данных сертификатов, ключей, управляемых учетных записей хранения и секретов со следующими командами PowerShell:

- Восстановление-AzKeyVaultCertificate

- Восстановление-AzKeyVaultKey

- Восстановление-AzKeyVaultManagedStorageAccount

- Восстановление-AzKeyVaultSecret

Как восстановить сертификаты Убежища ключей:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Как восстановить ключи Ключ убежища ключи:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Как восстановить учетные записи управляемых хранилищ Убежища:https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Как восстановить секреты убежища ключей:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Обеспечить защиту резервных и управляемых ключей клиента

**Руководство**: Убедитесь, что для Azure Key Vault включено мягкое удаление. Мягкое удаление позволяет восстановить удаленные своды ключей и объекты хранилища, такие как ключи, секреты и сертификаты. 

Как использовать мягкое удаление Клавиатуры Azure Key Vault: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

## <a name="incident-response"></a>реагирование на инциденты.

*Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)*

### <a name="101-create-an-incident-response-guide"></a>10.1: Создать руководство по реагированию на инциденты

**Руководство**: Создайте руководство по реагированию на инциденты для вашей организации. Убедитесь, что существуют письменные планы реагирования на инциденты, определяющие все роли персонала, а также этапы обработки/управления инцидентами от обнаружения до обзора после инцидента. Эти процессы должны быть сосредоточены на защите чувствительных систем, таких как те, которые используют секреты Key Vault.

Как настроить автоматизацию рабочих процессов в Центре безопасности Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Руководство по созданию собственного процесса реагирования на инциденты безопасности:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Анатомия инцидента в Центре реагирования на инцидент:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Клиент может также использовать NIST в компьютерной безопасности инцидентов Руководство по обработке помощи в создании своего собственного плана реагирования на инциденты: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Создание процедуры оценки и приоритетов инцидентов

**Руководство**: Центр безопасности присваивает серьезность каждому оповещению, чтобы помочь вам определить приоритеты, какие предупреждения должны быть исследованы в первую очередь. Тяжесть основана на том, насколько уверен центр безопасности в поиске или аналитический используется для выдачи оповещения, а также уровень уверенности, что был злой умысел за деятельность, которая привела к оповещению. Кроме того, четко пометить подписки (для бывших. производства, не-prod) и создать систему именования для четкой идентификации и классификации ресурсов Azure, особенно тех, которые обрабатывают конфиденциальные данные, такие как секреты Убежища ключей Azure.


**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="103-test-security-response-procedures"></a>10.3: Процедуры реагирования на тестовую безопасность

**Руководство:** Проведение упражнений для проверки возможностей реагирования на инциденты систем на регулярной каденции, чтобы защитить экземпляры Azure Key Vault и связанные с ними ресурсы. Выявление слабых мест и пробелов и пересмотр плана по мере необходимости.

Обратитесь к публикации NIST: Руководство по тестированию, обучению и осуществлению программ для ИТ-планов и возможностей: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Клиент

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Предоставьте контактные данные об инцидентах безопасности и назначьте уведомления о поместью для инцидентов безопасности

**Руководство**: Контактная информация об инцидентах безопасности будет использоваться корпорацией Майкрософт для связи с вами, если Центр реагирования на безопасность Майкрософт (MSRC) обнаружит, что доступ к вашим данным был получен незаконной или несанкционированной стороной.  Просмотрите инциденты после того, чтобы убедиться, что проблемы будут решены.

Как установить контакт с безопасностью Центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Мониторинг Центра безопасности Azure:** Да

**Ответственность**: Клиент

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Включите оповещения о безопасности в систему реагирования на инциденты

**Руководство:** Экспорт оповещений и рекомендаций Центра безопасности Azure с использованием функции непрерывного экспорта, чтобы помочь определить риски для ресурсов с поддержкой Azure Key Vault. Непрерывный экспорт позволяет экспортировать оповещения и рекомендации либо вручную, либо в постоянном, непрерывном режиме.  Для потоковой передачи оповещений в Azure Sentinel можно использовать разъем данных Центра безопасности Azure. 

 

Как настроить непрерывный экспорт: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Как передавать оповещения в Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Автоматизация реагирования на оповещения о безопасности

**Руководство**: Используйте функцию автоматизации рабочего процесса в Центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" на оповещения и рекомендации по защите ресурсов, защищенных Azure Key Vault. 

 

Как настроить автоматизацию рабочего процесса и логические приложения: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Мониторинг Центра безопасности Azure**: В настоящее время недоступен

**Ответственность**: Клиент

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Проведение регулярного тестирования на проникновение ресурсов Azure и обеспечение исправления всех критических данных по безопасности в течение 60 дней

**Руководство**: Вы не должны выполнять тестирование пера на службе Azure Key Vault напрямую, однако рекомендуется протестировать ресурсы Azure, которые используют Key Vault для обеспечения безопасности секретов.

Вам нужно будет следовать правилам взаимодействия корпорации Майкрософт, чтобы убедиться, что ваши тесты на проникновение не нарушают политики Майкрософт:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Вы можете найти более подробную информацию о стратегии Корпорации Майкрософт и выполнении Red Teaming и тестировании на проникновение в реальном времени в майкации в отношении облачной инфраструктуры, служб и приложений, управляемой корпорацией Майкрософт, здесь: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Мониторинг Центра безопасности Azure:** не применимо

**Ответственность**: Общие сведения

## <a name="next-steps"></a>Дальнейшие действия

- Посмотреть [тест безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Узнайте больше об [базовых линиях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
