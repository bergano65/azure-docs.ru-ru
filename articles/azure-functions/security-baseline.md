---
title: Базовый план безопасности Azure для функций Azure
description: Базовый план безопасности Azure для функций Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 040eeda3edc8aa1165915a157cb7e1bdd1594740
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796503"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Базовый план безопасности Azure для функций Azure

Базовый план безопасности Azure для функций Azure содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовая информация для этой службы взята из [теста безопасности Azure версии 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), который содержит рекомендации по защите облачных решений в Azure с помощью наших рекомендаций.

Дополнительные сведения см. в статье [Общие сведения о базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в разделе Security [Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Защитите ресурсы с помощью групп безопасности сети или брандмауэра Azure в виртуальной сети.

**Руководство**. Интеграция приложений функций Azure с виртуальной сетью Azure. Приложения-функции, выполняемые в плане Premium, имеют те же возможности размещения, что и веб приложения в службе приложений Azure, включая функцию "Интеграция виртуальной сети".  Виртуальные сети Azure позволяют разместить многие ресурсы Azure, такие как функции Azure, в сети, не поддерживающей маршрутизацию в Интернете.

- [Интеграция функций с виртуальной сетью Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-vnet)

- [Общие сведения об интеграции виртуальной сети для функций Azure и службы приложений Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Использование центра безопасности Azure и рекомендации по защите сети для защиты сетевых ресурсов и сетевых конфигураций, связанных с приложениями функций Azure.

Если вы используете группы безопасности сети (группы безопасности сети) с реализацией функций Azure, включите журналы потоков NSG и отправьте журналы в учетную запись хранения Azure для аудита трафика. Вы также можете отправить журналы потоков NSG в рабочую область Log Analytics и использовать Аналитика трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитика трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять несетевые настройки.

- [Общие сведения о безопасности сети, предоставляемой центром безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [Включение журналов потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Как включить и использовать Аналитика трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="13-protect-critical-web-applications"></a>1,3: защита критически важных веб-приложений

**Рекомендации**. чтобы полностью защитить конечные точки функции Azure в рабочей среде, следует рассмотреть возможность реализации одного из следующих функций безопасности уровня приложения:
- Включите проверку подлинности и авторизацию службы приложений для приложения-функции.
- Используйте службу управления API Azure (APIM) для проверки подлинности запросов.
- Разверните приложение функции в Среда службы приложений Azure.

Кроме того, убедитесь, что удаленная отладка отключена для производственных функций Azure. Кроме того, общий доступ к ресурсам между источниками (CORS) не должен разрешать всем доменам доступ к приложению-функции Azure. Разрешить взаимодействие только необходимых доменов с приложением функции Azure.

Рассмотрите возможность развертывания брандмауэра веб-приложения Azure (WAF) в составе конфигурации сети для дополнительной проверки входящего трафика. Включите параметр диагностики для WAF и приема журналов в учетную запись хранения, концентратор событий или рабочую область Log Analytics. 

- [Как защитить конечные точки функции Azure в рабочей среде](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Развертывание WAF Azure](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: запретите обмен данными с известными вредоносными IP-адресами

**Руководство**. Включение стандарта защиты от атак DDoS в виртуальных сетях, связанных с приложениями функций, для защиты от атак от атак DDoS. Используйте интегрированную аналитику угроз центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми общедоступными IP-адресами
Кроме того, настройте интерфейсный шлюз, например брандмауэр веб-приложения Azure, для проверки подлинности всех входящих запросов и фильтрации вредоносного трафика. Брандмауэр веб-приложения Azure помогает защитить ваши приложения-функции Azure. для этого следует проверить наличие входящего сетевого трафика, чтобы блокировать внедрение кода SQL, межсайтовые сценарии, передачи вредоносных программ и атаки от атак DDoS. Введение в WAF требует либо Среда службы приложений, либо использовать частные конечные точки (Предварительная версия). Убедитесь, что частные конечные точки больше не находятся в (Предварительная версия), прежде чем использовать их с рабочими нагрузками.

- [Azure Functions networking options](https://docs.microsoft.com/azure/azure-functions/functions-networking-options) (Параметры сети для Функций Azure)

- [План функций Azure Premium](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Общие сведения о средах службы приложений](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Рекомендации по работе с сетями в среде службы приложений](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Настройка защиты от атак DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Развертывание брандмауэра Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Общие сведения об интегрированной системе аналитики угроз в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Общие сведения о адаптивной защите сети в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Общие сведения об управлении доступом к сети в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

- [Использование частных конечных точек для функций Azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: запись сетевых пакетов и журналов потоков

**Руководство**. Если вы используете группы безопасности сети (группы безопасности сети) с реализацией функций Azure, включите журналы потоков для групп безопасности сети и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков в рабочую область Log Analytics и использовать Аналитика трафика для получения сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитика трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять несетевые настройки.

- [Включение журналов потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Как включить и использовать Аналитика трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Включение наблюдателя за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание систем обнаружения вторжений на основе сети и предотвращения вторжений (ИДЕНТИФИКАТОРы и IP-адреса)

**Руководство**. Настройте интерфейсный шлюз, например брандмауэр веб-приложения Azure, для проверки подлинности всех входящих запросов и фильтрации вредоносного трафика. Брандмауэр веб-приложения Azure может помочь защитить ваши приложения-функции, проверив входящий сетевой трафик, чтобы блокировать атаки путем внедрения кода SQL, межсайтовые сценарии, передачи вредоносных программ и атаки от атак DDoS. Введение в WAF требует либо Среда службы приложений, либо использовать частные конечные точки (Предварительная версия). Убедитесь, что частные конечные точки больше не находятся в (Предварительная версия), прежде чем использовать их с рабочими нагрузками.

Кроме того, существует несколько вариантов Marketplace, таких как Barracuda WAF для Azure, которые доступны в Azure Marketplace и включают в себя функции ИДЕНТИФИКАТОРов и IP-адресов.

- [Azure Functions networking options](https://docs.microsoft.com/azure/azure-functions/functions-networking-options) (Параметры сети для Функций Azure)

- [План функций Azure Premium](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Общие сведения о средах службы приложений](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Рекомендации по работе с сетями в среде службы приложений](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Общие сведения о брандмауэре веб-приложения Azure](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [Использование частных конечных точек для функций Azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

- [Общие сведения о облачной службе Barracuda WAF](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="17-manage-traffic-to-web-applications"></a>1,7. Управление трафиком к веб-приложениям

**Руководство**. Настройка внешнего шлюза для сети, например брандмауэра веб-приложения Azure с комплексным шифрованием TLS. Введение в WAF требует либо Среда службы приложений, либо использовать частные конечные точки (Предварительная версия). Убедитесь, что частные конечные точки больше не находятся в (Предварительная версия), прежде чем использовать их с рабочими нагрузками.

- [Azure Functions networking options](https://docs.microsoft.com/azure/azure-functions/functions-networking-options) (Параметры сети для Функций Azure)

- [План функций Azure Premium](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Общие сведения о средах службы приложений](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Рекомендации по работе с сетями в среде службы приложений](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Общие сведения о брандмауэре веб-приложения Azure](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [Настройка сквозного протокола TLS с помощью шлюза приложений на портале](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

- [Использование частных конечных точек для функций Azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: сократите сложность и издержки администрирования правил безопасности сети

**Руководство**. Использование тегов службы виртуальной сети для определения элементов управления доступом к сети для групп безопасности сети или брандмауэра Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, AzureAppService) в соответствующем поле источника или назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, включенными в тег службы, и автоматически обновляет тег службы при изменении адресов.

- [Дополнительные сведения об использовании тегов служб](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определение и реализация стандартных конфигураций безопасности для сетевых параметров, связанных с функциями Azure. Используйте псевдонимы политик Azure в пространствах имен Microsoft. Web и Microsoft. Network, чтобы создать настраиваемые политики для аудита или принудительного применения конфигурации сети для функций Azure. Вы также можете использовать встроенные определения политик для функций Azure, например:
- Функция CORS не должна разрешать всем ресурсам доступ к вашим приложениям-функциям.
- Приложение-функция должно быть доступно только по HTTPS
- В вашем приложении-функции необходимо использовать последнюю версию TLS

Вы также можете использовать схемы Azure для упрощения крупномасштабных развертываний Azure с помощью ключевых артефактов среды пакетов, таких как шаблоны Azure Resource Manager, управление доступом на основе ролей (RBAC) и политики в одном определении схемы. Вы можете легко применить схему к новым подпискам, средам, а также точно настраивать управление и управление с помощью управления версиями.

- [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Создание Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="110-document-traffic-configuration-rules"></a>1,10: правила конфигурации трафика документов

**Руководство**. Если вы используете группы безопасности сети (группы безопасности сети) с реализацией функций Azure, используйте теги для группы безопасности сети и других ресурсов, связанных с сетевой безопасностью и потоком трафика. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и (или) Длительность (т. д.) для любых правил, которые разрешают трафик из сети в сеть.

Используйте любое из встроенных определений политик Azure, связанных с тегами, например "требовать тег и его значение", чтобы убедиться, что все ресурсы созданы с помощью тегов и уведомлять вас о существующих ресурсах без тегов.

Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий с ресурсами на основе их тегов.

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11. использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений сетевых параметров и ресурсов, связанных с развертываниями функций Azure. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых параметров или ресурсов. 

- [Просмотр и получение событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Создание оповещений в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в разделе [Security Control: logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: используйте источники синхронизации утвержденного времени

**Руководство**. Корпорация Майкрософт поддерживает источник времени, используемый для ресурсов Azure, таких как функции Azure для меток времени в журналах.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="22-configure-central-security-log-management"></a>2,2: Настройка централизованного управления журналом безопасности

**Руководство**. для ведения журнала аудита на плоскости управления включите параметры диагностики журнала действий Azure и отправьте журналы в рабочую область log Analytics, в концентратор событий Azure или в учетную запись хранения Azure для архивации. С помощью данных журнала действий Azure можно определить "что, кто и когда" для любых операций записи (размещение, публикация, удаление), выполненных на уровне плоскости управления для ресурсов Azure.

Функции Azure также предлагают встроенную интеграцию с Azure Application Insights для мониторинга функций. Application Insights собирает данные журнала, производительности и ошибок. Она автоматически обнаруживает аномалии производительности и включает мощные средства анализа, помогающие диагностировать проблемы и понять, как используются функции.

Если в приложении-функции Azure имеется встроенное настраиваемое ведение журнала безопасности или аудита, включите параметр диагностики "Функтионапплогс" и отправьте журналы в рабочую область Log Analytics, концентратор событий Azure или учетную запись хранения Azure для архивации. 

При необходимости вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM. 

- [Включение параметров диагностики для журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Как настроить функции Azure с помощью Azure Application Insights](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [Как включить параметры диагностики (созданные пользователем журналы) для функций Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Как подключить метку Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Включите ведение журнала аудита для ресурсов Azure.

**Руководство**. для ведения журнала аудита на плоскости управления включите параметры диагностики журнала действий Azure и отправьте журналы в рабочую область log Analytics, в концентратор событий Azure или в учетную запись хранения Azure для архивации. С помощью данных журнала действий Azure можно определить "что, кто и когда" для любых операций записи (размещение, публикация, удаление), выполненных на уровне плоскости управления для ресурсов Azure.

Если в приложении-функции Azure имеется встроенное настраиваемое ведение журнала безопасности или аудита, включите параметр диагностики "Функтионапплогс" и отправьте журналы в рабочую область Log Analytics, концентратор событий Azure или учетную запись хранения Azure для архивации. 

- [Включение параметров диагностики для журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Как включить параметры диагностики (созданные пользователем журналы) для функций Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: получение журналов безопасности из операционных систем

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="25-configure-security-log-storage-retention"></a>2,5: Настройка хранения журнала безопасности

**Рекомендации**. в Azure Monitor задайте срок хранения журнала для рабочих областей log Analytics, связанных с приложениями функций Azure в соответствии с нормативными требованиями вашей организации.

- [Настройка параметров хранения журнала](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="26-monitor-and-review-logs"></a>2,6: мониторинг и просмотр журналов

**Руководство**. Включение параметров диагностики журнала действий Azure, а также параметров диагностики для приложения "функции Azure" и отправка журналов в log Analytics рабочую область. Выполнение запросов в Log Analytics для поиска терминов, выявления тенденций, анализа закономерностей и предоставления многих других ценных сведений на основе собранных данных.

Включите Application Insights, чтобы приложения функций Azure собираются данные журналов, производительности и ошибок. Данные телеметрии, собранные Application Insights, можно просмотреть в портал Azure.

Если в приложении-функции Azure имеется встроенное настраиваемое ведение журнала безопасности или аудита, включите параметр диагностики "Функтионапплогс" и отправьте журналы в рабочую область Log Analytics, концентратор событий Azure или учетную запись хранения Azure для архивации. 

При необходимости вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM. 

- [Включение параметров диагностики для журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Включение параметров диагностики для функций Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Как настроить функции Azure с помощью Azure Application Insights и просмотреть данные телеметрии](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [Как подключить метку Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Включение оповещений для аномальных действий

**Руководство**. Включение параметров диагностики журнала действий Azure, а также параметров диагностики для приложения "функции Azure" и отправка журналов в log Analytics рабочую область. Выполнение запросов в Log Analytics для поиска терминов, выявления тенденций, анализа закономерностей и предоставления многих других ценных сведений на основе собранных данных. Вы можете создавать оповещения на основе запросов рабочей области Log Analytics.

Включите Application Insights, чтобы приложения функций Azure собираются данные журналов, производительности и ошибок. Вы можете просмотреть данные телеметрии, собранные Application Insights, и создать оповещения в портал Azure.

При необходимости вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM. 

- [Включение параметров диагностики для журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Включение параметров диагностики для функций Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Включение Application Insights для функций Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#enable-application-insights-integration)

- [Создание оповещений в Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Как подключить метку Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="28-centralize-anti-malware-logging"></a>2,8: централизованное ведение журнала защиты от вредоносных программ

**Руководство**: неприменимо; Приложения функций Azure не обрабатывают и не создают журналы, связанные с защитой от вредоносных программ.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="29-enable-dns-query-logging"></a>2,9: Включение ведения журнала запросов DNS

**Руководство**: неприменимо; Приложения функций Azure не обрабатывают и не создают связанные с пользователем журналы, относящиеся к DNS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="210-enable-command-line-audit-logging"></a>2,10: Включение ведения журнала аудита в командной строке

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="identity-and-access-control"></a>Идентификаторы и управление доступом

*Дополнительные сведения см. в разделе [Управление безопасностью: идентификация и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: ведение инвентаризации учетных записей администраторов

**Руководство**. Azure Active Directory (AD) содержит встроенные роли, которые должны быть явно назначены и доступны для запросов. Используйте модуль Azure AD PowerShell для выполнения нерегламентированных запросов для обнаружения учетных записей, входящих в группы администраторов. 

- [Как получить роль каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Как получить членов роли каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Измените пароли по умолчанию, если это применимо

**Руководство**. Управление доступом к плоскости управления для функций Azure осуществляется с помощью Azure Active Directory (AD). В Azure AD нет концепции паролей по умолчанию.

Доступ к плоскости данных можно контролировать с помощью нескольких средств, включая ключи авторизации, ограничения сети и проверку удостоверения AAD. Ключи авторизации используются клиентами, которые подключаются к конечным точкам HTTP службы "функции Azure" и могут быть повторно созданы в любое время. По умолчанию эти ключи создаются для новых конечных точек HTTP.

Для приложений-функций доступны несколько методов развертывания, некоторые из которых могут использовать набор созданных учетных данных. Проверьте методы развертывания, которые будут использоваться для приложения.

- [Защита конечной точки HTTP](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Получение и повторное создание ключей авторизации](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#obtaining-keys)

- [Технологии развертывания в функциях Azure](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: используйте выделенные учетные записи администратора

**Руководство**. Создание стандартных рабочих процедур, связанных с использованием выделенных административных учетных записей. Используйте управление удостоверениями и доступом центра безопасности Azure для мониторинга количества административных учетных записей.

Кроме того, чтобы помочь вам в отслеживании выделенных административных учетных записей, вы можете использовать рекомендации из центра безопасности Azure или встроенных политик Azure, например: необходимо удалить более одного владельца с устаревшими учетными записями с правами владельца из подписки. внешние учетные записи с правами владельца должны быть удалены из подписки.

- [Как использовать центр безопасности Azure для мониторинга удостоверений и доступа (Предварительная версия)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Как использовать политику Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4. Использование единого входа (SSO) с Azure Active Directory

**Рекомендации**. везде, где это возможно, используйте Azure Active Directory SSO вместо настройки отдельных автономных учетных данных для доступа к приложению-функции. Используйте рекомендации по управлению удостоверениями и доступом в центре безопасности Azure. Реализуйте единый вход для приложений функций Azure с помощью функции проверки подлинности и авторизации в службе приложений.

- [Общие сведения о проверке подлинности и авторизации в функциях Azure](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#identity-providers)

- [Общие сведения об использовании единого входа в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: используйте многофакторную проверку подлинности для всех Azure Active Directoryного доступа

**Руководство**. Включение многофакторной проверки подлинности Azure Active Directory (AD) и следование рекомендациям по управлению удостоверениями и доступом в центре безопасности Azure.

- [Как включить MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Мониторинг удостоверений и доступа в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: используйте выделенные компьютеры (рабочие станции с привилегированным доступом) для всех административных задач

**Руководство**. Использование рабочих станций привилегированного доступа (привилегированным доступом) с настроенной многофакторной проверкой подлинности (MFA) для входа и настройки ресурсов Azure.

- [Дополнительные сведения о рабочих станциях с привилегированным доступом](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Как включить MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: регистрация и оповещение о подозрительных действиях из учетных записей администраторов

**Руководство**. Использование Azure Active Directory (AD) Управление привилегированными пользователями (PIM) для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде.

Кроме того, с помощью обнаружения рисков Azure AD можно просматривать предупреждения и отчеты о поведении опасного пользователя.

- [Развертывание управление привилегированными пользователями (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [Общие сведения об обнаружении рисков в Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: управление ресурсами Azure из только утвержденных расположений

**Руководство**. Используйте условный доступ с именованными расположениями, чтобы разрешить доступ к портал Azure только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

- [Настройка именованных расположений в Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="39-use-azure-active-directory"></a>3,9: используйте Azure Active Directory

**Руководство**. Использование Azure Active Directory (AD) в качестве централизованной системы проверки подлинности и авторизации для приложений функций Azure. Azure AD защищает данные с помощью надежного шифрования для неактивных и транзитных данных. Кроме того, в Azure AD имеются Salt-записи, хэши и безопасно хранят учетные данные пользователя.

- [Настройка приложения "функции Azure" для использования имени входа Azure AD](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Создание и настройка экземпляра AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: регулярно просматривайте и выверяйте доступ пользователей

**Руководство**. Azure Active Directory (AD) предоставляет журналы для упрощения обнаружения устаревших учетных записей. Кроме того, используйте проверку доступа удостоверений Azure, чтобы эффективно управлять членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователей можно проверить регулярно, чтобы убедиться, что доступ к ним имеют только нужные пользователи. 

- [Общие сведения об отчетах Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Использование проверок доступа для идентификации Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: монитор пытается получить доступ к отключенным учетным записям

**Руководство**. Использование Azure Active Directory (AD) в качестве централизованной системы проверки подлинности и авторизации для приложений-функций Azure. Azure AD защищает данные с помощью надежного шифрования для неактивных и транзитных данных. Кроме того, в Azure AD имеются Salt-записи, хэши и безопасно хранят учетные данные пользователя.

У вас есть доступ к источникам журнала событий входа в Azure AD, аудита и риску, которые позволяют интегрироваться с Azure Sentinel или сторонними SIEM.

Этот процесс можно упростить, создав параметры диагностики для учетных записей пользователей Azure AD и отправив журналы аудита и журналы входа в рабочую область Log Analytics. Вы можете настроить нужные оповещения журнала в Log Analytics.

- [Настройка приложения "функции Azure" для использования имени входа Azure AD](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Как интегрировать журналы действий Azure в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Как настроить метку Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: предупреждение об отклонении режима входа учетной записи

**Руководство**. Использование Azure Active Directory (AD) в качестве централизованной системы проверки подлинности и авторизации для приложений функций Azure. Для учета поведения учетной записи для входа в плоскость управления (портал Azure) используйте функции защиты идентификации Azure Active Directory (AD) и средства обнаружения рисков, чтобы настроить автоматические ответы на обнаруженные подозрительные действия, связанные с удостоверениями пользователей. Вы также можете принимать данные в метку Azure для дальнейшего изучения.

- [Как просмотреть рискованные входы в Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Настройка и включение политик риска для защиты идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Как подключить метку Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: предоставьте корпорации Майкрософт доступ к соответствующим пользовательским данным во время сценариев поддержки

**Руководство**: Сейчас недоступно; Защищенное хранилище в настоящее время не поддерживается для функций Azure.

- [Список поддерживаемых защищенное хранилище служб](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в разделе [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: поддержание инвентаризации конфиденциальной информации

**Руководство**. Использование тегов для отслеживания ресурсов Azure, в которых хранятся или обрабатываются конфиденциальные данные.

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Изолируйте системы, хранящие или обрабатывающие конфиденциальные данные

**Руководство**. Реализация отдельных подписок и групп управления для разработки, тестирования и производства. Приложения-функции Azure должны быть разделены виртуальной сетью (VNet)/субнет и помечены соответствующим образом.

Вы также можете использовать частные конечные точки для выполнения сетевой изоляции. Частная конечная точка Azure — это сетевой интерфейс, который обеспечивает безопасное и безопасное подключение к службе (например, конечная точка HTTPs приложения "функции Azure") на базе частной связи Azure. Частная конечная точка использует частный IP-адрес из виртуальной сети, фактически передавая ее в виртуальную сеть. Частные конечные точки находятся в (Предварительная версия) для приложений-функций, выполняющихся в плане Premium. Убедитесь, что частные конечные точки больше не находятся в (Предварительная версия), прежде чем использовать их с рабочими нагрузками.

- [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Создание Группы управления](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Azure Functions networking options](https://docs.microsoft.com/azure/azure-functions/functions-networking-options) (Параметры сети для Функций Azure)

- [План функций Azure Premium](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Общие сведения о частной конечной точке](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

- [Использование частных конечных точек для функций Azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Отслеживайте и блокируйте несанкционированную пересылку конфиденциальной информации

**Руководство**. Развертывание автоматизированного инструмента на периметре сети, который отслеживает несанкционированную передачу конфиденциальной информации и блокирует такие передачи при оповещении специалистов по информационной безопасности. 

Корпорация Майкрософт управляет базовой инфраструктурой для функций Azure и реализует четкие элементы управления для предотвращения потери или раскрытия данных клиента.

- [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: неприменимо

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: шифрование всех конфиденциальных данных во время передачи

**Руководство**. в портал Azure для приложений-функций Azure в разделе "функции платформы: Сетевые подключения: SSL" включите параметр "только HTTPS" и установите для минимальной версии TLS значение 1,2.

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: использование активного средства обнаружения для распознавания конфиденциальных данных

**Руководство**: Сейчас недоступно; функции предотвращения идентификации, классификации и защиты от потери данных в настоящее время недоступны для функций Azure. Пометьте приложения-функции, которые могут обрабатывать конфиденциальную информацию, и реализуют сторонние решения, если это необходимо для обеспечения соответствия.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и имеет большую длину для защиты от потери данных и доступности клиентов. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

- [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6. Использование Azure RBAC для управления доступом к ресурсам

**Руководство**. Использование управления доступом на основе ролей Azure Active Directory (AD) для управления доступом к плоскости управления функциями Azure (портал Azure). 

- [Настройка RBAC в Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами IaaS.

Корпорация Майкрософт управляет базовой инфраструктурой для функций Azure и реализует четкие элементы управления для предотвращения потери или раскрытия данных клиента.

- [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг центра безопасности Azure**: Сейчас недоступно

**Ответственность**: клиент

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: шифрование конфиденциальной информации при хранении

**Руководство**. при создании приложения-функции необходимо создать или связать учетную запись хранения Azure общего назначения, которая поддерживает хранилище BLOB-объектов, очередей и таблиц. Это связано с тем, что функции используют службу хранилища Azure для таких операций, как Управление триггерами и ведение журнала выполнения функций. Служба хранилища Azure шифрует все данные в неактивных учетных записях хранения. По умолчанию данные шифруются с помощью ключей, управляемых корпорацией Майкрософт. Для дополнительного управления ключами шифрования можно предоставить управляемые клиентом ключи для шифрования данных больших двоичных объектов и файлов. Эти ключи должны присутствовать в Azure Key Vault, чтобы приложение функции могло получить доступ к учетной записи хранения.

- [Общие сведения о вопросах хранения для функций Azure](https://docs.microsoft.com/azure/azure-functions/storage-considerations)

- [Общие сведения о шифровании службы хранилища Azure для неактивных данных](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: журналы и оповещения об изменениях критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналом действий Azure для создания оповещений о том, когда изменения выполняются в рабочих приложениях функций Azure, а также в других критических или связанных ресурсах.

- [Создание оповещений для событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="vulnerability-management"></a>управление уязвимостями;

*Дополнительные сведения см. в разделе [Security Control: управлением уязвимостей](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: Запуск автоматизированных средств проверки уязвимостей

**Руководство**. принятие девсекопсй практики, чтобы обеспечить безопасность приложений в функциях Azure и сохранить максимально безопасную безопасность на протяжении всего жизненного цикла. Девсекопс включает в себя группу безопасности Организации и их возможности в DevOpsных методиках, позволяющих всем членам группы обеспечивать безопасность.

Кроме того, следуйте рекомендациям из центра безопасности Azure, чтобы помочь в защите приложений функций Azure.

- [Как добавить постоянную проверку безопасности в конвейер CI/CD](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [Реализация рекомендаций по оценке уязвимостей в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5,2: Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Развертывание автоматизированного решения по управлению исправлениями сторонних производителей

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: сравнение проверок уязвимостей "назад-назад"

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5. Использование процесса оценки рисков для определения приоритета устранения обнаруженных уязвимостей

**Руководство**. Корпорация Майкрософт выполняет Управление уязвимостью в базовых системах, поддерживающих функции Azure, однако вы можете использовать уровень серьезности рекомендаций в центре безопасности Azure, а также оценить безопасность для оценки рисков в среде. Оценка безопасности основана на том, сколько рекомендаций центра безопасности устранено. Чтобы определить приоритеты для предварительного разрешения, рассмотрите уровень серьезности каждого из них.

- [Справочное руководство по вопросам безопасности](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Общая

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в разделе [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1. Использование обнаружения ресурсов Azure

**Руководство**. Использование графа ресурсов Azure для запроса или обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов и протоколов и т. д.) в рамках ваших подписок.  Обеспечьте соответствующие разрешения (чтение) в клиенте и перечислите все подписки Azure, а также ресурсы в ваших подписках.

Хотя классические ресурсы Azure могут быть обнаружены с помощью графа ресурсов, настоятельно рекомендуется создавать и использовать Azure Resource Manager ресурсы, идущие вперед.

- [Создание запросов с помощью графа ресурсов Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Просмотр подписок Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Общие сведения об Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="62-maintain-asset-metadata"></a>6,2: сохранение метаданных активов

**Руководство**. применение тегов к ресурсам Azure с помощью метаданных для логической организации их в таксономию.

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: удалите неавторизованные ресурсы Azure

**Руководство**. Использование тегов, групп управления и отдельных подписок (при необходимости) для Организации и мониторинга ресурсов Azure. Регулярно выверка инвентаризации и своевременное удаление неавторизованных ресурсов из подписки.

Кроме того, используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик: неразрешенные типы ресурсов, разрешенные типы ресурсов

- [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Создание Группы управления](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: ведение инвентаризации утвержденных ресурсов Azure и наименований программного обеспечения

**Руководство**. Определение утвержденных ресурсов Azure и утвержденного программного обеспечения для ресурсов вычислений.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: отслеживание неутвержденных ресурсов Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в ваших подписках. 

Используйте граф ресурсов Azure для запроса или обнаружения ресурсов в своих подписках.  Убедитесь, что все ресурсы Azure, представленные в окружении, утверждены. 

- [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Создание запросов с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="68-use-only-approved-applications"></a>6,8: Используйте только утвержденные приложения

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="69-use-only-approved-azure-services"></a>6,9: Используйте только утвержденные службы Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в подписках клиентов, с помощью следующих встроенных определений политик: недопустимые типы ресурсов, разрешенные типы ресурсов

- [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Как отказаться от определенного типа ресурса с помощью политики Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="610-implement-approved-application-list"></a>6,10: реализация списка утвержденных приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: ограничьте возможность пользователей взаимодействовать с диспетчером ресурсов Azure с помощью сценариев

**Руководство**. Настройка условного доступа Azure для ограничения возможности пользователей взаимодействовать с Azure Resource Manager путем настройки "блокировать доступ" для приложения "Управление Microsoft Azure".

- [Настройка условного доступа для блокировки доступа к Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: ограничьте возможность пользователей выполнять сценарии в ресурсах вычислений

**Руководство**: неприменимо; Эта рекомендация предназначена для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: физическое или логическое разделение приложений с высоким риском

**Руководство**. для обеспечения изоляции с учетом конфиденциальных или высокорисковных приложений-функций Azure реализуйте отдельные подписки и группы управления.

Развертывание приложений-функций Azure с высоким риском в своей виртуальной сети (VNet). Безопасность периметра в функциях Azure достигается через виртуальных сетей. Функции, выполняемые в плане Premium или Среда службы приложений (ASE), можно интегрировать с виртуальных сетей. Выберите оптимальную архитектуру для вашего варианта использования.

- [Azure Functions networking options](https://docs.microsoft.com/azure/azure-functions/functions-networking-options) (Параметры сети для Функций Azure)

- [План функций Azure Premium](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Рекомендации по работе с сетями в среде службы приложений](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Создание внешнего ASE](https://docs.microsoft.com/azure/app-service/environment/create-external-ase)

Как создать внутренний ASE:

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Создание NSG с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="secure-configuration"></a>Безопасная конфигурация

*Дополнительные сведения см. в разделе [Security Control: Secure Configuration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Установите безопасные конфигурации для всех ресурсов Azure.

**Руководство**. Определение и реализация стандартных конфигураций безопасности для приложения функции Azure с помощью политики Azure. Используйте псевдонимы политик Azure в пространстве имен Microsoft. Web, чтобы создать настраиваемые политики для аудита или принудительного применения конфигурации приложений функций Azure. Вы также можете использовать встроенные определения политик, такие как:
- В приложении-функции должно использоваться управляемое удостоверение.
- Удаленная отладка должна быть отключена для приложений-функций.
- Приложение-функция должно быть доступно только по HTTPS

- [Просмотр доступных псевдонимов политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Установка безопасных конфигураций операционной системы

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: поддержание защищенных конфигураций ресурсов Azure

**Руководство**. Использование политики Azure [Deny] и [развертывание если не существует] для обеспечения безопасности параметров в ресурсах Azure.

- [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Общие сведения о влиянии политики Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: поддержание защищенных конфигураций операционных систем

**Руководство**: неприменимо; Хотя можно развертывать локальные функции, это руководство предназначено для вычислений с ресурсами IaaS. При развертывании локальных функций вы несете ответственность за безопасную настройку среды.

- [Общие сведения о локальных функциях](https://docs.microsoft.com/azure/azure-functions/functions-runtime-install)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: безопасное хранение конфигурации ресурсов Azure

**Руководство**. безопасное хранение шаблонов ARM и пользовательских определений политик Azure в системе управления версиями.

- [Что такое инфраструктура как код](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)

- [Разработка политики как рабочих процессов кода](https://docs.microsoft.com/azure/governance/policy/concepts/policy-as-code)

- [Как сохранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Документация по Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: безопасное хранение пользовательских образов операционной системы

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: развертывание средств управления конфигурацией системы

**Руководство**. Используйте встроенные определения политик Azure, а также псевдонимы политик Azure в пространстве имен Microsoft. Web для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.

- [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией системы для операционных систем

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9. Реализация автоматического мониторинга конфигурации для служб Azure

**Руководство**. Используйте встроенные определения политик Azure, а также псевдонимы политик Azure в пространстве имен Microsoft. Web для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Используйте политику Azure [аудит], [запретить] и [развернуть, если не существует] для автоматического применения конфигураций для ресурсов Azure.

- [Настройка и управление политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10. Реализация автоматического мониторинга конфигурации для операционных систем

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами IaaS.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="711-manage-azure-secrets-securely"></a>7,11: безопасное управление секретами Azure

**Руководство**. Использование управляемых удостоверений в сочетании с Azure Key Vault для упрощения и безопасного управления секретами в облачных приложениях. Управляемые удостоверения позволяют приложению функции проходить проверку подлинности в любой службе, поддерживающей проверку подлинности Azure AD, включая Key Vault без каких бы то ни было учетных данных в коде.

- [Создание Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Использование управляемых удостоверений в Службе приложений и Функциях Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

- [Как обеспечить проверку подлинности Key Vault с помощью управляемого удостоверения](https://docs.microsoft.com/azure/key-vault/managed-identity)

- [Использование Key Vault ссылок для службы приложений и функций Azure](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: безопасно управляйте удостоверениями и автоматически

**Руководство**. Использование управляемых удостоверений для предоставления приложению функции Azure автоматически управляемого удостоверения в Azure AD. Управляемые удостоверения позволяют проходить проверку подлинности в любой службе, поддерживающей проверку подлинности Azure AD, включая Key Vault без каких бы то ни было учетных данных в коде.

- [Использование управляемых удостоверений в Службе приложений и Функциях Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: устранение непреднамеренной раскрытия учетных данных

**Руководство**. Реализация средства проверки учетных данных для указания учетных данных в коде. Средство проверки учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault. 

- [Настройка средства проверки учетных данных](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в разделе [Security Control: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Руководство**: неприменимо; Это руководство предназначено для вычислений с ресурсами IaaS.

Защита от вредоносных программ Майкрософт включена на базовом узле, который поддерживает службы Azure (например, функции Azure), но не выполняется в содержимом клиента.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Майкрософт

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся средами вычислений

**Руководство**: неприменимо; Эта рекомендация предназначена для нерасчетных ресурсов, предназначенных для хранения данных.


**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Убедитесь, что программное обеспечение и подписи для защиты от вредоносных программ обновлены

**Руководство**: неприменимо; Эта рекомендация предназначена для нерасчетных ресурсов, предназначенных для хранения данных.

Защита от вредоносных программ Майкрософт включена на базовом узле, который поддерживает службы Azure (например, функции Azure), но не выполняется в содержимом клиента.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: неприменимо

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в разделе [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Обеспечьте регулярную автоматическую архивацию

**Руководство**. Использование функции резервного копирования и восстановления для планирования регулярного резервного копирования приложения. Приложения-функции, выполняемые в плане Premium, имеют те же возможности размещения, что и веб приложения в службе приложений Azure, включая функцию резервного копирования и восстановления.

Кроме того, используйте решение системы управления версиями, например Azure Repos и Azure DevOps, для безопасного хранения кода и управления им. Azure DevOps Services использует многие функции службы хранилища Azure для обеспечения доступности данных в случае сбоя оборудования, нарушения работы службы или сбоя в регионе. Кроме того, Группа Azure DevOps соответствует процедурам защиты данных от случайного или вредоносного удаления.

- [Резервное копирование приложения в Azure](https://docs.microsoft.com/azure/app-service/manage-backup)

- [Сведения о доступности данных в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Как сохранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Документация по Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом.

**Руководство**. Использование функции резервного копирования и восстановления для планирования регулярного резервного копирования приложения. Приложения-функции, выполняемые в плане Premium, имеют те же возможности размещения, что и веб приложения в службе приложений Azure, включая функцию резервного копирования и восстановления. Резервное копирование управляемых пользователем ключей в Azure Key Vault.

Кроме того, используйте решение системы управления версиями, например Azure Repos и Azure DevOps, для безопасного хранения кода и управления им. Azure DevOps Services использует многие функции службы хранилища Azure для обеспечения доступности данных в случае сбоя оборудования, нарушения работы службы или сбоя в регионе. Кроме того, Группа Azure DevOps соответствует процедурам защиты данных от случайного или вредоносного удаления.

- [Резервное копирование приложения в Azure](https://docs.microsoft.com/azure/app-service/manage-backup)

- [Резервное копирование ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Сведения о доступности данных в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Как сохранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Документация по Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Руководство**. обеспечение возможности периодически выполнять восстановление с помощью функции резервного копирования и восстановления. Если вы используете другое автономное расположение для резервного копирования кода, периодически обеспечивайте возможность выполнения полного восстановления. Проверка восстановления резервных копий ключей, управляемых клиентом.

- [Восстановление приложения в Azure из резервной копии](https://docs.microsoft.com/azure/app-service/web-sites-restore)

- [Восстановление приложения в Azure из моментального снимка](https://docs.microsoft.com/azure/app-service/app-service-web-restore-snapshots)

- [Восстановление ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и управляемых клиентом ключей

**Руководство**. резервные копии из функции резервного копирования и восстановления используют учетную запись хранения Azure в подписке. Служба хранилища Azure шифрует все данные в неактивных учетных записях хранения. По умолчанию данные шифруются с помощью ключей, управляемых корпорацией Майкрософт. Для дополнительного управления ключами шифрования можно предоставить ключи, управляемые клиентом, для шифрования данных хранилища.

Если вы используете управляемые пользователем ключи, убедитесь, что в Key Vault включен параметр обратимого удаления для защиты ключей от случайного или вредоносного удаления.

- [Шифрование неактивных данных в службе хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [Включение обратимого удаления в Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Общая

## <a name="incident-response"></a>Реагирование на инциденты

*Дополнительные сведения см. в разделе [контроль безопасности: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: создайте руководство по реагированию на инциденты

**Руководство**. Создание руководства по реагированию на инциденты для вашей организации. Убедитесь, что имеются письменные планы реагирования на инциденты, которые определяют все роли персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.

- [Настройка автоматизации рабочих процессов в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

- [Руководство по созданию собственного процесса реагирования на инциденты безопасности](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Анатомия инцидента в центре Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Клиент также может воспользоваться руководством по обработке инцидентов безопасности компьютера NIST, чтобы помочь в создании собственного плана реагирования на инциденты.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: создание оценки инцидента и процедуры определения приоритетов

**Руководство**. Центр безопасности назначает серьезность каждому оповещению, чтобы вы могли определить, какие предупреждения следует изучить первыми. Серьезность основывается на том, насколько надежным является центр безопасности в поиске или аналитике, используемой для выдаче оповещения, а также об уровне достоверности, который был вредоносной задачей, вызвавшей оповещение.

Кроме того, четко помечать подписки (например, Рабочая, не производственная) и создайте систему именования, чтобы четко определить и классифицировать ресурсы Azure.

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: Общая

### <a name="103-test-security-response-procedures"></a>10,3: тестирование процедур реагирования на безопасность

**Руководство**. проведение упражнений по тестированию возможностей реагирования на инциденты систем на регулярной ритмичности. Выявление слабых точек и пробелов и пересмотр плана по мере необходимости.

- [Обратитесь к публикации NIST: руководство по тестированию, обучению и упражнениям для ИТ-планов и возможностей.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: укажите контактные данные инцидента безопасности и настройте уведомления о событиях для инцидентов безопасности.

**Руководство**. контактные данные инцидентов безопасности будут использоваться корпорацией Майкрософт для связи с вами, если центр Microsoft Security Response Center (MSRC) обнаруживает, что доступ к данным клиента был получен незаконные или неавторизованной стороной.  Проверьте инциденты после факта, чтобы убедиться, что проблемы устранены.

- [Как задать контакт безопасности центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Мониторинг центра безопасности Azure**: Да

**Ответственность**: клиент

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**. Экспорт оповещений и рекомендаций центра безопасности Azure с помощью функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Вы можете использовать соединитель данных центра безопасности Azure для потоковой передачи оповещений в Azure Sentinel.

- [Настройка непрерывного экспорта](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Потоковая передача предупреждений в Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Автоматизируйте ответ на оповещения системы безопасности

**Руководство**. Использование функции автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов на оповещения системы безопасности и рекомендации с Logic Apps.

- [Настройка автоматизации рабочего процесса и Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: клиент

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в разделе [контроль безопасности: тесты на проникновение и примеры для красных команд](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критических результатов безопасности.

**Руководство**. соблюдение правил корпорации Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт. Используйте стратегию и исполнение Microsoft, а затем протестировать проникновение в реальном времени для управляемой корпорацией Майкрософт облачной инфраструктуры, служб и приложений.

- [Правила тестирования уязвимости Задействований](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud красное объединение](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Общая

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со статьей о [производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Дополнительные сведения о [базовых показателях безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
