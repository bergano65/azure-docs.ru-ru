---
title: Подключение к Synapse SQL с помощью Power BI Professional
description: В этом руководстве описаны действия по подключению Power BI Desktop к SQL по запросу (предварительная версия).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: d88406646099a136d196a104f9cf4352a367f6d2
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899112"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Подключение к Synapse SQL с помощью Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

В этом руководстве описаны действия по подключению Power BI Desktop к SQL по запросу (предварительная версия).

## <a name="prerequisites"></a>Предварительные требования

Для выполнения запросов необходимы следующие средства:

- Выберите клиент SQL:

  - Azure Data Studio
  - SQL Server Management Studio

- Установленная служба Power BI Desktop

Параметры

| Параметр                                 | Описание                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Адрес конечной точки службы SQL по запросу    | Будет использоваться в качестве имени сервера.                                   |
| Регион конечной точки службы SQL по запросу     | Используется для определения хранилища для использования в примерах. |
| Имя пользователя и пароль для доступа к конечной точке | Будет использоваться для доступа к конечной точке.                               |
| База данных, которая будет использоваться для создания представлений     | База данных, используемая в качестве начальной точки в примерах.       |

## <a name="first-time-setup"></a>Изначальная настройка

Перед использованием примеров необходимо выполнить два действия:

1. Создание базы данных для представлений.
2. Создание учетных данных, которые будут использоваться SQL по запросу для доступа к файлам в хранилище.

### <a name="create-database"></a>Создание базы данных

В этой статье по началу работы следует создать собственную базу данных, чтобы использовать ее в качестве демоверсии. База данных необходима для создания представлений. Используйте эту базу данных в примерах запросов в этой документации.

> [!NOTE]
> Базы данных используются только для просмотра метаданных, а не реальных данных.
>
> Запишите имя используемой базы данных, ведь оно потребуется позже.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Создание учетных данных

Перед выполнением запросов нужно создать учетные данные. Эти учетные данные будут использоваться службой SQL по запросу для доступа к файлам в хранилище.

> [!NOTE]
> Для доступа к учетной записи хранения необходимо создать учетные данные. Хотя SQL по запросу может получать доступ к хранилищу из разных регионов, наличие хранилища и рабочей области Azure Synapse в одном регионе обеспечит лучшую производительность.

**Фрагмент кода для создания учетных данных для контейнеров данных Census** , выполните:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="create-a-power-bi-desktop-report"></a>Создание отчета Power BI Desktop

Откройте приложение Power BI Desktop и выберите вариант **Получить данные** .

![Открытие приложения Power BI Desktop и выбор раздела "Получить данные"](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Шаг 1. Выбор источника данных

Выберите в меню **Azure** , а затем — **База данных SQL Azure** .
![Выберите источник данных.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Шаг 2. Выбор базы данных

Запишите URL-адрес и имя базы данных, в которой находится представление.
![Выбор базы данных в конечной точке.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к [запросу файлов хранилища](get-started-azure-data-studio.md), чтобы узнать, как подключиться к SQL по запросу с помощью Azure Data Studio.
 
