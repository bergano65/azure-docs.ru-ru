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
ms.openlocfilehash: 71e87a3295a9cd73dca2f97b3fa04a5d5ff76f84
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798687"
---
# <a name="security-attributes-for-azure-sql-database"></a>Атрибуты безопасности для базы данных SQL Azure

В этой статье описываются распространенные атрибуты безопасности, встроенные в базу данных SQL Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

База данных SQL включает в себя [отдельной базы данных](sql-database-single-index.yml) и [управляемый экземпляр](sql-database-managed-instance.md). Чтобы оба предложения, за исключением оговоренных применяются следующие записи.

## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Шифрование при хранении:<ul><li>Шифрование на стороне сервера</li><li>Шифрование на стороне сервера с использованием управляемых клиентом ключей</li><li>Другие функции шифрования, такие как на стороне клиента или постоянного шифрования</ul>| Да | Вызывается «шифрования занят,», как описано в статье [Always Encrypted](sql-database-always-encrypted.md). Шифрование на стороне сервера использует [прозрачное шифрование данных](transparent-data-encryption-azure-sql.md).|
| Шифрование при передаче:<ul><li>Шифрование Azure ExpressRoute</li><li>Шифрование в виртуальной сети</li><li>Шифрование между виртуальными сетями</ul>| Да | С использованием HTTPS. |
| Обработка, например CMK или BYOK ключ шифрования| Да | Предлагаются управляемые службы и управляемых пользователем ключей обработки. Последнее, предлагается через [Azure Key Vault](../key-vault/index.yml). |
| Шифрование на уровне столбцов, предоставляемых службами данных Azure| Да | Через [Always Encrypted](sql-database-always-encrypted.md). |
| Зашифрованные вызовов API| Да | С помощью HTTPS/SSL. |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Поддержка конечной точки службы| Да | Применяется к [отдельной базы данных](sql-database-single-index.yml) только. |
| Поддержка внедрения Azure виртуальной сети| Да | Применяется к [управляемый экземпляр](sql-database-managed-instance.md) только. |
| Сетевая изоляция и поддержка брандмауэра| Да | Брандмауэр на уровне сервера и уровне базы данных. Изоляция сети предназначена для [управляемый экземпляр](sql-database-managed-instance.md) только. |
| Принудительного туннелирования поддержки| Да | [Управляемый экземпляр](sql-database-managed-instance.md) через [ExpressRoute](../expressroute/index.yml) VPN. |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержки, например Log Analytics или Application Insights для мониторинга Azure| Да | SecureSphere, решение SIEM от компании Imperva, также поддерживается через [концентраторов событий](../event-hubs/index.yml) интеграция через [аудита SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Проверка подлинности| Да | Azure Active Directory (Azure AD) |
| Authorization| Да | Нет |

## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Плоскость управления и плоскости управления ведение журнала и аудит| Да | Да, для некоторых событий только |
| Плоскость данных ведение журнала и аудит | Да | С помощью [Подсистема аудита SQL](sql-database-auditing.md) |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией, такие как управление версиями конфигурации| Нет  | Нет |

## <a name="additional-security-attributes-for-sql-database"></a>Атрибуты дополнительную безопасность для базы данных SQL

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Профилактических: оценка уязвимостей | Да | См. в разделе [оценка уязвимостей SQL, служба поможет вам определить уязвимости базы данных](sql-vulnerability-assessment.md). |
| Профилактических: данные обнаружения и классификации  | Да | См. в разделе [базы данных SQL Azure и хранилище данных SQL обнаружение и классификация данных](sql-database-data-discovery-and-classification.md). |
| Обнаружения: обнаружение угроз | Да | См. в разделе [Advanced Threat Protection для базы данных Azure SQL](sql-database-threat-detection-overview.md). |
