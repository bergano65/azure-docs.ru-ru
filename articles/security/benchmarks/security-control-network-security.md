---
title: Контроль безопасности Azure - Сетевая безопасность
description: Безопасность сети безопасности безопасности
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251878"
---
# <a name="security-control-network-security"></a>Контроль безопасности: Сетевая безопасность

Рекомендации по сетевой безопасности сосредоточены на определении того, какие сетевые протоколы, порты TCP/UDP и сетевые службы разрешены или отключены от доступа к службам Azure.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Защита ресурсов, использующих группы сетевой безопасности или брандмауэр Azure в виртуальной сети

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1,1 | 9.2, 9.4, 14.1-14.3 | Customer |

Убедитесь, что все развертывания подсети Виртуальной сети имеют группу сетевой безопасности, применяемую с элементами управления сетевого доступа, специфичными для надежных портов и источников вашего приложения. Используйте службы Azure с включенной частной ссылкой, развертывайте службу внутри Vnet или подключайтесь в частном порядке с помощью частных конечных точек. Для конкретных требований службы, пожалуйста, обратитесь к рекомендации безопасности для этой конкретной службы.

Кроме того, если у вас есть конкретный случай использования, требования могут быть выполнены путем внедрения Azure Firewall.

Общая информация о частной ссылке:

https://docs.microsoft.com/azure/private-link/private-link-overview

Как создать виртуальную сеть:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Как создать NSG с конфигурацией безопасности:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Как развернуть и настроить брандмауэр Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Мониторинг и регистрация конфигурации и трафика Vnets, Subnets и NICs

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.2 | 9.3, 12.2 | Customer |

Используйте Центр безопасности Azure и следуйте рекомендациям по защите сетей, чтобы помочь обезопасить сетевые ресурсы в Azure. Включите журналы потока NSG и отправьте журналы в учетную запись хранения для аудита трафика.

Как включить журналы потока NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Понимание сетевой безопасности, предоставляемой Центром безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3: Защита критически важных веб-приложений

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1,3 | 9,5 | Customer |

Развертывание брандмауэра Web Application Firewall Azure (WAF) перед критическими веб-приложениями для дополнительной проверки входящего трафика. Включить диагностическую настройку для WAF и глотать журналы в учетную запись хранения, концентратор событий или рабочее пространство аналитики журнала.

Как развернуть Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Запретить связь с известными вредоносными IP-адресами

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.4 | 12,3 | Customer |

Включите стандартную защиту DDoS в виртуальных сетях Azure для защиты от DDoS-атак. Используйте Azure Security Center Integrated Threat Intelligence, чтобы отказать в связи с известными вредоносными IP-адресами.

Развертывание Брандмауэра Azure на каждой из границ сети организации &quot;с&quot; включенным и настроенным на оповещение и отрицание вредоносного сетевого трафика.

Используйте Azure Security Center Just In Time Network для настройки НСГ, чтобы ограничить экспозицию конечных точек для утвержденных IP-адресов в течение ограниченного периода времени.

Используйте адаптивную сеть Azure Security Center, чтобы рекомендовать конфигурации NSG, ограничивающие порты и исходные мпвора на основе фактического анализа трафика и угроз.

Как настроить DDoS-защиту:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Как развернуть брандмауэр Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Поймите Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Поймите закаливание адаптивной сети Центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Понять Центр безопасности Azure как раз во время управления доступом к сети:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5: Запись сетевых пакетов и журналов потока

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.5 | 12.5, 15.8 | Customer |

Запись журналов потока NSG в учетную запись хранилища для генерации записей потока. При необходимости исследования аномальной активности включите пакет пакетnetwork-наблюдатель Network Watcher.

Как включить журналы потока NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Как включить сетевой наблюдатель:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Развертывание сетевых систем обнаружения/предотвращения вторжений (IDS/IPS)

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.6 | 12.6, 12.7 | Customer |

Развертывание Брандмауэра Azure на каждой из границ сети организации &quot;с&quot; включенным и настроенным на оповещение и отрицание вредоносного сетевого трафика.

Как развернуть брандмауэр Azure:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Как настроить оповещения с помощью брандмауэра Azure:https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Управление трафиком для веб-приложений

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.7 | 12.9, 12.10 | Customer |

Развертывание шлюза приложений Azure для веб-приложений с включенным httpS/SSL для надежных сертификатов.

Как развернуть шлюз приложений:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Как настроить шлюз приложения для использования HTTPS:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Понять уровень 7 балансировки нагрузки с azure веб-приложений шлюзов:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Минимизация сложности и административных накладных расходов на правила сетевой безопасности

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.8 | 1.5 | Customer |

Используйте тегов виртуальных сетевых служб для определения элементов управления доступом к сети в группах сетевой безопасности или брандмауэре Azure Firewall. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указывая имя тега службы (например, ApiManagement) в соответствующем поле источника или назначения правила, можно разрешить или отклонить трафик для соответствующей службы. Корпорация Майкрософт управляет адресными префиксами, охватываемыми тегом службы, и автоматически обновляет тег службы при изменении адресов.

Понять и использовать теги службы:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Поддержание стандартных конфигураций безопасности для сетевых устройств

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.9 | 11.1 | Customer |

Определите и внедрить стандартные конфигурации безопасности для сетевых ресурсов с помощью политики Azure.

Можно также использовать планы Azure для упрощения крупномасштабных развертываний Azure путем упаковки ключевых элементов среды, таких как шаблоны управления ресурсами Azure, элементы управления RBAC и политики, в единое определение чертежа. Вы можете применить план к новым подпискам и тонкой настройки управления и управления с помощью версий.

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Образцы политики Azure для сетей:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Как создать план Azure:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10: Правила конфигурации трафика документов

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1,1 | 11.2 | Customer |

Используйте теги для НСК и других ресурсов, связанных с сетевой безопасностью и потоком трафика. Для отдельных правил NSG используйте поле &quot;описания,&quot; чтобы указать бизнес-потребности и/или продолжительность (и т.д.) для любых правил, разрешающие трафик в/из сети.

Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Как создать виртуальную сеть:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Как создать NSG с config безопасности:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Используйте автоматизированные инструменты для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.11 | 11,3 | Customer |

Используйте политику Azure для проверки (и/или исправления) конфигурации для сетевых ресурсов.

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Образцы политики Azure для сетей:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>Дальнейшие действия

- Посмотреть следующий контроль безопасности: [Журнал и мониторинг](security-control-logging-monitoring.md)
