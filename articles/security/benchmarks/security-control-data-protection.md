---
title: Управление безопасностью Azure — защита данных
description: Защита данных в системе безопасности
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934520"
---
# <a name="security-control-data-protection"></a>Управление безопасностью: защита данных

Рекомендации по защите данных касаются проблем, связанных с шифрованием, списками управления доступом, контролем доступа на основе удостоверений и ведения журнала аудита для доступа к данным.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: поддержание инвентаризации конфиденциальной информации

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 4.1 | Версия 13.1 | Customer |

Используйте теги для отслеживания ресурсов Azure, в которых хранятся или обрабатываются конфиденциальные данные.

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Изолируйте системы, хранящие или обрабатывающие конфиденциальные данные

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 4.2 | 13.2 | Customer |

Реализуйте отдельные подписки и группы управления для разработки, тестирования и производства. Ресурсы должны быть разделены виртуальной сетью или подсетью, помечены соответствующим образом и защищены брандмауэром NSG или Azure. Ресурсы, которые хранят или обрабатывают конфиденциальные данные, должны быть достаточно изолированы. Для виртуальных машин, которые хранят или обрабатывают конфиденциальные данные, реализуйте политику и процедуры, чтобы отключить их, когда они не используются.

Как создать дополнительные подписки Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание Группы управления:

https://docs.microsoft.com/azure/governance/management-groups/create

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Создание виртуальной сети.

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Создание NSG с конфигурацией безопасности:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Как развернуть брандмауэр Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Как настроить оповещение или оповещение и запретить его с помощью брандмауэра Azure:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Отслеживайте и блокируйте несанкционированную пересылку конфиденциальной информации

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 4.3 | 13.3 | Customer |

Развертывайте автоматизированное средство на периметре сети, которое отслеживает несанкционированную передачу конфиденциальной информации и блокирует такие передачи при оповещении специалистов по информационной безопасности.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: шифрование всех конфиденциальных данных во время передачи

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 4.4. | 14.4 | Общая |

Зашифруйте всю конфиденциальную информацию во время передачи. Убедитесь, что все клиенты, подключающиеся к ресурсам Azure, могут согласовать TLS 1,2 или более поздней версии.

Следуйте рекомендациям центра безопасности Azure для шифрования неактивных данных и шифрования при передаче, где это применимо.

Общие сведения о шифровании при передаче с помощью Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: использование активного средства обнаружения для распознавания конфиденциальных данных

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 4.5. | 14,5 | Customer |

Если для вашей службы в Azure нет доступных функций, используйте средство активного обнаружения стороннего производителя для обнаружения всех конфиденциальных данных, хранимых, обрабатываемых или передаваемых технологическими системами Организации, включая те, которые находятся на сайте или в Удаленный поставщик услуг и обновляет данные инвентаризации конфиденциальной информации Организации.

Используйте Azure Information Protection для идентификации конфиденциальной информации в документах Office 365.

Используйте Information Protection Azure SQL для помощи в классификации и маркировке информации, хранящейся в базах данных SQL Azure.

Как реализовать обнаружение данных SQL Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Как реализовать Azure Information Protection:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6. Использование Azure RBAC для управления доступом к ресурсам

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 4.6 | 14,6 | Customer |

Используйте Azure AD RBAC для управления доступом к данным и ресурсам. в противном случае используйте специфические методы управления доступом к службам.

Общие сведения об Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Как настроить RBAC в Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7. Использование защиты от потери данных на основе узла для обеспечения контроля доступа

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 4,7 | 14,7 | Customer |

Реализуйте сторонние средства, такие как автоматизированное решение для защиты от потери данных на основе узлов, чтобы принудительно применять элементы управления доступом к данным даже при копировании данных из системы.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: шифрование конфиденциальной информации при хранении

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 4.8 | 14,8 | Customer |

Используйте шифрование неактивных ресурсов на всех ресурсах Azure. Корпорация Майкрософт рекомендует разрешить Azure управлять ключами шифрования, однако существует возможность управлять собственными ключами в некоторых экземплярах. 

Общие сведения о шифровании неактивных в Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Как настроить ключи шифрования, управляемые клиентом:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: журналы и оповещения об изменениях критических ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 4,9 | 14,9 | Customer |

Используйте Azure Monitor с журналом действий Azure, чтобы создавать оповещения о том, когда изменения выполняются с важными ресурсами Azure.

Создание оповещений для событий журнала действий Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Дальнейшие действия

См. следующий контроль безопасности: [Управление уязвимостью](security-control-vulnerability-management.md)
