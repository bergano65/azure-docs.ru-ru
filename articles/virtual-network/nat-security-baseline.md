---
title: Базовый уровень безопасности Azure для NAT виртуальной сети
description: Базовый план безопасности NAT для виртуальной сети содержит рекомендации и ресурсы для реализации рекомендаций по безопасности, указанных в статье о производительности системы безопасности Azure.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4ab71138f110df19ec84fa8707b480ad1bc6e72c
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255178"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>Базовый уровень безопасности Azure для NAT виртуальной сети

Этот базовый план безопасности применяет рекомендации из [тестового показателя безопасности Azure версии 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview-v1) к сети NAT для виртуальных сетей Майкрософт. Azure Security Benchmark содержит рекомендации по обеспечению безопасности облачных решений в Azure.
Содержимое группируются по **элементам управления безопасностью** , определенным в тестовом производительности системы безопасности Azure, и связанным рекомендациям, относящимся к NAT виртуальной сети. **Элементы управления** , неприменимые к NAT виртуальной сети, были исключены.

 
Сведения о том, как виртуальная сеть NAT полностью сопоставляется с тестовым уровнем безопасности Azure, см. в [полном файле сопоставления базового уровня безопасности NAT виртуальной сети](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: безопасность сети](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

**Руководство**. Виртуальная сеть NAT не поддерживает управление исходящим трафиком с помощью групп безопасности сети (NSG).  Входящий трафик разрешен только в ответ на исходящий исходный поток.

Однако журналы потоков группы безопасности сети (NSG) можно использовать с ресурсами шлюза NAT для мониторинга исходящего трафика.

Используйте центр безопасности Azure и следуйте рекомендациям по защите сети, чтобы помочь защитить сетевые ресурсы Azure. Включите журналы потоков для групп безопасности сети и отправьте журналы в учетную запись хранения Azure для аудита. Кроме того, можно отправить журналы потоков в рабочую область Log Analytics, а затем использовать Аналитика трафика для получения сведений о шаблонах трафика в облаке Azure. Некоторые преимущества Аналитика трафика — возможность визуализировать сетевые активности, выявлять горячие участки и угрозы безопасности, анализировать шаблоны потоков трафика и указывать несетевые настройки. 

- [Общие сведения о NAT для виртуальной сети](https://docs.microsoft.com/azure/virtual-network/nat-overview)

- [Ресурс шлюза NAT](https://docs.microsoft.com/azure/virtual-network/nat-gateway-resource)

- [Включение журналов потоков для групп безопасности сети](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal) 

- [Как включить и использовать Аналитика трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Общие сведения о безопасности сети, предоставляемой центром безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="15-record-network-packets"></a>1,5: запись сетевых пакетов

**Руководство**. Включение записи пакетов наблюдателя за сетями для изучения аномальных действий. 

- [Создание экземпляра наблюдателя за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9. Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Определение и реализация стандартных конфигураций безопасности для подсетей, настроенных с помощью ресурсов ШЛЮЗа NAT, с использованием встроенных или настраиваемых определений и назначений политик Azure.

- [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Примеры политик Azure для работы в сети](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование журнала действий Azure для мониторинга конфигураций ресурсов и обнаружения изменений в ресурсах шлюза NAT и ресурсах виртуальной сети. Создайте оповещения в Azure Monitor, чтобы получать уведомления при изменении критических ресурсов.

- [Просмотр и получение событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Как создать оповещения в службе Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в статье [производительность системы безопасности Azure: ведение журнала и мониторинг](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="22-configure-central-security-log-management"></a>2.2. Настройка централизованного управления журналами безопасности

**Рекомендации**. прием журналов, связанных с NAT виртуальной сети, с помощью Azure Monitor для объединения данных безопасности, создаваемых устройствами конечных точек, сетевыми ресурсами и другими системами безопасности. В Azure Monitor используйте рабочие области Log Analytics для запроса и выполнения анализа и используйте учетные записи хранения Azure для долгосрочного и архивного хранения.

Кроме того, вы можете включить эти данные в Azure Sentinel или SIEM стороннего производителя.

- [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard) 

- [Как получить журналы и метрики платформы с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 

- [Начало работы с Azure Monitor и интеграция SIEM стороннего производителя](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Мониторинг Центра безопасности Azure**. Да

**Ответственность**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Включение журналов аудита для ресурсов Azure

**Руководство**. журналы действий, которые автоматически доступны, содержат все операции записи (размещение, публикация, удаление) для ресурсов шлюза NAT, за исключением операций чтения (Get). Журналы действий можно использовать для поиска ошибок при устранении неполадок или для мониторинга того, как пользователь в вашей организации изменил ресурс.

- [Как получить журналы и метрики платформы с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 

- [Общие сведения о ведении журналов и различных типах журналов в Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview) 

В настоящее время NAT виртуальной сети не создает никаких дополнительных журналов диагностики, которые настраиваются клиентами.

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Включение оповещений для аномальных действий

**Руководство**. Использование центра безопасности Azure с рабочей областью log Analytics для мониторинга и оповещения о аномальных действиях, обнаруженных в журналах и событиях безопасности. Вместо этого можно включить и встроенные данные в Azure Sentinel.

- [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard) 

- [Управление оповещениями в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) 

- [Как оповещать данные журнала Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: Инвентаризация и управление активами](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1. Использование автоматизированного решения для обнаружения ресурсов

**Руководство**. Использование графа ресурсов Azure для запроса или обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов и протоколов и т. д.) в подписках. 

Обеспечьте соответствующие разрешения (чтение) в клиенте и перечислите все подписки и ресурсы Azure в ваших подписках.

Хотя классические ресурсы Azure могут быть обнаружены с помощью графа ресурсов, настоятельно рекомендуется создавать и использовать ресурсы на основе Azure Resource Manager.

- [Запросы графа ресурсов Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2. Ведение метаданных активов

**Руководство**. применение тегов к ресурсам Azure, группам ресурсов и подпискам для логической организации их в таксономию. Каждый тег состоит из пары "имя — значение". Например, имя Environment и значение Production можно применить ко всем ресурсам в рабочей среде.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Удаление неавторизованных ресурсов Azure

**Руководство**. применение тегов для управления ресурсами Azure.

- [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) 

- [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create) 

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: определение и обслуживание инвентаризации утвержденных ресурсов Azure

**Руководство**: Сейчас недоступно

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5. Отслеживание неутвержденных ресурсов Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в ваших подписках.
Используйте Azure Resource Graph для запроса или обнаружения ресурсов в подписках. Убедитесь в том, что все ресурсы Azure, представленные в среде, утверждены.

- [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) 
- [Как создавать запросы с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9. Использование только утвержденных служб Azure

**Руководство**. Использование политики Azure для ограничения служб, которые можно подготавливать в вашей среде.

- [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Как отказаться от определенного типа ресурса с помощью Политики Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="secure-configuration"></a>Настройка безопасности

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: безопасная конфигурация](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3. Сохранение безопасных конфигураций для ресурсов Azure

**Руководство**: неприменимо; В NAT виртуальной сети нет конфигураций безопасности.

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5. Безопасное хранение конфигурации ресурсов Azure

**Руководство**. Использование Azure DevOps для безопасного хранения и управления кодом, например пользовательскими определениями политик Azure, Azure Resource Manager шаблонами и скриптами настройки требуемого состояния. Чтобы получить доступ к ресурсам, которыми вы управляете в Azure DevOps, вы можете предоставить или отклонить разрешения для определенных пользователей, встроенных групп безопасности или групп, определенных в Azure Active Directory (Azure AD), если они интегрированы с Azure DevOps, или Active Directory, если они интегрированы с TFS. 

- [Как хранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [О разрешениях и группах в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: развертывание средств управления конфигурацией для ресурсов Azure

**Руководство**: неприменимо; В NAT виртуальной сети нет конфигураций безопасности.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="incident-response"></a>реагирование на инциденты.

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Создание руководства по реагированию на инциденты

**Руководство**. для предложения необходимо создать процедуру реагирования на инциденты, чтобы обеспечить выполнение соответствующих процессов реагирования на инциденты, чтобы он получал соответствующий уровень приоритета до разрешения инцидентов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Создание процедуры оценки инцидента и определения приоритетов

**Руководство**. для предложения необходимо создать оценку инцидента и процедуру определения приоритетов, чтобы обеспечить адекватный риск или оценку угроз, чтобы получить соответствующий уровень приоритета до разрешения инцидентов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="103-test-security-response-procedures"></a>10.3. Проверка процедур реагирования на угрозы

**Руководство**. процедура реагирования на систему безопасности инцидента должна быть создана и протестирована для предложения, чтобы обеспечить правильное устранение рисков с соответствующим уровнем приоритета за счет прогнозируемого разрешения инцидентов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Предоставление контактных сведений и настройка уведомлений по инцидентам безопасности

**Руководство**. процедура реагирования на систему безопасности инцидента должна быть создана и протестирована для предложения, чтобы обеспечить правильное устранение рисков с соответствующим уровнем приоритета за счет прогнозируемого разрешения инцидентов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**. процедура реагирования на систему безопасности инцидента должна быть создана и протестирована для предложения, чтобы обеспечить правильное устранение рисков с соответствующим уровнем приоритета за счет прогнозируемого разрешения инцидентов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6. Автоматизация реагирования на оповещения системы безопасности

**Руководство**. процедура реагирования на систему безопасности инцидента должна быть создана и протестирована для предложения, чтобы обеспечить правильное устранение рисков с соответствующим уровнем приоритета за счет прогнозируемого разрешения инцидентов.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в статье [тесты производительности системы безопасности Azure: испытания на проникновение и команды красных команд](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критических результатов безопасности.

**Рекомендации**. Следуйте правилам тестирования уязвимости Microsoft Cloud, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт. Используйте стратегию и исполнение Microsoft, а затем протестировать проникновение в реальном времени для управляемой корпорацией Майкрософт облачной инфраструктуры, служб и приложений. 

- [Правила тестирования уязвимости Задействований](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud красное объединение](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="next-steps"></a>Дальнейшие действия

- См. [Обзор производительности системы безопасности Azure v2](/azure/security/benchmarks/overview)
- Дополнительные сведения о [базовой конфигурации безопасности Azure](/azure/security/benchmarks/security-baselines-overview).
