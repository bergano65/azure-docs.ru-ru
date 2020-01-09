---
title: Управление безопасностью Azure — Сетевая безопасность
description: Безопасность сети управления безопасностью
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: d052226470042d374544de0b7e1ced4ca0f48a14
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564157"
---
# <a name="security-control-network-security"></a>Управление безопасностью: Сетевая безопасность

Рекомендации по сетевой безопасности позволяют указать, какие сетевые протоколы, порты TCP/UDP и подключенные к сети службы разрешают или отклонили доступ к службам Azure.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Защитите ресурсы с помощью групп безопасности сети или брандмауэра Azure в виртуальной сети.

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 1,1 | 9,2, 9,4, 14,1-14.3 | Customer |

Убедитесь, что все развертывания подсетей виртуальных сетей имеют группу безопасности сети, применяемую к элементам управления доступом к сети, относящимся к доверенным портам и источникам приложения. Используйте службы Azure с включенной частной ссылкой, разверните службу в виртуальной сети или Подключайтесь в частном порядке с помощью частных конечных точек. Требования к конкретной службе см. в рекомендации по безопасности для этой конкретной службы.

Кроме того, если у вас есть конкретный вариант использования, для реализации брандмауэра Azure необходимо выполнить требования.

Общие сведения о частной ссылке: https://docs.microsoft.com/azure/private-link/private-link-overview

Создание виртуальной сети.

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Как создать NSG с конфигурацией безопасности:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Как развернуть и настроить брандмауэр Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых карт

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 1.2 | 9,3, 12,2 | Customer |

Используйте центр безопасности Azure и следуйте рекомендациям по защите сети, чтобы помочь защитить сетевые ресурсы в Azure. Включите журналы потоков NSG и отправьте журналы в учетную запись хранения для аудита трафика.

Как включить журналы потоков NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Общие сведения о безопасности сети, предоставляемой центром безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1,3: защита критически важных веб-приложений

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 1,3 | 9,5 | Customer |

Развертывание брандмауэра веб-приложения Azure (WAF) перед критически важными веб-приложениями для дополнительной проверки входящего трафика. Включите параметр диагностики для WAF и приема журналов в учетную запись хранения, концентратор событий или рабочую область Log Analytics.

Как развернуть Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: запретите обмен данными с известными вредоносными IP-адресами

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 1.4 | 12,3 | Customer |

Включите стандартную защиту от атак DDoS в виртуальных сетях Azure, чтобы защититься от атак от атак DDoS. Используйте интегрированную аналитику угроз центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными IP-адресами.

Разверните брандмауэр Azure на всех границах сети Организации с включенной аналитикой угроз, чтобы &quot;предупреждения и запретить&quot; для вредоносного сетевого трафика.

Используйте центр безопасности Azure JIT-доступ к сети, чтобы настроить группы безопасности сети, чтобы ограничить раскрытие конечных точек с утвержденными IP адресами в течение ограниченного периода времени.

Используйте адаптивную защиту сети в центре безопасности Azure, чтобы рекомендовать NSG конфигурации, ограничивающие порты и исходные IP-адреса на основе фактического трафика и аналитики угроз.

Настройка защиты от атак DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Как развернуть брандмауэр Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Общие сведения о центре безопасности Azure, интегрированной с системой анализа угроз.

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Общие сведения о адаптивной защите сети в центре безопасности Azure

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Общие сведения об управлении доступом к сети в центре безопасности Azure

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1,5: запись сетевых пакетов и журналов потоков

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 1.5 | 12,5, 15,8 | Customer |

Запись журналов потоков NSG в учетную запись хранения для создания записей нефиксированного формата. Если требуется для изучения аномальных действий, включите запись пакетов наблюдателя за сетями.

Включение журналов потоков NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Включение наблюдателя за сетями: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: развертывание систем обнаружения вторжений на основе сети и предотвращения вторжений (ИДЕНТИФИКАТОРы и IP-адреса)

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 1.6 | 12,6, 12,7 | Customer |

Разверните брандмауэр Azure на всех границах сети Организации с включенной аналитикой угроз, чтобы &quot;предупреждения и запретить&quot; для вредоносного сетевого трафика.

Как развернуть брандмауэр Azure: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Настройка оповещений с помощью брандмауэра Azure: https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1,7. Управление трафиком к веб-приложениям

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 1.7 | 12,9, 12,10 | Customer |

Разверните шлюз приложений Azure для веб-приложений с включенным протоколом HTTPS/SSL для доверенных сертификатов.

Развертывание шлюза приложений: https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Настройка шлюза приложений для использования протокола HTTPS: https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Основные сведения о балансировке нагрузки уровня 7 с помощью шлюзов веб-приложений Azure: https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: сократите сложность и издержки администрирования правил безопасности сети

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 1.8 | 1.5 | Customer |

Используйте теги службы виртуальной сети &nbsp;, чтобы определить элементы управления доступом к сети для групп безопасности сети или брандмауэра Azure. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, ApiManagement) в соответствующем поле источника или назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, включенными в тег службы, и автоматически обновляет тег службы при изменении адресов.

Основные сведения и использование тегов служб: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: поддержание стандартных конфигураций безопасности для сетевых устройств

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 1.9 | 11.1 | Customer |

Определение и реализация стандартных конфигураций безопасности для сетевых ресурсов с помощью политики Azure.

Вы также можете использовать схемы Azure для упрощения крупномасштабных развертываний Azure с помощью ключевых артефактов среды пакетов, таких как шаблоны Azure Resource Manager, элементы управления RBAC и политики, в одном определении схемы. Схему можно применить к новым подпискам и средам, а также настроить управление и управление с помощью управления версиями.

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Примеры политики Azure для работы в сети:

https://docs.microsoft.com/azure/governance/policy/samples/#network

Создание Azure Blueprint.

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1,10: правила конфигурации трафика документов

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 1,1 | 11.2 | Customer |

Используйте теги для группы безопасности сети и других ресурсов, связанных с сетевой безопасностью и потоком трафика. Для отдельных правил NSG используйте поле &quot;описание&quot;, чтобы указать бизнес-потребности и (или) Длительность (т. д.) для любых правил, разрешающих входящий и исходящий трафик из сети.

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Создание виртуальной сети.

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Создание NSG с конфигурацией безопасности:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11. использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 1.11 | 11,3 | Customer |

Используйте политику Azure для проверки (и/или исправьте) конфигурации сетевых ресурсов.

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Примеры политики Azure для работы в сети:

https://docs.microsoft.com/azure/governance/policy/samples/#network

## <a name="next-steps"></a>Дальнейшие действия

- См. следующий контроль безопасности: [ведение журнала и мониторинг](security-control-logging-monitoring.md)