---
title: Импорт и экспорт базы данных занимает много времени
description: Для импорта или экспорта базы данных SQL Azure и Azure SQL Управляемый экземпляр Служба импорта и экспорта занимает много времени.
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: f98cfcd49806061a969a9227f9ade05f70ce79ff
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982316"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Для импорта или экспорта базы данных SQL Azure и Управляемый экземпляр службы импорта и экспорта требуется много времени.

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

При использовании службы импорта и экспорта процесс может занять больше времени, чем ожидалось. В этой статье описываются возможные причины этой задержки и альтернативные методы решения проблемы.

## <a name="azure-sql-database-importexport-service"></a>Служба импорта и экспорта базы данных SQL Azure

Служба импорта и экспорта базы данных SQL Azure — это веб-служба на основе RESTFUL, которая выполняется в каждом центре обработки данных Azure. Эта служба вызывается при использовании параметра [импорта базы данных](database-import.md#using-azure-portal) или [экспорта](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) для перемещения базы данных в портал Azure. Служба предоставляет бесплатные службы очереди запросов и вычислений для выполнения импорта и экспорта между базой данных SQL Azure и хранилищем BLOB-объектов Azure.

Операции импорта и экспорта не представляют традиционную физическую резервную копию базы данных, а представляют собой логическую резервную копию базы данных, использующую Специальный формат BACPAC. Формат BACPAC позволяет избежать использования физического формата, который может отличаться в зависимости от версий Microsoft SQL Server, базы данных SQL Azure и Управляемый экземпляр SQL Azure.

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

## <a name="things-to-consider-when-you-export-or-import-a-database"></a>Вопросы, которые следует учитывать при экспорте или импорте базы данных

* Все методы, описанные в этой статье, используют квоту единицы транзакций базы данных (DTU), что приводит к регулированию службы базы данных SQL Azure. [СТАТИСТИКУ DTU для базы данных можно просмотреть на портал Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). Если база данных достигла пределов ресурсов, [Обновите уровень служб](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) , чтобы добавить дополнительные ресурсы.
* В идеале следует запускать клиентские приложения (например, служебную программу SqlPackage или пользовательское приложение DAC) из виртуальной машины в том же регионе, что и база данных. В противном случае могут возникнуть проблемы с производительностью, связанные с задержкой сети.
* Экспорт больших таблиц без кластеризованных индексов может быть очень медленным или даже причиной сбоя. Такое поведение обусловлено тем, что таблица не может быть разбита и экспортирована параллельно. Вместо этого он должен быть экспортирован в одну транзакцию, что приводит к снижению производительности и потенциальному сбою при экспорте, особенно для больших таблиц.


## <a name="related-documents"></a>Связанные документы

[Рекомендации по экспорту базы данных](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
