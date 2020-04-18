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
ms.openlocfilehash: 599b2a280e386e49eb114f448f55b17ed7e823d7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616765"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Обнаружение данных & классификацию для базы данных Azure S'L и аналитики Synapse Azure

Классификация обнаружения данных & встроена в базу данных Azure S'L. Он предоставляет расширенные возможности для обнаружения, классификации, маркировки и отчетности конфиденциальных данных в базах данных.

Наиболее конфиденциальные данные могут включать деловую, финансовую, медицинскую или личную информацию. Обнаружение и классификация этих данных может сыграть ключевую роль в подходе организации к защите информации. На основе этих процессов может формироваться инфраструктура для решения следующих задач:

- Содействие соблюдению стандартов конфиденциальности данных и требований к соблюдению нормативных требований.
- Различные сценарии безопасности, такие как мониторинг (аудит) и оповещение об аномальном доступе к конфиденциальным данным.
- Контроль доступа к базам данных и их ужесточение, содержащие высококонфиденциальные данные.

Data Discovery & классификация является частью расширенного предложения [по безопасности данных,](sql-database-advanced-data-security.md) которое представляет собой единый пакет для передовых возможностей безопасности S'L. Вы можете получить доступ к классификации & обнаружения данных и управлять ими через центральный раздел **расширенной безопасности данных на** портале Azure.

> [!NOTE]
> Эта статья относится к базе данных Azure S'L и аналитике Azure Synapse. Для простоты мы используем *базу данных S'L,* чтобы сослаться как на базу данных S'L, так и на Azure Synapse. Для получения информации о сервере [SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999)S'L (на территории) см.

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Что такое классификация обнаружения данных &?

& классификация Data Discovery представляет собой набор передовых служб и новые возможности базы данных S'L. Она формирует новую парадигму защиты информации для базы данных S'L, направленную на защиту данных, а не только базы данных. Парадигма включает в себя:

- **Открытие и рекомендации:** Механизм классификации сканирует базу данных и определяет столбцы, содержащие потенциально конфиденциальные данные. Затем он предоставляет вам простой способ обзора и применения рекомендуемой классификации через портал Azure.

- **Маркировка:** К столбику можно настойчиво применять метки классификации чувствительности, используя новые атрибуты метаданных, которые были добавлены в движок базы данных S'L. Эти метаданные могут быть использованы для продвинутых, чувствительных сценариев аудита и защиты.

- **Чувствительность к результатам запроса:** Чувствительность набора результатов запроса рассчитывается в режиме реального времени для целей аудита.

- **Видимость:** Состояние классификации базы данных можно просмотреть в подробной панели мониторинга на портале Azure. Кроме того, вы можете загрузить отчет в формате Excel для использования в целях соответствия и аудита и других потребностей.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Откройте для себя, классифицируют и маркируют чувствительные столбцы

В этом разделе описаны шаги для:

- Обнаружение, классификация и маркировка столбцов, содержащих конфиденциальные данные в базе данных.
- Просмотр текущего состояния классификации базы данных и отчетов об экспорте.

Классификация включает в себя два типа атрибутов метаданных:

- **Метки**: Основные атрибуты классификации, используемые для определения уровня чувствительности данных, хранящихся в столбце.  
- **Типы информации**: Атрибуты, предоставляющие более подробную информацию о типе данных, хранящихся в столбце.

### <a name="define-and-customize-your-classification-taxonomy"></a>Определение и настройка таксономии классификации

& классификация Data Discovery поставляется со встроенным набором меток чувствительности и встроенным набором типов информации и логикой обнаружения. Теперь вы можете настраивать таксономию и задавать набор и ранжирование конструкций классификации для своей среды.

Вы определяете и настраиваете свою таксономию классификации в одном центральном месте для всей организации Azure. Это местоположение находится в [Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)как часть вашей политики безопасности. Эту задачу может сделать только человек, у наиболее правна в группе управления корневыми подразделениями организации.

В рамках управления политикой защиты информации s'L можно определить пользовательские метки, ранжировать их и связать с выбранным набором типов информации. Вы также можете добавить свои собственные типы пользовательской информации и настроить их со строками шаблонов. Шаблоны добавляются в логику обнаружения для идентификации данных этого типа в базах данных.

