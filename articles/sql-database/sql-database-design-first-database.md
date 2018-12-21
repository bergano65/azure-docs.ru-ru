---
title: Руководство. Разработка первой базы данных SQL Azure с использованием SSMS | Документация Майкрософт
description: Узнайте, как разработать первую базу данных Azure SQL с помощью SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 9fa36b9b87a8e9591b0c863826cd2278a29ba28e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956063"
---
# <a name="tutorial-design-your-first-azure-sql-database-using-ssms"></a>Руководство. Разработка первой базы данных SQL Azure с использованием SSMS

База данных SQL Azure — это реляционная база данных как услуга (DBaaS) в Microsoft Cloud (Azure). В рамках этого руководства вы узнаете, как с помощью портала Azure и [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) выполнять такие действия:

> [!div class="checklist"]
> * создать базу данных на портале Azure*;
> * настроить правило брандмауэра на уровне сервера на портале Azure;
> * Подключение к базе данных с помощью SQL Server Management Studio.
> * создать таблицы с помощью SSMS;
> * выполнить массовую загрузку данных с помощью BCP;
> * запросить данные с помощью SSMS.

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

> [!NOTE]
> В рамках этого руководства используется [ модель приобретения на основе единиц DTU](sql-database-service-tiers-dtu.md), но у вас есть возможность выбрать [модель приобретения на основе числа виртуальных ядер](sql-database-service-tiers-vcore.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуются:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (последняя версия);
- [BCP и SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (последняя версия).

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Создание пустой базы данных

База данных Azure SQL создается с определенным набором [вычислительных ресурсов и ресурсов хранения](sql-database-service-tiers-dtu.md). База данных создается в пределах [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md) и [логического сервера базы данных SQL Azure](sql-database-features.md).

Чтобы создать пустую базу данных SQL, выполните приведенные ниже действия.

1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.

1. На странице **создания** в разделе Azure Marketplace выберите **Базы данных**, а затем в разделе **Избранные** выберите **База данных SQL**.

   ![Создание пустой базы данных](./media/sql-database-design-first-database/create-empty-database.png)

   1. Заполните форму **База данных SQL**, указав следующую информацию, как показано на предыдущем рисунке.

      | Параметр       | Рекомендуемое значение | ОПИСАНИЕ |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Database name** (Имя базы данных) | *yourDatabase* | Допустимые имена баз данных см. в статье [Идентификаторы баз данных](/sql/relational-databases/databases/database-identifiers). |
      | **Подписка** | *yourSubscription*  | Дополнительные сведения о подписках см. [здесь](https://account.windowsazure.com/Subscriptions). |
      | **Группа ресурсов** | *yourResourceGroup* | Допустимые имена групп ресурсов см. в статье о [правилах и ограничениях именования](/azure/architecture/best-practices/naming-conventions). |
      | **Выбрать источник** | Пустая база данных | Указывает, что должна быть создана пустая база данных. |

   1. Щелкните **Сервер**, чтобы использовать имеющийся сервер или создать и настроить новый сервер для вашей базы данных. Выберите сервер или нажмите кнопку **Создать сервер** и заполните форму **Новый сервер**, указав следующие сведения:

      | Параметр       | Рекомендуемое значение | ОПИСАНИЕ |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Server name** (Имя сервера) | Любое глобально уникальное имя | Допустимые имена серверов см. в статье о [правилах и ограничениях именования](/azure/architecture/best-practices/naming-conventions). |
      | **Имя для входа администратора сервера** | Любое допустимое имя | Сведения о допустимых именах для входа см. в статье [Идентификаторы базы данных](/sql/relational-databases/databases/database-identifiers). |
      | **Пароль** | Любой допустимый пароль | Длина пароля должна составлять минимум восемь символов. В пароле должны использоваться символы трех категорий из перечисленных: прописные буквы, строчные буквы, цифры и специальные символы. |
      | **Местоположение.** | Любое допустимое расположение | Дополнительные сведения о регионах Azure см. [здесь](https://azure.microsoft.com/regions/). |

      ![Создание базы данных — сервер](./media/sql-database-design-first-database/create-database-server.png)

      Нажмите кнопку **Выбрать**.

   1. Щелкните **Ценовая категория**, чтобы указать уровень службы, число DTU или виртуальных ядер и объем хранилища. Вы можете изучить доступные ресурсы для каждого уровня служб (число DTU или виртуальных ядер и объем хранилища). По умолчанию выбрана [модель приобретения на основе единиц DTU](sql-database-service-tiers-dtu.md) **цен. категории "Стандартный"**, но у вас есть возможность выбрать [модель приобретения на основе числа виртуальных ядер](sql-database-service-tiers-vcore.md).

      > [!IMPORTANT]
      > Хранилище класса Premium объемом более 1 ТБ сейчас доступно во всех регионах, кроме следующих: северная часть Соединенного Королевства, центрально-западная часть США, южная часть Соединенного Королевства 2, Восточный Китай, центральный регион US DoD, Центральная Германия, восточный регион US DoD, юго-западный регион US Gov, юго-центральный регион US Gov, Северо-Восточная Германия, Северный Китай, восточный регион US Gov. В других регионах максимальный объем хранилища уровня "Премиум" равен 1 ТБ. См. [текущие ограничения для баз данных P11–P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).

      Выбрав уровень обслуживания, число DTU и объем хранилища, нажмите кнопку **Применить**.

   1. Заполните поле **Параметры сортировки** для пустой базы данных. В этом руководстве используйте значение по умолчанию. Дополнительные сведения о параметрах сортировки см. в [этой статье](/sql/t-sql/statements/collations).

1. Заполнив форму **База данных SQL**, нажмите кнопку **Создать**, чтобы подготовить базу данных. Этот шаг может занять несколько минут.

1. На панели инструментов щелкните **Уведомления**, чтобы отслеживать процесс развертывания.

     ![уведомление](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Создание правила брандмауэра

Служба базы данных SQL создает брандмауэр на уровне сервера. Брандмауэр предотвращает подключение внешних приложений и средств к серверу или к базам данных на сервере. Чтобы разрешить внешние подключения к базе данных, необходимо сначала добавить правило IP-адреса в брандмауэр. Выполните следующие действия, чтобы создать [правило брандмауэра на уровне сервера для базы данных SQL](sql-database-firewall-configure.md).

> [!NOTE]
> База данных SQL обменивается данными через порт 1433. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 1433 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к серверу Базы данных SQL Azure, пока ваш администратор не откроет порт 1433.

1. По завершении развертывания щелкните раздел **Базы данных SQL** в меню слева и выберите *yourDatabase* на странице **Базы данных SQL**. После этого откроется страница обзора базы данных, где будет указано полное **имя сервера** (например, *yourserver.database.windows.net*) и будут предоставлены параметры для дальнейшей настройки.

1. Скопируйте полное имя сервера. Оно понадобится вам для подключения к серверу и связанным базам данных из SQL Server Management Studio.

   ![Имя сервера](./media/sql-database-design-first-database/server-name.png)

1. Щелкните **Настройка брандмауэра для сервера** на панели инструментов. Откроется страница **Настройки брандмауэра** для сервера базы данных SQL.

   ![правило брандмауэра для сервера](./media/sql-database-design-first-database/server-firewall-rule.png)

   1. На панели инструментов щелкните **Добавить IP-адрес клиента**, чтобы добавить текущий IP-адрес в новое правило брандмауэра. С использованием правила брандмауэра можно открыть порт 1433 для одного IP-адреса или диапазона IP-адресов.

   1. Выберите команду **Сохранить**. Для текущего IP-адреса будет создано правило брандмауэра уровня сервера, с помощью которого можно открыть порт 1433 логического сервера.

   1. Нажмите кнопку **ОК**, а затем закройте страницу **Параметры брандмауэра**.

Теперь IP-адрес может проходить через брандмауэр. Теперь можно подключиться к серверу базы данных SQL и его базам данных с помощью SQL Server Management Studio или другого средства по своему усмотрению. Обязательно используйте созданную ранее учетную запись администратора сервера.

> [!IMPORTANT]
> По умолчанию доступ через брандмауэр базы данных SQL включен для всех служб Azure. На этой странице щелкните **Откл.**, чтобы отключить доступ для всех служб Azure.

## <a name="connect-to-the-database"></a>Подключение к базе данных

Используйте [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) для подключения к серверу базы данных SQL Azure.

1. Откройте среду SQL Server Management Studio.

1. В диалоговом окне **Подключение к серверу** введите следующие значения.

   | Параметр       | Рекомендуемое значение | ОПИСАНИЕ |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Тип сервера** | Ядро СУБД | Это обязательное значение. |
   | **Server name** (Имя сервера) | Полное имя сервера | Например, *yourserver.database.windows.net*. |
   | **Проверка подлинности** | Проверка подлинности SQL Server | В рамках работы с этим руководством мы настроили только один тип проверки подлинности — проверку подлинности SQL. |
   | **Имя входа** | Учетная запись администратора сервера | Это учетная запись, указанная при создании сервера. |
   | **Пароль** | Пароль учетной записи администратора сервера | Пароль, указанный при создании сервера. |

   ![Подключение к серверу](./media/sql-database-design-first-database/connect.png)

   1. Щелкните **Параметры** в диалоговом окне **Подключение к серверу**. В разделе **Подключение к базе данных** введите *yourDatabase*, чтобы подключиться к этой базе данных.

      ![Подключение к базе данных на сервере](./media/sql-database-design-first-database/options-connect-to-db.png)  

   1. Щелкните **Подключить**. В SSMS откроется окно **Обозреватель объектов**.

1. В **обозревателе объектов** разверните **Базы данных**, а затем выберите *yourDatabase*, чтобы просмотреть объекты в образце базы данных.

   ![Объекты базы данных](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-the-database"></a>Создание таблиц в базе данных

Создайте схему базы данных с четырьмя таблицами, моделирующими систему управления студентами для университетов, с помощью [Transact-SQL](/sql/t-sql/language-reference):

- Person
- Курс
- Студент
- Материалы

На приведенной ниже схеме показано, как эти таблицы связаны друг с другом. Некоторые из этих таблиц ссылаются на столбцы в других таблицах. Например, таблица *Student* ссылается на столбец *PersonId* таблицы *Person*. Изучите схему, чтобы понять, как таблицы в этом руководстве связаны друг с другом. Подробные сведения о создании эффективных таблиц баз данных см. в [этой статье](https://msdn.microsoft.com/library/cc505842.aspx). Дополнительные сведения о выборе типов данных см. в [этой статье](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Для создания и проектирования таблиц можно также использовать [конструктор таблиц в SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools).

![Связи между таблицами](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. В **обозревателе объектов** щелкните правой кнопкой мыши *yourDatabase* и выберите команду **Создать запрос**. Откроется пустое окно запроса, подключенное к базе данных.

1. Чтобы создать в базе данных четыре таблицы, в окне запроса выполните следующий запрос:

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

1. В разделе *yourDatabase* в **обозревателе объектов** разверните узел **Tables**, чтобы просмотреть созданные таблицы.

   ![Создание таблиц в SSMS](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Загрузка данных в таблицу

1. В папке загрузок создайте папку с именем *sampleData* для хранения примеров данных базы данных.

1. Щелкните правой кнопкой мыши приведенные ниже ссылки и сохраните их в папку *sampleData*.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

1. Откройте окно командной строки и перейдите в папку *sampleData*.

1. Выполните приведенные ниже команды, которые вставляют пример данных в таблицы. Укажите значения для *сервера*, *базы данных*, *пользователя* и *пароля*, соответствующие вашей среде.
  
   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Итак, вы загрузили пример данных в созданные ранее таблицы.

## <a name="query-data"></a>Запрос данных

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

1. В окне запроса выполните такой запрос:

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
> * Создание базы данных
> * Настройка правила брандмауэра.
> * Подключение к базе данных с помощью [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS).
> * создание таблиц.
> * Массовая загрузка данных.
> * Запрос данных.

Дополнительные сведения о проектировании базы данных с помощью Visual Studio и C# см. в следующем руководстве.

> [!div class="nextstepaction"]
> [Проектирование базы данных SQL Azure и подключение к ней с помощью C# и ADO.NET](sql-database-design-first-database-csharp.md)
