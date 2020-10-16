---
title: Средства управления безопасностью
description: Контрольный список средств управления безопасностью для оценки базы данных SQL Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: eec032ad56d00778627fc147761f61c03ba8bafd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442094"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Элементы управления безопасностью для базы данных SQL Azure и Управляемый экземпляр SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

В этой статье описываются элементы управления безопасностью, встроенные в базу данных SQL Azure и Управляемый экземпляр Azure SQL.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Сеть

| Управление безопасностью | Да/нет | Примечания |
|---|---|--|
| Поддержка конечных точек службы| Да | Применяется только к [базе данных SQL](../index.yml) . |
| Поддержка внедрения виртуальных сетей в Azure| Да | Применяется только к [SQL управляемый экземпляр](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Поддержка сетевой изоляции и брандмауэра| Да | Брандмауэр на уровне базы данных и на уровне сервера. Сетевая изоляция предназначена только для [управляемый экземпляр SQL](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Поддержка принудительного туннелирования| Да | [Управляемый экземпляр SQL](../managed-instance/sql-managed-instance-paas-overview.md) через VPN [ExpressRoute](../expressroute/../index.yml) . |

## <a name="monitoring--logging"></a>Мониторинг & ведения журнала

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure, например Log Analytics или Application Insights| Да | Секуресфере, решение SIEM из Imperva также поддерживается через интеграцию [концентраторов событий Azure](../event-hubs/../index.yml) с помощью [аудита SQL](../../azure-sql/database/auditing-overview.md). |
| Мониторинг и аудит в плоскости управления и управления| Да | Да для некоторых событий |
| Ведение журнала и аудит в плоскости данных | Да | С помощью [аудита SQL](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>Идентификация

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Аутентификация| Да | Azure Active Directory (Azure AD) |
| Авторизация| Да | None |

## <a name="data-protection"></a>Защита данных

| Управление безопасностью | Да/нет | Примечания |
|---|---|--|
| Шифрование неактивных на стороне сервера: ключи, управляемые корпорацией Майкрософт | Да | Под названием «шифрование в использовании», как описано в статье [Always encrypted](always-encrypted-certificate-store-configure.md). При шифровании на стороне сервера используется [прозрачное шифрование данных](transparent-data-encryption-tde-overview.md).|
| Шифрование при передаче:<ul><li>Шифрование Azure ExpressRoute</li><li>Шифрование в виртуальной сети</li><li>Шифрование между виртуальными сетями</ul>| Да | С использованием HTTPS. |
| Обработка ключей шифрования, например CMK или BYOK| Да | Предлагаются как управляемая службами, так и управляемая клиентом обработка ключей. Последнее предлагается через [Azure Key Vault](../key-vault/../index.yml). |
| Шифрование на уровне столбцов, предоставляемое службами данных Azure| Да | С помощью [Always encrypted](always-encrypted-certificate-store-configure.md). |
| Зашифрованные вызовы API| Да | Использование HTTPS/TLS. |

## <a name="configuration-management"></a>Управление конфигурацией

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией, например управление версиями конфигурации| Нет  | None |

## <a name="additional-security-controls-for-sql-database"></a>Дополнительные средства управления безопасностью для базы данных SQL

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Превентивные: Оценка уязвимостей | Да | См. раздел [Служба оценки уязвимостей SQL, позволяющая определить уязвимости базы данных](sql-vulnerability-assessment.md). |
| Превентивные: обнаружение и классификация данных  | Да | См. раздел [база данных SQL Azure и обнаружение данных Azure синапсе Analytics & классификация](data-discovery-and-classification-overview.md). |
| Обнаружение: обнаружение угроз | Да | См. [Дополнительные сведения о расширенной защите от угроз для базы данных SQL Azure](threat-detection-overview.md). |

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о [встроенных средствах управления безопасностью в службах Azure](../../security/fundamentals/security-controls.md).
