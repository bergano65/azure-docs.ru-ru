---
title: Запуск пакета SSIS с действием пакета «Выполнить SSIS»
description: В этой статье описывается, как запустить пакет s'L Server Integration Services (SSIS) в конвейере Azure Data Factory с помощью действия пакета «Выполнить SSIS».
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: f400463f6102d46d9da48bbb10466ad4ca04a69b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413241"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Запуск пакета Integration Services с помощью действия выполнения пакета SSIS в Фабрике данных Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

В этой статье описывается, как запустить пакет s'L Server Integration Services (SSIS) в конвейере Azure Data Factory с помощью действия пакета «Выполнить SSIS». 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Создайте время выполнения интеграции Azure-SSIS (IR), если у вас его уже нет, следуя пошаговым инструкциям в [учебнике: Подготовка Azure-SSIS IR.](tutorial-create-azure-ssis-runtime-portal.md)

## <a name="run-a-package-in-the-azure-portal"></a>Запуск пакета в портале Azure
В этом разделе используется пользовательский интерфейс (UI) Data Factory (UI) или приложение для создания конвейера Data Factory с действием пакета Execute SSIS, выполняющего пакет SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Создание конвейера с действием "Выполнить пакет SSIS"
На этом этапе для создания конвейера используется uI или приложение Data Factory. Добавьте действие выполнения пакета SSIS в конвейер и настройте его для запуска пакета Integration Services. 

