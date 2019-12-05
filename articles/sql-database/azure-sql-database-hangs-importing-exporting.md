---
title: Служба импорта и экспорта занимает много времени
description: Служба импорта и экспорта базы данных SQL Azure занимает много времени для импорта или экспорта базы данных.
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: e1638c9779ca50507a1ce30dd3bbc9c18248964a
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807074"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Служба импорта и экспорта базы данных SQL Azure занимает много времени для импорта или экспорта базы данных.

При использовании службы импорта и экспорта базы данных SQL Azure процесс может занять больше времени, чем ожидалось. В этой статье описываются возможные причины этой задержки и альтернативные методы решения проблемы.

## <a name="azure-sql-database-importexport-service"></a>Служба импорта и экспорта базы данных SQL Azure

Служба импорта и экспорта базы данных SQL Azure — это веб-служба на основе RESTFUL, которая выполняется в каждом центре обработки данных Azure. Эта служба вызывается при использовании параметра [импорта базы данных](sql-database-import.md#using-azure-portal) или [экспорта](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) для перемещения базы данных SQL в портал Azure. Служба предоставляет бесплатные службы очереди запросов и вычислений для выполнения импорта и экспорта между базой данных SQL Azure и хранилищем BLOB-объектов Azure.

Операции импорта и экспорта не представляют традиционную физическую резервную копию базы данных, а представляют собой логическую резервную копию базы данных, использующую Специальный формат BACPAC. Формат BACPAC позволяет избежать использования физического формата, который может отличаться в зависимости от версии Microsoft SQL Server и базы данных SQL Azure. Таким образом, его можно использовать для безопасного восстановления базы данных в базу данных SQL Server и в базу данных SQL.

## <a name="what-causes-delays-in-the-process"></a>Что вызывает задержки в процессе?

Служба импорта и экспорта базы данных SQL Azure предоставляет ограниченное количество виртуальных машин (ВМ) для каждого региона для обработки операций импорта и экспорта. Виртуальные машины для вычислений размещаются в каждом регионе, чтобы гарантировать, что импорт или экспорт не позволяет избежать задержек пропускной способности между регионами и расходов. Если в одном регионе выполняется слишком много запросов одновременно, при обработке операций могут возникать значительные задержки. Время, необходимое для выполнения запросов, может варьироваться от нескольких секунд до нескольких часов.

> [!NOTE]
> Если запрос не обрабатывается в течение четырех дней, служба автоматически отменяет запрос.

## <a name="recommended-solutions"></a>Рекомендуемые решения

Если экспорт базы данных используется только для восстановления после случайного удаления данных, все выпуски базы данных SQL Azure предоставляют возможность самостоятельного восстановления из созданных системой резервных копий. Но если эти экспорты необходимы по другим причинам, и если требуется постоянно ускорить или более предсказуемую производительность импорта и экспорта, рассмотрите следующие варианты.

* [Экспорт в BACPAC-файл с помощью служебной программы SqlPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Экспорт в BACPAC-файл с помощью SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Выполните импорт или экспорт BACPAC непосредственно в код с помощью API Microsoft SQL Server Data-Tier Application Framework (DacFx). Дополнительные сведения см. в следующих источниках:
  * [Экспорт приложения уровня данных](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Пространство имен Microsoft. SqlServer. DAC](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Скачать DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Вопросы, которые следует учитывать при экспорте или импорте базы данных SQL Azure

* Все методы, описанные в этой статье, используют квоту единицы транзакций базы данных (DTU), что приводит к регулированию службы базы данных SQL Azure. [СТАТИСТИКУ DTU для базы данных можно просмотреть на портал Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Если база данных достигла пределов ресурсов, [Обновите уровень служб](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) , чтобы добавить дополнительные ресурсы.
* В идеале следует запускать клиентские приложения (например, служебную программу SqlPackage или пользовательское приложение DAC) из виртуальной машины в том же регионе, что и база данных SQL. В противном случае могут возникнуть проблемы с производительностью, связанные с задержкой сети.
* Экспорт больших таблиц без кластеризованных индексов может быть очень медленным или даже причиной сбоя. Такое поведение обусловлено тем, что таблица не может быть разбита и экспортирована параллельно. Вместо этого он должен быть экспортирован в одну транзакцию, что приводит к снижению производительности и потенциальному сбою при экспорте, особенно для больших таблиц.


## <a name="related-documents"></a>Связанные документы

[Рекомендации по экспорту базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
