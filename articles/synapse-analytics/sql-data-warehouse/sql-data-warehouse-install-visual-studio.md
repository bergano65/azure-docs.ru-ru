---
title: Установка Visual Studio 2019.
description: Установка визуальных студийных студий и инструментов разработки серверов (SSDT) для аналитики S'L
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
ms.openlocfilehash: d0a1772706fa838f51322d5f5d5bd1b46eb9144a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351626"
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

Иногда релизы функций для S'L Analytics могут не включать поддержку SSDT. Следующие функции в настоящее время не поддерживаются:

- [Материализованные представления](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (в процессе)
- [Заказанные кластерные индексы columnstore](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (в процессе)
- [Заявление COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (в процессе)
- [Управление рабочей нагрузкой](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) - группы рабочей нагрузки и классификаторы (в процессе)
- [Безопасность на уровне строк](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Отправьте билет поддержки или проголосуйте [здесь,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) чтобы получить поддержку функции.
- [Динамическое маскирование данных](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Отправьте билет поддержки или проголосуйте [здесь,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) чтобы получить поддержку функции. 
- Функция [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 
- [Таблицы с ограничениями](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) не поддерживаются. Для этих объектов таблицы установите действие сборки на "Нет".

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас есть последняя версия SSDT, вы готовы [подключиться](sql-data-warehouse-query-visual-studio.md) к вашему пулу S'L.




<!--Other-->

[Скачивание Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
