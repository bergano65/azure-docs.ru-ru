---
title: Обнаружение и классификация данных
description: Обнаружение данных & классификацию для базы данных Azure S'L и аналитики Synapse Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: b3a08eb351d29fd71889807c9a21d03b564117a7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673747"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Обнаружение данных & классификацию для базы данных Azure S'L и аналитики Synapse Azure

& классификация Data Discovery предоставляет расширенные возможности, встроенные в базу данных Azure S'L для **обнаружения,** &  **классификации, маркировки,****сообщающей** конфиденциальные данные в базах данных. **classifying**

Обнаружение и классификация наиболее конфиденциальных данных (бизнес, финансы, здравоохранение, личные идентифицируемые данные и т.д.) может сыграть ключевую роль в вашей организационной защите информации. На основе этих процессов может формироваться инфраструктура для решения следующих задач:

- Соблюдение стандартов конфиденциальности данных и нормативных требований.
- Различные сценарии безопасности, такие как мониторинг (аудит) и оповещение об аномальном доступе к конфиденциальным данным.
- Управление доступом к базам данных, содержащим конфиденциальные данные, и усиление их защиты.

Data Discovery & Классификация является частью предложения [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), который представляет собой единый пакет для передовых возможностей безопасности S'L. Доступ к этой службе можно получить на центральном портале SQL ADS. Там же можете управлять ею.

> [!NOTE]
> Этот документ относится к базе данных Azure S'L и Azure Synapse. Для простоты используется база данных S'L, когда речь идет как о базе данных S'L, так и о Azure Synapse. Для сервера S'L [SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999)(на территории) см.

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Что такое обнаружение и классификация данных?

Служба обнаружения и классификации данных предоставляет набор расширенных служб и новые возможности SQL, формирующие новую концепцию защиты информации SQL, направленную на защиту данных, а не только базы данных.

- **Обнаружение и рекомендации**

  Подсистема классификации проверяет базы данных и выявляет столбцы, содержащие потенциально конфиденциальные данные. Затем она позволяет легко просмотреть их и применить соответствующие рекомендации по классификации на портале Azure.

- **Добавление меток**

  К столбцам можно добавить метки классификации конфиденциальности, используя новые атрибуты метаданных классификации, появившиеся в ядре SQL. Эти метаданные затем могут использоваться в более сложных сценариях аудита и защиты на основе конфиденциальности.

- **Конфиденциальность результирующего набора запроса**

  Конфиденциальность результирующего набора запроса вычисляется в режиме реального времени для аудита.

- **Видимость**

  Состояние классификации базы данных можно просмотреть в подробной панели мониторинга на портале. Кроме того, можно скачать отчет (в формате Excel) и использовать его для соблюдения соответствия и аудита, а также для других нужд.

## <a name="discover-classify--label-sensitive-columns"></a><a id="discover-classify-columns"></a>Обнаружение, классификация и добавление тегов для конфиденциальных столбцов

В следующем разделе описываются действия по обнаружению в базе данных столбцов, содержащих конфиденциальные данные, их классификации и назначению им меток, а также просмотру текущего состояния классификации базы данных и экспорту отчетов.

Классификация включает в себя два типа атрибутов метаданных:

- **Метки** — основные атрибуты классификации, используемые для определения уровня чувствительности данных, хранящихся в столбце.  
- **Типы информации** — Обеспечить дополнительную детализацию в типе данных, хранящихся в столбце.

## <a name="define-and-customize-your-classification-taxonomy"></a>Определение и настройка таксономии классификации

& классификация Data Discovery поставляется со встроенным набором меток чувствительности и встроенным набором типов информации и логикой обнаружения. Теперь вы можете настраивать таксономию и задавать набор и ранжирование конструкций классификации для своей среды.

Определение и настройка таксономии классификации выполняется в одном центральном расположении для всего клиента Azure. Это расположение находится в [центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), что является частью политики безопасности. Только пользователь с правами администратора в корневой группе управления клиента может выполнить эту задачу.

