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
ms.openlocfilehash: eb4e7907c3dcffed035307c2084160ce6051be13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409955"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Обнаружение данных & классификацию для базы данных Azure S'L и аналитики Synapse Azure

& классификация Data Discovery предоставляет расширенные возможности, встроенные в базу данных Azure S'L для **обнаружения,** &  **классификации, маркировки,****сообщающей** конфиденциальные данные в базах данных. **classifying**

Обнаружение и классификация наиболее конфиденциальных данных (деловых или финансовых, медицинских, персональных данных и т. д.) может играть ключевую роль в концепции защиты информации вашей организации. На основе этих процессов может формироваться инфраструктура для решения следующих задач:

- Соблюдение стандартов конфиденциальности данных и нормативных требований.
- Различные сценарии безопасности, такие как мониторинг (аудит) и оповещение об аномальном доступе к конфиденциальным данным.
- Управление доступом к базам данных, содержащим конфиденциальные данные, и усиление их защиты.

Data Discovery & Классификация является частью предложения [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), который представляет собой единый пакет для передовых возможностей безопасности S'L. Доступ к этой службе можно получить на центральном портале SQL ADS. Там же можете управлять ею.

> [!NOTE]
> Этот документ относится к базе данных Azure S'L и Azure Synapse. Для простоты используется база данных S'L, когда речь идет как о базе данных S'L, так и о Azure Synapse. Для сервера S'L [SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999)(на территории) см.

## <a name="what-is-data-discovery--classification"></a><a id="subheading-1"></a>Что такое обнаружение и классификация данных?

Служба обнаружения и классификации данных предоставляет набор расширенных служб и новые возможности SQL, формирующие новую концепцию защиты информации SQL, направленную на защиту данных, а не только базы данных.

- **Обнаружение и рекомендации**

  Подсистема классификации проверяет базы данных и выявляет столбцы, содержащие потенциально конфиденциальные данные. Затем она позволяет легко просмотреть их и применить соответствующие рекомендации по классификации на портале Azure.

- **Добавление меток**

  К столбцам можно добавить метки классификации конфиденциальности, используя новые атрибуты метаданных классификации, появившиеся в ядре SQL. Эти метаданные затем могут использоваться в более сложных сценариях аудита и защиты на основе конфиденциальности.

- **Конфиденциальность результирующего набора запроса**

  Конфиденциальность результирующего набора запроса вычисляется в режиме реального времени для аудита.

- **Видимость**

  Состояние классификации базы данных можно просмотреть в подробной панели мониторинга на портале. Кроме того, можно скачать отчет (в формате Excel) и использовать его для соблюдения соответствия и аудита, а также для других нужд.

## <a name="discover-classify--label-sensitive-columns"></a><a id="subheading-2"></a>Обнаружение, классификация и добавление тегов для конфиденциальных столбцов

В следующем разделе описываются действия по обнаружению в базе данных столбцов, содержащих конфиденциальные данные, их классификации и назначению им меток, а также просмотру текущего состояния классификации базы данных и экспорту отчетов.

Классификация включает в себя два типа атрибутов метаданных:

- метки — основные атрибуты классификации, которые служат для определения уровня конфиденциальности данных, хранящихся в столбце;  
- типы сведений — предоставляют более подробные сведения о типе данных, хранящихся в столбце.

## <a name="define-and-customize-your-classification-taxonomy"></a>Определение и настройка таксономии классификации

& классификация Data Discovery поставляется со встроенным набором меток чувствительности и встроенным набором типов информации и логикой обнаружения. Теперь вы можете настраивать таксономию и задавать набор и ранжирование конструкций классификации для своей среды.

Определение и настройка таксономии классификации выполняется в одном центральном расположении для всего клиента Azure. Это расположение находится в [центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), что является частью политики безопасности. Только пользователь с правами администратора в корневой группе управления клиента может выполнить эту задачу.

В процессе управления политикой Information Protection можно определить пользовательские метки, ранжировать их и связать их с выбранным набором типов сведений. Вы также можете добавить собственные настраиваемые типы сведений и настроить их с использованием шаблонов строки, которые добавляются в логику обнаружения для идентификации этого типа данных в базах данных.
Дополнительные сведения о настройке политики и управлении ею см. в статье [Customize the SQL information protection policy in Azure Security Center (Preview)](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409) (Настройка политики защиты информации SQL в центре безопасности Azure (предварительная версия)).

После определения политики на уровне клиента можно приступить к классификации отдельных баз данных с помощью настроенной политики.

## <a name="classify-your-sql-database"></a>Классификация базы данных SQL

