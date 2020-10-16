---
title: Базовый план безопасности Azure для фабрики данных Azure
description: Базовый план безопасности Azure для фабрики данных Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 515cfd5267917f88131571adcb1bea0db274157c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89437944"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>Базовый план безопасности Azure для фабрики данных Azure

Базовый план безопасности Azure для фабрики данных Azure содержит рекомендации, которые помогут повысить уровень безопасности развертывания.

Базовый план безопасности для этой службы взят из [Эталона безопасности Azure версии 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), содержащего рекомендации по обеспечению безопасности облачных решений в Azure с помощью наших практических рекомендаций.

Дополнительные сведения см. в статье [Базовые показатели безопасности для Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в статье [Управление безопасностью: безопасность сети](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: защита ресурсов Azure в виртуальных сетях

**Руководство**. при создании Azure-SSIS Integration Runtime (IR) вы можете присоединить его к виртуальной сети. Это позволит фабрике данных Azure создавать определенные сетевые ресурсы, например группу безопасности сети (NSG) и подсистему балансировки нагрузки. Вы также можете предоставить собственный статический общедоступный IP-адрес или настроить фабрику данных Azure для вас. В NSG, автоматически созданной фабрикой данных Azure, порт 3389 по умолчанию открыт для всего трафика. Заблокируйте это, чтобы обеспечить доступ только администраторам.

Self-Hosted IRs можно развернуть на локальном компьютере или в виртуальной машине Azure внутри виртуальной сети. Убедитесь, что для развертывания подсети виртуальной сети настроено NSG, разрешающее только административный доступ. Для защиты Azure-SSIS IR в правиле брандмауэра Windows на каждом IR-узле по умолчанию разрешается исходящие порты 3389. Вы можете защитить ресурсы, настроенные виртуальной сетью, путем связывания NSG с подсетью и задания правил с определенными правилами.

Где доступна частная ссылка, используйте частные конечные точки для защиты любых ресурсов, связанных с конвейером фабрики данных Azure, например Azure SQL Server. Благодаря частному каналу трафик между виртуальной сетью и службой проходит через магистральную сеть Майкрософт, что устраняет уязвимость в общедоступном Интернете.

* [Создание Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

* [Создание и Настройка локальной среды IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

* [Создание виртуальной сети](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Создание NSG с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Присоединение Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network#virtual-network-configuration)

* [Общие сведения о частной ссылке Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Используйте центр безопасности Azure и исправьте рекомендации по защите сети для виртуальной сети и группы безопасности сети, связанной с развертыванием Integration Runtime.

Кроме того, включите журналы потоков группы безопасности сети (NSG) для NSG, защищающего развертывание Integration Runtime, и отправьте журналы в учетную запись хранения Azure для аудита трафика.

Вы также можете отправить журналы потоков для группы безопасности сети в рабочую область Log Analytics и использовать аналитику трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитики трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять некорректные сетевые настройки.

* [Общие сведения о безопасности сети, предоставляемой центром безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Как включить журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Общие сведения о безопасности сети, предоставляемой центром безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Как включать и использовать Аналитику трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3. Защита критических веб-приложений

**Руководство**: неприменимо; Эта рекомендация предназначена для служб приложений Azure или ресурсов вычислений, в которых размещены веб-приложения.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4. Запрет взаимодействия с известными опасными IP-адресами

**Руководство**. Включение стандарта защиты от атак DDoS в виртуальных сетях, связанных с развертыванием Integration Runtime, для защиты от атак типа "отказ в обслуживании". Используйте интегрированную аналитику угроз Центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми IP-адресами Интернета.

* [Настройка защиты от атак DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Общие сведения об интегрированной аналитике угроз в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="15-record-network-packets"></a>1,5: запись сетевых пакетов

**Руководство**. Включение журналов потоков для группы безопасности сети (NSG) для NSG, защищающих развертывание Integration Runtime, и отправка журналов в учетную запись хранения Azure для аудита трафика.

Вы также можете отправить журналы потоков для группы безопасности сети в рабочую область Log Analytics и использовать аналитику трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитики трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять некорректные сетевые настройки.

* [Как включить журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Общие сведения о безопасности сети, предоставляемой центром безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Как включать и использовать Аналитику трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание системы обнаружения вторжений на основе сети и предотвращения вторжения (ИДЕНТИФИКАТОРы и IP-адреса)

**Рекомендации**. Если вы хотите проверить исходящий трафик от Azure-SSIS IR, можно направить трафик, инициированный из Azure-SSIS IR, в локальное устройство брандмауэра с помощью Azure ExpressRoute Force Tunneling или сетевого виртуального модуля (NVA) из Azure Marketplace, поддерживающего возможности идентификации и IP-адресов. Если система обнаружения или предотвращения вторжений на основе проверки атакующего кода не является обязательной, можно использовать Брандмауэр Azure с аналитикой угроз.

* [Присоединение Azure-SSIS Integration Runtime к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Развертывание брандмауэра Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Настройка оповещений с помощью брандмауэра Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7. Управление трафиком к веб-приложениям

**Руководство**: неприменимо; Эта рекомендация предназначена для служб приложений Azure или ресурсов вычислений, в которых размещены веб-приложения.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Уменьшение сложности и дополнительных затрат на администрирование в правилах безопасности сети

**Руководство**. Использование тегов службы виртуальной сети для определения элементов управления доступом к сети в группе безопасности сети (NSG) или в брандмауэре Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, Датафакториманажемент) в соответствующем поле источника или назначения правила, можно разрешить или запретить входящий трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, входящих в тег службы, и автоматически обновляет этот тег при изменении адресов.

* [Общие сведения и использование тегов служб](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Общие сведения о тегах служб, связанных с фабрикой данных Azure](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9. Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определение и реализация стандартных конфигураций безопасности для сетевых параметров и сетевых ресурсов, связанных с экземплярами фабрики данных Azure, с помощью политики Azure. Используйте псевдонимы политик Azure в пространствах имен Microsoft. данных и "Microsoft. Network" для создания настраиваемых политик для аудита или принудительного применения конфигурации сети для экземпляров фабрики данных Azure. Вы также можете использовать встроенные определения политик, связанные с сетью или экземплярами фабрики данных Azure, например:
- Стандарт Защиты от атак DDoS должен быть включен.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Примеры политик Azure для работы в сети](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [Создание схемы Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10. Документация по правилам конфигурации трафика

**Руководство**. Использование тегов для ресурсов, относящихся к сетевой безопасности и потоку трафика для экземпляров фабрики данных Azure для предоставления метаданных и логической организации.

Используйте любые встроенные определения политик Azure, связанные с разметкой, например "требовать тег и его значение", чтобы обеспечить создание всех ресурсов с помощью тегов и уведомления о существующих непомеченных ресурсах.

Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий с ресурсами на основе их тегов.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для мониторинга конфигураций сетевых ресурсов и обнаружения изменений сетевых ресурсов, связанных с экземплярами фабрики данных Azure. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических сетевых ресурсов.

* [Как просматривать и извлекать события журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Как создать оповещения в службе Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в статье [Управление безопасностью: ведение журналов и мониторинг](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1. Использование утвержденных источников синхронизации времени

**Руководство**. Корпорация Майкрософт поддерживает источник времени, используемый для ресурсов Azure, таких как фабрика данных Azure для меток времени в журналах.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2. Настройка централизованного управления журналами безопасности

**Руководство**. прием журналов с помощью Azure Monitor для агрегирования данных безопасности, создаваемых фабрикой данных Azure. В Azure Monitor Вы можете выполнить запрос к рабочей области Log Analytics, настроенной для получения журналов действий фабрики данных Azure. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения журналов или концентраторов событий для экспорта данных в другие системы.

Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним инцидентам безопасности и управлению событиями (SIEM). Вы также можете интегрировать фабрику данных Azure с Git, чтобы использовать несколько преимуществ системы управления версиями, такие как возможность отслеживать изменения и проводить аудит изменений, а также возвратиться к изменениям, которые представляют ошибки.

* [Как настроить параметры диагностики](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Начало работы с Azure Monitor и интеграция SIEM стороннего производителя](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Система управления версиями в фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/source-control)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Включение журналов аудита для ресурсов Azure

**Руководство**. для ведения журнала аудита на плоскости управления включите параметры диагностики журнала действий Azure и отправьте журналы в log Analytics рабочую область, концентраторы событий Azure или учетную запись хранения Azure для архивации. С помощью данных журнала действий Azure вы можете определить, что нужно сделать, с чем и когда, для операций записи (PUT, POST, DELETE), выполненных на уровне управления для ресурсов Azure.

Используйте параметры диагностики для настройки журналов диагностики для нерасчетных ресурсов в фабрике данных Azure, таких как метрики и конвейер — запуск данных. Фабрика данных Azure хранит данные о запуске конвейера в течение 45 дней. Чтобы сохранить эти данные в течение длительного периода времени, сохраните журналы диагностики в учетной записи хранения для аудита или проверки вручную и укажите время хранения в днях. Вы также можете выполнить потоковую передачу журналов в концентраторы событий Azure или отправить журналы в Log Analytics рабочую область для анализа.

* [Как включить параметры диагностики для журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Общие сведения о журналах диагностики фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Сбор журналов безопасности из операционных систем

**Рекомендации**. Если вы используете Integration Runtime на виртуальной машине Azure, вы можете использовать Azure Monitor для получения данных с виртуальной машины. Установка расширения Log Analytics VM позволяет Azure Monitor получать данные с виртуальных машин Azure. Затем центр безопасности Azure может обеспечить мониторинг журнала событий безопасности для виртуальных машин. Учитывая объем данных, создаваемый журналом событий безопасности, он не сохраняется по умолчанию.

Если ваша организация хочет сохранить данные журнала событий безопасности, она может храниться на уровне сбора данных, после чего их можно запросить в Log Analytics.

* [Как выполнять получение данных из виртуальных машин Azure в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [Включение сбора данных в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5. Настройка хранения журнала безопасности

**Руководство**. Включение параметров диагностики для фабрики данных Azure. Если вы хотите сохранить журналы в рабочей области Log Analytics, задайте для нее период хранения согласно нормативным требованиям вашей организации. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения.

* [Как включить журналы диагностики в фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#set-up-diagnostic-logs)

* [Как задать параметры хранения журнала для рабочих областей Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6. Мониторинг и просмотр журналов

**Руководство**. Включение параметров диагностики для фабрики данных Azure и отправка журналов в рабочую область log Analytics. Используйте Log Analytics для анализа и мониторинга журналов для аномального поведения и регулярного просмотра результатов. Убедитесь, что вы также включили параметры диагностики для всех хранилищ данных, связанных с развертываниями фабрики данных Azure. Инструкции по включению параметров диагностики см. в разделе Основы безопасности каждой службы.

Если вы используете Integration Runtime на виртуальной машине Azure, включите также параметры диагностики для виртуальной машины.

Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

* [Схема Log Analytics](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#schema-of-logs-and-events)

* [Как получить данные из виртуальной машины Azure с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Включение оповещений для аномальных действий

**Рекомендации**. Вы можете создавать оповещения о поддерживаемых метриках в фабрике данных, перейдя к &amp; разделу метрики оповещений в Azure Monitor.

Настройте параметры диагностики для фабрики данных Azure и отправьте журналы в рабочую область Log Analytics. В рабочей области Log Analytics настройте оповещения, которые должны выполняться при заданном наборе условий. Кроме того, вы можете включить и подключить данные к Azure Sentinel или сторонним SIEM.

Кроме того, убедитесь, что включены параметры диагностики для служб, связанных с хранилищами данных. Рекомендации можно найти в базовом плане безопасности каждой службы.

* [Оповещения в фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/monitor-visually#alerts)

* [Страница «все поддерживаемые метрики»](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)

* [Как настроить оповещения журнала в рабочей области Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8. Централизованное ведение журнала защиты от вредоносных программ

**Рекомендации**. Если вы используете Integration Runtime на виртуальной машине Azure, вы можете использовать антивредоносное по Майкрософт для облачных служб и виртуальных машин Azure, а также настроить виртуальные машины для записи событий в учетную запись хранения Azure. Настройте рабочую область Log Analytics для приема событий из учетных записей хранения и создавайте предупреждения там, где это необходимо. Следуйте рекомендациям в центре безопасности Azure: "вычисление &amp; приложений".

* [Настройка антивредоносного по Майкрософт для облачных служб и виртуальных машин](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Включение мониторинга на уровне гостя для виртуальных машин](https://docs.microsoft.com/azure/cost-management/azure-vm-extended-metrics)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9. Включение ведения журнала запросов DNS

**Руководство**: неприменимо; Фабрика данных Azure не обрабатывает и не создает журналы, связанные с DNS.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Неприменимо

### <a name="210-enable-command-line-audit-logging"></a>2.10. Включение ведения журнала аудита для командной строки

**Рекомендации**. Если вы используете Integration Runtime на виртуальной машине Azure, вы можете включить ведение журнала аудита для командной строки. Центр безопасности Azure обеспечивает мониторинг журнала событий безопасности для виртуальных машин Azure. Центр безопасности подготавливает Microsoft Monitoring Agent на всех поддерживаемых виртуальных машинах Azure и на всех новых, созданных, если включена автоматическая подготовка или вы можете установить агент вручную. Агент включает событие создания процесса 4688 и поле CommandLine в событии 4688. Новые процессы, созданные на виртуальной машине, записываются в журнал событий и отслеживаются с помощью служб обнаружения в центре безопасности.

* [Сбор данных в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="identity-and-access-control"></a>Идентификаторы и управление доступом

*Дополнительные сведения см. в статье [Управление безопасностью: идентификаторы и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Инвентаризация учетных записей администраторов

**Руководство**. в фабрике данных Azure убедитесь, что вы регулярно следите за доступом пользователей и выполняете их согласование. Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль участника, владельца либо администратора подписки Azure.

Кроме того, на уровне клиента Azure Active Directory (AD) имеет встроенные роли, которые должны быть явно назначены и доступны для запросов. Используйте модуль Azure AD PowerShell для выполнения нерегламентированных запросов, чтобы обнаружить учетные записи, которые являются членами административных групп, имеющих административный доступ к плоскости управления экземпляров фабрики данных Azure.

Хотя Azure AD является рекомендуемым методом для администрирования доступа пользователей, помните, что если вы используете Integration Runtime на виртуальной машине Azure, ваша ВИРТУАЛЬная машина также может иметь локальные учетные записи. Как локальные, так и доменные учетные записи должны проверяться и управляться обычным образом с минимальным объемом памяти. Кроме того, мы советуем рассмотреть, что диспетчер привилегированных удостоверений проверяется на наличие JIT-функции, чтобы снизить доступность административных разрешений.

* [Roles and permissions for Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions) (Роли и разрешения для службы "Фабрика данных Azure")

* [Сведения о диспетчере привилегированных удостоверений](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Как получить роль каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Как получить членов роли каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Сведения о локальных учетных записях](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2. Изменение паролей по умолчанию, где применимо

**Руководство**. фабрика данных azure использует Azure Active Directory (AD) для предоставления доступа к портал Azure, а также к консоли фабрики данных Azure. В Azure AD нет концепции паролей по умолчанию, однако вы несете ответственность за изменение или запрет паролей по умолчанию для пользовательских или сторонних приложений.

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3. Применение выделенных административных учетных записей

**Руководство**. Создание стандартных операционных процедур, связанных с использованием выделенных административных учетных записей для доступа к плоскости управления Azure (портал Azure), а также консоли фабрики данных Azure. Используйте управление удостоверениями и доступом центра безопасности Azure, чтобы отслеживать количество административных учетных записей в Azure AD.

Кроме того, чтобы отслеживать выделенные административные учетные записи, вы можете использовать рекомендации из Центра безопасности Azure или встроенные политики Azure, например следующие:
- Подписке должно быть назначено несколько владельцев.
- Устаревшие учетные записи с разрешениями владельца следует удалять из подписки.
- Внешние учетные записи с разрешениями владельца следует удалять из подписки.

Если вы используете Integration Runtime на виртуальной машине Azure, учетные записи администратора на виртуальных машинах Azure также можно настроить с помощью диспетчера привилегированных удостоверений Azure (PIM). Диспетчер привилегированных удостоверений Azure предоставляет несколько вариантов, таких как JIT-Аутентификация, многофакторная проверка подлинности и делегирование, чтобы разрешения были доступны только для определенных временных периодов и для утверждения требуется другой пользователь.

* [Общие сведения об удостоверениях и доступе центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Использование Политики Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Сведения о диспетчере привилегированных удостоверений](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Roles and permissions for Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions) (Роли и разрешения для службы "Фабрика данных Azure")

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4. Использование единого входа с Azure Active Directory

**Руководство**. Использование регистрации приложений Azure (субъекта-службы) для получения маркера, который приложение или функция может использовать для доступа к хранилищам служб восстановления и взаимодействия с ними.

* [Как вызывать интерфейсы API службы Azure](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Регистрация клиентского приложения (субъекта-службы) в Azure AD](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Сведения об API служб восстановления Azure](https://docs.microsoft.com/rest/api/recoveryservices/)

* [Сведения о REST API для фабрики данных Azure](https://docs.microsoft.com/rest/api/datafactory/)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5. Использование многофакторной проверки подлинности для любого доступа на основе Azure Active Directory

**Рекомендации**. Включите многофакторную идентификацию (MFA) в Azure Active Directory и следуйте рекомендациям по управлению идентификацией и доступом в Центре безопасности Azure.

* [Включение MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Мониторинг идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Использование выделенных компьютеров (рабочих станций с привилегированным доступом) для всех административных задач

**Руководство**. Используйте рабочие станции с привилегированным доступом (PAW) с настроенной Многофакторной идентификацией (MFA) для входа в ресурсы Azure и их настройки.

* [Использование рабочих станций с привилегированным доступом](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Включение MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: журналы и оповещения о подозрительных действиях учетных записей администраторов

**Рекомендации**. Используйте Azure Active Directory отчеты о безопасности для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде. Используйте Центр безопасности Azure для мониторинга действий идентификации и доступа.

Если вы используете Integration Runtime на виртуальной машине Azure, вы можете дополнительно подключить ВИРТУАЛЬную машину к Azure Sentinel. Microsoft Azure Sentinel — это масштабируемое ориентированное на облако решение для управления информационной безопасностью и событиями безопасности (SIEM), а также для автоматического ответа с помощью оркестрации операций защиты. Azure Sentinel обеспечивает интеллектуальные средства для анализа данных безопасности и аналитику угроз по всему предприятию, предоставляя единое решение для обнаружения предупреждений, видимости угроз, упреждающего поиска и реагирования на угрозы.

* [Как определить пользователей Azure AD, помеченных для события риска](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Мониторинг пользовательских действий идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8. Управление ресурсами Azure только из утвержденных расположений

**Руководство**. Используйте именованные расположения с условным доступом, чтобы разрешить доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

* [Как настроить именованные расположения](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="39-use-azure-active-directory"></a>3.9. Использование Azure Active Directory

**Руководство**. фабрика данных может быть связана с управляемым удостоверением для ресурсов Azure, представляющих определенную фабрику данных. Это управляемое удостоверение можно использовать для проверки подлинности базы данных SQL Azure. С помощью этого удостоверения назначенная фабрика может обращаться к данным и копировать их из вашей базы данных или в нее.

Если вы используете Integration Runtime (IR) на виртуальной машине Azure, вы можете использовать управляемые удостоверения для проверки подлинности в любой службе, поддерживающей проверку подлинности Azure AD, включая Key Vault без каких-либо учетных данных в коде. Код, выполняемый на виртуальной машине, может использовать управляемое удостоверение для запроса маркеров доступа для служб, поддерживающих проверку подлинности Azure AD.

* [Создание и настройка экземпляра Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

* [Что такое управляемые удостоверения для ресурсов Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

* [Копирование и преобразование данных в базе данных SQL Azure с помощью фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#using-service-principal-authentication)

* [Настройка проверки подлинности Azure Active Directory и управление ею с помощью базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10. Регулярная проверка и согласование доступа пользователей

**Руководство**. Azure Active Directory (AD) предоставляет журналы для упрощения обнаружения устаревших учетных записей. Кроме того, используйте проверку доступа удостоверений Azure, чтобы эффективно управлять членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователей можно проверять на регулярной основе, чтобы только у авторизованных пользователей был постоянный доступ.

Если вы используете интеграцию среды выполнения на виртуальной машине Azure, необходимо проверить локальные группы безопасности и пользователей, чтобы убедиться в отсутствии непредвиденных учетных записей, которые могут нарушить безопасность системы.

* [Использование проверок доступа для идентификации Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Общие сведения об отчетах Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Использование проверок доступа для идентификации Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: монитор пытается получить доступ к отключенным учетным данным

**Руководство**. у вас есть доступ к источникам журналов событий входа Azure Active Directory (AD), аудита и риску, которые позволяют интегрироваться с любым средством SIEM/Monitoring. Этот процесс можно упростить, создав параметры диагностики для учетных записей пользователей Azure AD и отправив журналы аудита и журналы входа в рабочую область Log Analytics. Вы можете настроить нужные оповещения журнала в Log Analytics.

Если вы используете Integration Runtime на виртуальной машине Azure, подключите эту ВИРТУАЛЬную машину к Azure Sentinel. Microsoft Azure Sentinel — это масштабируемое ориентированное на облако решение для управления информационной безопасностью и событиями безопасности (SIEM), а также для автоматического ответа с помощью оркестрации операций защиты. Azure Sentinel обеспечивает интеллектуальные средства для анализа данных безопасности и аналитику угроз по всему предприятию, предоставляя единое решение для обнаружения предупреждений, видимости угроз, упреждающего поиска и реагирования на угрозы.

* [Как интегрировать журналы действий Azure в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Авторизация доступа к ресурсам концентраторов событий с помощью Azure Active Directory](https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12. Предупреждение при подозрительном входе в учетную запись

**Руководство**. Использование Azure Active Directory (AD) в качестве централизованной системы проверки подлинности и авторизации для ресурсов фабрики данных Azure, таких как база данных SQL Azure или виртуальные машины Azure. Для отклонения учетной записи для входа в плоскость управления (портал Azure) используйте функции обнаружения защита идентификации Azure AD и риска, чтобы настроить автоматические ответы на обнаруженные подозрительные действия, связанные с удостоверениями пользователей. Вы также можете включить данные в Azure Sentinel для дальнейшего изучения.

* [Просмотр рискованных входов в Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Как настроить и включить политики рисков с помощью защиты идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Настройка и администрирование аутентификации Azure Active Directory с помощью SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell)

* [Включение аутентификации Azure Active Directory для среды выполнения интеграции Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13. Предоставление корпорации Майкрософт доступа к соответствующим данным клиентов в рамках сценариев поддержки

**Руководство**. в сценариях поддержки, в которых корпорации Майкрософт требуется доступ к данным клиентов, защищенное хранилище Azure предоставляет пользователям интерфейс для просмотра и утверждения или отклонения запросов на доступ к данным клиентов. Обратите внимание, что хотя хранилище Azure недоступно для фабрики данных Azure, хранилище Azure поддерживает базу данных SQL Azure и виртуальные машины Azure.

* [Общие сведения о защищенное хранилище](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в статье [Управление безопасностью: защита данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Инвентаризация конфиденциальных данных

**Руководство**. Используйте теги для пометки ресурсов Azure, в которых хранятся или обрабатываются конфиденциальные данные.

Используйте функцию обнаружения и классификации данных в базе данных SQL Azure. Обнаружение и классификация данных предоставляет расширенные возможности, встроенные в базу данных SQL Azure, для обнаружения, классификации и пометки &amp; защиты конфиденциальных данных в базах данных.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Как использовать обнаружение и классификацию данных для Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2. Изолирование систем, хранящих или обрабатывающих конфиденциальные данные

**Руководство**. Реализуйте отдельные подписки и группы управления для разработки, тестирования и производства. Среды выполнения интеграции должны быть разделены виртуальной сетью (VNet)/субнет и помечены соответствующим образом.

 Вы также можете использовать частные конечные точки для выполнения сетевой изоляции. Частная конечная точка Azure — это сетевой интерфейс, который защищенно и надежно подключается к службе через Приватный канал Azure. Частная конечная точка использует частный IP-адрес из виртуальной сети, по сути перемещая службу в виртуальную сеть.

* [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Общие сведения о частной ссылке](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

**Руководство**. для источников данных (например, базы данных SQL Azure), в которых хранятся или обрабатываются конфиденциальные сведения о развертывании фабрики данных Azure, пометьте связанные ресурсы как конфиденциальные с помощью тегов.

Где доступна частная ссылка, используйте частные конечные точки для защиты любых ресурсов, связанных с конвейером фабрики данных Azure. Трафик между виртуальной сетью и службой проходит через магистральную сеть Майкрософт, что позволяет избежать рисков общедоступного Интернета. Кроме того, можно уменьшить риск утечка данных, настроив для группы безопасности сети (NSG) четкий набор правил для исходящего трафика и связав его с подсетью NSG.

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Создание NSG с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Общие сведения о частной ссылке Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Шифрование любой конфиденциальной информации при передаче

**Рекомендации**. Если облачное хранилище данных поддерживает протокол HTTPS или TLS, все передачи данных между службами перемещения данных в фабрике данных и облачным хранилищем данных выполняются через защищенный канал HTTPS или TLS. Для TLS используется версия 1.2.

Для всех подключений к базе данных SQL Azure и Azure синапсе Analytics (ранее — хранилище данных SQL) требуется шифрование (SSL/TLS), во время передачи данных в базу данных и из нее. Разрабатывая конвейер с помощью JSON, добавьте свойство "encryption" и в строке подключения задайте для него значение true. Для службы хранилища Azure вы можете использовать в строке подключения протокол HTTPS.

* [Общие сведения о шифровании при передаче в фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Совмещаемая блокировка

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Использование средства активного обнаружения для распознавания конфиденциальных данных

**Руководство**. Если вы используете фабрику данных Azure для копирования и преобразования экземпляров базы данных SQL Azure, используйте функцию обнаружения и классификации данных в базе данных SQL Azure. Обнаружение и классификация данных предоставляет расширенные возможности, встроенные в базу данных SQL Azure, для обнаружения, классификации и пометки &amp; защиты конфиденциальных данных в базах данных.

Функции обнаружения и классификации данных пока недоступны для других служб Azure.

* [Как использовать обнаружение и классификацию данных для Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6. Контроль доступа к ресурсам с помощью RBAC

**Руководство**. Использование управления доступом на основе ролей Azure (Azure RBAC) для управления доступом к плоскости управления фабрики данных azure (портал Azure).

Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль участника, владельца либо администратора подписки Azure.

Для источников данных фабрики данных, таких как база данных SQL Azure, обратитесь к базовому плану безопасности для этой службы, чтобы получить дополнительные сведения об Azure RBAC.

* [Настройка Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

* [Roles and permissions for Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions) (Роли и разрешения для службы "Фабрика данных Azure")

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

**Руководство**. функции для идентификации, классификации и предотвращения потерь данных пока недоступны для службы хранилища Azure или ресурсов для вычислений. Установите сторонние решения, если это необходимо для обеспечения соответствия требованиям.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и предпринимает все возможные усилия для защиты клиентов от потери данных и раскрытия информации. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Совмещаемая блокировка

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Шифрование конфиденциальной информации при хранении

**Рекомендации**. рекомендуется включить механизм шифрования данных для любых хранилищ данных, связанных с развертываниями фабрики данных Azure. Для получения дополнительных сведений о шифровании неактивных данных можно обратиться к базовому плану безопасности для этой службы.

Если вы используете Integration Runtime на виртуальной машине Azure, виртуальные диски на Виртуальные машины Windows (ВМ) шифруются при помощи шифрования на стороне сервера или с помощью шифрования дисков Azure (ADE). Шифрование дисков Azure использует функцию Windows BitLocker для шифрования управляемых дисков с помощью управляемых клиентом ключей в гостевой виртуальной машине. Шифрование на стороне сервера с использованием управляемых клиентом ключей улучшает работу шифрования дисков Azure, позволяя использовать любые типы ОС и образы для виртуальных машин путем шифрования данных в службе хранилища.

Можно сохранить учетные данные или секретные значения в Azure Key Vault и использовать их во время выполнения конвейера для передачи в действия. Также можно хранить учетные данные для хранилищ данных и вычислений в Azure Key Vault. Фабрика данных Azure извлекает учетные данные при выполнении действия, которое использует хранилище данных или вычислительный ресурс.

* [Общие сведения о шифровании при хранении в фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)

* [Шифрование на стороне сервера для управляемых дисков Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption)

* [Шифрование дисков Azure для виртуальных машин Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview)

* [Использование секретов Azure Key Vault в действиях конвейера](https://docs.microsoft.com/azure/data-factory/how-to-use-azure-key-vault-secrets-pipeline-activities)

* [Как Azure Key Vault учетные данные](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Включение в журнал и создание оповещений по изменениям критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналом действий Azure для создания оповещений о том, когда изменения выполняются в фабрике данных Azure и связанных ресурсах.

* [Создание оповещений для событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Создание оповещений для событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Ведение журнала Аналитики Службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="vulnerability-management"></a>управление уязвимостями;

*Дополнительные сведения см. в статье [Управление безопасностью: управление уязвимостями](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Выполнение автоматизированных средства анализа уязвимостей

**Руководство**. Если вы используете базу данных SQL Azure в качестве хранилища данных, включите расширенную защиту данных для базы данных SQL Azure и следуйте рекомендациям центра безопасности Azure по выполнению оценки уязвимостей на серверах Azure SQL.

Если вы используете Integration Runtime на виртуальной машине Azure, следуйте рекомендациям центра безопасности Azure по выполнению оценки уязвимостей на виртуальных машинах. Используйте рекомендованное или стороннее решение для проведения оценки уязвимостей для виртуальных машин.

* [Как запустить оценку уязвимостей в базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [Включение расширенной защиты данных](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Реализация рекомендаций по оценке уязвимостей в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Развертывание автоматизированного решения для управления исправлениями операционной системы

**Рекомендации**. Если вы используете Integration Runtime на виртуальной машине Azure, используйте решение Azure Управление обновлениями для управления обновлениями и исправлениями для виртуальных машин. Управление обновлениями использует локально настроенный репозиторий обновлений для обновления поддерживаемых систем Windows. Такие средства, как System Center Updates Publisher (Updates Publisher), позволяют публиковать пользовательские обновления в Windows Server Update Services (WSUS). Этот сценарий позволяет Управление обновлениями исправлять компьютеры, использующие Configuration Manager в качестве репозитория обновлений программного обеспечения стороннего производителя.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и предпринимает все возможные усилия для защиты клиентов от потери данных и раскрытия информации. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

* [Решение для управления обновлениями в Azure](https://docs.microsoft.com/azure/automation/automation-update-management)

* [Управление обновлениями и исправлениями для виртуальных машин Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-update-management)

* [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Совмещаемая блокировка

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Развертывание автоматизированного решения по управлению исправлениями для программ сторонних разработчиков

**Рекомендации**. Если вы используете Integration Runtime на виртуальной машине Azure, вы можете использовать стороннее решение для управления исправлениями. Решение Azure Управление обновлениями можно использовать для управления обновлениями и исправлениями для виртуальных машин. Управление обновлениями использует локально настроенный репозиторий обновлений для обновления поддерживаемых систем Windows. Такие средства, как System Center Updates Publisher (Updates Publisher), позволяют публиковать пользовательские обновления в Windows Server Update Services (WSUS). Этот сценарий позволяет Управление обновлениями исправлять компьютеры, использующие Configuration Manager в качестве репозитория обновлений программного обеспечения стороннего производителя.

* [Решение для управления обновлениями в Azure](https://docs.microsoft.com/azure/automation/automation-update-management)

* [Управление обновлениями и исправлениями для виртуальных машин Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-update-management)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4. Сравнение проверок смежных уязвимостей

**Руководство**. Если вы используете Integration Runtime на виртуальной машине Azure, экспортируйте результаты сканирования с одинаковыми интервалами и сравните результаты, чтобы убедиться, что уязвимости исправлены. При использовании рекомендаций по управлению уязвимостями, предлагаемых центром безопасности Azure, вы можете выполнить сведение на портал выбранного решения для просмотра исторических данных сканирования.

* [Общие сведения об интегрированном сканере уязвимостей для виртуальных машин](https://docs.microsoft.com/azure/security-center/built-in-vulnerability-assessment)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Использование процесса оценки рисков для определения приоритета в устранении обнаруженных уязвимостей

**Руководство**. при запуске Integration Runtime на виртуальной машине Azure можно использовать средство проверки уязвимости в машинном коде. Сканер уязвимостей, который входит в состав Центра безопасности Azure, разработан компанией Qualys. Сканер Qualys — это ведущий инструмент для идентификации уязвимостей на виртуальных машинах Azure в режиме реального времени.

Когда Центр безопасности обнаруживает уязвимости, он отображает результаты и соответствующие сведения в качестве рекомендаций. Соответствующие сведения включают в себя действия по исправлению, связанные CVE, оценки CVSS и многое другое. Вы можете просмотреть обнаруженные уязвимости для одной или нескольких подписок или для конкретной виртуальной машины.

* [Встроенный сканер уязвимостей для виртуальных машин](https://docs.microsoft.com/azure/security-center/built-in-vulnerability-assessment)

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

**Руководство**. При необходимости используйте теги, группы управления и отдельные подписки, чтобы упорядочить и отслеживать ресурсы Azure. Регулярно сверяйте ресурсы, чтобы своевременно удалять неавторизованные ресурсы из подписки.

Кроме того, используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик:
- Недопустимые типы ресурсов
- Допустимые типы ресурсов

* [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: определение и обслуживание инвентаризации утвержденных ресурсов Azure

**Руководство**. Определите утвержденные ресурсы Azure и программное обеспечение для вычислительных ресурсов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5. Отслеживание неутвержденных ресурсов Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в ваших подписках.

Используйте Azure Resource Graph для запроса или обнаружения ресурсов в подписках. Убедитесь в том, что все ресурсы Azure, представленные в среде, утверждены.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Как создавать запросы с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6. Отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

**Рекомендации**. Если вы используете Integration Runtime на виртуальной машине Azure, используйте инвентаризацию виртуальных машин Azure для автоматизации сбора сведений обо всех программах на виртуальных машинах. Служба автоматизации Azure обеспечивает полный контроль во время развертывания, использования и вывода из эксплуатации рабочих нагрузок и ресурсов.

Примечание. имя программного обеспечения, версия, издатель и время обновления доступны в портал Azure. Чтобы получить доступ к дате установки и другим сведениям, клиент должен включить диагностику на уровне гостя и перенести журналы событий Windows в рабочую область Log Analytics.

* [Общие сведения о службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-intro)

* [Как включить инвентаризацию виртуальных машин Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Удаление неутвержденных ресурсов Azure и программных приложений

**Руководство**. Если вы используете Integration Runtime на виртуальной машине Azure, служба автоматизации Azure предоставляет полный контроль во время развертывания, эксплуатации и списания рабочих нагрузок и ресурсов. Вы можете использовать Отслеживание изменений, чтобы выбрать все программное обеспечение, установленное на виртуальных машинах. Вы можете реализовать собственный процесс или использовать конфигурацию состояния службы автоматизации Azure для удаления неавторизованного программного обеспечения.

* [Общие сведения о службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-intro)

* [Отслеживание изменений в среде с помощью решения для отслеживания изменений](https://docs.microsoft.com/azure/automation/change-tracking)

* [Обзор настройки состояния службы автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="68-use-only-approved-applications"></a>6.8. Использование только утвержденных приложений

**Руководство**. Если вы используете Integration Runtime на виртуальной машине Azure, используйте адаптивные элементы управления приложениями в центре безопасности Azure, чтобы гарантировать выполнение только авторизованного программного обеспечения и блокирование выполнения всех несанкционированных программ на виртуальных машинах.

* [Как использовать адаптивные элементы управления приложениями в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Мониторинг Центра безопасности Azure**: Да

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

**Руководство**. адаптивное управление приложениями — это интеллектуальное, автоматизированное комплексное решение из центра безопасности Azure, которое помогает контролировать, какие приложения могут выполняться на компьютерах Azure и не на базе Azure (Windows и Linux). Реализуйте стороннее решение, если это не соответствует требованиям вашей организации.

Обратите внимание, что это применимо только в том случае, если Integration Runtime выполняется на виртуальной машине Azure.

* [Как использовать адаптивные элементы управления приложениями в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: ограничьте возможность пользователей работать с Azure Resource Manager

**Руководство**. Настройте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "Блокировать доступ" для приложения "Управление Microsoft Azure".

* [Как настроить условный доступ для блокировки доступа к Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12. Ограничение возможности пользователей выполнять сценарии в ресурсах вычислений

**Руководство**. Если вы используете интеграцию среды выполнения на виртуальной машине Azure, то в зависимости от типа сценариев вы можете использовать конфигурации операционной системы или сторонние ресурсы, чтобы ограничить возможность пользователей выполнять сценарии в ресурсах вычислений Azure. Можно также использовать адаптивные элементы управления приложениями центра безопасности Azure, чтобы гарантировать выполнение только авторизованного программного обеспечения и блокирование выполнения всех несанкционированных программ на виртуальных машинах Azure.

* [Управление выполнением скриптов PowerShell в средах Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Как использовать адаптивные элементы управления приложениями в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13. Физическое или логическое разделение приложений с высоким риском

**Руководство**. приложения с высоким риском, развернутые в среде Azure, могут быть изолированы с помощью виртуальной сети, подсети, подписок, групп управления и т. д., а также с помощью брандмауэра Azure, брандмауэра веб-приложения (WAF) или группы безопасности сети (NSG).

* [Виртуальные сети и виртуальные машины в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [Что такое Брандмауэр Azure?](https://docs.microsoft.com/azure/firewall/overview)

* [Что такое брандмауэр веб-приложения Azure?](https://docs.microsoft.com/azure/web-application-firewall/overview)

* [Группы безопасности сети](https://docs.microsoft.com/azure/virtual-network/security-overview)

* [Что такое виртуальная сеть Azure?](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

* [Упорядочение ресурсов с помощью групп управления Azure](https://docs.microsoft.com/azure/governance/management-groups/overview)

* [Руководство по выбору модели подписки](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="secure-configuration"></a>Безопасная конфигурация

*Дополнительные сведения см. в статье [Управление безопасностью: безопасная конфигурация](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Установка безопасных конфигураций для всех ресурсов Azure

**Руководство**. Определение и реализация стандартных конфигураций безопасности для фабрики данных Azure с помощью политики Azure. Используйте псевдонимы политик Azure в пространстве имен Microsoft. данных фактов, чтобы создать настраиваемые политики для аудита или принудительного применения конфигурации экземпляров фабрики данных Azure.

* [Просмотр доступных псевдонимов политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Сохранение безопасных конфигураций для операционных систем

**Рекомендации**. Если интеграция среды выполнения выполняется на виртуальной машине Azure, используйте рекомендацию центра безопасности Azure [исправьте уязвимости в конфигурациях безопасности на виртуальных машинах], чтобы сохранить конфигурации безопасности для всех ресурсов вычислений.

* [Как отслеживать рекомендации центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [Как исправлять рекомендации центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3. Сохранение безопасных конфигураций для ресурсов Azure

**Рекомендации**. Используйте Политику Azure [отказывать] и [развернуть, если не существует], чтобы обеспечить безопасность параметров в ресурсах Azure.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Сведения о действии Политик Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [Сведения о создании шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4. Сохранение безопасных конфигураций для операционных систем

**Рекомендации**. Если вы используете Integration Runtime на виртуальной машине Azure, обратите внимание, что существует несколько вариантов обеспечения безопасности конфигурации виртуальных машин для развертывания.
- Azure Resource Manager шаблоны: это файлы на основе JSON, используемые для развертывания виртуальной машины из портал Azure, а пользовательский шаблон должен поддерживаться. Корпорация Майкрософт выполняет обслуживание базовых шаблонов.
- Пользовательский виртуальный жесткий диск (VHD). в некоторых случаях может потребоваться использовать пользовательские VHD-файлы, например, при работе со сложными средами, которыми нельзя управлять с помощью других средств. Конфигурация состояния службы автоматизации Azure. После развертывания базовой ОС ее можно использовать для более детального управления параметрами и принудительного применения с помощью платформы автоматизации.

В большинстве случаев шаблоны базовых виртуальных машин Майкрософт, Объединенные с конфигурацией требуемого состояния службы автоматизации Azure, могут помочь в собрании и обслуживании требований безопасности.

* [Сведения о том, как скачать шаблон виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

* [Сведения о создании шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Как отправить пользовательский виртуальный жесткий диск виртуальной машины в Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5. Безопасное хранение конфигурации ресурсов Azure

**Руководство**. Если вы используете пользовательские определения политики Azure, используйте Azure DevOps или Azure Repos для безопасного хранения кода и управления им.

* [Как хранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Документация по Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6. Безопасное хранение пользовательских образов операционной системы

**Руководство**. при использовании пользовательских образов используйте управление доступом на основе ролей Azure (Azure RBAC), чтобы предоставить доступ к образам только полномочным пользователям. Для образов контейнеров Храните их в реестре контейнеров Azure и используйте Azure RBAC, чтобы обеспечить доступ к образам только полномочным пользователям.

Роль участника фабрики данных можно использовать для создания фабрик данных и управления ими, а также для дочерних ресурсов в них.

* [Общие сведения об Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

* [Общие сведения об Azure RBAC для реестра контейнеров](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

* [Настройка Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

* [Roles and permissions for Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions) (Роли и разрешения для службы "Фабрика данных Azure")

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: развертывание средств управления конфигурацией для ресурсов Azure

**Руководство**. Используйте встроенные определения политик Azure, а также псевдонимы политик Azure в пространстве имен Microsoft., чтобы создать настраиваемые политики для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией для операционных систем

**Рекомендации**. Эта рекомендация может применяться, если Integration Runtime выполняется на виртуальной машине Azure. Настройка состояния службы автоматизации Azure — это служба управления конфигурацией для узлов Desired State Configuration (DSC) в любом облачном или локальном центре обработки данных. Она обеспечивает быструю и простую масштабируемость тысяч компьютеров из безопасного центрального расположения. Вы можете легко переносить компьютеры в облачную среду, присваивать им декларативные конфигурации, а также просматривать отчеты, отражающие соответствие каждого компьютера требуемому состоянию.

* [Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9. Реализация автоматического мониторинга конфигурации для ресурсов Azure

**Руководство**. Используйте встроенные определения политик Azure, а также псевдонимы политик Azure в пространстве имен Microsoft., чтобы создать настраиваемые политики для оповещения, аудита и принудительного применения конфигураций системы. Используйте политику Azure [аудит], [запретить] и [развернуть, если не существует] для автоматического применения конфигураций для ресурсов Azure.

* [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10. Реализация автоматизированного мониторинга конфигурации для операционных систем

**Рекомендации**. Эта рекомендация может применяться, если Integration Runtime выполняется на виртуальной машине Azure. Настройка состояния службы автоматизации Azure — это служба управления конфигурацией для узлов Desired State Configuration (DSC) в любом облачном или локальном центре обработки данных. Она обеспечивает быструю и простую масштабируемость тысяч компьютеров из безопасного центрального расположения. Вы можете легко переносить компьютеры в облачную среду, присваивать им декларативные конфигурации, а также просматривать отчеты, отражающие соответствие каждого компьютера требуемому состоянию.

* [Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11. Безопасное управление секретами Azure

**Рекомендации**. Используйте управляемое удостоверение службы в сочетании с Azure Key Vault, чтобы упростить и защитить управление секретами для облачных приложений.

Можно также сохранить учетные данные или секретные значения в Azure Key Vault и использовать их во время выполнения конвейера для передачи в действия. Убедитесь, что обратимое удаление включено.

* [Интеграция с управляемыми удостоверениями Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [Создание Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [Проверка подлинности в Key Vault](https://docs.microsoft.com/azure/key-vault/general/authentication)

* [Назначение политики доступа Key Vault](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal)

* [Использование секретов Azure Key Vault в действиях конвейера](https://docs.microsoft.com/azure/data-factory/how-to-use-azure-key-vault-secrets-pipeline-activities)

* [Обратимое удаление в Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12. Безопасное и автоматическое управление удостоверениями

**Руководство**. при создании фабрики данных можно создать управляемое удостоверение вместе с созданием фабрики. Управляемое удостоверение является управляемым приложением, зарегистрированным в Azure Active Directory, и представляет эту конкретную фабрику данных.

* [Управляемое удостоверение для фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13. Устранение непреднамеренного раскрытия учетных данных

**Руководство**. Реализуйте сканер учетных данных для обнаружения учетных данных в коде. Сканер учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault.

* [Как настроить сканер учетных данных](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в статье [Управление безопасностью: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1. Использование централизованно управляемого программного обеспечения для защиты от вредоносных программ

**Рекомендации**. Если вы используете Integration Runtime на виртуальной машине Azure, вы можете использовать антивредоносное по майкрософт для виртуальные машины Windows Azure, чтобы постоянно отслеживать и защищать ресурсы.

* [Настройка антивредоносного по Майкрософт для облачных служб и виртуальных машин](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2. Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся вычислительными

**Руководство**. Защита от вредоносных программ Майкрософт (Microsoft Antimalware) включена на базовом узле, поддерживающем службы Azure (например, Служба приложений Azure), но не выполняется в вашем содержимом.

Предварительно проверять все файлы, передаваемые в Нерасчетные ресурсы Azure, такие как служба приложений, Data Lake Storage, хранилище BLOB-объектов и т. д.

Используйте средство обнаружения угроз для служб данных в центре безопасности Azure, чтобы обнаружить вредоносные программы, отправленные в учетные записи хранения.

* [Сведения о антивредоносном по Майкрософт для облачных служб и виртуальных машин Azure](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Сведения об обнаружении угроз для служб данных в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3. Своевременное обновление программного обеспечения для защиты от вредоносных программ и подписей

**Рекомендации**. при развертывании антивредоносное по Майкрософт для Azure автоматически устанавливает последние обновления сигнатур, платформы и подсистемы по умолчанию. Следуйте рекомендациям в центре безопасности Azure: "вычисление &amp; приложений", чтобы обеспечить актуальность всех конечных точек с последними сигнатурами. ОС Windows может быть дополнительно защищена с помощью дополнительной защиты, чтобы ограничить риск атак на антивирусную или вредоносную программу с помощью службы Microsoft Защитник Advanced Threat Protection, которая интегрируется с центром безопасности Azure.

* [Развертывание антивредоносного по Майкрософт для облачных служб и виртуальных машин Azure](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Advanced Threat Protection в Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в статье [Управление безопасностью: восстановление данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1. Обеспечение регулярного автоматического резервного копирования

**Рекомендации**. Если вы используете Integration Runtime на виртуальной машине Azure, включите Azure Backup и настройте виртуальную машину, а также желаемую частоту и срок хранения для автоматического резервного копирования.

Для любого хранилища данных обратитесь к базовому плану безопасности этой службы, чтобы получить рекомендации по регулярному выполнению автоматической архивации.

* [Общие сведения о резервном копировании виртуальных машин Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [Создание резервной копии виртуальной машины Azure на основе параметров виртуальной машины](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом

**Рекомендации**. Если вы используете Integration Runtime на виртуальной машине Azure, включите Azure Backup и целевые виртуальные машины Azure, а также желаемую частоту и сроки хранения. Резервное копирование ключей, управляемых клиентом, в Azure Key Vault.

Для любого хранилища данных обратитесь к базовому плану безопасности этой службы, чтобы получить рекомендации по регулярному выполнению автоматической архивации.

* [Общие сведения о резервном копировании виртуальных машин Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [Как создать резервную копию ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Рекомендации**. Если вы используете Integration Runtime на виртуальной машине Azure, убедитесь, что возможность периодически выполнять восстановление данных в Azure Backup. При необходимости протестируйте содержимое для восстановления изолированной виртуальной ЛС. Периодически тестируйте резервные копии ключей, управляемых клиентом.

Для любого хранилища данных обратитесь к базовому плану безопасности этой службы за инструкциями по проверке резервных копий.

* [Как восстановить файлы из резервной копии виртуальной машины Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

* [Как восстановить резервную копию ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и ключей, управляемых клиентом

**Руководство**. Если вы используете Integration Runtime на виртуальной машине Azure, и вы выполняете резервное копирование виртуальной машины с Azure Backup, ваша виртуальная машина шифруется при хранении с помощью шифрование службы хранилища (SSE). Azure Backup также могут выполнять резервное копирование виртуальных машин Azure, зашифрованных с помощью шифрования дисков Azure. Шифрование дисков Azure интегрируется с ключами шифрования BitLocker (Бекс), которые защищается в хранилище ключей как секреты. Шифрование дисков Azure также интегрируется с ключами шифрования Azure Key Vault ключей (ключи обмена ключами). Включите обратимое удаление в Key Vault для защиты ключей от случайного или злонамеренного удаления.

* [Обратимое удаление для виртуальных машин](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete)

* [Как использовать обратимое удаление в Key Vault с помощью интерфейса командной строки](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="incident-response"></a>Реагирование на инциденты

*Дополнительные сведения см. в статье [Управление безопасностью: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Создание руководства по реагированию на инциденты

**Руководство**. Создайте руководство по реагированию на инциденты для вашей организации. Убедитесь, что имеются письменные планы реагирования на инциденты, которые определяют все действия персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.

* [Как настроить автоматизацию рабочих процессов в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Руководство по созданию собственного процесса реагирования на инциденты безопасности](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Анатомия инцидента Центра Майкрософт по реагированию на угрозы](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Клиент также может воспользоваться руководством по обработке инцидентов безопасности компьютера NIST, чтобы помочь в создании собственного плана реагирования на инциденты.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Создание процедуры оценки инцидента и определения приоритетов

**Руководство**. Центр безопасности назначает каждому оповещению серьезность, которая поможет определить, какие предупреждения следует расследовать первыми. Серьезность основывается на том, насколько уверен Центр безопасности в исследовании или аналитике, используемой для оповещения, а также на уровне достоверности злонамеренности события, приведшего к оповещению.

Кроме того, четко помечайте подписки (для устар. производственной, непроизводственной) и создайте систему именования, чтобы четко определить и классифицировать ресурсы Azure.

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="103-test-security-response-procedures"></a>10.3. Проверка процедур реагирования на угрозы

**Рекомендации**. Выполните упражнения, чтобы периодически протестировать возможности ваших систем реагировать на угрозы. Выявите слабые точки и пробелы и пересмотрите план по мере необходимости.

* [См. публикацию NIST: руководство по тестированию, обучению и выполнению программ для ИТ-планов и возможностей](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Предоставление контактных сведений и настройка уведомлений по инцидентам безопасности

**Рекомендации**. Корпорация Майкрософт будет использовать информацию об инциденте безопасности для связи с вами, если центр Microsoft Security Response Center (MSRC) обнаружит, что к вашим пользовательским данным был получен незаконный или несанкционированный доступ. Проверьте инциденты после факта обращения, чтобы убедиться, что проблемы устранены.

* [Как задать контакт безопасности Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Включение оповещений системы безопасности в систему реагирования на инциденты

**Рекомендации**. Экспортируйте оповещения и рекомендации центра безопасности Azure с помощью функции непрерывного экспорта. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Можно использовать соединитель данных Центра безопасности Azure для потоковой передачи оповещений Sentinel.

* [Как настроить непрерывный экспорт данных](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Как выполнить потоковую передачу оповещений в Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6. Автоматизация реагирования на оповещения системы безопасности

**Руководство**. Используйте функцию автоматизации рабочих процессов в Центре безопасности Azure для автоматического запуска реагирования с помощью Logic Apps в оповещениях и рекомендациях системы безопасности.

* [Как настроить автоматизацию рабочего процесса и Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в статье [Управление безопасностью: тесты на проникновение и попытки нарушения безопасности "красной командой"](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критических результатов безопасности.

**Руководство**. 

* [Следуйте правилам взаимодействия Майкрософт, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Дополнительные сведения о стратегии корпорации Майкрософт и реализации "красной команды", а также о тестировании на основе уязвимости для облачной инфраструктуры, служб и приложений Майкрософт см. здесь](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Мониторинг Центра безопасности Azure**. Неприменимо

**Ответственность**: Совмещаемая блокировка

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со статьей [Тесты производительности системы безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/overview).
- Дополнительные сведения о [базовой конфигурации безопасности Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).
