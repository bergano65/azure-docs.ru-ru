---
title: Управление безопасностью Azure — Сетевая безопасность
description: Безопасность сети управления безопасностью Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dad01212be3589af7167082ff22c624fa776772a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82193128"
---
# <a name="security-control-network-security"></a>Управление безопасностью: Сетевая безопасность

Рекомендации по сетевой безопасности позволяют указать, какие сетевые протоколы, порты TCP/UDP и подключенные к сети службы разрешают или отклонили доступ к службам Azure.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: защита ресурсов Azure в виртуальных сетях

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1, 14,2, 14,3 | Customer |

Убедитесь, что все развертывания подсетей виртуальных сетей имеют группу безопасности сети, применяемую к элементам управления доступом к сети, относящимся к доверенным портам и источникам приложения. Если это возможно, используйте частные конечные точки с закрытой ссылкой для защиты ресурсов службы Azure в виртуальной сети путем расширения удостоверения виртуальной сети для службы. Если частные конечные точки и частная ссылка недоступны, используйте конечные точки службы. Требования к конкретной службе см. в рекомендации по безопасности для этой конкретной службы. 

Кроме того, при наличии определенного варианта использования требование может быть удовлетворено реализацией брандмауэра Azure.

- [Общие сведения о конечных точках службы виртуальной сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Общие сведения о частной ссылке Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Создание виртуальной сети](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Создание NSG с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Развертывание и настройка брандмауэра Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 1.2 | 9,3, 12,2, 12,8 | Customer |

Используйте центр безопасности Azure и следуйте рекомендациям по защите сети, чтобы помочь защитить сетевые ресурсы в Azure. Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправить журналы потоков NSG в рабочую область Log Analytics и использовать Аналитика трафика для получения ценных сведений о потоке трафика в облаке Azure. Некоторые преимущества Аналитики трафика — это возможность визуализировать сетевые активности и определять горячие участки, выявлять угрозы безопасности, анализировать шаблоны потоков трафика и выявлять некорректные сетевые настройки.

- [Как включить журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Как включать и использовать Аналитику трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Общие сведения о безопасности сети, предоставляемой центром безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3. Защита критических веб-приложений

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 1.3 | 9,5 | Customer |

Развертывание брандмауэра веб-приложения Azure (WAF) перед критически важными веб-приложениями для дополнительной проверки входящего трафика. Включите параметр диагностики для WAF и приема журналов в учетную запись хранения, концентратор событий или рабочую область Log Analytics.

- [Развертывание WAF Azure](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4. Запрет взаимодействия с известными опасными IP-адресами

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 1.4 | 12,3 | Customer |

Включите стандартную защиту от атак DDoS в виртуальных сетях Azure, чтобы защититься от атак от атак DDoS. Используйте интегрированную аналитику угроз центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными IP-адресами.

Разверните брандмауэр Azure на всех границах сети Организации с включенной логикой "предупреждения и отказ" для вредоносного сетевого трафика.

Используйте центр безопасности Azure JIT-доступ к сети, чтобы настроить группы безопасности сети, чтобы ограничить раскрытие конечных точек с утвержденными IP адресами в течение ограниченного периода времени.

Используйте адаптивную защиту сети в центре безопасности Azure, чтобы рекомендовать NSG конфигурации, ограничивающие порты и исходные IP-адреса на основе фактического трафика и аналитики угроз.

- [Настройка защиты от атак DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Развертывание брандмауэра Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Общие сведения об интегрированной аналитике угроз в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Общие сведения о адаптивной защите сети в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Общие сведения об управлении доступом к сети в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1,5: запись сетевых пакетов

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 1.5 | 12,5 | Customer |

Включите запись пакетов наблюдателя за сетями для изучения аномальных действий.

- [Как включить Наблюдатель за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Развертывание сетевых систем обнаружения и предотвращения вторжений (IDS/IPS)

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 1.6 | 12,6, 12,7 | Customer |

Выберите предложение из Azure Marketplace, которое поддерживает функции ИДЕНТИФИКАТОРов и IP-адресов с возможностями проверки полезной нагрузки.  Если система обнаружения или предотвращения вторжений на основе проверки атакующего кода не является обязательной, можно использовать Брандмауэр Azure с аналитикой угроз. Фильтрация Брандмауэра Azure на основе аналитики угроз может создавать оповещения и запрещать трафик, поступающий с известных вредоносных IP-адресов и доменов, а также передающий на них. IP-адреса и домены также передаются из канала Microsoft Threat Intelligence.

Разверните выбранное решение брандмауэра на каждом из границ сети вашей организации, чтобы обнаружить и/или отклонить вредоносный трафик.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Развертывание брандмауэра Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Настройка оповещений с помощью брандмауэра Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7. Управление трафиком к веб-приложениям

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 1.7 | 12,9, 12,10 | Customer |

Разверните шлюз приложений Azure для веб-приложений с включенным протоколом HTTPS/TLS для доверенных сертификатов.

- [Развертывание шлюза приложений](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [Настройка шлюза приложений для использования протокола HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [Общие сведения о балансировке нагрузки уровня 7 с помощью шлюзов веб-приложений Azure](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Уменьшение сложности и дополнительных затрат на администрирование в правилах безопасности сети

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 1.8 | 1.5 | Customer |

Используйте теги службы виртуальной сети для определения элементов управления доступом к сети для групп безопасности сети или брандмауэра Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, ApiManagement) в соответствующем исходном поле или поле назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, входящих в тег службы, и автоматически обновляет этот тег при изменении адресов.

Группы безопасности приложений также можно использовать для упрощения сложной настройки безопасности. Группы безопасности приложений позволяют настроить сетевую безопасность как естественное расширение в структуре приложения. Это позволяет группировать виртуальные машины и определять политики безопасности сети на основе таких групп.

- [Общие сведения и использование тегов служб](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Общие сведения и использование групп безопасности приложений](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9. Поддержание стандартных конфигураций безопасности для сетевых устройств

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 1.9 | 11,1 | Customer |

Определение и реализация стандартных конфигураций безопасности для сетевых ресурсов с помощью политики Azure.

Вы также можете использовать схемы Azure для упрощения крупномасштабных развертываний Azure с помощью ключевых артефактов среды пакетов, таких как шаблоны диспетчера ресурсов Azure, элементы управления RBAC и политики, в одном определении схемы. Вы можете применить схему к новым подпискам, а также настроить управление и управление с помощью управления версиями.

- [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Примеры политик Azure для работы в сети](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Создание схемы Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10. Документация по правилам конфигурации трафика

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 1,10 | 11.2 | Customer |

Используйте теги для группы безопасности сети и других ресурсов, связанных с сетевой безопасностью и потоком трафика. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и длительность (и т. д.) любых правил, которые разрешают трафик в сеть и из нее.

Используйте любое из встроенных определений политик Azure, связанных с тегами, например "требовать тег и его значение", чтобы убедиться, что все ресурсы созданы с помощью тегов и уведомлять вас о существующих ресурсах без тегов.

Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий с ресурсами на основе их тегов.

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Создание виртуальной сети](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Создание группы безопасности сети с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 1.11 | 11,3 | Customer |

Используйте журнал действий Azure для мониторинга конфигураций ресурсов и обнаружения изменений в ресурсах Azure. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критических ресурсов.

- [Как просматривать и извлекать события журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Как создать оповещения в службе Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>Дальнейшие действия

- См. следующий контроль безопасности: [ведение журнала и мониторинг](security-control-logging-monitoring.md)
