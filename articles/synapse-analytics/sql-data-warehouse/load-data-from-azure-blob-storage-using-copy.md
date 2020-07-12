---
title: Учебник. Загрузка данных такси в Нью Йорк
description: В руководстве используется портал Azure и SQL Server Management Studio для загрузки данных такси в Нью-Йорке из большого двоичного объекта Azure для синапсе SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/31/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c4dbc63e8829d8a9ca3a3820fbb6675da4fad357
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262243"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Учебник. Загрузка набора данных такси в Нью Йорк

В этом руководстве используется [инструкция Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) для загрузки набора данных такси в Нью-Йорке из учетной записи хранилища BLOB-объектов Azure. В рамках этого руководства [портал Azure](https://portal.azure.com) и [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) используются, чтобы выполнить такие действия:

> [!div class="checklist"]
>
> * Создание пула SQL на портал Azure
> * настроить правило брандмауэра на уровне сервера на портале Azure;
> * подключиться к хранилищу данных с помощью SSMS;
> * создать учетную запись пользователя, предназначенную для загрузки данных;
> * Создание таблиц для образца набора данных 
> * Использование инструкции COPY T-SQL для загрузки данных в хранилище данных
> * просмотреть ход выполнения загрузки данных;

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="before-you-begin"></a>Перед началом

Перед началом работы с этим руководством скачайте и установите последнюю версию [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Создание пустой базы данных

Пул SQL создается с определенным набором [вычислительных ресурсов](memory-concurrency-limits.md). База данных создается в пределах [группы ресурсов Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) и [логического сервера SQL](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Чтобы создать пустую базу данных, выполните приведенные ниже действия.

1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.

2. Выберите **базы данных** на **новой** странице и выберите **Azure синапсе Analytics** в разделе **Избранное** на **новой** странице.

    ![создание хранилища данных](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Заполните форму, указав следующую информацию.

   | Параметр            | Рекомендуемое значение       | Описание                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *Безымян**            | mySampleDataWarehouse | Допустимые имена баз данных см. в статье об [идентификаторах базы данных](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). |
   | **Подписка**   | Ваша подписка     | Дополнительные сведения о подписках см. [здесь](https://account.windowsazure.com/Subscriptions). |
   | **Группа ресурсов** | myResourceGroup       | Допустимые имена групп ресурсов см. в статье о [правилах и ограничениях именования](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |
   | **Выбрать источник**  | Пустая база данных        | Указывает, что требуется создать пустую базу данных. Обратите внимание, что хранилище данных — это один из типов базы данных. |

    ![создание хранилища данных](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Выберите **Сервер**, чтобы создать и настроить сервер для новой базы данных. Заполните форму для **создания сервера**, указав следующую информацию.

    | Параметр                | Рекомендуемое значение          | Описание                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **Имя сервера**        | Любое глобально уникальное имя | Допустимые имена серверов см. в статье о [правилах и ограничениях именования](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |
    | **Имя для входа администратора сервера** | Любое допустимое имя           | Допустимые имена входа см. в статье об [идентификаторах базы данных](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). |
    | **Пароль**           | Любой допустимый пароль       | Длина пароля должна составлять минимум 8 символов. Пароль должен содержать символы трех категорий из перечисленных: прописные буквы, строчные буквы, цифры и специальные символы. |
    | **Расположение**           | Любое допустимое расположение       | Дополнительные сведения о регионах Azure см. [здесь](https://azure.microsoft.com/regions/). |

    ![создать сервер](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Щелкните **Выбрать**.

6. Выберите **уровень производительности** , чтобы указать, является ли хранилище данных Gen1 или Gen2, а также количество единиц использования хранилища данных.

7. В этом руководстве выберите пул SQL **Gen2**. По умолчанию ползунок имеет значение **DW1000c** .  Попробуйте переместить его вверх и вниз, чтобы увидеть, как он работает.

    ![настройка производительности](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Нажмите кнопку **Применить**.
9. В колонке подготовка выберите **Параметры сортировки** для пустой базы данных. В этом руководстве используйте значение по умолчанию. Дополнительные сведения о параметрах сортировки см. в [этой статье](/sql/t-sql/statements/collations?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

10. После заполнения формы выберите **создать** , чтобы подготовить базу данных. Подготовка занимает несколько минут.

11. На панели инструментов щелкните значок **Уведомления**, чтобы отслеживать процесс развертывания.
  
     ![уведомление](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>создадим правило брандмауэра на уровне сервера;

Брандмауэр на уровне сервера, который предотвращает подключение внешних приложений и средств к серверу или к любой базе данных на сервере. Чтобы разрешить это подключение, можно добавить правила брандмауэра, открывающие подключение для определенных IP-адресов.  Выполните приведенные ниже действия, чтобы создать [правило брандмауэра уровня сервера](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) для IP-адреса клиента.

> [!NOTE]
> Хранилище данных SQL обменивается данными через порт 1433. Если вы пытаетесь подключиться из корпоративной сети, то исходящий трафик через порт 1433 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к серверу, пока ваш ИТ-отдел не откроет порт 1433.

1. После завершения развертывания в меню слева выберите **базы данных SQL** , а затем выберите **mySampleDatabase** на странице **базы данных SQL** . После этого откроется страница обзора базы данных, где будет указано полное имя сервера (например, **mynewserver-20180430.database.windows.net**) и предоставлены параметры для дальнейшей настройки.

2. Скопируйте полное имя сервера. Оно понадобится вам при работе с последующими руководствами для подключения к серверу и связанным базам данных. Затем выберите имя сервера, чтобы открыть параметры сервера.

    ![поиск имени сервера](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

3. Выберите имя сервера, чтобы открыть параметры сервера.

    ![параметры сервера](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

4. Щелкните **Показать параметры брандмауэра**. Откроется страница **Параметры брандмауэра** сервера.

    ![правило брандмауэра для сервера](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

5. На панели инструментов выберите **Добавить IP-адрес клиента**, чтобы добавить текущий IP-адрес в новое правило брандмауэра. С использованием правила брандмауэра можно открыть порт 1433 для одного IP-адреса или диапазона IP-адресов.

6. Нажмите **Сохранить**. Для текущего IP-адреса будет создано правило брандмауэра на уровне сервера, с помощью которого можно открыть порт 1433 на сервере.

7. Нажмите кнопку **ОК** , а затем закройте страницу **Параметры брандмауэра** .

Теперь вы можете подключиться к серверу и его хранилищам данных, используя этот IP-адрес. Подключение выполняется из SQL Server Management Studio или другого инструмента на ваше усмотрение. При подключении используйте созданную ранее учетную запись ServerAdmin.  

> [!IMPORTANT]
> По умолчанию доступ через брандмауэр базы данных SQL включен для всех служб Azure. Выберите **Отключить** на этой странице, а затем нажмите кнопку **сохранить** , чтобы отключить брандмауэр для всех служб Azure.

## <a name="get-the-fully-qualified-server-name"></a>Получение полного имени сервера

Получите полное имя сервера на портале Azure. Это полное имя затем понадобится при подключении к серверу.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Выберите **Azure синапсе Analytics** в меню слева и выберите свою базу данных на странице **Azure синапсе Analytics** .
3. На странице портала Azure вашей базы данных в области **Основные компоненты** найдите и скопируйте **имя сервера**. В этом примере полное имя — mynewserver-20180430.database.windows.net.

    ![Сведения о подключении](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Подключение к серверу от имени администратора сервера

В этом разделе для подключения к серверу используется [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

1. Откройте среду SQL Server Management Studio.

2. В диалоговом окне **Соединение с сервером** введите следующие данные:

    | Параметр        | Рекомендуемое значение                            | Описание                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | Тип сервера    | Ядро СУБД                            | Это обязательное значение                                       |
    | Имя сервера    | Полное имя сервера            | Имя должно быть в таком формате: **mynewserver-20180430.database.windows.net**. |
    | Проверка подлинности | Проверка подлинности SQL Server                  | В рамках работы с этим руководством мы настроили только один тип проверки подлинности — проверку подлинности SQL. |
    | Имя входа          | Учетная запись администратора сервера                   | Это учетная запись, которая была указана при создании сервера. |
    | Пароль       | Пароль для учетной записи администратора сервера | Это пароль, указанный при создании сервера. |

    ![подключение к серверу](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. Щелкните **Подключить**. В SSMS открывается окно обозревателя объектов.

4. В обозревателе объектов разверните узел **Базы данных**. Затем разверните **Системные базы данных** и **master**, чтобы просмотреть объекты в базе данных master.  Разверните папку **mySampleDatabase**, чтобы просмотреть объекты в новой базе данных.

    ![объекты базы данных](./media/load-data-from-azure-blob-storage-using-polybase/connected.png)

## <a name="create-a-user-for-loading-data"></a>Создание учетной записи пользователя для загрузки данных

Учетная запись администратора сервера предназначена для операций управления и не подходит для выполнения запросов к пользовательским данным. Загрузка данных — это операция с интенсивным использованием памяти. Максимальные объемы памяти определяются в соответствии с настроенными единицами и [классами ресурсов](resource-classes-for-workload-management.md) [хранилища данных](what-is-a-data-warehouse-unit-dwu-cdwu.md) .

Лучше всего создать имя для входа и имя пользователя, предназначенные для загрузки данных. Затем добавьте пользователя загрузки в [класс ресурсов](resource-classes-for-workload-management.md), позволяющий выполнять соответствующее выделение максимальной памяти.

Поскольку вы подключены в качестве администратора сервера, вы можете создавать пользователей и имена для входа. Используйте эти действия для создания имени для входа и пользователя **LoaderRC20**. Затем назначьте пользователя классу ресурсов **staticrc20**.

1. В среде SSMS щелкните правой кнопкой мыши пункт **Главная** , чтобы открыть раскрывающееся меню, и выберите пункт **создать запрос**. Откроется новое окно запроса.

    ![Создание запроса в базе данных master](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. В окне запроса введите следующие команды T-SQL, чтобы создать имя для входа и пользователя LoaderRC20, заменив 'a123STRONGpassword!' на собственный пароль.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Нажмите кнопку **Выполнить**.

4. Щелкните правой кнопкой мыши **mySampleDataWarehouse** и выберите **Новый запрос**. Откроется окно нового запроса.  

    ![Новый запрос к примеру хранилища данных](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Введите следующие команды T-SQL, чтобы создать пользователя базы данных с именем LoaderRC20 для имени для входа LoaderRC20. Вторая строка предоставляет новому пользователю разрешения на управление (CONTROL) новым хранилищем данных.  Эти разрешения аналогичны назначению пользователя владельцем базы данных. Третья строка добавляет нового пользователя в качестве участника [класса ресурсов](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Нажмите кнопку **Выполнить**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Подключение к серверу от имени пользователя, выполняющего загрузку

Первый шаг к загрузке данных — это войти в систему как пользователь LoaderRC20.  

1. В обозревателе объектов выберите раскрывающееся меню **Подключение** и выберите **ядро СУБД**. Откроется диалоговое окно **Соединение с сервером** .

    ![Подключение с помощью нового имени для входа](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Введите полное имя сервера и в качестве имени для входа укажите **LoaderRC20**.  Введите пароль для LoaderRC20.

3. Щелкните **Подключить**.

4. Когда подключение будет готово, вы увидите в обозревателе объектов два подключения к серверу. Одно подключение — как ServerAdmin, а второе — как MedRCLogin.

    ![Подключение выполнено успешно](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Создание таблиц для демонстрационных данных

Все готово для начала процесса загрузки данных в новое хранилище данных. В этой части руководства показано, как использовать инструкцию COPY для загрузки набора данных CAB-файла в городе Нью-Йорка из большого двоичного объекта службы хранилища Azure. Дополнительные сведения о том, как получить данные в хранилище BLOB-объектов Azure или загрузить их непосредственно из источника, см. в разделе [Общие сведения о загрузке](design-elt-data-loading.md).

Выполните следующие скрипты SQL и укажите сведения о данных, которые вы хотите загрузить. К этим сведениям относится информация о расположении данных, формате содержимого данных и определении таблицы для данных.

1. В предыдущем разделе вы вошли в хранилище данных как пользователь LoaderRC20. В среде SSMS щелкните правой кнопкой мыши подключение LoaderRC20 и выберите **Новый запрос**.  Откроется окно нового запроса.

    ![Окно нового запроса загрузки](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Сравните окно запроса с предыдущим изображением.  Убедитесь, что окно нового запроса запущено как LoaderRC20 и выполняет запросы к базе данных MySampleDataWarehouse. Используйте это окно запроса для выполнения всех шагов загрузки.

7. Выполните следующие инструкции T-SQL для создания таблиц:

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>Загрузка данных в хранилище данных

В этом разделе для загрузки образца данных из Azure Storage Blob используется [инструкция Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) .  

> [!NOTE]
> В этом руководстве данные загружаются непосредственно в итоговую таблицу. Обычно для рабочих нагрузок загружается промежуточная таблица. Если данные расположены в промежуточной таблице, вы можете выполнять все необходимые преобразования. 

1. Чтобы загрузить данные, выполните следующие инструкции:

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. Просмотрите данные при загрузке. Вы загружаете несколько гигабайт данных и сжимаете их в высокопроизводительные кластеризованные индексы columnstore. Выполните следующий запрос, использующий динамические административные представления, для отображения состояния загрузки.

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. Просмотрите все запросы в системе.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Посмотрите, как данные упорядоченно загружаются в хранилище данных.

    ![Просмотр загруженных таблиц](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Плата взимается за вычислительные ресурсы и данные, которые загружаются в хранилище данных. По ним выставляется отдельный счет.

* Если вы хотите сохранить данные в хранилище, то можете приостановить работу вычислительных ресурсов, когда не используете хранилище данных. В случае приостановки работы вычислительных ресурсов плата будет взиматься только за хранение данных. Вы можете возобновить работу вычислительных ресурсов в любой момент, когда готовы приступить к работе с данными.
* Если вы хотите исключить будущие расходы, то можете удалить хранилище данных.

Выполните следующие действия, чтобы очистить ресурсы по необходимости.

1. Войдите в [портал Azure](https://portal.azure.com), выберите хранилище данных.

    ![Очистка ресурсов](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Чтобы приостановить вычисление, нажмите кнопку **Пауза**. Если работа хранилища данных приостановлена, то отобразится кнопка **Запуск**.  Чтобы возобновить вычисление, нажмите кнопку **Пуск**.

3. Чтобы удалить хранилище данных, чтобы не оплатить расчеты или хранилища, выберите **Удалить**.

4. Чтобы удалить созданный сервер, выберите **mynewserver-20180430.Database.Windows.NET** на предыдущем изображении и нажмите кнопку **Удалить**.  Будьте внимательны, так как удаление сервера приведет к удалению всех баз данных, назначенных этому серверу.

5. Чтобы удалить группу ресурсов, выберите **myResourceGroup**, а затем **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как создать хранилище данных, а также как создать пользователя для загрузки данных. Вы использовали простую [инструкцию Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples) для загрузки данных в хранилище данных.

Вы выполнили такие действия:
> [!div class="checklist"]
>
> * создали хранилище данных на портале Azure;
> * настроить правило брандмауэра на уровне сервера на портале Azure;
> * подключились к хранилищу данных с помощью SSMS;
> * создали учетную запись пользователя, предназначенную для загрузки данных;
> * Созданы таблицы для образца данных
> * Использование инструкции COPY T-SQL для загрузки данных в хранилище данных
> * просмотрели ход выполнения загрузки данных;

Перейдите к обзору разработки, чтобы узнать, как перенести существующую базу данных в Azure синапсе Analytics.

> [!div class="nextstepaction"]
> [Разработка решений для переноса существующей базы данных в Azure синапсе Analytics](sql-data-warehouse-overview-develop.md)

Дополнительные примеры загрузки и ссылки см. в следующей документации:

- [Справочная документация по инструкции COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [Примеры копирования для каждого метода проверки подлинности](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [Быстрый Запуск копирования для одной таблицы](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