1. На обзоре фабрики данных или на главной странице портала Azure выберите плитку **«Автор & монитора»,** чтобы запустить uI или приложение Data Factory в отдельной вкладке. 

   ![Домашняя страница фабрики данных](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   На странице **Начало работы** выберите **Create pipeline** (Создать конвейер). 

   ![Страница "Начало работы"](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. В наборе **инструментов «Деятельность»** расширьте **General**. Затем перетащите действие **пакета Выполнения SSIS** на поверхность конструктора конвейера. 

   ![Перетаскивание действия выполнения пакета SSIS в область конструктора](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. На **общей** вкладке для действия пакета Выполнения SSIS укажите имя и описание действия. Установите дополнительные значения **тайм-аута** и **повтора.**

   ![Настройка свойств на вкладке "Общие"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. На **вкладке «Настройки»** для действия пакета «Выполнить SSIS» выберите ИК Azure-SSIS, где требуется запустить пакет. Если ваш пакет использует аутентификацию Windows для доступа к хранилищам данных (например, серверы или файлы, которые являются файлами, находящиеся в предварительном помещении или в лазурных файлах), выберите **флажок проверки подлинности Windows.** Введите значения для учетных данных исполнения пакета в **полях домена,** **имени пользователя**и **пароля.** 

    Кроме того, в качестве значений можно использовать секреты, хранящиеся в хранилище ключей Azure. Для этого выберите флажок **«АЗURE KEY VAULT»** рядом с соответствующим итоговым учетным актом. Выберите или отодвите существующей службы, связанной с хранилищем ключей, или создайте новую. Затем выберите секретное имя или версию для вашего значения учетных данных.

    При создании или отсвачении службы, связанной с хранилищем ключей, можно выбрать или отсвазать существующее хранилище ключей или создать новый. Убедитесь в том, чтобы предоставить Data Factory управляемый доступ к хранилищу ключей, если вы еще не сделали этого. Вы также можете ввести свои секреты непосредственно в следующем формате: `<Key vault linked service name>/<secret name>/<secret version>`. Если вашему пакету требуется 32-разрядное время выполнения, выберите **32-битную** флажок.

   Для **расположения пакета,** выберите **SSISDB**, **Файловую систему (пакет)**, **файловую систему (проект)** или **встроенный пакет.** Если вы **выбираете SSISDB** в качестве местоположения пакета, которое автоматически выбирается, если ваш ИК Azure-SSIS был подготовлен каталогом SSIS (SSISDB), размещенным сервером базы данных Azure S'L или управляемым экземпляром, укажите свой пакет для запуска, который был развернут в SSISDB. 

    Если ваш ИК Azure-SSIS работает и флажок **ручных записей** очищается, просмотрите и выберите существующие папки, проекты, пакеты или среды из SSISDB. Выберите **Refresh,** чтобы получить из SSISDB новые папки, проекты, пакеты или среды, чтобы они были доступны для просмотра и выбора. Чтобы просмотреть или выбрать среды для выполнения пакетов, необходимо настроить проекты заранее, чтобы добавить эти среды в виде ссылок из тех же папок под SSISDB. Для получения дополнительной [информации см.](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)

   Для **уровня ведения журнала** выберите предопределенную область ведения журнала для выполнения пакета. Выберите **настраиваемый** флажок, если вы хотите ввести специальное имя журнала вместо этого. 

   ![Автоматическая установка свойств на вкладке "Параметры"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Если ваш ИК Azure-SSIS не работает или выбран флажок **ручных записей,** введите свой пакет и пути `<folder name>/<project name>/<package name>.dtsx` `<folder name>/<environment name>`среды из SSISDB непосредственно в следующих форматах: и .

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Если вы выберете **файловую систему (Пакет)** в качестве местоположения пакета, которое автоматически выбирается, если ваш ИК Azure-SSIS был подготовлен без SSISDB, укажите свой пакет для запуска, предоставив путь универсальной конвенции о именованиях (UNC) в файл пакета ()`.dtsx`в поле пути **пакета.** Например, если вы храните пакет в Azure `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`Files, его путь пакетов — это средство. 
   
   Если вы настраиваете пакет в отдельный файл, необходимо также предоставить`.dtsConfig`путь КООН в файл конфигурации () в поле **пути конфигурации.** Например, если вы храните конфигурацию в Azure Files, ее путь конфигурации находится в центре вашего `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`хранения.

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Если вы выберете **файловую систему (проект)** в качестве местоположения пакета, укажите свой пакет для запуска, предоставив путь КООН к файлу`.ispac`проекта () в поле пути **проекта** и файл пакета ()`.dtsx`из вашего проекта в поле имени **пакета.** Например, если вы храните проект в Azure `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`Files, его путь проекта — это удосызненое.

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Затем укажите учетные данные для доступа к файлам проекта, пакета или конфигурации. Если ранее вы вводили значения для учетных данных выполнения пакета (см. предыдущий), вы можете повторно использовать их, выбрав то **же самое, что и флажок учетных данных выполнения пакета.** В противном случае введите значения для учетных данных доступа к пакету в **полях домена,** **имени пользователя**и **пароля.** Например, если вы храните проект, пакет или конфигурацию `Azure`в Azure `<storage account name>`Files, домен `<storage account key>`является, имя пользователя — и пароль — это время. 

   Кроме того, в качестве значений можно использовать секреты, хранящиеся в хранилище ключей (см. предыдущий). Эти учетные данные используются для доступа к пакету и детским пакетам в задаче выполнения пакета, все из их собственного пути или того же проекта, а также конфигураций, которые включают те, которые указаны в пакетах. 

   Если вы выберете **встроенный пакет** в качестве местоположения пакета, перетащите и удалите пакет для запуска или **загрузите** его из папки файлов в предоставленную коробку. Ваш пакет будет автоматически сжат и встроен в полезную нагрузку активности. После встраивания, вы можете **скачать** свой пакет позже для редактирования. Можно также **параметризировать** встроенный пакет, назначив его параметру конвейера, который может быть использован в нескольких действиях, тем самым оптимизируя размер полезной нагрузки конвейера. Если ваш встроенный пакет не все зашифрованы, и мы обнаруживаем использование задачи пакета выполнения в нем, **выполнить пакет Панель поле будет** автоматически выбран и соответствующие пакеты ребенка с их ссылками файловой системы будут автоматически добавлены для вас также вставлять их. Если мы не можем обнаружить использование задачи пакета выполнения, вам придется вручную выбрать панельный ящик **задачи пакета выполнения** и добавить соответствующие пакеты ребенка со ссылками файловой системы один за разом для вас, чтобы также вставлять их. Если в пакетах для детей используются ссылки на сервер S'L, пожалуйста, убедитесь, что сервер S'L доступен на вашем ИК Azure-SSIS.  Использование справочников по проектам для пакетов детей в настоящее время не поддерживается.
   
   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Если вы использовали уровень защиты **EncryptAllWithPassword** или **EncryptSensitiveWithPassword** при создании пакета с помощью инструментов данных сервера S'L Server, введите значение для пароля в коробке **паролей шифрования.** Кроме того, в качестве его значения можно использовать секрет, хранящийся в хранилище ключей (см. предыдущий). Если вы использовали уровень защиты **EncryptSensitiveWithUserKey,** повторно введите конфиденциальные значения в файлах конфигурации или на **параметрах SSIS,** **менеджерах соединения**или вкладках **Переопределения свойств** (см. более поздние). 

   Если вы использовали уровень защиты **EncryptAllWithUserKey,** он не поддерживается. Необходимо перенастроить пакет, чтобы использовать другой уровень защиты с `dtutil` помощью инструментов данных сервера S'L или утилиты командной строки. 
   
   Для **уровня ведения журнала** выберите предопределенную область ведения журнала для выполнения пакета. Выберите **настраиваемый** флажок, если вы хотите ввести специальное имя журнала вместо этого. Если вы хотите войти в число выполнения пакетов за пределами стандартных поставщиков журналов, которые могут быть указаны в пакете, укажите папку журнала, предоставив свой путь КООН в поле **пути регистрации.** Например, если вы храните журналы в azure `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`Files, ваш путь регистрации — это упор— — это средство. Субфолдер создается на этом пути для каждого отдельного запуска пакета и назван в честь идентификатора выполнения sSIS Package, в котором файлы журнала генерируются каждые пять минут. 
   
   Наконец, укажите учетные данные для доступа к папке журнала. Если вы ранее вводили значения для учетных данных доступа к пакету (см. предыдущий), вы можете повторно использовать их, выбрав то же самое, что и **флажок учетных данных доступа пакетов.** В противном случае введите значения для учетных данных доступа к журналу в **полях домена,** **имени пользователя**и **пароля.** Например, если вы храните журналы в Azure Files, `<storage account name>`домен является, `<storage account key>` `Azure`имя пользователя, и пароль . 

    Кроме того, в качестве значений можно использовать секреты, хранящиеся в хранилище ключей (см. предыдущий). Эти учетные данные используются для хранения журналов. 
   
   Для всех ранее упомянутых путей КООН, полностью квалифицированное имя файла должно быть менее 260 символов. Название каталога должно быть меньше 248 символов.

1. На вкладке **Параметры SSIS** для действия пакета Execute SSIS, если ваш ИК Azure-SSIS работает, **SSISDB** выбран в качестве местоположения пакета, а флажок **ручных записей** на вкладке **«Настройки»** очищается, отображаются существующие параметры SSIS в выбранном проекте или пакете из SSISDB для определения значений. В противном случае, вы можете ввести их один за разом, чтобы присвоить значения им вручную. Убедитесь, что они существуют и правильно введены для выполнения пакета, чтобы добиться успеха. 
   
   Если вы использовали уровень защиты **EncryptSensitiveWithUserKey** при создании пакета с помощью инструментов данных сервера и **файловой системы (Package)** или **файловой системы (Project)** в качестве местоположения пакета, вам также необходимо повторно ввести конфиденциальные параметры, чтобы присвоить значения им в файлах конфигурации или на этой вкладке. 
   
   При присвоении значений параметрам можно добавить динамическое содержимое, используя выражения, функции, переменные системы Data Factory и параметры или переменные конвейера Data Factory. Кроме того, в качестве значений можно использовать секреты, хранящиеся в хранилище ключей (см. предыдущий).

   ![Установка свойств на вкладке SSIS Parameters (Параметры SSIS)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. На вкладке **«Менеджеры подключения»** для действия пакета «Выполнить SSIS» если ваш ИК Azure-SSIS выполняется, **SSISDB** выбирается в качестве местоположения пакета, а флажок **ручных записей** на вкладке **«Настройки»** очищается, отображаются существующие менеджеры соединения в выбранном проекте или пакете из SSISDB для присвоения значений их свойствам. В противном случае, вы можете ввести их один за разом, чтобы присвоить значения их свойствам вручную. Убедитесь, что они существуют и правильно введены для выполнения пакета, чтобы добиться успеха. 
   
   Если вы использовали уровень защиты **EncryptSensitiveWithUserKey** при создании пакета с помощью инструментов данных сервера и **файловой системы (Package)** или **файловой системы (Project)** в качестве местоположения пакета, вам также необходимо повторно ввести конфиденциальные свойства менеджера соединения, чтобы присвоить значения им в файлах конфигурации или на этой вкладке. 
   
   При присвоении значений свойствам диспетчера соединения можно добавить динамическое содержимое, используя выражения, функции, переменные системы Data Factory и параметры или переменные конвейера Data Factory. Кроме того, в качестве значений можно использовать секреты, хранящиеся в хранилище ключей (см. предыдущий).

   ![Установка свойств на вкладке Connection Managers (Диспетчеры подключений)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. На вкладке **«Переопределения свойств»** для действия пакета «Выполнить SSIS» введите пути существующих свойств в выбранном пакете один за другим, чтобы присвоить значения им вручную. Убедитесь, что они существуют и правильно введены для выполнения пакета, чтобы добиться успеха. Например, чтобы переопределить значение переменной пользователя, введите `\Package.Variables[User::<variable name>].Value`ее путь в следующем формате: . 
   
   Если вы использовали уровень защиты **EncryptSensitiveWithUserKey** при создании пакета с помощью инструментов данных сервера и **файловой системы (Package)** или **файловой системы (Project)** в качестве местоположения пакета, вам также необходимо повторно ввести конфиденциальные свойства, чтобы присвоить значения им в файлах конфигурации или на этой вкладке. 
   
   При присвоении значений свойствам можно добавить динамическое содержимое, используя выражения, функции, переменные системы Data Factory и параметры или переменные конвейера Data Factory.

   ![Установка свойств на вкладке Property Overrides (Переопределения свойств)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Значения, присвоенные в файлах конфигурации и на **вкладке Параметры SSIS,** могут быть перекрыты с помощью вкладок **«Менеджеры соединения»** или **«Переопределения свойств».** Значения, присвоенные на вкладке **«Менеджеры соединения»,** также могут быть перекрыты с помощью вкладки **«Переопределения свойств».**

1. Чтобы проверить конфигурацию конвейера, выберите **Validate** на панели инструментов. Чтобы закрыть **отчет о проверке трубопровода,** выберите. **>>**

1. Чтобы опубликовать конвейер на Фабрику данных, выберите **Publish All**. 

### <a name="run-the-pipeline"></a>Запуск конвейера
На этом шаге вы активируете выполнение конвейера. 

1. Чтобы вызвать запуск конвейера, выберите **Триггер** на панели инструментов и выберите **Trigger сейчас.** 

   ![Trigger Now (Активировать сейчас)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. На странице **Запуск конвейера** нажмите кнопку **Готово**. 

### <a name="monitor-the-pipeline"></a>Мониторинг конвейера

1. Перейдите на вкладку **Мониторинг** слева. Вы видите запуск конвейера и его состояние вместе с другой информацией, например временем **запуска.** Чтобы обновить список, нажмите кнопку **Обновить**.

   ![Запуски конвейера](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Выберите ссылку **View Activity Runs** (Просмотр запусков действий) в столбце **Действия**. Вы видите только один запуск активности, потому что конвейер имеет только одно действие. Это действие пакета «Выполнить SSIS».

   ![Выполнение действия](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Выполнить следующий запрос против базы данных SSISDB на вашем сервере S'L, чтобы убедиться, что пакет выполнен. 

   ```sql
   select * from catalog.executions
   ```

   ![Проверка выполнения пакета](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Вы также можете получить идентификатор выполнения SSISDB из вывода выполнения действия конвейера и использовать идентификатор для проверки более полных журналов выполнения и сообщений об ошибках в студии управления серверами S'L.

   ![Получите идентификатор выполнения.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Запуск конвейера по расписанию с помощью триггера

Можно также создать запланированный триггер для конвейера, чтобы конвейер работал по расписанию, например, почасовой или ежедневной. Пример см. в разделе [Запуск конвейера по расписанию](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Выполнение пакета с помощью PowerShell
В этом разделе используется Azure PowerShell для создания конвейера Data Factory с действием пакета Execute SSIS, выполняемого пакетом SSIS. 

Чтобы установить модули Azure PowerShell, выполните пошаговые инструкции из [этой статьи](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Создание фабрики данных с помощью ИК Azure-SSIS
Можно либо использовать существующую фабрику данных, которая уже имеет Azure-SSIS ИК-иК,или создать новую фабрику данных с Azure-SSIS IR. Следуйте инструкциям в [учебнике: Развертывание пакетов SSIS в Azure через PowerShell.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Создание конвейера с действием "Выполнить пакет SSIS" 
На этом этапе создается конвейер с действием "Выполнить пакет SSIS". Это действие запускает пакет SSIS. 

1. Создайте файл JSON под названием *RunSSISPackagePipeline.json* в папке *C: «ADF»RunSISPackage* с контентом, похожим на следующий пример.

   > [!IMPORTANT]
   > Перед сохранением файла замените имена объектов, описания и пути, значения свойств или параметров, пароли и другие переменные значения. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Для выполнения пакетов, хранящихся в файловых системах, файловых долях или файлах Azure, введите значения для свойств вашего пакета и определения местоположения журнала следующим образом:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                           },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Для выполнения пакетов в проектах, хранящихся в файловых системах, файловых долях или файлах Azure, введите значения для свойства местоположения пакета следующим образом:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Чтобы выполнить встроенные пакеты, введите значения для свойства местоположения пакета следующим образом:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. В Папке С.В. ПауэрШелл переключитесь на папку *C: ADF-RunSSISPackage.*

3. Для создания конвейера **RunSSISPackagePipeline**запустите **cmdlet Set-AzDataFactoryV2Pipeline.**

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Вот пример выход:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Запуск конвейера
Для запуска конвейера используйте **смдлет Invoke-AzDataFactoryV2Pipeline.** Командлет позволяет получить идентификатор выполнения конвейера для дальнейшего мониторинга.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Мониторинг конвейера

Запустите приведенный ниже скрипт PowerShell, чтобы проверять состояние выполнения, пока не закончится копирование данных. Скопируйте или вставьте следующий сценарий в окне PowerShell и выберите Enter. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Вы также можете контролировать конвейер с помощью портала Azure. Пошаговые инструкции см. в разделе [Мониторинг конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Запуск конвейера по расписанию с помощью триггера
На предыдущем этапе вы запустили конвейер по требованию. Можно также создать триггер расписания для выполнения конвейера по расписанию, например, почасовой или ежедневной.

1. Создайте файл JSON под названием *MyTrigger.json* в папке *C: «ADF»RunSSISPackage* со следующим содержанием: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. В Папке С.В. ПауэрШелл переключитесь на папку *C: ADF-RunSSISPackage.*
1. Выполнить **Set-AzDataFactoryV2Trigger** cmdlet, который создает триггер. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. По умолчанию триггер находится в остановленном состоянии. Запустите триггер, запустив смдлет **Start-AzDataFactoryV2Trigger.** 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Подтвердите, что триггер запущен с запуска Смдлета **Get-AzDataFactoryV2Trigger.** 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Через час выполните следующую команду. Например, если текущее время 15:25 (UTC), запустите команду в 16:00 (UTC). 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Выполнить следующий запрос против базы данных SSISDB на вашем сервере S'L, чтобы убедиться, что пакет выполнен. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Дальнейшие действия
См. в следующей записи блога:
- [Модернизация и расширение рабочих процессов ETL/ELT с помощью действий SSIS в конвейерах Фабрики данных Azure](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