Узнайте больше о настройке и управлении политикой в [политике защиты информации в s'L, как вам руководство.](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)

После определения политики в масштабах всей организации можно продолжить классификацию отдельных баз данных с помощью индивидуальной политики.

### <a name="classify-your-sql-database"></a>Классифицировать базу данных S'L

1. Перейдите на [портал Azure](https://portal.azure.com).

2. Перейдите к **расширенной безопасности данных** под заголовком **«Безопасность»** в панели базы данных Azure S'L. Выберите **Расширенную безопасность данных,** а затем выберите карту **& классификации Data Discovery.**

   ![Расширенное стекло безопасности данных на портале Azure](./media/sql-data-discovery-and-classification/data_classification.png)

3. На странице **обнаружения данных & классификации** вкладка **«Обзор»** содержит резюме текущего состояния классификации базы данных. Резюме включает в себя подробный список всех классифицированных столбцов, которые также можно фильтровать, чтобы показать только конкретные части схемы, типы информации и метки. Если вы еще не классифицировали столбцы, [перейдите к шагу 5](#step-5).

   ![Сводка по текущему состоянию классификации](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Чтобы загрузить отчет в формате Excel, выберите **Экспорт** в верхнем меню панели.

5. <a id="step-5"></a>Для начала классификации данных выберите вкладку **классификации** на странице **обнаружения данных & классификации.**

    Механизм классификации сканирует базу данных для столбцов, содержащих потенциально конфиденциальные данные, и предоставляет список рекомендуемых классификаций столбцов.

6. Просмотр и применение рекомендаций по классификации:

   - Для просмотра списка рекомендуемых классификаций столбцов выберите панель рекомендаций в нижней части панели.

   - Чтобы принять рекомендацию для определенного столбца, выберите флажок в левой колонке соответствующего ряда. Чтобы отметить все рекомендации как принятые, выберите leftmost флажок в заголовке таблицы рекомендаций.

       ![Обзор и выбор из списка рекомендаций по классификации](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Чтобы применить выбранные рекомендации, выберите **Принять выбранные рекомендации.**

7. Вы также можете классифицировать столбцы вручную, как альтернативу или в дополнение к классификации, основанной на рекомендациях:

   1. Выберите **классификацию Добавить** в верхнем меню панели.

   1. В открываемом окне контекста выберите схему, таблицу и столбец, которые необходимо классифицировать, а также тип информации и метку чувствительности.

   1. Выберите **классификацию Добавить** в нижней части окна контекста.

      ![Выберите столбец для классификации](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Для завершения классификации и постоянной маркировки (тега) столбцов базы данных с новыми метаданными классификации выберите **Сохранить** в верхнем меню окна.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Аудит доступа к конфиденциальным данным

Важным аспектом парадигмы защиты информации является возможность контроля за доступом к конфиденциальным данным. [Аудит базы данных Azure S'L](sql-database-auditing.md) был расширен, чтобы `data_sensitivity_information`включить новое поле в журнал аудита под названием . Это поле регистрирует классификацию чувствительности (метки) данных, которые были возвращены запросом. Ниже приведен пример:

![Журнал аудита](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Разрешения

Эти встроенные роли могут читать классификацию данных базы данных Azure S'L:

- Владелец
- Читатель
- Участник
- Диспетчер безопасности SQL
- Администратор доступа пользователей

Эти встроенные роли могут изменить классификацию данных базы данных Azure S'L:

- Владелец
- Участник
- Диспетчер безопасности SQL

Подробнее о разрешениях на основе ролей в [RBAC для ресурсов Azure.](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="manage-classification"></a>Управление классификациями

Управлять классификациями можно t-S'SL, REST API или PowerShell.

### <a name="use-t-sql"></a>Использование T-SQL

Можно использовать T-S'L для добавления или удаления классификаций столбцов, а также для получения всех классификаций для всей базы данных.

> [!NOTE]
> При использовании T-S'L для управления метками в политике защиты информации организации (набор меток, отображаемых в рекомендациях портала), нет проверки, которую вы добавляете в столбец. Таким образом, это до вас, чтобы проверить это.

Для получения информации об использовании T-S'L для классификаций см.

- Добавление или обновление классификации одного или нескольких столбцов: [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Удаление классификации из одного или нескольких столбцов: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Для просмотра всех классификаций в базе данных: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>Используйте API отдыха

Вы можете использовать REST API для программного управления классификациями и рекомендациями. Опубликованный REST API поддерживает следующие операции:

- [Создайте или обновление](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): Создает или обновляет метку чувствительности указанного столбца.
- [Удалить](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): Удаляет метку чувствительности указанного столбца.
- [Рекомендация «Отключить»](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): Рекомендации по чувствительности отсутсвий на указанном столбце.
- [Рекомендация включить](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): Включает рекомендации чувствительности к указанному столбце. (Рекомендации включены по умолчанию для всех столбцов.)
- [Получить](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): Получает чувствительность этикетки указанного столбца.
- [Список текущих по базам данных](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): Получает текущие метки чувствительности указанной базы данных.
- [Список Рекомендуемый Базой данных](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): Получает рекомендуемые метки чувствительности указанной базы данных.

### <a name="use-powershell-cmdlets"></a>Использование командлетов PowerShell
Вы можете использовать PowerShell для управления классификациями и рекомендациями для базы данных Azure S-L и управляемых экземпляров.

#### <a name="powershell-cmdlets-for-sql-database"></a>Смдлеты PowerShell для базы данных S'L

- [Get-AzSqlDatabaseSensitivityКлассификация](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityКлассификация](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Удалить-AzSqlDatabaseSensitivityКлассификация](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivity Рекомендация](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Рекомендация о включании-AzSqlDatabaSesensitivity](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [От"-AzSqlDatabaseSensitivityРекомендация](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>Смдлеты PowerShell для управляемых экземпляров

- [Get-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Удалить-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Включить-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [От"-AzSqlInstanceИнформацияЧувствительность](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Следующие шаги

- Подробнее о [расширенной безопасности данных](sql-database-advanced-data-security.md).
- Рекомендуется настроить [аудит базы данных SQL Azure](sql-database-auditing.md) для мониторинга и аудита доступа к секретным конфиденциальным данным.
- Для презентации, которая включает в себя обнаружение и классификацию данных, [см. Обнаружение, классификация, маркировка & защиты данных S'L Данные разоблачены](https://www.youtube.com/watch?v=itVi9bkJUNc).
