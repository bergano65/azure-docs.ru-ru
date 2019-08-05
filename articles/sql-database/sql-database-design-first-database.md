---
title: Руководство по проектированию первой реляционной базы данных в службе "База данных SQL Azure" с помощью SQL Server Management Studio | Документация Майкрософт
description: Узнайте, как создать свою первую отдельную реляционную базу данных в службе "База данных SQL Azure" с помощью SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.openlocfilehash: c6ad1cd7af02f281c53ece23a018f8b5ec0c7da9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640941"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Руководство по Проектирование отдельной реляционной базы данных в службе "База данных SQL Azure" с помощью SQL Server Management Studio

База данных SQL Azure — это реляционная база данных как услуга (DBaaS) в Microsoft Cloud (Azure). В рамках этого руководства вы узнаете, как с помощью портала Azure и [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) выполнять такие действия:

> [!div class="checklist"]
> - создавать отдельную базу данных с помощью портала Azure*;
> - настраивать правило брандмауэра IP-адресов на уровне сервера с помощью портала Azure;
> - Подключение к базе данных с помощью SQL Server Management Studio.
> - создать таблицы с помощью SSMS;
> - выполнить массовую загрузку данных с помощью BCP;
> - запросить данные с помощью SSMS.

\* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

> [!TIP]
> Следующий модуль Microsoft Learn поможет вам бесплатно научиться [разрабатывать и настраивать приложение ASP.NET, которое запрашивает базу данных SQL Azure](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), а также создавать простую базу данных.
> [!NOTE]
> В рамках этого руководства используется отдельная база данных. Но вы также можете использовать базу данных в составе эластичного пула или базу данных экземпляра в управляемом экземпляре. Чтобы подключиться к управляемому экземпляру, ознакомьтесь с руководствами по управляемому экземпляру: [Краткое руководство Настройка виртуальной машины Azure для подключения к Управляемому экземпляру Базы данных SQL Azure](sql-database-managed-instance-configure-vm.md) и [Краткое руководство. Настройка подключения "точка — сеть" к Управляемому экземпляру Базы данных SQL Azure с локального компьютера](sql-database-managed-instance-configure-p2s.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуются:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (последняя версия);
- [BCP и SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (последняя версия).

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com/).

## <a name="create-a-blank-single-database"></a>создать пустую отдельную базу данных.

Отдельная база данных в службе "База данных SQL Azure" создается с определенным набором вычислительных ресурсов и ресурсов хранения. База данных создается в [группе ресурсов Azure](../azure-resource-manager/resource-group-overview.md) и управляется с помощью [сервера базы данных](sql-database-servers.md).

Чтобы создать отдельную пустую базу данных SQL, выполните приведенные ниже действия.

1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.
2. На странице **создания** в разделе Azure Marketplace выберите **Базы данных**, а затем в разделе **Избранные** выберите **База данных SQL**.

   ![Создание пустой базы данных](./media/sql-database-design-first-database/create-empty-database.png)

3. Заполните форму **База данных SQL**, указав следующую информацию, как показано на предыдущем рисунке.

    | Параметр       | Рекомендуемое значение | ОПИСАНИЕ |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Database name** (Имя базы данных) | *yourDatabase* | Допустимые имена баз данных см. в статье [Идентификаторы баз данных](/sql/relational-databases/databases/database-identifiers). |
    | **Подписка** | *yourSubscription*  | Дополнительные сведения о подписках см. [здесь](https://account.windowsazure.com/Subscriptions). |
    | **Группа ресурсов** | *yourResourceGroup* | Допустимые имена групп ресурсов см. в статье о [правилах и ограничениях именования](/azure/architecture/best-practices/naming-conventions). |
    | **Выбрать источник** | Пустая база данных | Указывает, что должна быть создана пустая база данных. |

4. Щелкните **Сервер**, чтобы использовать имеющийся сервер базы данных или создать и настроить новый сервер базы данных. Выберите существующий сервер или нажмите кнопку **Создать сервер** и заполните форму **Новый сервер**, указав следующие сведения.

    | Параметр       | Рекомендуемое значение | ОПИСАНИЕ |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Server name** (Имя сервера) | Любое глобально уникальное имя | Допустимые имена серверов см. в статье о [правилах и ограничениях именования](/azure/architecture/best-practices/naming-conventions). |
    | **Имя для входа администратора сервера** | Любое допустимое имя | Сведения о допустимых именах для входа см. в статье [Идентификаторы базы данных](/sql/relational-databases/databases/database-identifiers). |
    | **Пароль** | Любой допустимый пароль | Длина пароля должна составлять минимум восемь символов. В пароле должны использоваться символы трех категорий из перечисленных: прописные буквы, строчные буквы, цифры и специальные символы. |
    | **Местоположение.** | Любое допустимое расположение | Дополнительные сведения о регионах Azure см. [здесь](https://azure.microsoft.com/regions/). |

    ![Создание базы данных — сервер](./media/sql-database-design-first-database/create-database-server.png)

5. Нажмите кнопку **Выбрать**.
6. Щелкните **Ценовая категория**, чтобы указать уровень служб, число DTU или виртуальных ядер и объем хранилища. Вы можете изучить доступные ресурсы для каждого уровня служб (число DTU или виртуальных ядер и объем хранилища).

    Выбрав уровень служб, число DTU или виртуальных ядер и объем хранилища, нажмите кнопку **Применить**.

7. Заполните поле **Параметры сортировки** для пустой базы данных. В этом руководстве используйте значение по умолчанию. Дополнительные сведения о параметрах сортировки см. в [этой статье](/sql/t-sql/statements/collations).

8. Заполнив форму **База данных SQL**, нажмите кнопку **Создать**, чтобы подготовить к работе отдельную базу данных. Этот шаг может занять несколько минут.

9. На панели инструментов щелкните **Уведомления**, чтобы отслеживать процесс развертывания.

   ![уведомление](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Создание правила брандмауэра для IP-адресов на уровне сервера

Служба "База данных SQL" создает брандмауэр IP-адресов на уровне сервера. Он не позволяет внешним приложениям и средствам подключаться к серверу и к любой базе данных на сервере, если не создано правило брандмауэра, позволяющее пропускать их IP-адреса через брандмауэр. Чтобы разрешить внешние подключения к отдельной базе данных, необходимо сначала добавить правило брандмауэра IP-адресов, указав в нем свой IP-адрес (или диапазон IP-адресов). Выполните следующие действия, чтобы создать [правило брандмауэра IP-адресов на уровне сервера Базы данных SQL](sql-database-firewall-configure.md).

> [!IMPORTANT]
> Служба "База данных SQL" обменивается данными через порт 1433. Если вы пытаетесь подключиться к этой службе из корпоративной сети, исходящий трафик через порт 1433 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к отдельной базе данных, пока ваш администратор не откроет порт 1433.

1. По завершении развертывания щелкните раздел **Базы данных SQL** в меню слева и выберите *yourDatabase* на странице **Базы данных SQL**. После этого откроется страница обзора базы данных, где будет указано полное **имя сервера** (например, *yourserver.database.windows.net*) и будут предоставлены параметры для дальнейшей настройки.

2. Скопируйте полное имя сервера. Оно понадобится вам для подключения к серверу и связанным базам данных из SQL Server Management Studio.

   ![Имя сервера](./media/sql-database-design-first-database/server-name.png)

3. Щелкните **Настройка брандмауэра для сервера** на панели инструментов. Откроется страница **параметров брандмауэра** для сервера Базы данных SQL.

   ![Правило брандмауэра для IP-адресов на уровне сервера](./media/sql-database-design-first-database/server-firewall-rule.png)

4. На панели инструментов щелкните **Добавить IP-адрес клиента**, чтобы добавить текущий IP-адрес в новое правило брандмауэра IP-адресов. С использованием правила брандмауэра IP-адресов можно открыть порт 1433 для одного IP-адреса или диапазона IP-адресов.

5. Выберите команду **Сохранить**. Для текущего IP-адреса будет создано правило брандмауэра для IP-адресов на уровне сервера, с помощью которого можно открыть порт 1433 сервера Базы данных SQL.

6. Нажмите кнопку **ОК**, а затем закройте страницу **Параметры брандмауэра**.

Теперь IP-адрес может проходить через брандмауэр IP-адресов. Теперь можно подключиться к отдельной базе данных с помощью SQL Server Management Studio или другого средства по своему усмотрению. Обязательно используйте созданную ранее учетную запись администратора сервера.

> [!IMPORTANT]
> По умолчанию доступ через брандмауэр IP-адресов Базы данных SQL включен для всех служб Azure. На этой странице щелкните **Откл.** , чтобы отключить доступ для всех служб Azure.

## <a name="connect-to-the-database"></a>Подключение к базе данных

Используйте [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) для подключения к отдельной базе данных.

1. Откройте среду SQL Server Management Studio.
2. В диалоговом окне **Подключение к серверу** введите следующие значения.

   | Параметр       | Рекомендуемое значение | ОПИСАНИЕ |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Тип сервера** | Ядро СУБД | Это обязательное значение. |
   | **Server name** (Имя сервера) | Полное имя сервера | Например, *yourserver.database.windows.net*. |
   | **Аутентификация** | Проверка подлинности SQL Server | В рамках работы с этим руководством мы настроили только один тип проверки подлинности — проверку подлинности SQL. |
   | **Имя входа** | Учетная запись администратора сервера | Это учетная запись, указанная при создании сервера. |
   | **Пароль** | Пароль учетной записи администратора сервера | Пароль, указанный при создании сервера. |

   ![Подключение к серверу](./media/sql-database-design-first-database/connect.png)

3. Щелкните **Параметры** в диалоговом окне **Подключение к серверу**. В разделе **Подключение к базе данных** введите *yourDatabase*, чтобы подключиться к этой базе данных.

    ![Подключение к базе данных на сервере](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. Щелкните **Подключить**. В SSMS откроется окно **Обозреватель объектов**.

5. В **обозревателе объектов** разверните **Базы данных**, а затем выберите *yourDatabase*, чтобы просмотреть объекты в образце базы данных.

   ![Объекты базы данных](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Создание таблиц в базе данных

Создайте схему базы данных с четырьмя таблицами, моделирующими систему управления студентами для университетов, с помощью [Transact-SQL](/sql/t-sql/language-reference):

- Модель Person
- Курс
- Студент
- Материалы

На приведенной ниже схеме показано, как эти таблицы связаны друг с другом. Некоторые из этих таблиц ссылаются на столбцы в других таблицах. Например, таблица *Student* ссылается на столбец *PersonId* таблицы *Person*. Изучите схему, чтобы понять, как таблицы в этом руководстве связаны друг с другом. Подробные сведения о создании эффективных таблиц баз данных см. в [этой статье](https://msdn.microsoft.com/library/cc505842.aspx). Дополнительные сведения о выборе типов данных см. в [этой статье](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Для создания и проектирования таблиц можно также использовать [конструктор таблиц в SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools).

![Связи между таблицами](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. В **обозревателе объектов** щелкните правой кнопкой мыши *yourDatabase* и выберите команду **Создать запрос**. Откроется пустое окно запроса, подключенное к базе данных.

2. Чтобы создать в базе данных четыре таблицы, в окне запроса выполните следующий запрос:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![создание таблиц.](./media/sql-database-design-first-database/create-tables.png)

3. В разделе *yourDatabase* в **обозревателе объектов** разверните узел **Tables**, чтобы просмотреть созданные таблицы.

   ![Создание таблиц в SSMS](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Загрузка данных в таблицу

1. В папке загрузок создайте папку с именем *sampleData* для хранения примеров данных базы данных.

2. Щелкните правой кнопкой мыши приведенные ниже ссылки и сохраните их в папку *sampleData*.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Откройте окно командной строки и перейдите в папку *sampleData*.

4. Выполните приведенные ниже команды, которые вставляют пример данных в таблицы. Укажите значения для *сервера*, *базы данных*, *пользователя* и *пароля*, соответствующие вашей среде.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Итак, вы загрузили пример данных в созданные ранее таблицы.

## <a name="query-data"></a>Запрашивание данных

Чтобы извлечь сведения из таблиц базы данных, выполните приведенные ниже запросы. Дополнительные сведения о создании запросов SQL см. в [этой статье](https://technet.microsoft.com/library/bb264565.aspx). Первый запрос объединяет четыре таблицы для поиска студентов, которые посещают занятия у преподавателя Dominick Pope и оценки которых выше 75 %. Второй запрос объединяет четыре таблицы и находит курсы, на которые когда-либо записывался Noe Coleman.

1. В окне запроса SQL Server Management Studio выполните следующий запрос:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. В окне запроса выполните такой запрос:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали множество основных задач базы данных. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> - Создание отдельной базы данных
> - Настройка правила брандмауэра IP-адресов на уровне сервера
> - Подключение к базе данных с помощью [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).
> - создание таблиц.
> - Массовая загрузка данных.
> - Запрос данных.

Дополнительные сведения о проектировании базы данных с помощью Visual Studio и C# см. в следующем руководстве.

> [!div class="nextstepaction"]
> [Руководство. Проектирование базы данных SQL Azure и подключение к ней с помощью C# и ADO.NET](sql-database-design-first-database-csharp.md)
