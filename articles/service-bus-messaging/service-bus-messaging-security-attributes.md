---
title: Общие атрибуты безопасности для сообщений служебной шины Azure
description: Контрольный список Общие атрибуты безопасности для оценки сообщений служебной шины Azure
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d68ffe6561da6a23c288dfabd1d3eb6b34099bb3
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003112"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Атрибуты безопасности для сообщений служебной шины Azure

В этой статье описываются атрибуты безопасности, встроенные в сообщений служебной шины Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да или нет | Примечания |
|---|---|--|
| Шифрование при хранении:<ul><li>Шифрование на стороне сервера</li><li>Шифрование на стороне сервера с использованием управляемых клиентом ключей</li><li>Другие возможности шифрования (например, на стороне клиента, постоянное шифрование и т. д.)</ul>|  Да для серверных шифрование на месте по умолчанию. | Управляемые клиентом ключи и BYOK еще не поддерживаются. Шифрование на стороне клиента является обязанностью клиента |
| Шифрование при передаче:<ul><li>Шифрование ExpressRoute</li><li>При шифровании виртуальной сети</li><li>Шифрование между виртуальными сетями</ul>| Да | Поддерживает стандартный механизм HTTPS/TLS. |
| Управление ключами шифрования (CMK, BYOK, и т.д.)| Нет |   |
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | Вызовы API осуществляются через [Azure Resource Manager](../azure-resource-manager/index.yml) и HTTPS. |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да или нет | Примечания |
|---|---|--|
| Поддержка конечной точки службы| Да (только для уровня "премиум") | Конечные точки службы виртуальной сети поддерживаются для [уровня "премиум" Service Bus](service-bus-premium-messaging.md) только. |
| Внедрение поддержки виртуальной сети| Нет | |
| Сетевая изоляция и множество поддержки| Да (только для уровня "премиум") |  |
| Принудительного туннелирования поддержки| Нет |  |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Azure Monitor поддержки (Log analytics, Application insights, и т.д.)| Да | Поддерживаются через [Azure Monitor и оповещения](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Authentication| Да | Управлять с помощью [Azure Active Directory управляемое удостоверение службы](service-bus-managed-service-identity.md); см. в разделе [шины службы проверки подлинности и авторизации](service-bus-authentication-and-authorization.md).|
| Авторизация| Да | Поддержка авторизации с помощью [RBAC](service-bus-role-based-access-control.md) (Предварительная версия) и маркер SAS, см. в разделе [шины службы проверки подлинности и авторизации](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Управление и ведение журнала и аудит| Да | Доступны журналы операций; см. в разделе [журналы диагностики служебной шины](service-bus-diagnostic-logs.md).  |
| Ведение журнала данных и аудита| Нет |  |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Поддержка конфигурации management (Управление версиями конфигурации и т. д.)| Да | Поддерживает управление версиями поставщика ресурсов через [API Azure Resource Manager](/rest/api/resources/).|
