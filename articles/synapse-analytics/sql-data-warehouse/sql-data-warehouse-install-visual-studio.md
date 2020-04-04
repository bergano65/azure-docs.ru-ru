---
title: Установка Visual Studio 2019.
description: Установка визуальных студийных студий и инструментов разработки серверов (SSDT) для Synapse S'L
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b8e37a15f473e5777fe5412b785a0916bd78f095
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655914"
---
# <a name="getting-started-with-visual-studio-2019"></a>Начало работы с Visual Studio 2019

Visual Studio **2019** S'L Server Data Tools (SSDT) представляет собой единый инструмент, позволяющий делать следующее:

- Подключение, запрос и разработка приложений
- Используйте исследователь объектов для визуального изучения всех объектов в модели данных, включая таблицы, представления, сохраненные процедуры и т.д.
- Создание скриптов определения данных T-S'L (DDL) для объектов
- Разработка хранилища данных с использованием государственного подхода с помощью проектов баз данных SSDT
- Интеграция проекта базы данных с системами управления исходными источниками, такими как Git, с Azure Repos
- Настройка конвейеров непрерывной интеграции и развертывания с серверами автоматизации, такими как Azure DevOps

## <a name="install-visual-studio-2019"></a>Установка Visual Studio 2019.

Смотрите [Скачать Visual Studio 2019,][] чтобы скачать и установить Visual Studio **16.3 и выше**. Во время установки выберите рабочую нагрузку для хранения и обработки данных. В Visual Studio 2019 больше не требуется установка автономного SSDT.

## <a name="unsupported-features-in-ssdt"></a>Неподдерживаемые функции в SSDT

Бывают случаи, когда релизы функций для Synapse S'L могут не включать поддержку SSDT. Следующие функции в настоящее время не поддерживаются:

- [Материализованные представления](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Заказанные кластерные индексы columnstore](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [Заявление COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Управление рабочей нагрузкой](sql-data-warehouse-workload-management.md) - группы рабочей нагрузки и классификаторы
- [Безопасность на уровне строк](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Отправьте билет поддержки или проголосуйте [здесь,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) чтобы получить поддержку функции.
- [Динамическое маскирование данных](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
  - Отправьте билет поддержки или проголосуйте [здесь,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) чтобы получить поддержку функции.
- [Таблицы с ограничениями](sql-data-warehouse-table-constraints.md#table-constraints) не поддерживаются. Для этих объектов таблицы установите действие сборки на "Нет".

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас есть последняя версия SSDT, вы готовы [подключиться](sql-data-warehouse-query-visual-studio.md) к вашему пулу S'L.

<!--Other-->

[Скачивание Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
