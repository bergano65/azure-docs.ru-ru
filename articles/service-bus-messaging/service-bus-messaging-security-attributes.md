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
ms.openlocfilehash: 11977a5aa36b023e468ce6a54862b5138995c417
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64513517"
---
# <a name="common-security-attributes-for-azure-service-bus-messaging"></a>Общие атрибуты безопасности для сообщений служебной шины Azure

Средства безопасности интегрированы в каждом аспекте службы Azure. В этой статье описываются наиболее распространенные атрибуты безопасности, встроенные в сообщений служебной шины Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Шифрование при хранении:<ul><li>Шифрование на стороне сервера</li><li>Шифрование на стороне сервера с использованием управляемых клиентом ключей</li><li>Другие возможности шифрования (например, на стороне клиента, постоянное шифрование и т. д.)</ul>|  Да для серверных шифрование на месте по умолчанию. | Управляемые клиентом ключи и BYOK еще не поддерживаются. Шифрование на стороне клиента является обязанностью клиента |
| Шифрование при передаче:<ul><li>Шифрование ExpressRoute</li><li>При шифровании виртуальной сети</li><li>Шифрование между виртуальными сетями</ul>| Yes | Поддерживает стандартный механизм HTTPS/TLS. |
| Управление ключами шифрования (CMK, BYOK, и т.д.)| Нет  |   |
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Yes | Вызовы API осуществляются через [Azure Resource Manager](../azure-resource-manager/index.yml) и HTTPS. |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Поддержка конечной точки службы| Да (только для уровня "премиум") | Конечные точки службы виртуальной сети поддерживаются для [уровня "премиум" Service Bus](service-bus-premium-messaging.md) только. |
| Внедрение поддержки виртуальной сети| Нет  | |
| Сетевая изоляция и множество поддержки| Да (только для уровня "премиум") |  |
| Принудительного туннелирования поддержки| Нет  |  |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Azure Monitor поддержки (Log analytics, Application insights, и т.д.)| Yes | Поддерживаются через [Azure Monitor и оповещения](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Authentication| Yes | Управлять с помощью [Azure Active Directory управляемое удостоверение службы](service-bus-managed-service-identity.md); см. в разделе [шины службы проверки подлинности и авторизации](service-bus-authentication-and-authorization.md).|
| Авторизация| Yes | Поддержка авторизации с помощью [RBAC](service-bus-role-based-access-control.md) (Предварительная версия) и маркер SAS, см. в разделе [шины службы проверки подлинности и авторизации](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Управление и ведение журнала и аудит| Yes | Доступны журналы операций; см. в разделе [журналы диагностики служебной шины](service-bus-diagnostic-logs.md).  |
| Ведение журнала данных и аудита| Нет  |  |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка конфигурации management (Управление версиями конфигурации и т. д.)| Yes | Поддерживает управление версиями поставщика ресурсов через [API Azure Resource Manager](/rest/api/resources/).|
