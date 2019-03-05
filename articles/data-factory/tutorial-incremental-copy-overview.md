---
title: Пошаговое копирование данных с помощью фабрики данных Azure | Документация Майкрософт
description: В этих руководствах описывается добавочное копирование данных из исходного в целевое хранилище данных. В первом руководстве данные копируются из одной таблицы.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: cb75e943416c227730589ab5e7feeb7b8ba5e245
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957935"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Добавочная загрузка данных из исходного хранилища данных в целевое

В решениях для интеграции данных добавочная (разностная) загрузка данных после начальной загрузки данных является широко используемым сценарием. В руководствах этого раздела показаны различные способы пошаговой загрузки данных с помощью Фабрики данных Azure.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Разностная загрузка данных из базы данных c использованием предела
В этом случае следует определить предел в базе данных-источнике. Предел представляет собой столбец, содержащий метку времени последнего обновления или добавочный ключ. Решение разностной загрузки загружает измененные данные между значениями старого и нового пределов. Рабочий процесс для этого подхода показан на следующей схеме: 

![Рабочий процесс использования предела](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Пошаговые инструкции представлены в следующих статьях: 

- [Пошаговая загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure](tutorial-incremental-copy-powershell.md)
- [Добавочная загрузка данных из нескольких таблиц в SQL Server в базу данных SQL Azure](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Разностная загрузка данных из базы данных SQL с помощью технологии отслеживания изменений
Технология отслеживания изменений — это упрощенное решение в SQL Server и Базе данных SQL Azure, которое предоставляет эффективный механизм отслеживания изменений для приложений. Эта технология позволяет приложению легко идентифицировать вставленные, обновленные или удаленные данные. 

Рабочий процесс для этого подхода показан на следующей схеме:

![Рабочий процесс для использования отслеживания изменений](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Пошаговые инструкции представлены в следующем руководстве: <br/>
[Добавочная загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure с использованием сведений об отслеживании изменений](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Загрузка новых и измененных файлов только с использованием LastModifiedDate
Можно сначала получить метаданные (LastModifiedDate) для файлов, а затем скопировать новые и измененные файлы только в целевое хранилище.

Пошаговые инструкции представлены в следующем руководстве: <br/>
[Incrementally copy new and changed files based on LastModifiedDate by using the Copy Data tool](tutorial-incremental-copy-lastmodified-copy-data-tool.md) (Добавочное копирование новых и измененных файлов на основе параметра LastModifiedDate с помощью средства "Копирование данных")

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Загрузка новых файлов только с использованием имен папок или файлов, секционированных по времени
Можно скопировать только новые файлы или папки, если они секционированы по времени и информация о временной секции указана в имени файла или папки (например, /yyyy/mm/dd/file.csv). 

Пошаговые инструкции представлены в следующем руководстве: <br/>
[Incrementally copy new files based on time partitioned file name by using the Copy Data tool](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) (Добавочное копирование новых файлов на основе имени файла, секционированного по времени, с помощью средства "Копирование данных")

## <a name="next-steps"></a>Дополнительная информация
Перейдите к следующему руководству: 

> [!div class="nextstepaction"]
>[Пошаговая загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure](tutorial-incremental-copy-powershell.md)