1. Перейдите на [портал Azure](https://portal.azure.com).

2. Перейдите к разделу **Расширенная защита данных** под заголовком "Безопасность" в области "База данных SQL Azure". Нажмите, чтобы включить расширенную безопасность данных, а затем нажмите на карту **обнаружения данных & классификации.**

   ![Проверка базы данных](./media/sql-data-discovery-and-classification/data_classification.png)

3. Вкладка **Обзор** содержит сводку по текущему состоянию базы данных, включая подробный список всех классифицированных столбцов, который можно отфильтровать, чтобы просмотреть определенные части схемы, типы информации и метки. Если столбцы еще не классифицированы, [перейдите к шагу 5](#step-5).

   ![Сводка по текущему состоянию классификации](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Чтобы скачать отчет в формате Excel, щелкните параметр **Экспорт** в верхнем меню окна.

   ![Экспорт в Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Чтобы начать классификацию данных, щелкните вкладку **Классификация** в верхней части окна.

    ![Классификация данных](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. Механизм классификации сканирует базу данных для столбцов, содержащих потенциально конфиденциальные данные, и предоставляет список **рекомендуемых классификаций столбцов.** Вот как можно просмотреть и применить рекомендации по классификации.

   - Чтобы просмотреть список рекомендуемых классификаций столбцов, щелкните панель рекомендаций в нижней части окна.

      ![Классификация данных](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Просмотрите список рекомендаций. Чтобы принять рекомендацию для определенного столбца, установите флажок в левом столбце соответствующей строки. Вы также можете принять *все рекомендации*, установив флажок в заголовке таблицы рекомендаций.

       ![Просмотр списка рекомендаций](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Чтобы применить выбранные рекомендации, нажмите синюю кнопку **Принять выбранные рекомендации**.

      ![Применение рекомендаций](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Вы также можете **классифицировать столбцы вручную** вместо использования рекомендованных классификаций или в дополнение к ним.

   - В меню в верхней части страницы щелкните **Добавить классификацию**.

      ![Добавление классификации вручную](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - В открывшемся контекстном окне выберите схему, таблицу и столбец, которые нужно классифицировать, а затем выберите тип сведений и метку классификации. После этого нажмите синюю кнопку **Добавить классификацию** в нижней части контекстного окна.

      ![Выбор столбца для классификации](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Чтобы завершить классификацию и назначить столбцам базы данных новые метаданные (метки) классификации, щелкните элемент **Сохранить** в меню в верхней части окна.

   ![Сохранять](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a name="auditing-access-to-sensitive-data"></a><a id="subheading-3"></a>Аудит доступа к конфиденциальным данным

Важным аспектом в концепции защиты информации является возможность контролировать доступ к конфиденциальным данным. [Аудит базы данных SQL Azure](sql-database-auditing.md) был расширен — в журнал аудита добавлено новое поле *data_sensitivity_information*, в которое записываются классификации конфиденциальности (метки) фактических данных, возвращенных запросом.

![Журнал аудита](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="subheading-4"></a>Разрешения

Следующие встроенные роли могут читать классификацию данных базы `Owner` `Reader`данных Azure S'L: , `Contributor` `SQL Security Manager` и `User Access Administrator`.

Следующие встроенные роли могут изменить классификацию данных базы `Owner` `Contributor`данных Azure S'L: `SQL Security Manager`.

Узнайте больше о [ресурсах RBAC для Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="subheading-5"></a>Управление классификациями

# <a name="t-sql"></a>[Т-СЗЛ](#tab/azure-t-sql)
Используйте T-SQL, чтобы добавить или удалить классификацию столбца или извлечь все классификации для целой базы данных.

> [!NOTE]
> При использовании T-SQL для управления метками добавляемые в столбец метки не проверяются на предмет присутствия в политике защиты информации организации (набор меток, которые отображаются в рекомендациях портала). Поэтому проверку нужно выполнять вам.

- Добавление и обновление классификации одного или нескольких столбцов: [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Удаление классификации одного или нескольких столбцов: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Просмотр всех классификаций в базе данных: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

# <a name="rest-apis"></a>[Отдых AIS](#tab/azure-rest-api)
Можно использовать REST AIS для программного управления классификациями и рекомендациями. Опубликованные интерфейсы REST API поддерживают следующие операции:

- [Создание или обновление](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) - Создает или обновляет метку чувствительности данного столбца
- [Delete](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete). Удаление метки конфиденциальности данного столбца.
- [Рекомендация "Отключить"](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) - Рекомендации по чувствительности к сравмна на данном столбце
- [Рекомендация включить](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) - Позволяет рекомендации чувствительности к данному столбецу (рекомендации включены по умолчанию на всех столбцах)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get). Получение метки конфиденциальности данного столбца.
- [List Current By Database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase). Получение списка текущих меток конфиденциальности для конкретной базы данных.
- [Список Рекомендуемый Базой данных](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) - Получает рекомендуемые метки чувствительности данной базы данных

# <a name="powershell-cmdlet"></a>[Командлет PowerShell](#tab/azure-powelshell)
Вы можете использовать PowerShell для управления классификациями и рекомендациями для базы данных Azure S'L и управляемой инстанции.

### <a name="powershell-cmdlet-for-azure-sql-database"></a>МощностьShell Cmdlet для базы данных Azure S'L
- [Get-AzSqlDatabaseSensitivityКлассификация](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityКлассификация](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Удалить-AzSqlDatabaseSensitivityКлассификация](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivity Рекомендация](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Рекомендация о включании-AzSqlDatabaSesensitivity](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [От"-AzSqlDatabaseSensitivityРекомендация](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>PowerShell Cmdlets для управляемого экземпляра
- [Get-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Удалить-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Включить-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [От"-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

---

## <a name="next-steps"></a><a id="subheading-6"></a>Дальнейшие действия

- Дополнительные сведения о [расширенной защите данных](sql-database-advanced-data-security.md).
- Рекомендуется настроить [аудит базы данных SQL Azure](sql-database-auditing.md) для мониторинга и аудита доступа к секретным конфиденциальным данным.
- Для презентации на YouTube, которая включает в себя классификацию & data Discovery, см [&. Данные разоблачены](https://www.youtube.com/watch?v=itVi9bkJUNc).

<!--Anchors-->
[What is data discovery & classification]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Permissions]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6
