---
title: Что такое безопасность на уровне столбцов для Azure Synapse?
description: Безопасность столбцов позволяет клиентам контролировать доступ к столбцам таблиц базы данных на основе контекста выполнения пользователя или членства в группе, упрощая проектирование и кодирование безопасности в приложении, а также позволяя осуществлять ограничения на доступ к столбцу.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: b0a783ad5db86ca783ff1cebceec8d77ab528047
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687923"
---
# <a name="column-level-security"></a>Безопасность на уровне столбцов

Безопасность столбцов позволяет клиентам контролировать доступ к столбцам таблиц на основе контекста выполнения пользователя или членства в группе.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
С момента публикации этого видео [Security Row](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) стал доступен для Azure Synapse.

Безопасность на уровне столбцов упрощает проектирование и кодирование безопасности в приложении, позволяя ограничить доступ столбцов для защиты конфиденциальных данных. Например, можно предоставить конкретным пользователям доступ только к определенным столбцам таблицы, имеющей отношение к их отделу. Логика ограничения находится на уровне базы данных, а не на отдалении от данных на другом уровне приложения. База данных применяет ограничения доступа каждый раз, когда попытка доступа к данным с любого уровня. Это ограничение делает вашу безопасность более надежной и надежной, уменьшая площадь поверхности вашей общей системы безопасности. Кроме того, безопасность на уровне столбцов также устраняет необходимость введения представлений для фильтрации столбцов для наложения ограничений на доступ пользователей.

Вы можете реализовать безопасность на уровне столбцов с помощью оператора [GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-S'L. При использовании этого механизма поддерживаются проверки подлинности SQL и Azure Active Directory (AAD).

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Синтаксис

```syntaxsql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Пример

В следующем примере `TestUser` показано, как `SSN` ограничить `Membership` доступ к столбце таблицы:

Создайте `Membership` таблицу с столбцом SSN, используемым для хранения номеров социального страхования:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Разрешить `TestUser` доступ ко всем столбцам, за исключением столбца SSN, который имеет конфиденциальные данные:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Запросы выполняются `TestUser` как неудав:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Варианты использования

Некоторые примеры использования безопасности на уровне столбцов сегодня:

- В компании, предоставляющей финансовые услуги, обращаться к номерам социального страхования (SSN), номерам телефонов и другим персональным данным клиентов могут только менеджеры по работе с клиентами.
- Поставщик медицинских услуг позволяет только врачам и медсестрам иметь доступ к конфиденциальной медицинской документации, не позволяя сотрудникам биллингового отдела просматривать эти данные.
