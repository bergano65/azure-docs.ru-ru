---
title: Запуск пакета служб SSIS с помощью действия "выполнение пакета служб SSIS" в Azure
description: В этой статье описывается, как выполнить пакет SQL Server Integration Services (SSIS) в конвейере фабрики данных Azure с помощью действия "выполнение пакета служб SSIS".
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 09/13/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: b8ed0a04d2d13556f38873ef5f346d49ba4d1845
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673736"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Запуск пакета Integration Services с помощью действия выполнения пакета SSIS в Фабрике данных Azure
В этой статье описывается, как выполнить пакет SQL Server Integration Services (SSIS) в конвейере фабрики данных Azure с помощью действия "выполнение пакета служб SSIS". 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Создайте среду выполнения интеграции Azure SSIS (IR), если у вас ее еще нет, следуя пошаговым инструкциям в [руководстве по подготовке Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Запуск пакета в портале Azure
В этом разделе вы используете пользовательский интерфейс фабрики данных (UI) или приложение для создания конвейера фабрики данных с действием выполнить пакет служб SSIS, которое выполняет пакет служб SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Создание конвейера с действием "Выполнить пакет SSIS"
На этом шаге вы используете пользовательский интерфейс или приложение фабрики данных для создания конвейера. Добавьте действие выполнения пакета SSIS в конвейер и настройте его для запуска пакета Integration Services. 

1. На странице Обзор или Домашняя страница фабрики данных в портал Azure выберите плитку " **дизайнер & Monitor** ", чтобы запустить пользовательский интерфейс или приложение фабрики данных на отдельной вкладке. 

   ![Домашняя страница фабрики данных](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   На странице **Начало работы** выберите **Create pipeline** (Создать конвейер). 

   ![Страница "Начало работы"](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. На панели элементов **действия** разверните узел **Общие**. Затем перетащите действие **выполнить пакет служб SSIS** в область конструктора конвейера. 

   ![Перетаскивание действия выполнения пакета SSIS в область конструктора](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. На вкладке **Общие** действия выполнение пакета служб SSIS введите имя и описание действия. Задайте необязательное **время ожидания** и значения **повтора** .

   ![Настройка свойств на вкладке "Общие"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. На вкладке **Параметры** действия выполнение пакета служб SSIS выберите Azure-SSIS IR, в которой нужно запустить пакет. Если пакет использует проверку подлинности Windows для доступа к хранилищам данных (например, серверы SQL Server или файловые ресурсы в локальной среде или файлы Azure), установите флажок **Проверка подлинности Windows** . Введите значения учетных данных для выполнения пакета в полях **домен**, **имя пользователя**и **пароль** . 

    Кроме того, можно использовать секреты, хранящиеся в хранилище ключей Azure, в качестве их значений. Для этого установите флажок **хранилище ключей Azure** рядом с нужными учетными данными. Выберите или измените существующую связанную службу хранилища ключей или создайте новую. Затем выберите имя или версию секрета для учетных данных.

    При создании или изменении связанной службы хранилища ключей можно выбрать или изменить существующее хранилище ключей или создать новое. Не забудьте предоставить управляемой идентификации фабрики данных доступ к хранилищу ключей, если это еще не сделано. Вы также можете ввести секреты непосредственно в следующем формате: `<Key vault linked service name>/<secret name>/<secret version>`. Если для выполнения пакета требуется 32-разрядная среда выполнения, установите флажок **32-разрядная среда выполнения** .

   В качестве **расположения пакета**выберите **SSISDB**, **Файловая система (пакет)** или **Файловая система (проект)** . Если выбрать **SSISDB** в качестве расположения пакета, который автоматически выбирается, если Azure-SSIS IR был подготовлен с помощью каталога SSIS (SSISDB), размещенного на сервере базы данных SQL Azure или управляемом экземпляре, укажите пакет для запуска, который был развернут в SSISDB. 

    Если Azure-SSIS IR работает, а флажок **записи вручную** снят, найдите и выберите существующие папки, проекты, пакеты или среды из SSISDB. Выберите **Обновить** , чтобы получить вновь добавленные папки, проекты, пакеты или среды из SSISDB, чтобы они были доступны для обзора и выбора. Чтобы просмотреть или выбрать среды для выполнения пакета, необходимо заранее настроить проекты, чтобы добавить эти среды в качестве ссылок из тех же папок в SSISDB. Дополнительные сведения см. в разделе [Создание и сопоставьте среды SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   Для **уровня ведения журнала** выберите предопределенную область ведения журнала для выполнения пакета. Установите флажок **настроено** , если хотите вместо этого ввести настраиваемое имя журнала. 

   ![Автоматическая установка свойств на вкладке "Параметры"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Если Azure-SSIS IR не запущена или флажок **записи вручную** установлен, введите путь к пакету и среде из SSISDB непосредственно в следующих форматах: `<folder name>/<project name>/<package name>.dtsx` и `<folder name>/<environment name>`.

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Если в качестве расположения пакета выбрана **Файловая система (пакет)** , которая будет автоматически выбрана, если Azure-SSIS IR была подготовлена без SSISDB, укажите путь к файлу пакета в формате unc (@no __t_1_) в поле **путь к пакету** .`.dtsx` Например, если пакет хранится в службе файлов Azure, его путь к пакету — `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Если пакет настроен в отдельном файле, необходимо также указать UNC-путь к файлу конфигурации (`.dtsConfig`) в поле **путь конфигурации** . Например, если вы храните конфигурацию в службе файлов Azure, ее путь конфигурации — `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Если в качестве расположения пакета выбрать **Файловая система (проект)** , укажите путь в формате UNC к файлу проекта (`.ispac`) в поле **путь к проекту** и файл пакета (`.dtsx`) из проекта в **имени пакета** . Box. Например, если проект хранится в службе файлов Azure, его путь к проекту — `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Затем укажите учетные данные для доступа к файлам проекта, пакета или конфигурации. Если ранее были введены значения учетных данных для выполнения пакета (см. предыдущий), их можно повторно использовать, установив флажок **учетные данные выполнения пакета** . В противном случае введите значения учетных данных для доступа к пакету в полях **домен**, **имя пользователя**и **пароль** . Например, если вы храните проект, пакет или конфигурацию в службе "файлы Azure", домен имеет значение `Azure`, имя пользователя — `<storage account name>`, а пароль — `<storage account key>`. 

   Кроме того, можно использовать секреты, хранящиеся в хранилище ключей, в качестве их значений (см. предыдущий). Эти учетные данные используются для доступа к пакету и дочерним пакетам в задаче «Выполнение пакета», все из их собственного пути или проекта, а также конфигурации, включая указанные в пакетах. 
   
   Если вы использовали уровень защиты **EncryptAllWithPassword** или **EncryptSensitiveWithPassword** при создании пакета с помощью SQL Server Data Tools, введите значение пароля в поле **пароль шифрования** . Кроме того, в качестве значения можно использовать секрет, хранящийся в хранилище ключей (см. предыдущий). Если вы использовали уровень защиты **EncryptSensitiveWithUserKey** , повторно введите конфиденциальные значения в файлах конфигурации или на вкладках **Параметры служб SSIS**, **Диспетчеры соединений**или **переопределения свойств** (см. Далее). 

   Если вы использовали уровень защиты **EncryptAllWithUserKey** , он не поддерживается. Необходимо перенастроить пакет для использования другого уровня защиты с помощью SQL Server Data Tools или служебной программы командной строки `dtutil`. 
   
   Для **уровня ведения журнала** выберите предопределенную область ведения журнала для выполнения пакета. Установите флажок **настроено** , если хотите вместо этого ввести настраиваемое имя журнала. Если вы хотите регистрировать выполнение пакета, не используя стандартные регистраторы, которые можно указать в пакете, укажите папку журнала, указав UNC-путь в поле **путь к журналу** . Например, если вы храните журналы в службе файлов Azure, ваш путь к журналу будет `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Вложенная папка создается по этому пути для каждого отдельного пакета, который выполняется и назван после идентификатора запуска действия "выполнение пакета служб SSIS", в котором файлы журнала создаются каждые пять минут. 
   
   Наконец, укажите учетные данные для доступа к папке журнала. Если вы ранее указали значения для учетных данных для доступа к пакету (см. предыдущий), их можно повторно использовать, установив флажок **учетные данные для доступа к пакету** . В противном случае введите значения учетных данных для доступа к журналу в полях **домен**, **имя пользователя**и **пароль** . Например, если вы храните журналы в службе файлов Azure, домен имеет значение `Azure`, имя пользователя — `<storage account name>`, а пароль — `<storage account key>`. 

    Кроме того, можно использовать секреты, хранящиеся в хранилище ключей, в качестве их значений (см. предыдущий). Эти учетные данные используются для хранения журналов. 
   
   Для всех перечисленных UNC-путей полное имя файла должно содержать менее 260 символов. Имя каталога должно содержать менее 248 символов.

1. На вкладке **Параметры служб SSIS** для действия выполнение пакета служб SSIS, если Azure-SSIS IR выполняется, в качестве расположения пакета выбрано **SSISDB** , а флажок **записи вручную** на вкладке **Параметры** снят, существующие службы SSIS для назначения значений в выбранном проекте или пакете из SSISDB отображаются параметры. В противном случае их можно ввести один за другим, чтобы присвоить им значения вручную. Убедитесь, что они существуют и правильно введены для успешной выполнения пакета. 
   
   Если при создании SQL Server Data Tools пакета использовался уровень защиты **EncryptSensitiveWithUserKey** , а в качестве расположения пакета выбрана **Файловая система (пакет)** или **Файловая система (проект)** , необходимо также повторно ввести ваши конфиденциальные параметры для назначения им значений в файлах конфигурации или на этой вкладке. 
   
   При назначении значений параметрам можно добавлять динамическое содержимое с помощью выражений, функций, системных переменных фабрики данных и параметров конвейера фабрики данных или переменных. Кроме того, можно использовать секреты, хранящиеся в хранилище ключей, в качестве их значений (см. предыдущий).

   ![Установка свойств на вкладке SSIS Parameters (Параметры SSIS)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. На вкладке **Диспетчеры соединений** для действия выполнение пакета служб SSIS, если Azure-SSIS IR выполняется, в качестве расположения пакета выбрано **SSISDB** , а флажок **записи вручную** на вкладке **Параметры** снят, существующий Диспетчеры подключений в выбранном проекте или пакете из SSISDB отображаются для присвоения значений их свойствам. В противном случае их можно ввести один за другим, чтобы присвоить значения их свойствам вручную. Убедитесь, что они существуют и правильно введены для успешной выполнения пакета. 
   
   Если при создании SQL Server Data Tools пакета использовался уровень защиты **EncryptSensitiveWithUserKey** , а в качестве расположения пакета выбрана **Файловая система (пакет)** или **Файловая система (проект)** , необходимо также повторно ввести ваши конфиденциальные свойства диспетчера соединений используются для назначения им значений в файлах конфигурации или на этой вкладке. 
   
   При назначении значений свойствам диспетчера соединений можно добавить динамическое содержимое с помощью выражений, функций, системных переменных фабрики данных и параметров конвейера фабрики данных или переменных. Кроме того, можно использовать секреты, хранящиеся в хранилище ключей, в качестве их значений (см. предыдущий).

   ![Установка свойств на вкладке Connection Managers (Диспетчеры подключений)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. На вкладке **переопределения свойств** для действия выполнение пакета служб SSIS введите пути существующих свойств в выбранном пакете по одному, чтобы назначить значения для них вручную. Убедитесь, что они существуют и правильно введены для успешной выполнения пакета. Например, чтобы переопределить значение пользовательской переменной, введите ее путь в следующем формате: `\Package.Variables[User::<variable name>].Value`. 
   
   Если при создании SQL Server Data Tools пакета использовался уровень защиты **EncryptSensitiveWithUserKey** , а в качестве расположения пакета выбрана **Файловая система (пакет)** или **Файловая система (проект)** , необходимо также повторно ввести ваши конфиденциальные свойства для назначения им значений в файлах конфигурации или на этой вкладке. 
   
   При присвоении значений свойствам можно добавить динамическое содержимое с помощью выражений, функций, системных переменных фабрики данных и параметров конвейера фабрики данных или переменных.

   ![Установка свойств на вкладке Property Overrides (Переопределения свойств)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Значения, назначенные в файлах конфигурации и на вкладке **Параметры служб SSIS** , можно переопределить с помощью вкладок **Диспетчеры соединений** или **переопределения свойств** . Значения, назначенные на вкладке **Диспетчеры соединений** , также можно переопределить с помощью вкладки **переопределения свойств** .

1. Чтобы проверить конфигурацию конвейера, на панели инструментов выберите **проверить** . Чтобы закрыть **отчет о проверке конвейера**, выберите **>>** .

1. Чтобы опубликовать конвейер в фабрике данных, выберите **опубликовать все**. 

### <a name="run-the-pipeline"></a>Запуск конвейера
На этом шаге вы активируете выполнение конвейера. 

1. Чтобы запустить конвейер, выберите **триггер** на панели инструментов и щелкните **Активировать сейчас**. 

   ![Запустить сейчас](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. На странице **Запуск конвейера** нажмите кнопку **Готово**. 

### <a name="monitor-the-pipeline"></a>Мониторинг конвейера

1. Перейдите на вкладку **Мониторинг** слева. Вы увидите выполнение конвейера и его состояние, а также другие сведения, например время **запуска запуска** . Чтобы обновить список, нажмите кнопку **Обновить**.

   ![Запуски конвейера](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Выберите ссылку **View Activity Runs** (Просмотр запусков действий) в столбце **Действия**. Вы увидите, что выполняется только одно действие, так как конвейер содержит только одно действие. Это действие выполнение пакета служб SSIS.

   ![Выполнение действия](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Выполните следующий запрос к базе данных SSISDB на сервере SQL Server, чтобы убедиться, что пакет выполнен. 

   ```sql
   select * from catalog.executions
   ```

   ![Проверка выполнения пакета](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Можно также получить идентификатор выполнения SSISDB из выходных данных выполнения действия конвейера и использовать идентификатор для проверки более полных журналов выполнения и сообщений об ошибках в SQL Server Management Studio.

   ![Получите идентификатор выполнения.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Запуск конвейера по расписанию с помощью триггера

Вы также можете создать запланированный триггер для конвейера, чтобы конвейер выполнялся по расписанию, например ежечасно или ежедневно. Пример см. в разделе [Запуск конвейера по расписанию](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Выполнение пакета с помощью PowerShell
В этом разделе используется Azure PowerShell для создания конвейера фабрики данных с действием выполнить пакет служб SSIS, которое выполняет пакет служб SSIS. 

Чтобы установить модули Azure PowerShell, выполните пошаговые инструкции из [этой статьи](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Создание фабрики данных с Azure-SSIS IR
Вы можете использовать существующую фабрику данных, уже имеющую Azure-SSIS IR подготовлена, или создать новую фабрику данных с Azure-SSIS IR. Выполните пошаговые инструкции из [руководства Развертывание пакетов служб SSIS в Azure с помощью PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Создание конвейера с действием "Выполнить пакет SSIS" 
На этом этапе создается конвейер с действием "Выполнить пакет SSIS". Это действие запускает пакет SSIS. 

1. Создайте файл JSON с именем *RunSSISPackagePipeline. JSON* в папке *C:\ADF\RunSSISPackage* с содержимым, как в следующем примере.

   > [!IMPORTANT]
   > Перед сохранением файла замените имена объектов, описания и пути, значения свойств или параметров, пароли и другие значения переменных. 
    
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

   Для выполнения пакетов, хранящихся в файловых системах, файловых ресурсах или файлах Azure, введите значения свойств пакета или расположения журнала следующим образом:

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

   Для выполнения пакетов в проектах, хранящихся в файловых системах, файловых ресурсах или файлах Azure, введите значения для свойства расположение пакета следующим образом:

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

2. В Azure PowerShell перейдите в папку *C:\ADF\RunSSISPackage* .

3. Чтобы создать конвейер **RunSSISPackagePipeline**, выполните командлет **Set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Ниже приведен пример выходных данных:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Запуск конвейера
Используйте командлет **Invoke-AzDataFactoryV2Pipeline** для запуска конвейера. Командлет позволяет получить идентификатор выполнения конвейера для дальнейшего мониторинга.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Мониторинг конвейера

Запустите приведенный ниже скрипт PowerShell, чтобы проверять состояние выполнения, пока не закончится копирование данных. Скопируйте или вставьте следующий скрипт в окно PowerShell и нажмите клавишу ВВОД. 

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

Вы также можете отслеживать конвейер с помощью портал Azure. Пошаговые инструкции см. в разделе [Мониторинг конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Запуск конвейера по расписанию с помощью триггера
На предыдущем шаге вы запустили конвейер по запросу. Можно также создать триггер расписания для запуска конвейера по расписанию, например ежечасно или ежедневно.

1. Создайте файл JSON с именем *MyTrigger. JSON* в папке *C:\ADF\RunSSISPackage* со следующим содержимым: 
        
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
    
1. В Azure PowerShell перейдите в папку *C:\ADF\RunSSISPackage* .
1. Выполните командлет **Set-AzDataFactoryV2Trigger** , который создает триггер. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. По умолчанию триггер находится в остановленном состоянии. Запустите триггер, выполнив командлет **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Убедитесь, что триггер запущен, выполнив командлет **Get-AzDataFactoryV2Trigger** . 

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

   Выполните следующий запрос к базе данных SSISDB на сервере SQL Server, чтобы убедиться, что пакет выполнен. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Дальнейшие действия
См. в следующей записи блога:
- [Модернизировать и расширьте рабочие процессы ETL/ELT с помощью действий SSIS в конвейерах фабрики данных Azure](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
