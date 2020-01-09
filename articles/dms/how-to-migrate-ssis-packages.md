---
title: Повторное развертывание пакетов служб SSIS в отдельной базе данных SQL
titleSuffix: Azure Database Migration Service
description: Узнайте, как выполнить миграцию или повторное развертывание пакетов SQL Server Integration Services и проектов в базе данных SQL Azure с помощью Azure Database Migration Service и Помощник по миграции данных.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: b1889410a6c6925ebba5632a08c34bc967ced627
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437971"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Повторное развертывание пакетов служб SSIS в базе данных SQL Azure с помощью Azure Database Migration Service

Если вы используете SQL Server Integration Services (SSIS) и хотите перенести проекты или пакеты служб SSIS из исходной базы данных SSISDB, размещенной в SQL Server, в целевом SSISDB, размещенном на сервере SQL Azure, можно повторно развернуть их с помощью мастера развертывания Integration Services. Мастер можно запустить из SQL Server Management Studio (SSMS).

Если используется более ранняя версия служб SSIS, чем 2012, то перед повторным развертыванием пакетов или проектов SSIS в модель развертывания проекта сначала необходимо преобразовать их с помощью мастера преобразования проектов Integration Services, который также можно запустить из SSMS. Дополнительные сведения см. в статье [Преобразование проектов в модель развертывания проекта](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) сейчас не поддерживает миграцию исходного SSISDB на сервер базы данных SQL Azure, но вы можете повторно развернуть проекты или пакеты служб SSIS, используя следующий процесс.

Вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
>
> * Оценка проектов и пакетов исходной SSIS.
> * Перенос проектов и пакетов SSIS в Azure.

## <a name="prerequisites"></a>Технические условия

Для выполнения этих действий вам потребуется следующее:

* SSMS 17.2 или более поздней версии.
* Экземпляр целевого сервера базы данных для размещения SSISDB. Если у вас ее еще нет, создайте сервер базы данных SQL Azure (без базы данных), используя портал Azure, перейдя к [форме](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (только логический сервер).
* Службы SSIS должны быть подготовлены в фабрике данных Azure (ADF), которая содержит Azure-SSIS Integration Runtime (IR) с целевой базой данных SSISDB, размещенной на экземпляре сервера Azure SQL Server (как описано в статье [подготовка Azure-SSIS Integration Runtime в фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Оценка проектов и пакетов исходной SSIS

Хотя оценка исходного SSISDB еще не интегрирована в базу данных Помощник по миграции (DMA) или Azure Database Migration Service (DMS), проекты и пакеты служб SSIS будут оцениваться или проверяться при повторном развертывании в целевом SSISDB, размещенном на сервере базы данных SQL Azure.

## <a name="migrate-ssis-projectspackages"></a>Управление проектами и пакетами SSIS

Чтобы перенести проекты или пакеты служб SSIS на сервер базы данных SQL Azure, выполните следующие действия.

1. Откройте среду SSMS и выберите **Параметры**, чтобы открыть диалоговое окно **Подключиться к серверу**.

2. На вкладке **имя входа** укажите сведения, необходимые для подключения к серверу базы данных SQL Azure, на котором будет размещена целевая SSISDB.

    ![Вкладка "Вход в систему" SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. На вкладке **Свойства соединения** в текстовом поле **Подключение к базе данных** выберите или введите **SSISDB**, а затем нажмите кнопку **Подключиться**.

    ![Вкладка "Свойства подключения" SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. В обозревателе объектов SSMS разверните узел **Каталоги Integration Services**, разверните **SSISDB** и,если нет существующих папок, щелкните **SSISDB** правой кнопкой мыши и создайте новую папку.

5. В разделе **SSISDB** разверните любую папку, щелкните **Проекты** правой кнопкой мыши и выберите **Развернуть проект**.

    ![Сведения об узле SSISDB SSIS](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. В мастере развертывания Integration Services на странице **Общие сведения** просмотрите сведения и нажмите кнопку **Далее**.

    ![Страница "Общие сведения" мастера развертывания](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. На странице **Выбор источника** укажите существующий проект служб SSIS, который вы хотите развернуть.

    Если среда SSMS также подключена к SQL Server, на котором размещена исходная SSISDB, выберите **Каталог Integration Services** и затем укажите имя сервера и путь к проекту в каталоге, чтобы развернуть проект напрямую.

    Вы также можете выбрать **Файл развертывания проекта** и указать путь к имеющемуся файлу развертывания проекта (.ispac) для развертывания проекта.

    ![Страница "Выбрать источник" мастера развертывания](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Выберите **Далее**.
9. На странице **Выбор места назначения** укажите назначение проекта.

    а. В текстовом поле имя сервера введите полное имя сервера базы данных SQL Azure (< server_name >. Database. Windows. NET).

    b. Укажите сведения для проверки подлинности и выберите **Подключиться**.

    ![Страница "Выбор места назначения" мастера развертывания](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Нажмите кнопку **Обзор** , чтобы указать папку назначения в SSISDB, а затем нажмите кнопку **Далее**.

    > [!NOTE]
    > Кнопка **Далее** становится доступной только после того, как вы нажали кнопку **Подключиться**.

10. На странице **Проверка** просмотрите ошибки или предупреждения и затем при необходимости измените пакеты.

    ![Страница "Проверка" мастера развертывания](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Выберите **Далее**.

12. На странице **Обзор** просмотрите параметры развертывания.

    > [!NOTE]
    > Вы можете изменить параметры, выбрав пункт **назад** или любую из ссылок на шаге в левой области.

13. Выберите **Развернуть**, чтобы запустить процесс развертывания.

14. После завершения развертывания можно просмотреть страницу "Результаты", на которой отображается информация об успешном или неудачном результате каждого действия развертывания.
    а. Если произошел сбой любого действия, в столбце **Результат** выберите **Сбой**, чтобы отобразить описание ошибки.
    b. При необходимости выберите **Сохранить отчет**, чтобы сохранить результаты в XML-файл.

15. Выберите **Закрыть**, чтобы выйти из мастера развертывания Integration Services.

Если развертывание проекта завершается успешно, вы можете запустить любые пакеты из этого проекта в среде выполнения интеграции Azure-SSIS IR.

## <a name="next-steps"></a>Дальнейшие действия

* Просмотрите [руководство по миграции базы данных Майкрософт](https://datamigration.microsoft.com/).
