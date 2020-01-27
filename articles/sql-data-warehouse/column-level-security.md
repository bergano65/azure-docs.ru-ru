---
title: Что такое безопасность на уровне столбцов для хранилища данных SQL?
description: Безопасность на уровне столбцов позволяет клиентам управлять доступом к столбцам таблицы базы данных на основе контекста выполнения или членства пользователя в группе, упрощая проектирование и кодирование безопасности в приложении, а также позволяя реализовать ограничения по столбцу. имеет.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 344701989a753e17d8a026f6bb771a6030bdb71f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513054"
---
# <a name="column-level-security"></a>Безопасность на уровне столбцов

Безопасность на уровне столбцов позволяет клиентам управлять доступом к столбцам таблицы на основе контекста выполнения или членства пользователя в группе.


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Так как это видео было опубликовано как [безопасность на уровне строк](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) , стало доступным для хранилища данных SQL. 

Безопасность на уровне столбцов упрощает проектирование и кодирование безопасности в приложении, позволяя ограничить доступ к столбцу для защиты конфиденциальных данных. Например, можно предоставить конкретным пользователям доступ только к определенным столбцам таблицы, имеющей отношение к их отделу. Логика ограничения находится на уровне базы данных, а не на отдалении от данных на другом уровне приложения. База данных применяет ограничения доступа при каждом попыток доступа к данным с любого уровня. Это ограничение повышает надежность и надежность системы безопасности за счет сокращения контактной зоны общей системы безопасности. Кроме того, безопасность на уровне столбцов также устраняет необходимость в добавлении представлений для фильтрации столбцов для установления ограничений доступа пользователей.

Безопасность на уровне столбцов можно реализовать с помощью инструкции T-SQL [Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) . При использовании этого механизма поддерживаются проверки подлинности SQL и Azure Active Directory (AAD).

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Синтаксис

```sql
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
В следующем примере показано, как ограничить доступ `TestUser` к столбцу `SSN` таблицы `Membership`:

Создайте таблицу `Membership` со столбцом SSN, используемым для хранения номеров социального страхования:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Разрешить `TestUser` доступ ко всем столбцам, кроме столбца SSN, который содержит конфиденциальные данные:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Запросы, выполняемые как `TestUser`, завершатся ошибкой, если они включают столбец SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Варианты использования

Ниже приведены некоторые примеры использования безопасности на уровне столбцов в настоящее время.

- В компании, предоставляющей финансовые услуги, обращаться к номерам социального страхования (SSN), номерам телефонов и другим персональным данным клиентов могут только менеджеры по работе с клиентами.
- Поставщик услуг здравоохранения обеспечивает доступ к конфиденциальным медицинские записи только в доктора и медсестрам, одновременно запрещая участникам отдела выставления счетов просматривать эти данные.
