---
title: Выполнение пакетов SSIS из SSDT
description: Узнайте, как выполнять пакеты SSIS в Azure из SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399426"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Выполнение пакетов SSIS в Azure из SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

В этой статье описывается особенность проектов интеграции серверов с поддержкой Azure s'L Server -SSIS на инструментах данных серверов S'L Server Tools (SSDT), которые позволяют запускать пакеты на Azure-SSIS Integration Runtime (IR) на фабрике данных Azure (ADF).  Эту функцию можно использовать для тестирования существующих пакетов SSIS перед тем, как снять & сдвиг/перенести их в Azure, или разработать новые пакеты SSIS для запуска в Azure.

С помощью этой функции можно создать новый ИК Azure-SSIS или прикрепить существующий объект к проектам SSIS, а затем выполнить пакеты на нем.  Мы поддерживаем запуск пакетов, которые будут развернуты в каталоге SSIS (SSISDB) в модели развертывания проекта и пакеты, которые будут развернуты в файловых системах/файловых долях/файлах в модели развертывания пакетов. 

## <a name="prerequisites"></a>Предварительные требования
Чтобы использовать эту функцию, пожалуйста, скачайте и установите новейший SSDT с расширением SSIS Projects для Visual Studio [отсюда](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) или в качестве автономного установщика [отсюда](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Проекты SSIS с лазурным обеспечением
На SSDT можно создать новые sSIS-проекты с поддержкой Azure, используя шаблон **Интеграционных Службы (С поддержкой Azure).**

![Новый проект SSIS с поддержкой Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Кроме того, можно включить существующие sSIS-проекты, нажав правой кнопкой мыши на узел проекта в панели Solution Explorer SSDT, чтобы всплывающее меню, а затем выбрать элемент меню **с поддержкой Azure.**

![Azure-Включить существующий проект SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Лазурный включение существующих sSIS-проектов требует, чтобы вы установили свою версию целевого сервера, чтобы быть последней, поддерживаемой Azure-SSIS IR, которая в настоящее время является **S'L Server 2017,** так что если вы еще не сделали этого, окно диалога появится, чтобы сделать это.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Подключение проектов с поддержкой Azure к SSIS на фабрике данных Azure
Подключив проекты с поддержкой Azure к SSIS в ADF, вы можете загрузить свои пакеты в файлы Azure и запустить их на ИК Azure-SSIS.  Для этого, пожалуйста, выполните следующие действия:

1. Нажмите на проект или связанный узел **ресурсов Azure** в панели Solution Explorer SSDT, чтобы всплывающее меню и выбрать элемент **Connect To SSIS в** меню Azure Data Factory для запуска **SSIS в ADF Connection Wizard.**

   ![Подключение к SSIS в ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. На **странице SSIS в ADF Введение,** просмотрите введение и нажмите на **кнопку Следующая,** чтобы продолжить.

   ![SSIS в ADF Введение](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. На странице **Select SSIS IR на** странице ADF выберите существующие ADF и Azure-SSIS IR для запуска пакетов или создания новых, если у вас их нет.
   - Чтобы выбрать существующую ИК-иК Azure-SSIS, выберите соответствующую подписку Azure и Сначала ADF.
   - Если вы выберете существующую ADF, которая не имеет ИК Azure-SSIS, нажмите на кнопку **Create SSIS IR,** чтобы создать новую на портале/приложении ADF.
   - Если вы выберете существующую подписку Azure, которая не имеет ADF, нажмите на кнопку **Create SSIS IR** для запуска **Интеграции Runtime Creation Wizard,** где вы можете ввести местои и префикс для нас, чтобы автоматически создать новую группу ресурсов Azure, фабрику данных и ИК SSIS от вашего имени, названную в следующем шаблоне: **YourPrefix-RG/IR-YourCreationTime**.
   
   ![Выберите SSIS IR в ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. На странице **хранения Select Azure** выберите существующую учетную запись хранения Azure, чтобы загрузить пакеты в файлы Azure или создать новую, если у вас их нет.
   - Чтобы выбрать существующую учетную запись хранения Azure, сначала выберите соответствующую подписку Azure.
   - Если вы выберете ту же подписку Azure, что и Ваш ИК Azure-SSIS, у которого нет учетной записи хранилища Azure, нажмите на кнопку **«Создайте лазурное хранилище»,** чтобы мы могли автоматически создать новую от вашего имени в том же месте, что и Ваш ИК Azure-SSIS, названный путем объединения префикса вашего имени Azure-SSIS и даты его создания.
   - Если вы выбираете другую подписку Azure, которая не имеет учетной записи хранилища Azure, нажмите на кнопку **«Создание лазурного хранения»,** чтобы создать новую на портале Azure.
   
   ![Выбор службы хранилища Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Нажмите на кнопку **Connect,** чтобы завершить подключение.  Мы отображим выбранную учетную запись ИК Azure-SSIS и Azure Storage под узлом **ресурсов Linked Azure** в панели Solution Explorer SSDT.  Мы также освежим состояние вашего ИК Azure-SSIS, в то время как вы можете управлять им, нажав правой кнопкой мыши на его узел, чтобы всплывающее меню, а затем выбрать элемент меню **Start'Stop'Manage,** который доставит вас к порталу/приложению ADF, чтобы сделать это.

## <a name="execute-ssis-packages-in-azure"></a>Выполнение пакетов SSIS в Azure
### <a name="starting-package-executions"></a>Запуск выполнения пакетов
После подключения проектов к SSIS в ADF можно выполнять пакеты на ИК Azure-SSIS.  У вас есть два варианта для начала выполнения пакетов:
-  Нажмите на кнопку **«Пуск»** в панели инструментов SSDT, чтобы упасть в меню и выбрать элемент **меню «Выполнить» в меню Azure** 

   ![Выполнение в Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Нажмите на узел пакета в панели Solution Explorer SSDT, чтобы всплывающее меню и выбрать **пакет «Выполнить» в** пункте меню Azure.

   ![Выполнить пакет в Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Выполнение пакетов в Azure требует, чтобы у вас был запущен ИК Azure-SSIS, поэтому, если ваш ИК Azure-SSIS остановлен, всплывает окно диалога, чтобы запустить его.  Исключая любое время настройки, этот процесс должен быть завершен в течение 5 минут, но может занять около 20 - 30 минут для присоединения Azure-SSIS к виртуальной сети.  После выполнения пакетов в Azure вы можете остановить иК Azure-SSIS, чтобы управлять его эксплуатационными расходами, нажав на его узел панели Solution Explorer SSDT, чтобы всплывающее меню, а затем выбрав элемент меню **Start-Stop-Manage,** который приведет вас к порталу /приложению ADF, чтобы сделать это.

### <a name="checking-package-execution-logs"></a>Проверка журналов исполнения пакетов
При запуске выполнения пакета мы будем форматировать и отображать его журнал в окне Прогресса SSDT.  Для длительного пакета мы будем периодически обновлять его журнал по минутам.  Вы можете остановить выполнение пакета, нажав на кнопку **Stop** в панели инструментов SSDT, которая немедленно отменит его.  Вы также можете временно найти журнал необработанных данных в `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`его Универсальной конвенции именования (UNC) путь: , но мы будем очистить его после одного дня.

### <a name="switching-package-protection-level"></a>Уровень защиты от переключения пакетов
Выполнение пакетов SSIS в Azure не поддерживает уровни защиты **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey.**  Следовательно, если ваши пакеты настроены с ними, мы временно переключим их в **EncryptSensitiveWithPassword**/**EncryptAllWithPassword,** соответственно, со случайными паролями, когда мы загружаем ваши пакеты в Azure Files для выполнения на вашем ИК Azure-SSIS.

> [!NOTE]
> Если ваши пакеты содержат задачи выполнения пакета, которые относятся к другим пакетам, настроенным с уровнями защиты **EncryptSensitiveWithUserKey**/**EncryptAllWithUser,** вам необходимо вручную перенастроить эти другие пакеты для использования **EncryptSensitivePassword**/**EncryptAllWithPassword**, соответственно, перед выполнением ваших пакетов.

Если ваши пакеты уже настроены с уровнями защиты **EncryptSensitiveWithPassword**/**EncryptAllWithPassword,** мы сохраним их неизменными, но будем использовать случайные пароли при загрузке ваших пакетов в Azure Files для выполнения на вашем ИК Azure-SSIS.

### <a name="using-package-configuration-file"></a>Использование файла конфигурации пакета
Если вы используете файлы конфигурации пакетов в модели развертывания пакетов для изменения переменных значений во время выполнения, мы автоматически загрузим эти файлы с вашими пакетами в файлы Azure для выполнения на ИК Azure-SSIS.

### <a name="using-execute-package-task"></a>Использование задачи выполнения пакета
Если пакеты содержат задачи выполнения пакетов, относящиеся к другим пакетам, хранящимся в локальных файловых системах, необходимо выполнить следующие дополнительные настройки:

1. Загрузите другие пакеты в файлы Azure в той же учетной записи Azure Storage, подключенной к вашим проектам, и получите новый путь КООН, например.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Заменить путь файла этих других пакетов в диспетчере подключения файлов задач «Выполнить пакет» новым путем КООН
   - Если машина под управлением SSDT не может получить доступ к новому пути КООН, можно изменить путь файла на панели свойств диспетчера подключения файлов
   - Кроме того, можно использовать переменную для пути файла для присвоения нужного значения во время выполнения

Если пакеты содержат задачи выполнения пакетов, относясь к другим пакетам в том же проекте, дополнительная настройка не требуется.

## <a name="next-steps"></a>Дальнейшие действия
После того как вы удовлетворены запуском пакетов в Azure из SSDT, вы можете развернуть и запустить их в качестве действий пакета Выполнения SSIS в конвейерах ADF, [см. Выполнить пакеты SSIS как выполнение действий пакета SSIS в конвейерах ADF.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
