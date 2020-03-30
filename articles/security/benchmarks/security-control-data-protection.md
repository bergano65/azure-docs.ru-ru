---
title: Контроль безопасности Azure - Защита данных
description: Защита данных по контролю безопасности
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934520"
---
# <a name="security-control-data-protection"></a>Контроль безопасности: Защита данных

Рекомендации по защите данных сосредоточены на решении проблем, связанных с шифрованием, списками контроля доступа, контролем доступа на основе личных данных и журналом аудита для доступа к данным.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Ведение перечня конфиденциальной информации

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 4.1 | Версия 13.1 | Customer |

Используйте теги для отслеживания ресурсов Azure, которые хранят или обрабатывают конфиденциальную информацию.

Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Изолировать системы хранения или обработки конфиденциальной информации

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 4.2 | 13.2 | Customer |

Внедрение отдельных подписок и/или групп управления для разработки, тестирования и производства. Ресурсы должны быть разделены VNet/Subnet, соответствующим образом помечены и защищены NSG или Azure Firewall. Ресурсы, хранимые или обрабатывающие конфиденциальные данные, должны быть достаточно изолированными. Для виртуальных машин, храняющих или обрабатывающих конфиденциальные данные, реализуйте политику и процедуру(ы), чтобы отключить их, когда они не используются.

Как создать дополнительные подписки Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Как создать группы управления:

https://docs.microsoft.com/azure/governance/management-groups/create

Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Как создать виртуальную сеть:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Как создать NSG с config безопасности:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Как развернуть брандмауэр Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Как настроить оповещения или оповещения и отрицать с Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Мониторинг и блокирование несанкционированной передачи конфиденциальной информации

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 4.3 | 13.3 | Customer |

Развертывание автоматизированного инструмента по периметру сети, который отслеживает несанкционированную передачу конфиденциальной информации и блокирует такие передачи, оповещая специалистов по информационной безопасности.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Шифрование всей конфиденциальной информации в пути

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 4.4. | 14.4 | Shared |

Шифрование всей конфиденциальной информации в пути. Убедитесь, что клиенты, подключенные к ресурсам Azure, смогут вести переговоры с TLS 1.2 или более.

Следуйте рекомендациям Центра безопасности Azure для шифрования в состоянии покоя и шифрования в пути, где это применимо.

Понять шифрование в пути с Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Используйте инструмент активного обнаружения для идентификации конфиденциальных данных

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 4.5. | 14.5 | Customer |

Если функция недоступна для вашей конкретной службы в Azure, используйте сторонний инструмент активного обнаружения для идентификации всей конфиденциальной информации, хранящейся, обрабатываемой или передаваемой технологическими системами организации, включая те, которые расположены на месте или в удаленного поставщика услуг и обновления инвентаря конфиденциальной информации организации.

Используйте Azure Information Protection для идентификации конфиденциальной информации в документах Office 365.

Используйте Azure S'L Protection Для оказания помощи в классификации и маркировке информации, хранящейся в базах данных Azure S'L.

Как реализовать обнаружение данных Лазурного ИЗл:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Как реализовать Azure Информацию:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Используйте Azure RBAC для управления доступом к ресурсам

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 4.6 | 14.6 | Customer |

Используйте Azure AD RBAC для управления доступом к данным и ресурсам, в противном случае используйте специфические методы управления доступом.

Поймите Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Как настроить RBAC в Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Используйте предотвращение потери данных на основе хоста для обеспечения контроля доступа

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 4,7 | 14.7 | Customer |

Внедрить сторонний инструмент, такой как автоматизированное решение по предотвращению потери данных на основе хоста, для обеспечения контроля доступа к данным даже при копировании данных в системе.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Шифрование конфиденциальной информации в состоянии покоя

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 4.8 | 14,8 | Customer |

Используйте шифрование в состоянии покоя на всех ресурсах Azure. Корпорация Майкрософт рекомендует разрешить Azure управлять ключами шифрования, однако в некоторых случаях существует возможность управлять собственными ключами. 

Понимание шифрования в состоянии покоя в Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Как настроить управляемые ключей шифрования клиента:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Войти и предупредить об изменениях в критических ресурсах Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 4.9 | 14.9 | Customer |

Используйте Azure Monitor с журналом активности Azure для создания оповещений о том, когда происходят изменения в критически важных ресурсах Azure.

Как создать оповещения для событий журнала активности Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Дальнейшие действия

Посмотреть следующий элемент управления безопасностью: [Управление уязвимостью](security-control-vulnerability-management.md)
