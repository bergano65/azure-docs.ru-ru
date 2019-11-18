---
title: Установка Visual Studio 2019
description: Установка Visual Studio и SQL Server Data Tools (SSDT) для хранилища данных SQL Azure.
services: sql-data-warehouse
ms.custom: seo-lt-2019
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 11/06/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 758c9b323e78e7e86312555f5fabdcc9c4c28330
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123732"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Начало работы с Visual Studio 2019 для хранилища данных SQL
Visual Studio **2019** SQL Server Data Tools (SSDT) — это единый инструмент, позволяющий выполнять следующие действия:

- Подключение, запрос и разработка приложений для хранилища данных SQL 
- Используйте обозреватель объектов, чтобы визуально исследовать все объекты в модели данных, включая таблицы, представления, хранимые процедуры и т. д.
- Создание скриптов языка описания данных (DDL) T-SQL для объектов
- Разработка хранилища данных с использованием подхода на основе состояния с проектами баз данных SSDT
- Интегрируйте проект базы данных с системами управления версиями, такими как Git, с Azure Repos
- Настройка конвейеров непрерывной интеграции и развертывания с помощью серверов автоматизации, таких как Azure DevOps

## <a name="install-visual-studio-2019"></a>Установка Visual Studio 2019
См. статью [Загрузка Visual Studio 2019][] для загрузки и установки visual Studio **16,3 и более поздних версий**. Во время установки выберите рабочую нагрузку "хранение и обработка данных". Автономная установка SSDT больше не требуется в Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Неподдерживаемые функции в SSDT

Бывают случаи, когда выпуски компонентов для хранилища данных SQL могут не поддерживать SSDT. Следующие функции в настоящее время не поддерживаются:

- [Материализованные представления](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (выполняется)
- [Упорядоченные кластеризованные индексы columnstore](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (выполняется)
- [Инструкция Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (выполняется)
- [Управление рабочей нагрузкой](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) — группы рабочей нагрузки и классификаторы (выполняется)
- [Безопасность на уровне строк](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Отправьте запрос в службу поддержки или Проголосуйте [здесь](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) , чтобы получить поддерживаемую функцию.
- [Динамическое маскирование данных](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Отправьте запрос в службу поддержки или Проголосуйте [здесь](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) , чтобы получить поддерживаемую функцию. 
- [Predict](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) , функция 

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда у вас установлена последняя версия SSDT, вы можете [Подключиться][connect] к хранилищу данных SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Загрузка Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