В рамках управления политикой защиты информации s'L можно определить пользовательские метки, ранжировать их и связать с выбранным набором типов информации. Вы также можете добавить собственные настраиваемые типы сведений и настроить их с использованием шаблонов строки, которые добавляются в логику обнаружения для идентификации этого типа данных в базах данных.
Узнайте больше о настройке и управлении политикой в [политике защиты информации в s'L, как вам руководство.](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)

После определения политики на уровне клиента можно приступить к классификации отдельных баз данных с помощью настроенной политики.

## <a name="classify-your-sql-database"></a>Классификация базы данных SQL

1. Перейдите на [портал Azure](https://portal.azure.com).

2. Перейдите к разделу **Расширенная защита данных** под заголовком "Безопасность" в области "База данных SQL Azure". Нажмите, чтобы включить расширенную безопасность данных, а затем нажмите на карту **обнаружения данных & классификации.**

   ![Проверка базы данных](./media/sql-data-discovery-and-classification/data_classification.png)

3. Вкладка **Обзор** содержит сводку по текущему состоянию базы данных, включая подробный список всех классифицированных столбцов, который можно отфильтровать, чтобы просмотреть определенные части схемы, типы информации и метки. Если столбцы еще не классифицированы, [перейдите к шагу 5](#step-5).

   ![Сводка по текущему состоянию классификации](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Чтобы скачать отчет в формате Excel, щелкните параметр **Экспорт** в верхнем меню окна.

5. <a id="step-5"></a>Чтобы начать классификацию данных, щелкните вкладку **Классификация** в верхней части окна.

6. Механизм классификации сканирует базу данных для столбцов, содержащих потенциально конфиденциальные данные, и предоставляет список **рекомендуемых классификаций столбцов.** Вот как можно просмотреть и применить рекомендации по классификации.

   - Чтобы просмотреть список рекомендуемых классификаций столбцов, нажмите на панель рекомендаций в нижней части окна

   - Просмотрите список рекомендаций. Чтобы принять рекомендацию для определенного столбца, установите флажок в левом столбце соответствующей строки. Вы также можете принять *все рекомендации*, установив флажок в заголовке таблицы рекомендаций.

       ![Просмотр списка рекомендаций](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Чтобы применить выбранные рекомендации, нажмите синюю кнопку **Принять выбранные рекомендации**.

7. Вы также можете **классифицировать столбцы вручную** вместо использования рекомендованных классификаций или в дополнение к ним.

   - В меню в верхней части страницы щелкните **Добавить классификацию**.

   - В открывшемся контекстном окне выберите схему, таблицу и столбец, которые нужно классифицировать, а затем выберите тип сведений и метку классификации. После этого нажмите синюю кнопку **Добавить классификацию** в нижней части контекстного окна.

      ![Выбор столбца для классификации](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Чтобы завершить классификацию и назначить столбцам базы данных новые метаданные (метки) классификации, щелкните элемент **Сохранить** в меню в верхней части окна.

## <a name="auditing-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Аудит доступа к конфиденциальным данным

Важным аспектом в концепции защиты информации является возможность контролировать доступ к конфиденциальным данным. [Аудит базы данных SQL Azure](sql-database-auditing.md) был расширен — в журнал аудита добавлено новое поле *data_sensitivity_information*, в которое записываются классификации конфиденциальности (метки) фактических данных, возвращенных запросом.

![Журнал аудита](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Разрешения

Следующие встроенные роли могут читать классификацию данных базы `Owner` `Reader`данных Azure S'L: , `Contributor` `SQL Security Manager` и `User Access Administrator`.

Следующие встроенные роли могут изменить классификацию данных базы `Owner` `Contributor`данных Azure S'L: `SQL Security Manager`.

Узнайте больше о [ресурсах RBAC для Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="manage-classification"></a>Управление классификациями

### <a name="using-t-sql"></a>Использование T-SQL
Используйте T-SQL, чтобы добавить или удалить классификацию столбца или извлечь все классификации для целой базы данных.

> [!NOTE]
> При использовании T-SQL для управления метками добавляемые в столбец метки не проверяются на предмет присутствия в политике защиты информации организации (набор меток, которые отображаются в рекомендациях портала). Поэтому проверку нужно выполнять вам.

- Добавление и обновление классификации одного или нескольких столбцов: [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Удаление классификации одного или нескольких столбцов: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Просмотр всех классификаций в базе данных: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="using-rest-api"></a>Использование API отдыха
Вы можете использовать REST API для программного управления классификациями и рекомендациями. Опубликованный REST API поддерживает следующие операции:

- [Создание или обновление](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) - Создает или обновляет метку чувствительности данного столбца
- [Delete](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete). Удаление метки конфиденциальности данного столбца.
- [Рекомендация "Отключить"](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) - Рекомендации по чувствительности к сравмна на данном столбце
- [Рекомендация включить](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) - Позволяет рекомендации чувствительности к данному столбецу (рекомендации включены по умолчанию на всех столбцах)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get). Получение метки конфиденциальности данного столбца.
- [List Current By Database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase). Получение списка текущих меток конфиденциальности для конкретной базы данных.
- [Список Рекомендуемый Базой данных](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) - Получает рекомендуемые метки чувствительности данной базы данных

### <a name="using-powershell-cmdlet"></a>Использование PowerShell Cmdlet
Вы можете использовать PowerShell для управления классификациями и рекомендациями для базы данных Azure S'L и управляемой инстанции.

#### <a name="powershell-cmdlet-for-azure-sql-database"></a>МощностьShell Cmdlet для базы данных Azure S'L
- [Get-AzSqlDatabaseSensitivityКлассификация](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityКлассификация](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Удалить-AzSqlDatabaseSensitivityКлассификация](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivity Рекомендация](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Рекомендация о включании-AzSqlDatabaSesensitivity](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [От"-AzSqlDatabaseSensitivityРекомендация](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instance"></a>PowerShell Cmdlets для управляемого экземпляра
- [Get-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Удалить-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Включить-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [От"-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Дальнейшие действия

- Подробнее о [расширенной безопасности данных](sql-database-advanced-data-security.md).
- Рекомендуется настроить [аудит базы данных SQL Azure](sql-database-auditing.md) для мониторинга и аудита доступа к секретным конфиденциальным данным.
- Для презентации, которая включает в себя классификацию & обнаружения данных, см [&. Данные разоблачены](https://www.youtube.com/watch?v=itVi9bkJUNc).
