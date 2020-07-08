---
title: Что такое безопасность на уровне столбцов в Azure Synapse?
description: С помощью функции безопасности на уровне столбцов можно управлять доступом к столбцам таблицы в базе данных на основе контекста выполнения пользователя или членства в группе. Это упрощает проектирование и реализацию системы безопасности в приложении и позволяет устанавливать ограничения на доступ к столбцам.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: a4da74c01f732f3a62d29847d5f61934dede9778
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208099"
---
# <a name="column-level-security"></a>Безопасность на уровне столбцов

С помощью функции безопасности на уровне столбцов можно управлять доступом к столбцам таблицы на основе контекста выполнения пользователя или членства в группе.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
С момента публикации этого видео для Azure Synapse стала доступна функция [безопасности на уровне строк](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Безопасность на уровне столбцов упрощает проектирование и реализацию безопасности в приложении, позволяя ограничивать доступ к столбцам для защиты конфиденциальных данных. Например, можно предоставить конкретным пользователям доступ только к определенным столбцам таблицы, имеющей отношение к их отделу. Логика ограничения находится на уровне базы данных, а не на отдалении от данных на другом уровне приложения. База данных применяет ограничения доступа при каждой попытке получения доступа к данным независимо от уровня. Это ограничение делает систему безопасности более надежной и устойчивой за счет уменьшения ее контактной зоны. Кроме того, безопасность на уровне столбцов также позволяет отказаться от ввода представлений для фильтрации столбцов с целью наложения на пользователей ограничений доступа.

Реализовать безопасность на уровне столбцов можно с помощью инструкции T-SQL [GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). При использовании этого механизма поддерживаются проверки подлинности SQL и Azure Active Directory (AAD).

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

В следующем примере показано, как ограничить доступ пользователя `TestUser` к столбцу `SSN` таблицы `Membership`:

Создайте таблицу `Membership` со столбцом SSN для хранения номеров социального страхования:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Предоставьте пользователю `TestUser` доступ ко всем столбцам, за исключением столбца SSN, который содержит конфиденциальные данные:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Запросы, выполняемые от имени `TestUser`, завершатся ошибкой, если они содержат столбец SSN:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Варианты использования

Ниже приведены некоторые примеры использования безопасности на уровне столбцов в современном мире.

- В компании, предоставляющей финансовые услуги, обращаться к номерам социального страхования (SSN), номерам телефонов и другим персональным данным клиентов могут только менеджеры по работе с клиентами.
- В медицинской организации доступ к конфиденциальным медицинским записям имеют только врачи и медсестры. Сотрудники отдела выставления счетов не могут просматривать эти данные.
