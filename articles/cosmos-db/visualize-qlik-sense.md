---
title: Подключение Qlik Sense к Azure Cosmos DB и визуализация данных
description: В этой статье описываются шаги, необходимые для подключения Azure Cosmos DB к Qlik Sense и визуализации данных.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 10/22/2018
ms.reviewer: sngun
ms.openlocfilehash: 4532962b6fd9f40fad625ab000116e5a617682e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765952"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Подключение Qlik Sense к Azure Cosmos DB и визуализация данных

Средство визуализации данных Qlik Sense позволяет объединить данные из разных источников в одно представление. Qlik Sense индексирует все связи в данных, чтобы вы могли оперативно получить аналитические сведения. С помощью Qlik Sense вы можете визуализировать данные из Azure Cosmos DB. В этой статье описываются шаги, необходимые для подключения Azure Cosmos DB к Qlik Sense и визуализации данных. 

> [!NOTE]
> Подключение Qlik Sense к Azure Cosmos DB в настоящее время поддерживается только для учетных записей API SQL и API Azure Cosmos DB для MongoDB.

Вы можете подключить Qlik Sense к Azure Cosmos DB следующими способами:

* через API SQL для Cosmos DB с помощью соединителя ODBC;

* через API Azure Cosmos DB для MongoDB с помощью соединителя Qlik Sense MongoDB (сейчас доступна предварительная версия);

* через API Azure Cosmos DB для MongoDB и SQL с помощью соединителя REST API в Qlik Sense;

* через API MongoDB для Cosmos DB с помощью соединителя gRPC для Qlik Core.
В этой статье подробно описан процесс подключения к API SQL для Cosmos DB с помощью соединителя ODBC.

В этой статье подробно описан процесс подключения к API SQL для Cosmos DB с помощью соединителя ODBC.

## <a name="prerequisites"></a>Технические условия

Перед выполнением инструкций, приведенных в этой статье, обеспечьте наличие следующих ресурсов:

* Скачайте [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) или настройте Qlik Sense в Azure, [установив приложение Qlik Sense из Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Скачайте пример [данных о видеоиграх](https://www.kaggle.com/gregorut/videogamesales) в формате CSV. Вы сохраните эти данные в учетной записи Cosmos DB, а затем визуализируете их в Qlik Sense.

* Создайте учетную запись API SQL Azure Cosmos DB, следуя инструкциям в [этом разделе](create-sql-api-dotnet.md#create-account) краткого руководства.

* [Создайте базу данных и коллекцию](create-sql-api-dotnet.md#create-collection-database), установив для коллекции пропускную способность 1000 ЕЗ/с. 

* Загрузите пример данных о продаже видеоигр в учетную запись Cosmos DB. Для импорта данных можно использовать [последовательный](import-data.md#SQLSeqTarget) или [массовый импорт](import-data.md#SQLBulkTarget) данных в средстве переноса данных Azure Cosmos DB. Импорт данных в учетную запись Cosmos DB занимает около 3–5 минут.

* Скачайте, установите и настройте драйвер ODBC, следуя инструкциям в [этой статье](odbc-driver.md). Данные о видеоиграх имеют простую структуру, и вам не придется редактировать схему. Просто используйте схему сопоставления коллекций по умолчанию.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Подключение Qlik Sense к Cosmos DB

1. Откройте Qlik Sense и выберите **Create new app** (Создать приложение). Укажите имя приложения и выберите **Create** (Создать).

   ![Создание нового приложения Qlik Sense](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. После успешного создания нового приложения выберите **Open app** (Открыть приложение) и щелкните **Add data from files and other sources** (Добавить данные из файлов и других источников). 

3. В списке источников данных выберите **ODBC**, чтобы открыть окно настройки нового подключения. 

4. Переключитесь в режим **User DSN** (Пользовательский DSN) и выберите созданное ранее подключение ODBC. Укажите имя подключения и щелкните **Create** (Создать). 

   ![Создание подключения](./media/visualize-qlik-sense/create-new-connection.png)

5. Создав подключение, выберите базу данных и коллекцию, в которой размещены данные о видеоиграх, и просмотрите их.

   ![Выбор базы данных и коллекции](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Теперь щелкните **Add data** (Добавить данные), чтобы загрузить данные в Qlik Sense. Загрузив данные в Qlik Sense, вы сможете извлекать из них полезные сведения и выполнять анализ. Можно использовать стандартные средства или создать собственное приложение для анализа данных о продажах видеоигр. Пример приведен на следующем рисунке. 

   ![Визуализируйте данные](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Ограничения при подключении с помощью ODBC 

Cosmos DB — это распределенная база данных без схемы, драйвера для которой основаны на потребностях разработчиков. Драйвер ODBC требует наличия базы данных со схемой, определяющей столбцы, типы данных и другие свойства. Обычный SQL-запрос или синтаксис DML с поддержкой реляционных баз данных не применим к API SQL для Cosmos DB, так как он не является стандартным ANSI SQL. Все инструкции SQL, полученные через драйвер ODBC, преобразуются в специальный синтаксис SQL для Cosmos DB, в котором не для всех конструкций есть четкие эквиваленты. Чтобы избежать проблем с преобразованием, при настройке подключения ODBC следует применить к нему схему. Статья [о подключении с помощью драйвера ODBC](odbc-driver.md) поможет вам настроить такую схему. Не забудьте создать сопоставление для каждой базы данных и коллекции в учетной записи Cosmos DB.

## <a name="next-steps"></a>Следующие шаги

Если вы используете другое средство визуализации, например Power BI, к нему можно подключиться с помощью инструкций в следующем документе:

* [Визуализация данных Azure Cosmos DB с помощью соединителя Power BI](powerbi-visualize.md)
