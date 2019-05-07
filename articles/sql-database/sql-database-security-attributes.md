---
title: Атрибуты безопасности для базы данных SQL Azure
description: Контрольный список атрибутов безопасности для оценки базы данных SQL Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: df1ffa07c9b813ee3da4952bbcc394f43c69b7ac
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204244"
---
# <a name="security-attributes-for-azure-sql-database"></a>Атрибуты безопасности для базы данных SQL Azure

В этой статье описываются наиболее распространенные атрибуты безопасности, встроенные в базу данных SQL Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

База данных SQL Azure включает в себя [отдельной базы данных](sql-database-single-index.yml) и [управляемый экземпляр](sql-database-managed-instance.md). В следующих операциях применяются к оба предложения, за исключением особо.

## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Шифрование при хранении:<ul><li>Шифрование на стороне сервера</li><li>Шифрование на стороне сервера с использованием управляемых клиентом ключей</li><li>Другие возможности шифрования (например, на стороне клиента, постоянное шифрование и т. д.)</ul>| Yes | Называется «шифрование в использование», как описано в статье [Always Encrypted](sql-database-always-encrypted.md). Шифрование на стороне службы использует [прозрачное шифрование данных](transparent-data-encryption-azure-sql.md) (TDE).|
| Шифрование при передаче:<ul><li>Шифрование ExpressRoute</li><li>Шифрование в виртуальной сети</li><li>Шифрование между виртуальными сетями</ul>| Yes | С использованием HTTPS. |
| Обработка ключа шифрования (CMK, BYOK и т. д.)| Yes | Предлагаются управляемые службы и управляемых пользователем ключей обработки (последний через [Azure Key Vault](../key-vault/index.yml). |
| Шифрование на уровне столбцов (службы данных Azure)| Yes | Через [Always Encrypted](sql-database-always-encrypted.md). |
| Вызовы API в зашифрованном виде| Yes | С помощью HTTPS/SSL. |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Поддержка конечной точки службы| Yes | Применяется к [отдельной базы данных](sql-database-single-index.yml) только. |
| Поддержка внедрения виртуальной сети| Yes | Применяется к [управляемый экземпляр](sql-database-managed-instance.md) только. |
| Сетевая изоляция / Поддержка защиты брандмауэром| Yes | Брандмауэр в оба уровня базы данных и сервера; Сетевая изоляция для [управляемый экземпляр](sql-database-managed-instance.md) только |
| Поддержка принудительного туннелирования | Yes | [управляемый экземпляр](sql-database-managed-instance.md) через [Azure ExpressRoute](../expressroute/index.yml) VPN |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Azure Monitor поддержки (Log analytics, Application insights, и т.д.)| Yes | Сторонние решения SIEM от компании Imperva (SecureSphere) также поддерживается, с помощью [концентраторов событий](../event-hubs/index.yml) интеграция через [Подсистема аудита SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Управление доступом — аутентификация| Yes | Azure Active Directory; |
| Управление доступом — авторизация| Yes |  |


## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Ведение журнала и аудит плана управления и контроля| Yes | Да, некоторые только для событий. |
| Ведение журнала и аудит плоскости данных | Yes | С помощью [Подсистема аудита SQL](sql-database-auditing.md). |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка конфигурации management (Управление версиями конфигурации и т. д.)| Нет   | | 

## <a name="additional-security-attributes-for-sql-database"></a>Атрибуты дополнительную безопасность для базы данных SQL

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Профилактических: оценка уязвимостей | Yes | См. в разделе [оценка уязвимостей SQL, служба поможет вам определить уязвимости базы данных](sql-vulnerability-assessment.md). |
| Профилактических: данные обнаружения и классификации  | Yes | См. в разделе [базы данных SQL Azure и хранилище данных SQL обнаружение и классификация данных](sql-database-data-discovery-and-classification.md). |
| Обнаружения: обнаружение угроз | Yes | См. в разделе [Advanced Threat Protection для базы данных Azure SQL](sql-database-threat-detection-overview.md). |
