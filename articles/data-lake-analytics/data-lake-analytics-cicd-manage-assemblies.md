---
title: Рекомендации по управлению сборками U-SQL в конвейере CI/CD для Azure Data Lake
description: Сведения о лучших методиках управления сборками U-SQL C# в конвейере CI/CD с помощью Azure DevOps.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 0f6f581d90b025dd538eb9b79da1a0addd4259f7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670291"
---
# <a name="best-practice-of-managing-u-sql-assemblies-in-cicd-pipeline"></a>Рекомендации по управлению сборками U-SQL в конвейере CI/CD

В этой статье вы узнаете, как управлять исходным кодом сборки U-SQL с помощью нового проекта базы данных U-SQL. Здесь вы также узнаете, как настроить конвейер непрерывной интеграции и развертывания (CI/CD) для регистрации сборки с помощью Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Использование проекта базы данных U-SQL для управления исходным кодом сборки

[Проект базы данных U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) — это тип проекта в Visual Studio, который помогает разработчикам быстро и легко разрабатывать, развертывать и администрировать свои базы данных U-SQL. Всеми объектами базы данных U-SQL (за исключением учетных данных) можно управлять с помощью проекта базы данных U-SQL. 

Рекомендуемый способ управления исходным кодом сборки C# и сценариями U-SQL на языке DDL для регистрации сборки:

* Используйте **проект базы данных U-SQL** для управления сценариями U-SQL регистрации сборки.
* Используйте **библиотеку классов (для приложения U-SQL)** для управления исходным кодом C# и зависимостями для операторов, функций и агрегаторов, определяемых пользователем (UDO/UDF/UDAG).
* Используйте проект базы данных U-SQL для ссылки на проект библиотеки классов. 

Проект базы данных U-SQL может ссылаться на проект библиотеки классов (для приложения U-SQL). Сборки, зарегистрированные в базе данных U-SQL, можно создать с помощью исходного кода C#, на который есть ссылка, из этого проекта библиотеки классов (для приложения U-SQL).

Для создания проектов и добавления ссылок можно выполнить следующие шаги:
1. Создайте проект библиотеки классов (для приложения U-SQL), используя **Файл > Создать> Проект**. Проект находится в узле **Azure Data Lake > U-SQL**.
   ![Создание проекта библиотеки классов C# с помощью инструментов Data Lake для Visual Studio](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
2. Добавьте свой код C#, определяемый пользователем, в проект библиотеки классов (для приложения U-SQL). 
3. Чтобы создать проект U-SQL, выберите **Файл > Создать > Проект**. Проект находится в узле **Azure Data Lake > U-SQL**.
   ![Создание проекта базы данных U-SQL с помощью инструментов Data Lake для Visual Studio](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
4. Добавьте ссылку на проект библиотеки классов C# для проекта базы данных U-SQL.

    ![Добавьте ссылки на проект базы данных U-SQL с помощью Средств Data Lake для Visual Studio.](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Добавьте ссылки на проект базы данных U-SQL с помощью Средств Data Lake для Visual Studio.](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)
5. Создайте сценарий сборки в проекте базы данных U-SQL. **Щелкните проект правой кнопкой мыши, а затем выберите "Добавить новый элемент"**.
   ![Добавление сценария сборки с помощью инструментов Data Lake для Visual Studio](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)
6. Откройте сценарий сборки и в представлении конструктора сборки выберите сборку, на которую указывают ссылки, в раскрывающемся меню **Создать сборку из ссылки**.

    ![Создание сборки из ссылки с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. При необходимости добавьте **Управляемые зависимости** и **Дополнительные файлы**. При добавлении дополнительных файлов средство будет использовать относительный путь, чтобы позже обеспечить возможность определения сборки на локальном компьютере и компьютере построения. 

**@_DeployTempDirectory** в окне редактора в нижней области — это предварительно определенная переменная, которая указывает инструменту на выходную папку сборки. В выходной папке сборки каждая сборка имеет вложенную папку с именем сборки. Все библиотеки DLL, а также дополнительные файлы, находятся в этой вложенной папке. 

## <a name="build-a-u-sql-database-project"></a>Создание проекта базы данных U-SQL

Выходные данные сборки для проекта базы данных U-SQL представляют собой пакет развертывания базы данных U-SQL с суффиксом `.usqldbpack`. Пакет `.usqldbpack` — это ZIP-файл, который объединяет все инструкции DDL в единый сценарий U-SQL в папке **DDL**, а также все библиотеки DLL и дополнительные файлы для сборок в папке **Temp**.

## <a name="deploy-a-u-sql-database"></a>Развертывание базы данных U-SQL

Пакет `.usqldbpack` можно развернуть для локальной учетной записи или учетной записи Azure Data Lake Analytics с помощью Visual Studio или соответствующего пакета SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Развертывание базы данных U-SQL в Visual Studio

Вы можете развернуть базу данных U-SQL, используя проект базы данных U-SQL или пакет `.usqldbpack` в среде Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Развертывание с помощью проекта базы данных U-SQL

1.  Щелкните правой кнопкой мыши проект базы данных U-SQL и выберите **Развернуть**.
2.  В **мастере развертывания Баз данных U-SQL**выберите **учетную запись ADLA**, для которой требуется выполнить развертывание базы данных. Поддерживаются оба типа учетных записей — локальные и ADLA.
3.  **Источники базы данных** заполняются автоматически, указывая на пакет .usqldbpack в выходной папке сборки проекта.
4.  Введите имя в раздел **Имя базы данных**, чтобы создать базу данных. Если база данных с этим именем уже существует в целевой учетной записи Azure Data Lake Analytics, все объекты, указанные в проекте базы данных, создаются без ее повторного создания.
5.  Щелкните **Отправить** для развертывания базы данных U-SQL. Все ресурсы (сборки и дополнительные файлы) загружаются, и отправляется задание U-SQL, которое включает все заявления DDL.

    ![Развертывание проекта базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Мастер развертывания проекта базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-u-sql-database-in-azure-devops"></a>Развертывание базы данных U-SQL в Azure DevOps

`PackageDeploymentTool.exe` предоставляет программный интерфейс и интерфейс командной строки, которые позволяют развернуть базы данных U-SQL. Пакет SDK включен в [пакет U-SQL SDK для NuGet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), размещенный в файле `build/runtime/PackageDeploymentTool.exe`.

В Azure DevOps вы можете использовать задачу командной строки и этот пакет SDK, чтобы настроить конвейер автоматизации для обновления базы данных U-SQL. См. дополнительные сведения об [использовании пакета SDK и настройке конвейера CI/CD для развертывания базы данных U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Дополнительная информация

* [Настройка конвейера CI/CD для Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Тестирование кода Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Запуск скриптов U-SQL на локальном компьютере](data-lake-analytics-data-lake-tools-local-run.md)
