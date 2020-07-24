---
title: Базовый уровень безопасности Azure для масштабируемых наборов виртуальных машин
description: Базовый план безопасности масштабируемых наборов виртуальных машин содержит практические руководства и ресурсы для реализации рекомендаций по безопасности, указанных в статье о производительности системы безопасности Azure.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 6db83d17a9e6f3df6d691d6de616ead5407e3f5f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133050"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Базовый уровень безопасности Azure для масштабируемых наборов виртуальных машин

Базовый план безопасности Azure для масштабируемых наборов виртуальных машин содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовый план безопасности для этой службы взят из [Эталона безопасности Azure версии 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), содержащего рекомендации по обеспечению безопасности облачных решений в Azure с помощью наших практических рекомендаций.

Дополнительные сведения см. в статье [Обзор базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в статье [Управление безопасностью: безопасность сети](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: защита ресурсов Azure в виртуальных сетях

**Рекомендации**. при создании виртуальной машины Azure необходимо создать виртуальную сеть или использовать существующую виртуальную сеть, а также настроить виртуальную машину с подсетью. Убедитесь, что все развернутые подсети имеют группу безопасности сети, к которой относятся элементы управления доступом к сети, относящиеся к доверенным портам и источникам приложений.

Кроме того, при наличии определенного варианта использования централизованного брандмауэра можно также использовать брандмауэр Azure для удовлетворения этих требований.

* [Сеть для масштабируемых наборов виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [Создание виртуальной сети](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Создание группы безопасности сети с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Развертывание и настройка брандмауэра Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых интерфейсов

**Руководство**. Использование центра безопасности Azure для выявления и соблюдения рекомендаций по защите сети для защиты ресурсов виртуальной машины Azure в Azure. Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика виртуальных машин на необычные действия.

* [Как включить журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Общие сведения о безопасности сети, предоставляемой центром безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3. Защита критических веб-приложений

**Руководство**. при использовании масштабируемого набора виртуальных машин (VMSS) для размещения веб-приложений используйте группу безопасности сети (NSG) в ПОДсети VMSS, чтобы ограничить сетевой трафик, порты и протоколы, которым разрешено обмениваться данными. При настройке группы безопасности сети, чтобы разрешить только требуемый трафик в приложение, следует использовать сетевой подход с минимальными привилегиями.

Вы также можете развернуть брандмауэр веб-приложения Azure (WAF) перед критически важными веб-приложениями для дополнительной проверки входящего трафика. Включите параметр диагностики для WAF и приема журналов в учетную запись хранения, концентратор событий или рабочую область Log Analytics.

* [Сеть для масштабируемых наборов виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [созданию шлюза приложений с брандмауэром веб-приложения с помощью портала Azure](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-web-application-firewall-portal)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: запретите обмен данными с известными вредоносными IP-адресами

**Рекомендации**. Включите стандартную защиту "отказ в обслуживании" (от атак DDoS) в виртуальных сетях, чтобы защититься от атак от атак DDoS. С помощью интегрированной аналитики угроз центра безопасности Azure можно отслеживать взаимодействие с известными вредоносными IP-адресами. Настройка брандмауэра Azure для каждого сегмента виртуальной сети с включенной аналитикой угроз и настройкой "предупреждение и отклонение" для вредоносного сетевого трафика.

Вы можете использовать безопасный сетевой доступ центра безопасности Azure, чтобы ограничить раскрытие Виртуальные машины Windows с утвержденными IP-адресами в течение ограниченного периода времени. Кроме того, используйте адаптивную защиту сети в центре безопасности Azure, чтобы рекомендовать NSG конфигурации, ограничивающие порты и исходные IP-адреса на основе фактического трафика и аналитики угроз.

* [Настройка защиты от атак DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Развертывание брандмауэра Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Общие сведения об интегрированной аналитике угроз в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

* [Общие сведения о адаптивной защите сети в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

* [Общие сведения об управлении доступом к сети в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="15-record-network-packets"></a>1,5: запись сетевых пакетов

**Руководство**. Вы можете записать журналы потоков NSG в учетную запись хранения, чтобы создавать записи нефиксированного формата для виртуальных машин Azure. При исследовании аномальных действий можно включить запись пакетов наблюдателя за сетями, чтобы сетевой трафик можно было проверить на наличие необычных и непредвиденных действий.

* [Как включить журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Как включить Наблюдатель за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание системы обнаружения вторжений на основе сети и предотвращения вторжения (ИДЕНТИФИКАТОРы и IP-адреса)

**Рекомендации**. путем объединения захватов пакетов, предоставляемых наблюдателем за сетями, и средства идентификации с открытым кодом можно выполнять обнаружение вторжения в сети для широкого спектра угроз. Кроме того, вы можете развернуть брандмауэр Azure в сегментах виртуальной сети, если в нем включена и настроена система "предупреждения и отказ" для вредоносного сетевого трафика.

* [Обнаружение вторжения в сеть с помощью наблюдателя за сетями и средств с открытым кодом](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools)

* [Развертывание брандмауэра Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Настройка оповещений с помощью брандмауэра Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7. Управление трафиком к веб-приложениям

**Руководство**. при использовании масштабируемого набора виртуальных машин (VMSS) для размещения веб-приложений вы можете развернуть шлюз приложений Azure для веб-приложений с ВКЛЮЧЕНным протоколом HTTPS/SSL для доверенных сертификатов. С помощью шлюза приложений Azure вы направляете веб-трафик приложений в определенные ресурсы, назначая прослушивателям порты, создавая правила и добавляя ресурсы во внутренний пул, например VMSS и т. д.

* [Развертывание шлюза приложений](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [Настройка шлюза приложений для использования протокола HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [Создание масштабируемого набора, который ссылается на шлюз приложений](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

* [Общие сведения о балансировке нагрузки уровня 7 с помощью шлюзов веб-приложений Azure](https://docs.microsoft.com/azure/application-gateway/overview)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Уменьшение сложности и дополнительных затрат на администрирование в правилах безопасности сети

**Руководство**. Использование тегов службы виртуальной сети для определения элементов управления доступом к сети для групп безопасности сети или брандмауэра Azure, настроенных для виртуальных машин Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, ApiManagement) в соответствующем исходном поле или поле назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, входящих в тег службы, и автоматически обновляет этот тег при изменении адресов.

* [Общие сведения и использование тегов служб](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9. Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определение и реализация стандартных конфигураций безопасности для масштабируемых наборов виртуальных машин Azure с помощью политики Azure. Вы также можете использовать схемы Azure для упрощения крупномасштабных развертываний виртуальных машин Azure с помощью артефактов для ключевых сред, таких как шаблоны Azure Resource Manager, назначения ролей и назначения политик Azure, в одном определении схемы. Вы можете применить схему к подпискам и включить управление ресурсами с помощью управления версиями схем.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Подробнее о шаблонах масштабируемых наборов виртуальных машин](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-start)

* [Примеры политик Azure для работы в сети](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [Создание схемы Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**. Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10. Документация по правилам конфигурации трафика

**Руководство**. Вы можете использовать теги для групп безопасности сети (NSG) и другие ресурсы, связанные с сетевой безопасностью и потоком трафика, настроенным для виртуальных машин Windows. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и (или) длительность для правил, разрешающих трафик из сети в сеть.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Создание виртуальной сети](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Создание группы безопасности сети с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для отслеживания изменений в конфигурациях сетевых ресурсов, связанных с масштабируемым набором виртуальных машин Azure. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых параметров или ресурсов.

Используйте политику Azure для проверки (и/или исправьте) конфигурации сетевого ресурса, связанного с масштабируемым набором виртуальных машин.

* [Как просматривать и извлекать события журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Как создать оповещения в службе Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Примеры политик Azure для работы в сети](https://docs.microsoft.com/azure/governance/policy/samples/#network)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в статье [Управление безопасностью: ведение журналов и мониторинг](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1. Использование утвержденных источников синхронизации времени

**Руководство**. Майкрософт поддерживает источники времени для ресурсов Azure. Однако вы можете управлять параметрами синхронизации времени для виртуальных машин.

* [Настройка синхронизации времени для ресурсов Windows Azure для вычислений](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

* [Настройка синхронизации времени для ресурсов Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Совмещаемая блокировка

### <a name="22-configure-central-security-log-management"></a>2.2. Настройка централизованного управления журналами безопасности

**Руководство**. журналы действий можно использовать для аудита операций и действий, выполняемых в ресурсах масштабируемого набора виртуальных машин. Журнал действий содержит все операции записи (размещение, запись, удаление) для ресурсов, за исключением операций чтения (GET). Журналы действий можно использовать для поиска ошибок при устранении неполадок или для мониторинга того, как пользователь в вашей организации изменил ресурс.

Вы можете включить и встроенные данные журнала, полученные из журналов действий Azure или ресурсов виртуальной машины, в Azure Sentinel или сторонние SIEM для централизованного управления журналом безопасности.

Используйте центр безопасности Azure, чтобы обеспечить мониторинг журнала событий безопасности для виртуальных машин Azure. Учитывая объем данных, создаваемый журналом событий безопасности, он не сохраняется по умолчанию.

Если ваша организация хочет сохранить данные журнала событий безопасности с виртуальной машины, она может храниться в Log Analytics рабочей области на требуемом уровне сбора данных, настроенном в центре безопасности Azure.

* [Как получить журналы и метрики платформы с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Начало работы с Azure Monitor и интеграция SIEM стороннего производителя](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

* [Сбор данных в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Мониторинг виртуальных машин в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Включение журналов аудита для ресурсов Azure

**Руководство**. журналы действий можно использовать для аудита операций и действий, выполняемых в ресурсах масштабируемого набора виртуальных машин. Журнал действий содержит все операции записи (размещение, запись, удаление) для ресурсов, за исключением операций чтения (GET). Журналы действий можно использовать для поиска ошибок при устранении неполадок или для мониторинга того, как пользователь в вашей организации изменил ресурс.

Включите сбор диагностических данных гостевой ОС, развернув расширение диагностики на виртуальных машинах. С помощью расширения диагностики можно выполнять сбор диагностических данных, таких как журналы приложений или счетчики производительности, с виртуальной машины Azure.

Для расширенной видимости приложений и служб, поддерживаемых масштабируемым набором виртуальных машин Azure, можно включить как Azure Monitor для виртуальных машин, так и Application Insights. С помощью Application Insights можно отслеживать приложения и собирать данные телеметрии, такие как HTTP-запросы, исключения и т. д., что позволяет сопоставлять проблемы между виртуальными машинами и приложением.

* [Как получить журналы и метрики платформы с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Просмотр и извлечение событий из журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Мониторинг виртуальных машин в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [Общие сведения об Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Сбор журналов безопасности из операционных систем

**Руководство**. Использование центра безопасности Azure для обеспечения мониторинга журнала событий безопасности для виртуальных машин Azure. Учитывая объем данных, создаваемый журналом событий безопасности, он не сохраняется по умолчанию.

Если ваша организация хочет сохранить данные журнала событий безопасности с виртуальной машины, она может храниться в Log Analytics рабочей области на требуемом уровне сбора данных, настроенном в центре безопасности Azure.

* [Сбор данных в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Мониторинг виртуальных машин в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5. Настройка хранения журнала безопасности

**Руководство**. Убедитесь, что для всех учетных записей хранения log Analytics или рабочих областей, используемых для хранения журналов виртуальных машин, задан срок хранения журнала в соответствии с нормативными требованиями вашей организации.

* [Мониторинг виртуальных машин в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [Настройка срока хранения Log Analytics рабочей области](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: мониторинг и просмотр журналов

**Руководство**. анализ и мониторинг журналов для аномального поведения и регулярная проверка результатов. Используйте Azure Monitor для просмотра журналов и выполнения запросов к данным журнала.

Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM, чтобы отслеживать и просматривать журналы.

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Общие сведения о рабочей области Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Выполнение пользовательских запросов в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Включение оповещений для аномальных действий

**Руководство**. Использование центра безопасности Azure с log Analytics рабочей областью для мониторинга и оповещения о аномальных действиях, обнаруженных в журналах и событиях безопасности для виртуальных машин Azure.

Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM, чтобы настроить оповещения о аномальных действиях.

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Управление оповещениями в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Как оповещать данные журнала Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**. Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8. Централизованное ведение журнала защиты от вредоносных программ

**Руководство**. Вы можете использовать антивредоносное по Майкрософт для облачных служб и виртуальных машин Azure и настроить виртуальные машины Windows для записи событий в учетную запись хранения Azure. Настройте рабочую область Log Analytics для приема событий из учетных записей хранения и создавайте предупреждения там, где это необходимо. Следуйте рекомендациям в центре безопасности Azure: "вычисление &amp; приложений". Для виртуальных машин Linux вам потребуется средство стороннего производителя для обнаружения уязвимостей в борьбе с вредоносными программами.

* [Настройка защиты от вредоносных программ Майкрософт для облачных служб и виртуальных машин](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Включение мониторинга на уровне гостя для виртуальных машин](https://docs.microsoft.com/azure/cost-management/azure-vm-extended-metrics)

* [Инструкции для адаптации серверов Linux к центру безопасности Azure](https://docs.microsoft.com/azure/security-center/quick-onboard-linux-computer)

* [Ниже приведены рекомендуемые рекомендации корпорации Майкрософт по обеспечению безопасности, которые могут служить списком критериев для выбранного программного обеспечения уязвимости.](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9. Включение ведения журнала запросов DNS

**Руководство**. Внедрение решения стороннего производителя из Azure Marketplace для решения регистрации DNS в соответствии с вашими организациями.

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10. Включение ведения журнала аудита для командной строки

**Руководство**. Центр безопасности Azure обеспечивает мониторинг журнала событий безопасности для виртуальных машин Azure (ВМ). Центр безопасности подготавливает Microsoft Monitoring Agent на всех поддерживаемых виртуальных машинах Azure и на всех новых, созданных, если включена автоматическая подготовка или вы можете установить агент вручную. Агент включает событие создания процесса 4688 и поле CommandLine в событии 4688. Новые процессы, созданные на виртуальной машине, записываются в журнал событий и отслеживаются с помощью служб обнаружения в центре безопасности.

Для виртуальных машин Linux можно вручную настроить ведение журнала консоли для каждого узла и использовать syslog для хранения данных. Кроме того, используйте рабочую область Log Analytics Azure Monitor, чтобы просматривать журналы и выполнять запросы к данным syslog из виртуальных машин Azure.

* [Сбор данных в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Выполнение пользовательских запросов в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Источники данных системного журнала в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="identity-and-access-control"></a>Идентификаторы и управление доступом

*Дополнительные сведения см. в статье [Управление безопасностью: идентификаторы и управление доступом](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Инвентаризация учетных записей администраторов

**Рекомендации**. Хотя Azure Active Directory является рекомендуемым методом для администрирования доступа пользователей, виртуальные машины Azure могут иметь локальные учетные записи. Как локальные, так и доменные учетные записи должны проверяться и управляться обычным образом с минимальным объемом памяти. Кроме того, используйте управление привилегированными пользователями Azure для административных учетных записей, используемых для доступа к ресурсам виртуальных машин.

* [Сведения для локальных учетных записей доступны по адресу](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

* [Сведения о диспетчере привилегированных удостоверений](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2. Изменение паролей по умолчанию, где применимо

**Руководство**. масштабируемый набор виртуальных машин Azure и Azure Active Directory не имеют концепции паролей по умолчанию. Клиент несет ответственность за сторонние приложения и службы Marketplace, которые могут использовать пароли по умолчанию.

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3. Применение выделенных административных учетных записей

**Руководство**. Создание стандартных рабочих процедур, связанных с использованием выделенных административных учетных записей, имеющих доступ к виртуальным машинам. Используйте управление удостоверениями и доступом центра безопасности Azure для мониторинга количества административных учетных записей. Все учетные записи администратора, используемые для доступа к ресурсам виртуальной машины Azure, также можно управлять с помощью Azure управление привилегированными пользователями (PIM). Управление привилегированными пользователями Azure предоставляет несколько вариантов, таких как JIT-повышение прав, требование многофакторной проверки подлинности перед предположением роли, и параметры делегирования, чтобы разрешения были доступны только для определенных временных периодов и для которых требуется утверждающий.

* [Общие сведения об удостоверениях и доступе центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Сведения о диспетчере привилегированных удостоверений](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4. Использование единого входа Azure Active Directory (SSO)

**Рекомендации**. везде, где это возможно, используйте единый вход с Azure Active Directory вместо настройки отдельных автономных учетных данных для каждой службы. Используйте рекомендации по управлению удостоверениями и доступом в центре безопасности Azure.

* [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

* [Мониторинг идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: используйте многофакторную проверку подлинности для всех Azure Active Directory доступа

**Руководство**. Включите многофакторную проверку подлинности Azure AD и следуйте рекомендациям по управлению идентификацией и доступом в Центре безопасности Azure.

* [Включение MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Мониторинг идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: используйте защищенные рабочие станции, управляемые Azure, для административных задач

**Руководство**. Используйте рабочие станции привилегированного доступа (PAW) с настроенной многофакторной проверкой подлинности для входа в ресурсы Azure и их настройки.

* [Рабочие станции с привилегированным доступом](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Включение MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: журналы и оповещения о подозрительных действиях учетных записей администраторов

**Руководство**. Использование Azure AD PRIVILEGED Identity Management (PIM) для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде. Используйте обнаружение рисков Azure AD для просмотра предупреждений и отчетов об опасном поведении пользователя. При необходимости клиенты могут получать оповещения центра безопасности Azure в Azure Monitor и настраивать пользовательские оповещения и уведомления с помощью групп действий.

* [Развертывание Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Основные сведения об обнаружении рисков в центре безопасности Azure (подозрительные действия)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

* [Как интегрировать журналы действий Azure в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Настройка групп действий для настраиваемых оповещений и уведомлений](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8. Управление ресурсами Azure только из утвержденных расположений

**Рекомендации**. Используйте Azure Active Directory политики условного доступа и именованные расположения, чтобы разрешить доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

* [Настройка именованных расположений в Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="39-use-azure-active-directory"></a>3.9. Использование Azure Active Directory

**Руководство**. Использование Azure Active Directory (Azure AD) в качестве централизованной системы проверки подлинности и авторизации. Azure AD защищает данные с помощью надежного шифрования для хранимых и транзитных данных. Кроме того, в Azure AD используются salt-записи, хэши и безопасное хранение учетных данных пользователей. Управляемые удостоверения можно использовать для проверки подлинности в любой службе, поддерживающей проверку подлинности Azure AD, включая Key Vault без каких бы то ни было учетных данных в коде. Ваш код, выполняемый на виртуальной машине, может использовать управляемое удостоверение для запроса маркеров доступа для служб, поддерживающих проверку подлинности Azure AD.

* [Создание и настройка экземпляра Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

* [Обзор управляемых удостоверений для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10. Регулярная проверка и согласование доступа пользователей

**Руководство**. Azure AD предоставляет журналы для облегчения поиска устаревших учетных записей. Кроме того, используйте проверку доступа к удостоверениям Azure Active Directory для эффективного управления членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователя можно проверить регулярно, чтобы убедиться, что доступ к ним имеют только нужные пользователи. При использовании виртуальных машин Azure необходимо проверить локальные группы безопасности и пользователей, чтобы убедиться в отсутствии непредвиденных учетных записей, которые могут нарушить безопасность системы.

* [Использование проверок доступа для идентификации Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: монитор пытается получить доступ к отключенным учетным данным

**Руководство**. Настройка параметров диагностики для Azure Active Directory, чтобы отправить журналы аудита и журналы входа в рабочую область log Analytics. Кроме того, используйте Azure Monitor для просмотра журналов и выполнения запросов к данным журналов из виртуальных машин Azure.

* [Общие сведения о рабочей области Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Как интегрировать журналы действий Azure в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Выполнение пользовательских запросов в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Мониторинг виртуальных машин в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: предупреждение об отклонении при входе в учетную запись

**Руководство**. Использование функций защиты учетных записей и рисков Azure Active Directory для настройки автоматических ответов на обнаруженные подозрительные действия, связанные с вашими ресурсами. Вы должны включить автоматические ответы через метку Azure, чтобы реализовать ответы на безопасность вашей организации.

* [Просмотр рискованных входов в Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Как настроить и включить политики рисков с помощью защиты идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг Центра безопасности Azure**. Сейчас это недоступно.

**Ответственность**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13. Предоставление корпорации Майкрософт доступа к соответствующим данным клиентов в рамках сценариев поддержки

**Руководство**. в сценариях поддержки, когда корпорации Майкрософт требуется доступ к данным клиентов (например, во время запроса на поддержку), используйте защищенное хранилище для виртуальных машин Azure, чтобы просматривать и утверждать или отклонять запросы на доступ к данным клиентов.

* [Основные сведения о защищенное хранилище](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в статье [Управление безопасностью: защита данных](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Инвентаризация конфиденциальных данных

**Руководство**. Использование тегов для отслеживания виртуальных машин Azure, в которых хранятся или обрабатываются конфиденциальные данные.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2. Изолирование систем, хранящих или обрабатывающих конфиденциальные данные

**Руководство**. Реализуйте отдельные подписки и группы управления для разработки, тестирования и производства. Ресурсы должны быть разделены виртуальной сетью или подсетью, помечены соответствующим образом и защищены в группе безопасности сети (NSG) или брандмауэре Azure. Для виртуальных машин, которые хранят или обрабатывают конфиденциальные данные, реализуйте политику и процедуры, чтобы отключить их, когда они не используются.

* [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Создание виртуальной сети](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Создание группы безопасности сети с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Развертывание брандмауэра Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Как настроить оповещение или оповещение и запретить с помощью брандмауэра Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Рекомендации**. реализуйте сторонние решения на периметрах сети, которые отслеживают несанкционированную передачу конфиденциальной информации и блокируют такие передачи при оповещении специалистов по информационной безопасности.

Для базовой платформы, которая управляется корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным, чтобы защититься от потери данных и доступности клиентов. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Шифрование любой конфиденциальной информации при передаче

**Рекомендации**. Передача данных между виртуальными машинами и между ними (ВМ), работающими под Windows, шифруется несколькими способами в зависимости от характера подключения, например при подключении к виртуальной машине в сеансе RDP или SSH.

Корпорация Майкрософт использует протокол TLS для защиты данных при их командировке между облачными службами и клиентами.

* [Шифрование передаваемых данных в виртуальных машинах](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Совмещаемая блокировка

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Использование средства активного обнаружения для распознавания конфиденциальных данных

**Руководство**. Используйте средство активного обнаружения стороннего производителя для обнаружения всех конфиденциальных данных, хранимых, обрабатываемых или передаваемых технологическими системами Организации, включая те, которые находятся на сайте или на удаленном поставщике услуг, и обновляет данные инвентаризации Организации.

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6. Использование управления доступом на основе ролей для управления доступом к ресурсам

**Руководство**. Использование управления доступом на основе РОЛЕЙ (RBAC) позволяет разделить обязанности внутри группы и предоставить доступ к пользователям на виртуальной машине, которые им необходимы для выполнения своих задач. Вместо того чтобы предоставлять всем неограниченные разрешения для виртуальной машины, можно разрешить только определенные действия. Вы можете настроить контроль доступа для виртуальной машины в портал Azure с помощью Azure CLI или Azure PowerShell.

* [Управление доступом на основе ролей (RBAC) для ресурсов Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

* [Встроенные роли Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Рекомендации**. реализуйте сторонние средства, такие как автоматизированное решение для защиты от потери данных на узлах, чтобы обеспечить возможность управления доступом для снижения риска нарушения целостности данных.

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Шифрование конфиденциальной информации при хранении

**Руководство**. виртуальные диски на виртуальных машинах шифруются при помощи шифрования на стороне сервера или с помощью шифрования дисков Azure (ADE). Шифрование дисков Azure использует функцию DM-Encryption в Linux для шифрования управляемых дисков с помощью управляемых клиентом ключей на гостевой виртуальной машине. Шифрование на стороне сервера с использованием управляемых клиентом ключей улучшает работу шифрования дисков Azure, позволяя использовать любые типы ОС и образы для виртуальных машин путем шифрования данных в службе хранилища.

* [Шифрование дисков Azure для масштабируемых наборов виртуальных машин](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Включение в журнал и создание оповещений по изменениям критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналом действий Azure для создания оповещений о том, когда изменения выполняются в масштабируемых наборах виртуальных машин и связанных ресурсах.

* [Создание оповещений для событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Ведение журнала Аналитики Службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="vulnerability-management"></a>управление уязвимостями;

*Дополнительные сведения см. в статье [Управление безопасностью: управление уязвимостями](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Выполнение автоматизированных средства анализа уязвимостей

**Рекомендации**. Следуйте рекомендациям центра безопасности Azure по выполнению оценки уязвимостей на виртуальных машинах Azure. Используйте рекомендованное или стороннее решение для проведения оценки уязвимостей для виртуальных машин.

* [Реализация рекомендаций по оценке уязвимостей в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Развертывание автоматизированного решения для управления исправлениями операционной системы

**Руководство**. Включение автоматического обновления ОС для поддерживаемых версий операционной системы или пользовательских образов, хранящихся в общей коллекции образов.

* [Автоматическое обновление ОС для масштабируемых наборов виртуальных машин в Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**: Customer

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Развертывание автоматизированного решения по управлению исправлениями для программ сторонних разработчиков

**Руководство**. масштабируемые наборы виртуальных машин Azure (VMSS) могут использовать автоматическое обновление образа ОС. Вы можете использовать расширение Azure Desired State Configuration (DSC) для базовых виртуальных машин в VMSS. DSC используется для настройки виртуальных машин в сети, чтобы они выполняли необходимое программное обеспечение.

* [Использование наборов масштабирования виртуальных машин с помощью расширения Azure DSC](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-dsc)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4. Сравнение проверок смежных уязвимостей

**Руководство**. Экспорт результатов сканирования с одинаковыми интервалами и сравнение результатов для проверки того, что уязвимости исправлены. При использовании рекомендаций по управлению уязвимостью, предлагаемых центром безопасности Azure, клиент может выполнить сведение на портал выбранного решения для просмотра исторических данных сканирования.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Использование процесса оценки рисков для определения приоритета в устранении обнаруженных уязвимостей

**Руководство**. Используйте оценки рисков по умолчанию (Оценка безопасности), предоставляемые центром безопасности Azure.

* [Оценка безопасности центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в статье [Управление безопасностью: инвентаризация и управление ресурсами](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1. Использование автоматизированного решения для обнаружения ресурсов

**Руководство**. Использование графа ресурсов Azure для запроса и обнаружения всех ресурсов (включая виртуальные машины) в подписках. Убедитесь, что у вас есть соответствующие разрешения (на чтение) в клиенте и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

* [Как создавать запросы с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Как просматривать подписки Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Общие сведения об Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="62-maintain-asset-metadata"></a>6.2. Ведение метаданных активов

**Руководство**. применение тегов к ресурсам Azure с помощью метаданных для логической организации их в соответствии с классификацией.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Удаление неавторизованных ресурсов Azure

**Руководство**. Использование тегов, групп управления и отдельных подписок (при необходимости) для Организации и мониторинга масштабируемых наборов и связанных ресурсов виртуальных машин. Регулярно сверяйте ресурсы, чтобы своевременно удалять неавторизованные ресурсы из подписки.

* [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: определение и обслуживание инвентаризации утвержденных ресурсов Azure

**Руководство**. Создание инвентаризации утвержденных ресурсов Azure и утвержденного программного обеспечения для ресурсов вычислений в соответствии с потребностями Организации.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5. Отслеживание неутвержденных ресурсов Azure

**Руководство**. Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик.
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

Кроме того, используйте Azure Resource Graph для запроса или обнаружения ресурсов в подписках. Это может помочь в средах с высоким уровнем безопасности, например с учетными записями хранения.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Как создавать запросы с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6. Отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Руководство**. служба автоматизации Azure обеспечивает полный контроль во время развертывания, эксплуатации и списания рабочих нагрузок и ресурсов. Используйте инвентаризацию виртуальных машин Azure, чтобы автоматизировать сбор сведений обо всех программах на виртуальных машинах. Примечание. имя программного обеспечения, версия, издатель и время обновления доступны в портал Azure. Чтобы получить доступ к дате установки и другим сведениям, клиент должен включить диагностику на уровне гостя и перенести журналы событий Windows в рабочую область Log Analytics.

В настоящее время адаптивные элементы управления приложениями недоступны для масштабируемых наборов виртуальных машин.

* [Общие сведения о службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-intro)

* [Как включить инвентаризацию виртуальных машин Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**. служба автоматизации Azure обеспечивает полный контроль во время развертывания, эксплуатации и списания рабочих нагрузок и ресурсов. Вы можете использовать Отслеживание изменений, чтобы выбрать все программное обеспечение, установленное на виртуальных машинах. Вы можете реализовать собственный процесс или использовать конфигурацию состояния службы автоматизации Azure для удаления неавторизованного программного обеспечения.

* [Общие сведения о службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-intro)

* [Отслеживание изменений в среде с помощью решения для отслеживания изменений](https://docs.microsoft.com/azure/automation/change-tracking)

* [Обзор настройки состояния службы автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="68-use-only-approved-applications"></a>6.8. Использование только утвержденных приложений

**Руководство**. в настоящее время адаптивные элементы управления приложениями недоступны для масштабируемых наборов виртуальных машин. Используйте стороннее программное обеспечение для управления использованием только утвержденных приложений.

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9. Использование только утвержденных служб Azure

**Руководство**. Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик.
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Как отказаться от определенного типа ресурса с помощью Политики Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: ведение инвентаризации утвержденных наименований программного обеспечения

**Руководство**. в настоящее время адаптивные элементы управления приложениями недоступны для масштабируемых наборов виртуальных машин. Реализуйте сторонние решения, если это не соответствует требованиям вашей организации.

* [Как использовать адаптивные элементы управления приложениями в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: ограничьте возможность пользователей работать с Azure Resource Manager

**Руководство**. Используйте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "Блокировать доступ" для приложения "Управление Microsoft Azure".

* [Как настроить условный доступ для блокировки доступа к Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12. Ограничение возможности пользователей выполнять сценарии в ресурсах вычислений

**Рекомендации**. в зависимости от типа сценариев можно использовать конфигурации операционной системы или сторонние ресурсы, чтобы ограничить возможность пользователей выполнять сценарии в ресурсах вычислений Azure.

* [Управление выполнением скриптов PowerShell в средах Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13. Физическое или логическое разделение приложений с высоким риском

**Руководство**. приложения с высоким риском, развернутые в среде Azure, могут быть изолированы с помощью виртуальной сети, подсети, подписок, групп управления и т. д., а также с помощью брандмауэра Azure, брандмауэра веб-приложения (WAF) или группы безопасности сети (NSG).

* [Виртуальные сети и виртуальные машины в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [Общие сведения о Брандмауэре Azure](https://docs.microsoft.com/azure/firewall/overview)

* [Обзор брандмауэра веб-приложения](https://docs.microsoft.com/azure/web-application-firewall/overview)

* [Общие сведения о защите сети](https://docs.microsoft.com/azure/virtual-network/security-overview)

* [Обзор виртуальной сети Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

* [Упорядочение ресурсов с помощью групп управления Azure](https://docs.microsoft.com/azure/governance/management-groups/overview)

* [Руководство по выбору модели подписки](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="secure-configuration"></a>Безопасная конфигурация

*Дополнительные сведения см. в статье [Управление безопасностью: безопасная конфигурация](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Установка безопасных конфигураций для всех ресурсов Azure

**Руководство**. Использование политики Azure или центра безопасности Azure для поддержки конфигураций безопасности для всех ресурсов Azure. Кроме того, Azure Resource Manager может экспортировать шаблон в нотация объектов JavaScript (JSON), который следует проверить, чтобы убедиться, что конфигурации соответствуют требованиям безопасности компании или превышают их.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Сведения о том, как скачать шаблон виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Сохранение безопасных конфигураций для операционных систем

**Руководство**. использование рекомендаций центра безопасности Azure [Исправление уязвимостей в конфигурациях безопасности на виртуальных машинах] для поддержки конфигураций безопасности во всех ресурсах вычислений.

* [Как отслеживать рекомендации центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [Как исправлять рекомендации центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3. Сохранение безопасных конфигураций для ресурсов Azure

**Руководство**. использование шаблонов Azure Resource Manager и политик Azure для безопасной настройки ресурсов Azure, связанных с масштабируемыми наборами виртуальных машин. Azure Resource Manager шаблоны — это файлы на основе JSON, которые используются для развертывания виртуальной машины вместе с ресурсами Azure, и настраиваемый шаблон необходимо поддерживать. Корпорация Майкрософт выполняет обслуживание базовых шаблонов. Используйте политику Azure [отказывать] и [развернуть, если не существует], чтобы обеспечить безопасность параметров в ресурсах Azure.

* [Сведения о создании шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Действия политик Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4. Сохранение безопасных конфигураций для операционных систем

**Руководство**. Существует несколько вариантов обеспечения безопасной конфигурации для виртуальных машин Azure (ВМ) для развертывания.

1. Azure Resource Manager шаблоны: это файлы на основе JSON, используемые для развертывания виртуальной машины из портал Azure, а пользовательский шаблон должен поддерживаться. Корпорация Майкрософт выполняет обслуживание базовых шаблонов.

2. Пользовательский виртуальный жесткий диск (VHD). в некоторых случаях может потребоваться использовать пользовательские VHD-файлы, например, при работе со сложными средами, которыми нельзя управлять с помощью других средств.

3. Конфигурация состояния службы автоматизации Azure. После развертывания базовой ОС ее можно использовать для более детального управления параметрами и принудительного применения с помощью платформы автоматизации.

В большинстве случаев шаблоны базовых виртуальных машин Майкрософт, Объединенные с конфигурацией требуемого состояния службы автоматизации Azure, могут помочь в собрании и обслуживании требований безопасности.

* [Сведения о том, как скачать шаблон виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

* [Сведения о создании шаблонов ARM](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Как отправить пользовательский виртуальный жесткий диск виртуальной машины в Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Совмещаемая блокировка

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5. Безопасное хранение конфигурации ресурсов Azure

**Руководство**. Использование Azure DevOps для безопасного хранения и управления кодом, например пользовательскими политиками Azure, Azure Resource Manager шаблонами, сценариями настройки требуемого состояния и т. д.  Для доступа к ресурсам, управляемым в Azure DevOps, таким как код, сборки и отслеживание работы, необходимо иметь разрешения для этих ресурсов. Большинство разрешений предоставляются через встроенные группы безопасности, как описано в разделе разрешения и доступ. Вы можете предоставить или отменить разрешения для определенных пользователей, встроенных групп безопасности или групп, определенных в Azure Active Directory (Azure AD), если они интегрированы с Azure DevOps или Active Directory, если они интегрированы с TFS.

* [Как хранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [О разрешениях и группах в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6. Безопасное хранение пользовательских образов операционной системы

**Руководство**. при использовании пользовательских образов (например, виртуального жесткого диска) используйте элементы управления доступом на основе ролей Azure, чтобы обеспечить доступ к образам только полномочным пользователям.

* [Общие сведения о RBAC в Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

* [Настройка RBAC в Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: развертывание средств управления конфигурацией для ресурсов Azure

**Руководство**. Использование политики Azure для оповещения, аудита и принудительного применения конфигураций системы для виртуальных машин. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией для операционных систем

**Руководство**. Настройка состояния службы автоматизации Azure — это служба управления конфигурацией для узлов DSC в любом облачном или локальном центре обработки данных. Она обеспечивает быструю и простую масштабируемость тысяч компьютеров из безопасного центрального расположения. Вы можете легко переносить компьютеры в облачную среду, присваивать им декларативные конфигурации, а также просматривать отчеты, отражающие соответствие каждого компьютера требуемому состоянию.

* [Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9. Реализация автоматического мониторинга конфигурации для ресурсов Azure

**Руководство**. Использование центра безопасности Azure для выполнения проверок базовых показателей для виртуальных машин Azure. Дополнительные методы автоматической настройки включают использование конфигурации состояния службы автоматизации Azure.

* [Как исправить рекомендации в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

* [Начало работы со службой "Настройка состояния службы автоматизации Azure"](https://docs.microsoft.com/azure/automation/automation-dsc-getting-started)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10. Реализация автоматизированного мониторинга конфигурации для операционных систем

**Руководство**. Настройка состояния службы автоматизации Azure — это служба управления конфигурацией для узлов DSC в любом облачном или локальном центре обработки данных. Она обеспечивает быструю и простую масштабируемость тысяч компьютеров из безопасного центрального расположения. Вы можете легко переносить компьютеры в облачную среду, присваивать им декларативные конфигурации, а также просматривать отчеты, отражающие соответствие каждого компьютера требуемому состоянию.

* [Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11. Безопасное управление секретами Azure

**Рекомендации**. Используйте управляемое удостоверение службы в сочетании с Azure Key Vault, чтобы упростить и защитить управление секретами для облачных приложений.

* [Интеграция с управляемыми удостоверениями Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [Создание Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [Как обеспечить проверку подлинности Key Vault с помощью управляемого удостоверения](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12. Безопасное и автоматическое управление удостоверениями

**Руководство**. Использование управляемых удостоверений для предоставления служб Azure с автоматически управляемым удостоверением в Azure AD. Управляемое удостоверение можно использовать для проверки подлинности в любой службе, которая поддерживает проверку подлинности Azure AD, включая Key Vault, при этом не сохраняя каких-либо учетных данных в коде.

* [Настройка управляемых удостоверений](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13. Устранение непреднамеренного раскрытия учетных данных

**Руководство**. Реализуйте сканер учетных данных для обнаружения учетных данных в коде. Сканер учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault.

* [Как настроить сканер учетных данных](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в статье [Управление безопасностью: защита от вредоносных программ](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Руководство**. Использование антивредоносного по Майкрософт для виртуальных машин Windows Azure для непрерывного мониторинга и защиты ресурсов. Для защиты от вредоносных программ на виртуальной машине Azure Linux вам потребуется стороннее средство.

* [Настройка антивредоносного по Майкрософт для облачных служб и виртуальных машин](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2. Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся вычислительными

**Руководство**. неприменимо к виртуальным машинам Azure, так как это ресурс вычислений.

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Неприменимо

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3. Своевременное обновление программного обеспечения для защиты от вредоносных программ и подписей

**Рекомендации**. при развертывании для виртуальных машин Windows антивредоносное по Майкрософт для Azure автоматически устанавливает последние обновления сигнатур, платформы и подсистемы по умолчанию. Следуйте рекомендациям в центре безопасности Azure: "вычисление &amp; приложений", чтобы обеспечить актуальность всех конечных точек с последними сигнатурами. ОС Windows может быть дополнительно защищена с помощью дополнительной защиты, чтобы ограничить риск атак на антивирусную или вредоносную программу с помощью службы Microsoft Защитник Advanced Threat Protection, которая интегрируется с центром безопасности Azure.

Для защиты от вредоносных программ на виртуальной машине Azure Linux вам потребуется стороннее средство.

* [Развертывание антивредоносного по Майкрософт для облачных служб и виртуальных машин Azure](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Advanced Threat Protection в Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

* [Настройка антивредоносного по Майкрософт для облачных служб и виртуальных машин](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в статье [Управление безопасностью: восстановление данных](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Обеспечьте регулярное автоматическое резервное копирование

**Руководство**. Создание моментального снимка экземпляра масштабируемого набора виртуальных машин Azure или управляемого диска, подключенного к экземпляру с помощью PowerShell или API-интерфейсов RESTful. Вы также можете использовать службу автоматизации Azure для выполнения сценариев резервного копирования через регулярные интервалы.

* [Создание моментального снимка экземпляра масштабируемого набора виртуальных машин и управляемого диска](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Введение в службу автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-intro)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом

**Руководство**. создание моментальных снимков виртуальных машин Azure или управляемых дисков, подключенных к этим экземплярам, с помощью PowerShell или интерфейсов API. Создайте резервную копию ключей, управляемых клиентом, в Azure Key Vault.

Включите Azure Backup и целевые виртуальные машины Azure, а также желаемую частоту и сроки хранения. Сюда входит полная архивация состояния системы. При использовании шифрования дисков Azure служба архивации виртуальных машин Azure автоматически обрабатывает резервные копии ключей, управляемых клиентом.

* [Резервное копирование на виртуальных машинах Azure, использующих шифрование](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [Общие сведения о резервном копировании виртуальных машин Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [Создание моментального снимка экземпляра масштабируемого набора виртуальных машин и управляемого диска](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Как создать резервную копию ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Руководство**. обеспечение возможности периодически выполнять восстановление данных управляемого диска в Azure Backup. При необходимости проверьте содержимое восстановления в изолированной виртуальной сети или подписке. Клиент для проверки восстановления резервных копий ключей, управляемых клиентом.

При использовании шифрования дисков Azure можно восстановить масштабируемые наборы виртуальных машин с помощью ключей шифрования дисков. При использовании шифрования дисков виртуальную машину Azure можно восстановить с помощью ключей шифрования дисков.

* [Резервное копирование на виртуальных машинах Azure, использующих шифрование](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [Восстановление диска и создание восстановленной виртуальной машины в Azure](https://docs.microsoft.com/azure/backup/tutorial-restore-disk)

* [Как восстановить резервную копию ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Как включить шифрование дисков для масштабируемых наборов виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и ключей, управляемых клиентом

**Руководство**. Включение защиты от удаления для управляемого диска с помощью блокировок. Включите обратимое удаление и очистку защиты в Key Vault, чтобы защитить ключи от случайного или вредоносного удаления.

* [Блокировка ресурсов для предотвращения непредвиденных изменений](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)

* [Обзор защиты Azure Key Vault обратимого удаления и очистки](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

## <a name="incident-response"></a>Реагирование на инциденты

*Дополнительные сведения см. в статье [Управление безопасностью: реагирование на инциденты](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Создание руководства по реагированию на инциденты

**Руководство**. Создайте руководство по реагированию на инциденты для вашей организации. Убедитесь в том, что имеются письменные планы реагирования на инциденты, которые определяют все действия персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.

* [Руководство по созданию собственного процесса реагирования на инциденты безопасности](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Анатомия инцидента Центра Майкрософт по реагированию на угрозы](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Использование руководства по обработке инцидентов безопасности компьютера от NIST для создания собственного плана реагирования на инциденты](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Создание процедуры оценки инцидента и определения приоритетов

**Руководство**. Центр безопасности назначает каждому оповещению серьезность, которая поможет определить, какие предупреждения следует расследовать первыми. Серьезность основывается на том, насколько уверен Центр безопасности в исследовании или аналитике, используемой для оповещения, а также на уровне достоверности злонамеренности события, приведшего к оповещению.

Кроме того, четко помечайте подписки тегами (например, рабочие, нерабочие) и создайте систему именования, чтобы четко определить и классифицировать ресурсы Azure, особенно обрабатывающие конфиденциальные данные. Вы несете ответственность за назначение приоритета оповещениям, требующим действий по исправлению, в зависимости от важности ресурсов Azure и среды, в которой произошел инцидент.

* [Оповещения безопасности в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [использование тегов для упорядочения ресурсов в Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="103-test-security-response-procedures"></a>10.3. Проверка процедур реагирования на угрозы

**Руководство**. Выполняйте упражнения, чтобы периодически тестировать возможности ваших систем реагировать на угрозы для защиты ресурсов Azure. Выявите слабые точки и пробелов и пересмотрите план по мере необходимости.

* [Публикация NIST — руководство по тестированию, обучению и выполнению программ для ИТ-планов и возможностей](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Предоставление контактных сведений и настройка уведомлений по инцидентам безопасности

**Руководство**. Корпорация Майкрософт будет использовать информацию об инциденте безопасности для связи с вами, если центр Microsoft Security Response Center (MSRC) обнаружит, что к вашим данным был получен незаконный или несанкционированный доступ. Проверьте инциденты после факта обращения, чтобы убедиться в том, что проблемы устранены.

* [Как задать контакт безопасности Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**. Экспортируйте оповещения и рекомендации Центра безопасности Azure с помощью функции непрерывного экспорта с целью выявления рисков для ресурсов Azure. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Вы можете использовать соединитель данных Центра безопасности Azure для потоковой передачи оповещений в Azure Sentinel.

* [Настройка непрерывного экспорта данных](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Как выполнить потоковую передачу оповещений в Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6. Автоматизация реагирования на оповещения системы безопасности

**Руководство**. Использование функции автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" в оповещениях системы безопасности и рекомендациях по защите ресурсов Azure.

* [Как настроить автоматизацию рабочего процесса и Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**. Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в статье [Управление безопасностью: тесты на проникновение и попытки нарушения безопасности "красной командой"](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критических результатов безопасности.

**Руководство**. соблюдение правил корпорации Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт. Используйте стратегию и исполнение Microsoft, а затем протестировать проникновение в реальном времени для управляемой корпорацией Майкрософт облачной инфраструктуры, служб и приложений.

* [Правила тестирования уязвимости Задействований](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud красное объединение](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Совмещаемая блокировка

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со статьей [Тесты производительности системы безопасности Azure](/azure/security/benchmarks/overview).
- Дополнительные сведения о [базовой конфигурации безопасности Azure](/azure/security/benchmarks/security-baselines-overview).
