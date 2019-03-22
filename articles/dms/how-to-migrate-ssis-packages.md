---
title: Перенос пакетов SQL Server Integration Services в Azure | Документация Майкрософт
description: Сведения о переносе пакетов SQL Server Integration Services в Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 884af4624c1e92ee765353c90fd189220664381d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183430"
---
# <a name="migrate-sql-server-integration-services-packages-to-azure"></a>Перенос пакетов SQL Server Integration Services в Azure
Если вы используете SQL Server Integration Services (SSIS) и хотите перенести проекты или пакеты SSIS из исходной SSISDB, размещенной на SQL Server, в целевую SSISDB, расположенную на сервере Базы данных SQL Azure или в Управляемом экземпляре Базы данных SQL Azure, вы можете повторно развернуть их с помощью мастера развертывания Integration Services. Мастер можно запустить из SQL Server Management Studio (SSMS).

Если используется более ранняя версия служб SSIS, чем 2012, то перед повторным развертыванием пакетов или проектов SSIS в модель развертывания проекта сначала необходимо преобразовать их с помощью мастера преобразования проектов Integration Services, который также можно запустить из SSMS. Дополнительные сведения см. в статье [Преобразование проектов в модель развертывания проекта](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Служба Azure Database Migration Service (DMS) сейчас не поддерживает перенос исходной SSISDB, но вы можете повторно развернуть пакеты или проекты SSIS, выполнив следующие действия. 

В этой статье раскрываются следующие темы:
> [!div class="checklist"]
> * Оценка проектов и пакетов исходной SSIS.
> * Перенос проектов и пакетов SSIS в Azure.

## <a name="prerequisites"></a>Технические условия
Для выполнения этих действий вам потребуется следующее:

- SSMS 17.2 или более поздней версии.
- Экземпляр целевого сервера базы данных для размещения SSISDB.
 
  Если у вас его нет, выполните следующие действия.
    - Для Базы данных SQL Azure создайте сервер базы данных SQL Azure (без базы данных) с помощью портала Azure, перейдя к [форме](https://ms.portal.azure.com/#create/Microsoft.SQLServer) SQL Server (только логический сервер).
    - Для Управляемого экземпляра Базы данных SQL Azure выполните инструкции, приведенные в статье [Создание Управляемого экземпляра Базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).

- Службы SSIS должны быть подготовлены в Фабрике данных Azure, содержащей среду Azure-SSIS Integration Runtime (IR) с целевой SSISDB, размещенной в экземпляре сервера Базы данных SQL Azure (как описано в статье [Подготовка Integration Runtime Azure–SSIS в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)) или в Управляемом экземпляре Базы данных SQL Azure (как описано в статье [Создание среды выполнения интеграции Azure-SSIS в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)). 

## <a name="assess-source-ssis-projectspackages"></a>Оценка проектов и пакетов исходной SSIS
Хотя оценка исходной SSISDB еще не интегрирована в Помощник по миграции баз данных (DMA) или Azure Database Migration Service (DMS), ваши проекты и пакеты SSIS будут оцениваться и проверяться при повторном развертывании в целевую SSISDB, размещенную на сервере Базы данных SQL Azure или в Управляемом экземпляре Базы данных SQL Azure.

## <a name="migrate-ssis-projectspackages"></a>Управление проектами и пакетами SSIS
Чтобы перенести проекты и пакеты SSIS на сервер Базы данных SQL Azure или в Управляемый экземпляр Базы данных SQL Azure, выполните следующие действия.

1.  Откройте среду SSMS и выберите **Параметры**, чтобы открыть диалоговое окно **Подключиться к серверу**.

2.  На вкладке **Вход в систему** укажите сведения для подключения к серверу Базы данных SQL Azure или к Управляемому экземпляру Базы данных SQL Azure, на которых размещена целевая SSISDB.

    ![Вкладка "Вход в систему" SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)
 
3.  На вкладке **Свойства соединения** в текстовом поле **Подключение к базе данных** выберите или введите **SSISDB**, а затем нажмите кнопку **Подключиться**.

    ![Вкладка "Свойства подключения" SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4.  В обозревателе объектов SSMS разверните узел **Каталоги Integration Services**, разверните **SSISDB** и,если нет существующих папок, щелкните **SSISDB** правой кнопкой мыши и создайте новую папку.

5.  В разделе **SSISDB** разверните любую папку, щелкните **Проекты** правой кнопкой мыши и выберите **Развернуть проект**.

    ![Сведения об узле SSISDB SSIS](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6.  В мастере развертывания Integration Services на странице **Общие сведения** просмотрите сведения и нажмите кнопку **Далее**.

    ![Страница "Общие сведения" мастера развертывания](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7.  На странице **Выбор источника** укажите существующий проект служб SSIS, который вы хотите развернуть.

    Если среда SSMS также подключена к SQL Server, на котором размещена исходная SSISDB, выберите **Каталог Integration Services** и затем укажите имя сервера и путь к проекту в каталоге, чтобы развернуть проект напрямую.

    Вы также можете выбрать **Файл развертывания проекта** и указать путь к имеющемуся файлу развертывания проекта (.ispac) для развертывания проекта.

    ![Страница "Выбрать источник" мастера развертывания](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8.  Щелкните **Далее**.
9.  На странице **Выбор места назначения** укажите назначение проекта.

       a. В текстовом поле "Имя сервера" введите полное доменное имя сервера Базы данных SQL Azure (<имя_сервера>.database.windows.net) или имя Управляемого экземпляра Базы данных SQL Azure (<имя_сервера.префикс_DNS>.database.windows.net).
 
       2. Укажите сведения для проверки подлинности и выберите **Подключиться**.
    
       ![Страница "Выбор места назначения" мастера развертывания](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Нажмите кнопку "Обзор", выберите целевую папку в SSISDB и нажмите кнопку "Далее".

    > [!NOTE]
    > Кнопка **Далее** становится доступной только после того, как вы нажали кнопку **Подключиться**. 

10. На странице **Проверка** просмотрите ошибки или предупреждения и затем при необходимости измените пакеты.

    ![Страница "Проверка" мастера развертывания](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Щелкните **Далее**.

12. На странице **Обзор** просмотрите параметры развертывания.

    > [!NOTE]
    > Вы можете изменить параметры, нажав кнопку "Назад", или выбрать любую из ссылок на шаги мастера в области слева.

13. Выберите **Развернуть**, чтобы запустить процесс развертывания.

14. После завершения развертывания можно просмотреть страницу "Результаты", на которой отображается информация об успешном или неудачном результате каждого действия развертывания.
    a. Если произошел сбой любого действия, в столбце **Результат** выберите **Сбой**, чтобы отобразить описание ошибки.
    2. При необходимости выберите **Сохранить отчет**, чтобы сохранить результаты в XML-файл.

15. Выберите **Закрыть**, чтобы выйти из мастера развертывания Integration Services.

Если развертывание проекта завершается успешно, вы можете запустить любые пакеты из этого проекта в среде выполнения интеграции Azure-SSIS IR.

## <a name="next-steps"></a>Дальнейшие действия
- Просмотрите [руководство по миграции базы данных Майкрософт](https://datamigration.microsoft.com/).