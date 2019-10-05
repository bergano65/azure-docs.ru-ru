---
title: Служба импорта и экспорта базы данных SQL Azure занимает много времени для импорта или экспорта базы данных | Документация Майкрософт
description: Служба импорта и экспорта базы данных SQL Azure занимает много времени для импорта или экспорта базы данных.
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974465"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Служба импорта и экспорта базы данных SQL Azure занимает много времени для импорта или экспорта базы данных.

При использовании службы импорта и экспорта базы данных SQL Azure можно заметить, что иногда процесс может занять много времени. Эта статья содержит дополнительные сведения о возможных причинах задержки три и альтернативных методах, которые можно использовать для решения этих проблем.

## <a name="azure-sql-database-importexport-service"></a>Служба импорта и экспорта базы данных SQL Azure

Служба импорта и экспорта базы данных SQL Azure — это веб-служба на основе RESTFUL, которая выполняется в каждом Microsoft Azure центре обработки данных. Это служба, которая вызывается при использовании параметров [импорта базы данных](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) или [экспорта](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) для перемещения базы данных SQL в портал Microsoft Azure. Служба предоставляет бесплатную службу очереди запросов и бесплатную службу вычислений для выполнения импорта и экспорта из базы данных SQL Microsoft Azure для Microsoft Azure хранилища больших двоичных объектов (BLOB).

Операции импорта и экспорта не являются традиционной физической резервной копией базы данных, а логической резервной копией базы данных, использующей Специальный формат BACPAC. Этот логический формат BACPAC позволяет избежать использования физического формата, который может отличаться в зависимости от версии SQL Server и базы данных SQL. Таким образом, его можно использовать для безопасного восстановления базы данных в базу данных SQL и в базу данных SQL Server.

## <a name="what-causes-the-process-to-take-a-long-time"></a>Что заставляет процесс занять много времени

Служба импорта и экспорта базы данных SQL Azure предоставляет ограниченное количество виртуальных машин (ВМ) для каждого региона для обработки операций импорта и экспорта. Среда выполнения вычислений размещается для каждого региона, чтобы гарантировать, что импорт или экспорт не позволит избежать задержек пропускной способности между регионами и расходов. Таким образом, если в одном регионе выполняется слишком много запросов, то при обработке операций происходят значительные задержки. Время, необходимое для выполнения запросов, может варьироваться от нескольких секунд до нескольких часов.

> [!NOTE]
> Если запрос не обрабатывается в течение четырех дней, служба автоматически отменяет запрос.

## <a name="recommended-solutions"></a>Рекомендуемые решения

Если экспорт базы данных используется только для восстановления после случайного удаления данных, все выпуски базы данных Azure SQL Server обеспечивают возможность самостоятельного восстановления из созданных системой резервных копий. Но если эти экспорты необходимы по другим причинам, и если требуется постоянно ускорить или более предсказуемую производительность импорта или экспорта, следует рассмотреть следующие варианты.

* [Экспорт в BACPAC-файл с помощью служебной программы SQLPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [Экспорт в BACPAC-файл с помощью SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Выполните импорт или экспорт BACPAC непосредственно в код с помощью API Microsoft® SQL Server® платформы приложений уровня данных (DacFx). Дополнительные сведения о проверке
  * [Экспорт приложения уровня данных](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Пространство имен Microsoft. SqlServer. DAC](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Скачать DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>Рекомендации по экспорту и импорту базы данных SQL Azure

* Все методы, описанные в этой статье, используют квоту DTU, что приводит к регулированию службой Azure SQLDB. [СТАТИСТИКУ DTU для базы данных можно просмотреть на портал Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Если база данных достигает пределов ресурсов, [Обновите уровень служб](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) , чтобы добавить дополнительные ресурсы.
* В идеале клиентские приложения (например, служебная программа SqlPackage или пользовательское приложение DAC) следует запускать с виртуальной машины в том же регионе, что и база данных SQL, или, в противном случае, могут возникнуть проблемы с производительностью из-за задержки в сети.
* Экспорт больших таблиц без кластеризованных индексов может выполняться очень быстро или даже привести к сбою. Это обусловлено тем, что таблица не может быть разделена и экспортирована параллельно и должна быть экспортирована в одну транзакцию, что приводит к медленная работа и потенциальным сбоям при экспорте, особенно для больших таблиц. 


## <a name="related-documents"></a>Связанные документы

[Рекомендации по экспорту базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
