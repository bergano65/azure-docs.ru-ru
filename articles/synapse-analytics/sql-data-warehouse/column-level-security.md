---
title: Что такое безопасность на уровне столбцов для Azure синапсе?
description: Безопасность на уровне столбцов позволяет клиентам управлять доступом к столбцам таблицы базы данных на основе контекста выполнения или членства пользователя в группе, упрощая проектирование и кодирование безопасности в приложении и позволяя реализовать ограничения на доступ к столбцам.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687923"
---
# <a name="column-level-security"></a>Безопасность на уровне столбцов

Безопасность на уровне столбцов позволяет клиентам управлять доступом к столбцам таблицы на основе контекста выполнения или членства пользователя в группе.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Так как это видео было опубликовано, значит [безопасность на уровне строк](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) стала доступной для Azure синапсе.

Безопасность на уровне столбцов упрощает проектирование и кодирование безопасности в приложении, позволяя ограничить доступ к столбцу для защиты конфиденциальных данных. Например, можно предоставить конкретным пользователям доступ только к определенным столбцам таблицы, имеющей отношение к их отделу. Логика ограничения находится на уровне базы данных, а не на отдалении от данных на другом уровне приложения. База данных применяет ограничения доступа при каждом попыток доступа к данным с любого уровня. Это ограничение повышает надежность и надежность системы безопасности за счет сокращения контактной зоны общей системы безопасности. Кроме того, безопасность на уровне столбцов также устраняет необходимость в добавлении представлений для фильтрации столбцов для установления ограничений доступа пользователей.

Безопасность на уровне столбцов можно реализовать с помощью инструкции T-SQL [Grant](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) . При использовании этого механизма поддерживаются проверки подлинности SQL и Azure Active Directory (AAD).

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

В следующем примере показано, как ограничить `TestUser` доступ к `SSN` столбцу `Membership` таблицы:

Создайте `Membership` таблицу со столбцом SSN, используемый для хранения номеров социального страхования:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Разрешить `TestUser` доступ ко всем столбцам, кроме столбца SSN, который имеет конфиденциальные данные:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Запросы, выполняемые как `TestUser` , завершатся ошибкой, если включают столбец SSN:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Варианты использования

Ниже приведены некоторые примеры использования безопасности на уровне столбцов в настоящее время.

- В компании, предоставляющей финансовые услуги, обращаться к номерам социального страхования (SSN), номерам телефонов и другим персональным данным клиентов могут только менеджеры по работе с клиентами.
- Поставщик услуг здравоохранения обеспечивает доступ к конфиденциальным медицинские записи только в доктора и медсестрам, одновременно запрещая участникам отдела выставления счетов просматривать эти данные.
