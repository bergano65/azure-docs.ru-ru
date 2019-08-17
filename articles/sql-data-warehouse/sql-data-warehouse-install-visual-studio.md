---
title: Установка Visual Studio 2019 для хранилища данных SQL | Документация Майкрософт
description: Установка Visual Studio и SQL Server Data Tools (SSDT) для хранилища данных SQL Azure.
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 02caa7154ef9cb8419b533f0cb2d0fd57e86bf9f
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563331"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Начало работы с Visual Studio 2019 для хранилища данных SQL
Visual Studio **2019** SQL Server Data Tools (SSDT) — это единый инструмент, позволяющий выполнять следующие действия:

- Подключение, запрос и разработка приложений для хранилища данных SQL 
- Используйте обозреватель объектов, чтобы визуально исследовать все объекты в модели данных, включая таблицы, представления, хранимые процедуры и т. д.
- Создание скриптов языка описания данных (DDL) T-SQL для объектов
- Разработка хранилища данных с использованием подхода на основе состояния с проектами баз данных SSDT
- Интеграция проекта базы данных с системами управления версиями, такими как Git, с помощью Azure DevOps репозиториев
- Настройка конвейеров непрерывной интеграции и развертывания с помощью серверов автоматизации, например Azure DevOps [ожидается в ближайшее время]

> [!NOTE]
> Сейчас проекты базы данных Visual Studio SSDT находятся на этапе предварительной версии. Для получения периодических обновлений об этой функции проголосуйте на сайте [UserVoice].

## <a name="install-visual-studio-2019"></a>Установка Visual Studio 2019
См. статью [Загрузка Visual Studio 2019][] для загрузки и установки Visual Studio. Во время установки выберите рабочую нагрузку "хранение и обработка данных". Автономная установка SSDT больше не требуется в Visual Studio 2019.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>Отчеты о проблемах с SSDT Visual Studio 2019 (Предварительная версия)

Чтобы сообщить о проблемах при использовании SSDT с хранилищем данных SQL, отправьте по электронной почте следующий список рассылки:<sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>Следующие шаги

Теперь, когда у вас установлена последняя версия SSDT, вы можете [Подключиться][connect] к хранилищу данных SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Загрузка Visual Studio 2019]: https://visualstudio.microsoft.com/vs/preview/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
