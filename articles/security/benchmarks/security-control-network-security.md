---
title: Контроль безопасности Azure - Сетевая безопасность
description: Сетевая безопасность Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f508de35470043cc71bfc8607367f28c04440b57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408304"
---
# <a name="security-control-network-security"></a>Контроль безопасности: Сетевая безопасность

Рекомендации по сетевой безопасности сосредоточены на определении того, какие сетевые протоколы, порты TCP/UDP и сетевые службы разрешены или отключены от доступа к службам Azure.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Защита ресурсов Azure в виртуальных сетях

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | Customer |

Убедитесь, что все развертывания подсети Виртуальной сети имеют группу сетевой безопасности, применяемую с элементами управления сетевого доступа, специфичными для надежных портов и источников вашего приложения. При наличии доступных данных используйте Private Endpoints с Private Link для защиты ресурсов службы Azure в виртуальной сети, расширяя идентификацию VNet. Если частные конечные точки и частная ссылка недоступны, используйте конечные точки обслуживания. Для конкретных требований службы, пожалуйста, обратитесь к рекомендации безопасности для этой конкретной службы. 

Кроме того, если у вас есть конкретный случай использования, требование может быть выполнено путем внедрения Azure Firewall.

- [Понять конечные точки виртуального сетевого обслуживания](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Понять частную ссылку Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Как создать виртуальную сеть](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Как создать NSG с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Как развернуть и настроить брандмауэр Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Мониторинг и регистрация конфигурации и трафика виртуальных сетей, подсетей и NICs

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | Customer |

Используйте Центр безопасности Azure и следуйте рекомендациям по защите сетей, чтобы помочь обезопасить сетевые ресурсы в Azure. Включите журналы потока NSG и отправьте журналы в учетную запись хранения для аудита трафика. Вы также можете отправлять журналы потоков NSG в рабочее пространство analytics Log и использовать аналитику трафика для получения информации о движении в облаке Azure. Некоторые преимущества Traffic Analytics — это возможность визуализации сетевой активности и выявления горячих точек, выявления угроз безопасности, понимания моделей движения и неправильной настройки сети.

- [Как включить журналы потока NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Как включить и использовать аналитику трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Понимание сетевой безопасности, предоставляемой Центром безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3: Защита критически важных веб-приложений

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1,3 | 9,5 | Customer |

Развертывание брандмауэра Web Application Firewall Azure (WAF) перед критическими веб-приложениями для дополнительной проверки входящего трафика. Включить диагностическую настройку для WAF и глотать журналы в учетную запись хранения, концентратор событий или рабочее пространство аналитики журнала.

- [Как развернуть Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Запретить связь с известными вредоносными IP-адресами

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.4 | 12,3 | Customer |

Включите стандартную защиту DDoS в виртуальных сетях Azure для защиты от DDoS-атак. Используйте Azure Security Center Integrated Threat Intelligence, чтобы отказать в связи с известными вредоносными IP-адресами.

Развертывание Брандмауэра Azure на каждой из границ сети организации с включенным и настроенным на "Оповещение и отрицание" для вредоносного сетевого трафика.

Используйте Azure Security Center Just In Time Network для настройки НСГ, чтобы ограничить экспозицию конечных точек для утвержденных IP-адресов в течение ограниченного периода времени.

Используйте адаптивную сеть Azure Security Center, чтобы рекомендовать конфигурации NSG, ограничивающие порты и исходные мпвора на основе фактического анализа трафика и угроз.

- [Как настроить DDoS-защиту](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Как развернуть брандмауэр Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Понять Azure Центр безопасности Интегрированная разведка угроз](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Понимание закаливания адаптивной сети Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Понять Центр безопасности Azure как раз во время управления доступом к сети](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5: Пакеты сети записи

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.5 | 12,5 | Customer |

Включить захват пакета Network Watcher для расследования аномальных действий.

- [Как включить сетевой наблюдатель](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Развертывание сетевых систем обнаружения/предотвращения вторжений (IDS/IPS)

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.6 | 12.6, 12.7 | Customer |

Выберите предложение от Azure Marketplace, которое поддерживает функциональность IDS/IPS с возможностями проверки полезной нагрузки.  Если обнаружение и/или предотвращение вторжения на основе инспекции полезной нагрузки не является обязательным требованием, можно использовать Брандмауэр Azure и Threat Intelligence. Фильтрация на основе интеллектуальных угроз Azure Firewall может предупреждать и отказывать в трафике на известные вредоносные IP-адреса и домены. IP-адреса и домены также передаются из канала Microsoft Threat Intelligence.

Развертывание решения для брандмауэра по вашему выбору на границах сети вашей организации для обнаружения и/или отказа от вредоносного трафика.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Как развернуть брандмауэр Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Как настроить оповещения с помощью брандмауэра Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Управление трафиком для веб-приложений

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.7 | 12.9, 12.10 | Customer |

Развертывание шлюза приложений Azure для веб-приложений с включенным httpS/SSL для надежных сертификатов.

- [Как развернуть шлюз приложений](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [Как настроить шлюз приложений для использования HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [Понять уровень 7 балансировки нагрузки с azure веб-приложений шлюзов](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Минимизация сложности и административных накладных расходов на правила сетевой безопасности

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.8 | 1.5 | Customer |

Используйте тегов виртуальных сетевых служб для определения элементов управления доступом к сети в группах сетевой безопасности или брандмауэре Azure Firewall. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указывая имя тега службы (например, ApiManagement) в соответствующем поле источника или назначения правила, можно разрешить или отклонить трафик для соответствующей службы. Корпорация Майкрософт управляет адресными префиксами, охватываемыми тегом службы, и автоматически обновляет тег службы при изменении адресов.

Вы также можете использовать группы безопасности приложений, чтобы упростить сложную конфигурацию безопасности. Группы безопасности приложений позволяют настроить сетевую безопасность как естественное расширение в структуре приложения. Это позволяет группировать виртуальные машины и определять политики безопасности сети на основе таких групп.

- [Понимание и использование теги службы](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Понимание и использование групп безопасности приложений](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Поддержание стандартных конфигураций безопасности для сетевых устройств

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.9 | 11.1 | Customer |

Определите и внедрить стандартные конфигурации безопасности для сетевых ресурсов с помощью политики Azure.

Можно также использовать планы Azure для упрощения крупномасштабных развертываний Azure путем упаковки ключевых элементов среды, таких как шаблоны Управления ресурсами Azure, элементы управления RBAC и политики, в единое определение чертежа. Вы можете применить план к новым подпискам, а также к тонкому управлению и управлению с помощью версий.

- [Как настроить и управлять политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Образцы политики Azure для сетей](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Как создать план Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10: Правила конфигурации трафика документов

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1,10 | 11.2 | Customer |

Используйте теги для НСК и других ресурсов, связанных с сетевой безопасностью и потоком трафика. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и/или продолжительность (и т.д.) для любых правил, разрешающие трафик в/из сети.

Используйте любое из встроенных определений политики Azure, связанных с пометкой, например "Требуемый тег и его значение", чтобы гарантировать, что все ресурсы создаются с помощью тегов и уведомлять вас о существующих немаркированных ресурсах.

Вы можете использовать Azure PowerShell или Azure CLI для поиска или выполнения действий на ресурсах на основе их тегов.

- [Как создавать и использовать теги](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Как создать виртуальную сеть](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Как создать NSG с config безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Используйте автоматизированные инструменты для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 1.11 | 11,3 | Customer |

Используйте журнал действий Azure для мониторинга конфигураций ресурсов и обнаружения изменений в ресурсах Azure. Создавайте оповещения в Azure Monitor, которые будут запускаться при изменении критически важных ресурсов.

- [Как просматривать и получать события журнала активности Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Как создать оповещения в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>Дальнейшие действия

- Смотрите следующий контроль безопасности: [журналирование и мониторинг](security-control-logging-monitoring.md)
