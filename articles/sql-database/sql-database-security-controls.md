---
title: Элементы управления безопасностью для базы данных SQL Azure
description: Контрольный список средств управления безопасностью для оценки базы данных SQL Azure
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 77ff55389bac53d8719d86b4ac77f281415af49f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886810"
---
# <a name="security-controls-for-azure-sql-database"></a>Элементы управления безопасностью для базы данных SQL Azure

В этой статье описываются элементы управления безопасностью, встроенные в базу данных SQL Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

База данных SQL включает как [одну базу данных](sql-database-single-index.yml) , так и [управляемый экземпляр](sql-database-managed-instance.md). Следующие записи применяются к обоим предложениям, за исключением случаев, когда указано иное.

## <a name="network"></a>Network

| Управление безопасностью | Да или нет | Примечания |
|---|---|--|
| Поддержка конечных точек службы| Да | Применяется только к [одной базе данных](sql-database-single-index.yml) . |
| Поддержка внедрения виртуальных сетей в Azure| Да | Применяется только к [управляемому экземпляру](sql-database-managed-instance.md). |
| Поддержка сетевой изоляции и брандмауэра| Да | Брандмауэр на уровне базы данных и на уровне сервера. Сетевая изоляция предназначена только для [управляемого экземпляра](sql-database-managed-instance.md) . |
| Поддержка принудительного туннелирования| Да | [Управляемый экземпляр](sql-database-managed-instance.md) через VPN [ExpressRoute](../expressroute/index.yml) . |

## <a name="monitoring--logging"></a>Мониторинг & ведения журнала

| Управление безопасностью | Да или нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure, например Log Analytics или Application Insights| Да | Секуресфере, решение SIEM из Imperva также поддерживается через интеграцию концентраторов [событий Azure](../event-hubs/index.yml) с помощью [аудита SQL](sql-database-auditing.md). |
| Мониторинг и аудит в плоскости управления и управления| Да | Да для некоторых событий |
| Ведение журнала и аудит в плоскости данных | Да | С помощью [аудита SQL](sql-database-auditing.md) |

## <a name="identity"></a>идентификации

| Управление безопасностью | Да или нет | Примечания|
|---|---|--|
| Проверка подлинности| Да | Azure Active Directory (Azure AD) |
| Authorization| Да | Отсутствуют |

## <a name="data-protection"></a>Защита данных

| Управление безопасностью | Да или нет | Примечания |
|---|---|--|
| Шифрование неактивных на стороне сервера: Ключи, управляемые корпорацией Майкрософт | Да | Под названием «шифрование в использовании», как описано в статье [Always encrypted](sql-database-always-encrypted.md). При шифровании на стороне сервера используется [прозрачное шифрование данных](transparent-data-encryption-azure-sql.md).|
| Шифрование при передаче:<ul><li>Шифрование Azure ExpressRoute</li><li>Шифрование в виртуальной сети</li><li>Шифрование между виртуальными сетями</ul>| Да | С использованием HTTPS. |
| Обработка ключей шифрования, например CMK или BYOK| Да | Предлагаются как управляемая службами, так и управляемая клиентом обработка ключей. Последнее предлагается через [Azure Key Vault](../key-vault/index.yml). |
| Шифрование на уровне столбцов, предоставляемое службами данных Azure| Да | С помощью [Always encrypted](sql-database-always-encrypted.md). |
| Зашифрованные вызовы API| Да | Использование HTTPS/SSL. |

## <a name="configuration-management"></a>Управление конфигурацией

| Управление безопасностью | Да или нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией, например управление версиями конфигурации| Нет  | Отсутствуют |

## <a name="additional-security-controls-for-sql-database"></a>Дополнительные средства управления безопасностью для базы данных SQL

| Управление безопасностью | Да или нет | Примечания|
|---|---|--|
| Превентивные: Оценка уязвимостей | Да | См. раздел [Служба оценки уязвимостей SQL, позволяющая определить уязвимости базы данных](sql-vulnerability-assessment.md). |
| Превентивные: обнаружение и классификация данных  | Да | См. раздел [база данных SQL Azure и обнаружение данных хранилища данных sql & Классификация](sql-database-data-discovery-and-classification.md). |
| Обнаружение: обнаружение угроз | Да | См. [Дополнительные сведения о расширенной защите от угроз для базы данных SQL Azure](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [встроенных средствах управления безопасностью в службах Azure](../security/fundamentals/security-controls.md).
