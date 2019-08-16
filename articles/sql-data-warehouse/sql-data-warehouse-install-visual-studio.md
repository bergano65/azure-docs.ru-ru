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
ms.openlocfilehash: 65cbd6679c85e82eaa5fd28bb91d54b443cc891e
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515475"
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
Чтобы скачать и установить Visual Studio, используйте следующую ссылку: [Загрузка Visual Studio 2019][]. Во время установки необходимо выбрать рабочую нагрузку "хранение и обработка данных". В Visual Studio 2019 больше не требуется автономная установка SSDT.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>Отчеты о проблемах с SSDT Visual Studio 2019 (Предварительная версия)

Чтобы сообщить о проблемах при использовании SSDT с хранилищем данных SQL, отправьте по электронной почте следующий список рассылки:<sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>Следующие шаги

Теперь, когда у вас установлена последняя версия SSDT, вы можете [Подключиться][connect] к хранилищу данных SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Загрузка Visual Studio 2019]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
