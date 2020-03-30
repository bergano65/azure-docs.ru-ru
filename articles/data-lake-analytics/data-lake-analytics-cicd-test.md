---
title: Тестирование кода Azure Data Lake Analytics
description: Сведения о добавлении тестовых случаев в расширенный код C# и U-SQL для Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70913963"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Тестирование кода Azure Data Lake Analytics

Озеро данных Azure предоставляет язык [U-S'L.](data-lake-analytics-u-sql-get-started.md) Для обработки данных в любом масштабе компания U-S'L сочетает декларативную систему СЗЛ с императивом C'L. В этом документе вы узнаете, как создавать тестовые кейсы для кода оператора, определяемого пользователями (UDO), с расширенным Кодом оператора, определяемого пользователем.

## <a name="test-u-sql-scripts"></a>Тестирование скриптов U-SQL

Скрипт U-S'L компилируется и оптимизируется для выполнения исполняемого кода для работы в Azure или на локальном компьютере. В процессе компиляции и оптимизации обрабатывается весь сценарий U-SQL. Вы не можете сделать традиционный модульный тест для каждого оператора. Но используя тестовый пакет SDK для U-SQL и пакет SDK для запуска в локальной среде, можно выполнить тесты на уровне сценариев.

### <a name="create-test-cases-for-u-sql-script"></a>Создание тестовых случаев для скриптов U-SQL

Средства Azure Data Lake для Visual Studio позволяют создавать тестовые случаи для скриптов U-SQL.

1. В обозревателе решений щелкните правой кнопкой мыши сценарий U-SQL, а затем выберите **Создать модульный тест**.

1. Создайте новый тестовый проект или вставьте тестовый случай в существующий тестовый проект.

   ![Создание конфигурации тестового проекта U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Управление источником тестовых данных

При тестировании сценариев U-SQL требуются файлы с тестовыми входными данными. Для управления тестовых данных в **Solution Explorer**нажмите на кнопку «U-S'L» и выберите **Свойства.** Вы можете ввести источник в **источнике данных тестирования**.

![Data Lake Tools для Visual Studio — настройка источника тестовых данных проекта](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

При вызове `Initialize()` интерфейса в тесте SDK, разработанном в соответствии с рабочим каталогом тестового проекта, создается временная локальная папка корня данных. Все файлы и папки в папке исхода исходных данных теста копируются во временную локальную папку корневой данных перед запуском тестовых случаев U-S'L. Можно добавить дополнительные папки источника тестовых данных, разделив пути к папкам с тестовыми данными точкой с запятой.

### <a name="manage-the-database-environment-for-testing"></a>Управление средой базы данных для тестирования

Если в ваших скриптах U-S'L используются или задавлемы с объектами базы данных U-S'L, необходимо инициализировать среду базы данных перед запуском тестовых случаев U-S'L. Такой подход может быть необходим при вызове сохраненных процедур. Интерфейс `Initialize()` в тестовом пакете SDK для U-SQL позволяет развернуть все базы данных, на которые ссылается проект U-SQL, во временную локальную корневую папку данных в рабочей папке тестового проекта.

Для получения более [подробной](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)информации о том, как управлять ссылками на проекты по проектам базы данных U-S'L для проекта U-S'L, см.

### <a name="verify-test-results"></a>Проверка результатов теста

Интерфейс `Run()` возвращает результат выполнения задания. *0* означает успех, а *1* означает неудачу. Чтобы проверить выходные данные, можно также использовать функции assert в C#.

### <a name="run-test-cases-in-visual-studio"></a>Запуск тестовых случаев в Visual Studio

Тестовый проект сценария U-SQL создается на базе платформы модульного тестирования C#. После создания проекта выберите **Test** > **Test** > **Explorer.** Вы можете запускать тестовые случаи от **Test Explorer.** Кроме того, нажмите правой кнопкой мыши файла .cs в вашем едином тесте и выберите **Run Tests.**

## <a name="test-c-udos"></a>Тестирование определяемых пользователем операторов C#

### <a name="create-test-cases-for-c-udos"></a>Создание тестовых случаев для определяемых пользователем операторов C#

Для тестирования операторов, определяемых пользователями (UDOs), можно использовать платформу тестирования единицы C'. При тестировании UDO необходимо подготовить соответствующий объект **IRowset** в качестве входных данных.

Существует два способа создания объекта **IRowset:**

- Загрузите данные из файла для создания **IRowset:**

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Используйте данные из сбора данных для создания **IRowset:**

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Проверка результатов теста

После вызова функций UDO результат можно проверить, используя схемы и проверку значения Rowset с помощью функций assert в C#. Вы можете добавить в свое решение **тестовый проект U-S'S'L C'Do.** Для этого выберите **проект File > New >** в Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Запуск тестовых случаев в Visual Studio

После создания проекта выберите **Test** > **Test** > **Explorer.** Вы можете запускать тестовые случаи от **Test Explorer.** Кроме того, нажмите правой кнопкой мыши файла .cs в вашем едином тесте и выберите **Run Tests.**

## <a name="run-test-cases-in-azure-pipelines"></a>Запуск тестовых случаев в трубопроводах Azure<a name="run-test-cases-in-azure-devops"></a>

Оба проекта, **тестовые проекты сценария U-SQL** и **тестовые проекты UDO C#**, наследуют проекты модульного теста C#. [Тестовая задача Visual Studio](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) в Azure Pipelines может выполнить эти тестовые случаи.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Запуск тестовых случаев U-S'L в трубопроводах Azure

Для тестирования U-S'L убедитесь, что вы загружаете `CPPSDK` компьютер сборки, а затем пройдите `CPPSDK` путь к `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`.

#### <a name="what-is-cppsdk"></a>Что такое CPPSDK?

CPPSDK — это пакет, включающий Microsoft Visual C++ 14 и Windows SDK 10.0.10240.0. Этот пакет включает среду, необходимую для выполнения U-S'L. Этот пакет можно получить в папке установки Средств Azure Data Lake для Visual Studio.

- Для Visual Studio 2015: `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`.
- Для Visual Studio 2017: `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`.
- Для Visual Studio 2019, он находится под`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Подготовка CPPSDK в агенте по сборке трубопроводов Azure

Наиболее распространенным способом подготовки зависимости CPPSDK в трубопроводах Azure является следующее:

1. Застегните папку, которая включает библиотеки CPPSDK.

1. Добавьте ZIP-файл в систему управления версиями Файл .zip гарантирует, что вы регистрируете все библиотеки в папке CPPSDK, `.gitignore` чтобы файлы не были проигнорированы из-за файла.

1. Распакуйте ZIP-файл в конвейер сборки.

1. Укажите `USqlScriptTestRunner` на эту незастегнутую папку на компьютере сборки.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Запуск тестовых случаев C' UDO в трубопроводах Azure

Для теста На УДО, не забудьте соссылкой следующие сборки, которые необходимы для UDOs.

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.Types
- Microsoft.Analytics.UnitTest

Если вы ссылаетесь на них с помощью [Microsoft.Azure.DataLake.USQL.Interfaces пакета Nuget ](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), добавьте задачу восстановления NuGet в конвейер сборки.

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка конвейера CI/CD для Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Запуск скриптов U-SQL на локальном компьютере](data-lake-analytics-data-lake-tools-local-run.md)
- [Для разработки базы данных U-S'L для разработки базы данных U-S'L](data-lake-analytics-data-lake-tools-develop-usql-database.md)
