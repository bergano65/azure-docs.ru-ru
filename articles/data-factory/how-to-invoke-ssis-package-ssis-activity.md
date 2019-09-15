---
title: Запуск пакета Integration Services с помощью действия выполнения пакета SSIS в Azure | Документация Майкрософт
description: В этой статье рассматривается запуск пакета SQL Server Integration Services (SSIS) в конвейере Фабрики данных Azure с помощью действия выполнения пакета SSIS.
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
ms.openlocfilehash: 9057cefa5108924c57dbc85bbb895b31e804a51c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000644"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Запуск пакета Integration Services с помощью действия выполнения пакета SSIS в Фабрике данных Azure
В этой статье описывается, как выполнить пакет SQL Server Integration Services (SSIS) в конвейере фабрики данных Azure с помощью действия "выполнение пакета служб SSIS". 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Создайте среду выполнения интеграции Azure SSIS (IR), если у вас ее еще нет. Для этого выполните пошаговую инструкцию из статьи [Подготовка Integration Runtime Azure–SSIS Подготовка Integration Runtime Azure – SSIS в Фабрике данных Azure](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Запуск пакета в портале Azure
В этом разделе для создания конвейера ADF с действием выполнения пакета SSIS, которое запускает пакет SSIS, используется пользовательский интерфейс (приложение) ADF.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Создание конвейера с действием "Выполнить пакет SSIS"
На этом этапе вы создадите конвейер, используя пользовательский интерфейс (приложение) ADF. Добавьте действие выполнения пакета SSIS в конвейер и настройте его для запуска пакета Integration Services. 

1. На странице обзора или домашней странице ADF на портале Azure щелкните плитку **Создание и мониторинг**, чтобы запустить пользовательский интерфейс (приложение) ADF на отдельной вкладке. 

   ![Домашняя страница фабрики данных](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   На странице **Начало работы** щелкните **Create pipeline** (Создать конвейер). 

   ![Страница "Начало работы"](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. На панели элементов **Действия** разверните элемент **Общие**, а затем перетащите действие **Execute SSIS Package** (Выполнить пакет SSIS) в область конструктора конвейера. 

   ![Перетаскивание действия выполнения пакета SSIS в область конструктора](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. На вкладке **Общие** для действия выполнения пакета SSIS введите имя и описание действия. Укажите необязательные значения времени ожидания и повторных попыток.

   ![Настройка свойств на вкладке "Общие"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. На вкладке **Параметры** для действия "выполнение пакета служб SSIS" выберите Azure-SSIS IR, в которой нужно запустить пакет. Если пакет использует проверку подлинности Windows для доступа к хранилищам данных, например серверы SQL Server/общие файловые ресурсы в локальной среде, файлы Azure и т. д., установите флажок **Проверка подлинности Windows** и введите значения учетных данных выполнения пакета (**домен** Пароль пользователя)/. / Кроме того, в качестве значений можно использовать секреты, хранящиеся в Azure Key Vault (AKV). Для этого щелкните флажок **хранилище ключей Azure** рядом с нужными учетными данными, выберите или измените существующую связанную службу AKV или создайте новую, а затем выберите имя или версию секрета для своего значения учетных данных.  При создании или редактировании связанной службы AKV вы можете выбрать или изменить существующий AKV или создать новый, но предоставьте удостоверение управляемой идентификации ADF AKV, если вы еще не сделали этого. Вы также можете ввести секреты непосредственно в следующем формате: `<AKV linked service name>/<secret name>/<secret version>`. Если для пакета требуется 32-разрядная среда выполнения, установите флажок **32-Bit runtime** (32-разрядная среда выполнения). 

   В качестве **расположения пакета**выберите **SSISDB**, **Файловая система (пакет)** или **Файловая система (проект)** . Если выбрать **SSISDB** в качестве расположения пакета, который автоматически выбирается, если Azure-SSIS IR была подготовлена с помощью каталога SSIS (SSISDB), размещенного на сервере или управляемый экземпляр базы данных SQL Azure, необходимо указать пакет для запуска, который был развернуто в SSISDB. Если Azure-SSIS IR выполняется и флажок **записи вручную** снят, можно просмотреть и выбрать существующие папки, проекты, пакеты и среды из SSISDB. Нажмите кнопку **Обновить**, чтобы получить только что добавленные папки, проекты, пакеты или среды из SSISDB, и они станут доступны для просмотра и выбора. Чтобы просмотреть или выбрать среды для выполнения пакета, необходимо заранее настроить проекты, чтобы добавить эти среды в качестве ссылок из тех же папок в SSISDB. Дополнительные сведения см. в разделе [Создание и сопоставление окружений служб SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   Для **уровня ведения журнала** выберите предопределенную область ведения журнала для выполнения пакета. Установите флажок **Настроено**, если вы хотите ввести имя настраиваемого ведения журнала. 

   ![Автоматическая установка свойств на вкладке "Параметры"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Если Azure-SSIS IR не запущена или установлен флажок **записи вручную** , можно ввести путь к пакету и среде из SSISDB непосредственно в следующих форматах: `<folder name>/<project name>/<package name>.dtsx` и. `<folder name>/<environment name>`

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Если в качестве расположения пакета выбрана **Файловая система (пакет)** , которая автоматически выбирается, если Azure-SSIS IR была подготовлена без SSISDB, необходимо указать пакет для запуска, указав UNC-путь к файл пакета (`.dtsx`) в **пути пакета**. Например, если пакет хранится в службе файлов Azure, его путь к пакету будет иметь `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`значение. 
   
   Если пакет настроен в отдельном файле, необходимо также указать UNC-путь к файлу конфигурации (`.dtsConfig`) в **пути конфигурации**. Например, если вы храните конфигурацию в службе файлов Azure, ее путь конфигурации будет иметь `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`значение.

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Если в качестве расположения пакета выбрать **Файловая система (проект)** , необходимо указать путь в формате UNC к файлу проекта (`.ispac`) в **пути проекта** и файле пакета (`.dtsx`) из проекта в  **Имя пакета**. Например, если вы храните проект в службе файлов Azure, его путь к проекту будет иметь `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`значение.

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Далее необходимо указать учетные данные для доступа к файлам проекта, пакета или конфигурации. Если вы ранее указали значения учетных данных для выполнения пакета (см. выше), их можно повторно использовать, установив флажок **учетные данные выполнения пакета** . В противном случае необходимо ввести значения для учетных данных доступа к пакету (**домен**/**имя_пользователя**/**пароль**). Например, если вы храните проект, пакет или конфигурацию в службе файлов Azure, **домен** имеет `Azure` **имя пользователя** `<storage account name>`, а **пароль** — `<storage account key>`. Кроме того, можно использовать секреты, хранящиеся в AKV, в качестве их значений (см. выше). Эти учетные данные будут использоваться для доступа к пакету и дочерним пакетам в задаче "выполнение пакета", из собственного пути или одного проекта, а также конфигураций, включая указанные в пакетах. 
   
   Если вы использовали уровень защиты **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** при создании пакета с помощью SQL Server Data Tools (SSDT), необходимо ввести значение пароля в поле **шифрование. пароль**. Кроме того, можно использовать секрет, хранящийся в AKV, как его значение (см. выше). Если вы использовали уровень защиты **EncryptSensitiveWithUserKey** , необходимо повторно ввести конфиденциальные значения в файлы/конфигурации или в свойство**Диспетчеры соединений** **параметров**/служб SSIS **Переопределяет** вкладки (см. ниже). Если вы использовали уровень защиты **EncryptAllWithUserKey** , он не поддерживается, поэтому необходимо перенастроить пакет для использования другого уровня защиты с помощью SSDT или `dtutil` служебной программы командной строки. 
   
   Для **уровня ведения журнала** выберите предопределенную область ведения журнала для выполнения пакета. Установите флажок **Настроено**, если вы хотите ввести имя настраиваемого ведения журнала. Если вы хотите регистрировать выполнение пакета, не используя стандартные регистраторы, которые можно указать в пакете, необходимо указать папку журнала, указав путь UNC в **пути к журналу**. Например, если вы храните журналы в службе файлов Azure, ваш путь к журналу будет `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`иметь значение. В этом пути будет создана вложенная папка для каждого отдельного пакета, который будет выполняться и назван после идентификатора запуска действия "выполнение пакета служб SSIS", в котором файлы журнала будут создаваться каждые пять минут. 
   
   Наконец, необходимо также указать учетные данные для доступа к папке журнала. Если вы ранее указали значения учетных данных для доступа к пакету (см. выше), их можно повторно использовать, установив флажок **учетные данные для доступа к пакету** . В противном случае необходимо ввести значения учетных данных для доступа к журналу (**домен**/**имя_пользователя**/**пароль**). Например, если вы храните журналы в службе файлов Azure, **домен** имеет `Azure` **имя пользователя** `<storage account name>`, а **пароль** `<storage account key>`—. Кроме того, можно использовать секреты, хранящиеся в AKV, в качестве их значений (см. выше). Эти учетные данные будут использоваться для хранения журналов. 
   
   Для всех UNC-путей, указанных выше, полное имя файла должно содержать менее 260 символов, а имя каталога должно содержать менее 248 символов.

5. На вкладке **Параметры служб SSIS** для действия выполнение пакета служб SSIS, если Azure-SSIS IR выполняется, в качестве расположения пакета выбрано **SSISDB** , а флажок **записи вручную** на вкладке **Параметры** не установлен, существующие службы SSIS параметры выбранного проекта или пакета из SSISDB будут отображаться для назначения им значений. В противном случае можно ввести их по очереди, чтобы присвоить им значения вручную. Убедитесь, что они существуют и введены правильно для успешного выполнения пакета. 
   
   Если вы использовали уровень защиты **EncryptSensitiveWithUserKey** при создании пакета через SSDT и файловую **систему (пакет)** /**Файловая система (проект)** выбраны в качестве расположения пакета, также необходимо повторно ввести ваши конфиденциальные параметры для назначения им значений в файлах конфигурации или на этой вкладке. 
   
   При назначении значений параметрам можно добавлять динамическое содержимое с помощью выражений, функций, системных переменных ADF, а также параметров и переменных конвейера ADF. Кроме того, можно использовать секреты, хранящиеся в AKV, в качестве их значений (см. выше).

   ![Установка свойств на вкладке SSIS Parameters (Параметры SSIS)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. На вкладке **Диспетчеры соединений** для действия выполнение пакета служб SSIS, если Azure-SSIS IR выполняется, в качестве расположения пакета выбирается **SSISDB** , а флажок **записи вручную** на вкладке **Параметры** снят, существующий Диспетчеры подключений в выбранном проекте или пакете из SSISDB будут отображаться для присвоения значений их свойствам. В противном случае их можно ввести один за другим, чтобы присвоить значения их свойствам вручную — убедитесь, что они существуют и правильно введены для успешной работы пакета. 
   
   Если вы использовали уровень защиты **EncryptSensitiveWithUserKey** при создании пакета через SSDT и файловую **систему (пакет)** /**Файловая система (проект)** выбраны в качестве расположения пакета, также необходимо повторно ввести ваши конфиденциальные свойства диспетчера соединений используются для назначения им значений в файлах конфигурации или на этой вкладке. 
   
   При назначении значений свойствам диспетчера соединений можно добавлять динамическое содержимое с помощью выражений, функций, системных переменных ADF, а также параметров и переменных конвейера ADF. Кроме того, можно использовать секреты, хранящиеся в AKV, в качестве их значений (см. выше).

   ![Установка свойств на вкладке Connection Managers (Диспетчеры подключений)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. На вкладке **переопределения свойств** для действия выполнение пакета служб SSIS можно ввести пути существующих свойств в выбранном пакете по одному, чтобы назначить значения для них вручную — убедитесь, что они существуют и правильно введены для пакета. выполнение завершилось с ошибкой, например, чтобы переопределить значение пользовательской переменной, введите ее путь в следующем формате: `\Package.Variables[User::<variable name>].Value`. 
   
   Если вы использовали уровень защиты **EncryptSensitiveWithUserKey** при создании пакета через SSDT и файловую **систему (пакет)** /**Файловая система (проект)** выбраны в качестве расположения пакета, также необходимо повторно ввести ваши конфиденциальные свойства для назначения им значений в файлах конфигурации или на этой вкладке. 
   
   При присвоении значений свойствам можно добавлять динамическое содержимое с помощью выражений, функций, системных переменных ADF, а также параметров и переменных конвейера ADF.

   ![Установка свойств на вкладке Property Overrides (Переопределения свойств)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Значения, назначенные в файлах конфигурации и на вкладке *Параметры служб SSIS* , можно переопределить с помощью **свойств диспетчеры**/соединений**переопределяет** вкладки, а те, которые назначены на вкладке **Диспетчеры соединений** , могут также можно переопределить с помощью вкладки « **переопределения свойств** ».

8. Чтобы проверить конфигурацию конвейера, щелкните **Проверка** на панели инструментов. Чтобы закрыть **отчет о проверке конвейера**, нажмите кнопку **>>** .

9. Опубликуйте конвейер в ADF, нажав кнопку **Опубликовать все**. 

### <a name="run-the-pipeline"></a>Запуск конвейера
На этом шаге вы активируете выполнение конвейера. 

1. Чтобы активировать конвейер, щелкните **Триггер** на панели инструментов, а затем **Trigger Now** (Активировать сейчас). 

   ![Trigger Now (Активировать сейчас)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. На странице **Запуск конвейера** нажмите кнопку **Готово**. 

### <a name="monitor-the-pipeline"></a>Мониторинг конвейера

1. Перейдите на вкладку **Мониторинг** слева. На ней отображается выполнение конвейера и его состояние вместе с другой информацией (например, время начала выполнения). Чтобы обновить это представление, щелкните **Refresh** (Обновить).

   ![Запуски конвейера](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Щелкните ссылку **View Activity Runs** (Просмотр выполнений действий) в столбце **Actions** (Действия). Вы видите выполнение только одного действия, так как конвейер содержит одно действие (действие "Выполнить пакет SSIS").

   ![Запуски операции](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Вы можете запустить следующий **запрос** к базе данных SSISDB на своем сервере SQL Server Azure, чтобы убедиться, что пакет выполнен. 

   ```sql
   select * from catalog.executions
   ```

   ![Проверка выполнения пакета](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Можно также получить идентификатор выполнения SSISDB из выходных данных выполнения действия конвейера и использовать этот идентификатор для проверки более полных журналов выполнения и сообщений об ошибках в SQL Server Management Studio (SSMS).

   ![Получите идентификатор выполнения.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Запуск конвейера по расписанию с помощью триггера

Вы также можете создать запланированный триггер для запуска конвейера по расписанию (ежечасно, ежедневно и т. д.). Пример см. в разделе [Запуск конвейера по расписанию](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Выполнение пакета с помощью PowerShell
В этом разделе для создания конвейера ADF с действием выполнения пакета служб SSIS, которое запускает пакет SSIS, используется Azure PowerShell. 

Чтобы установить модули Azure PowerShell, выполните пошаговые инструкции из [этой статьи](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Создание ADF с Azure-SSIS IR
Вы можете использовать существующий конвейер ADF, в котором уже подготовлена Azure-SSIS IR, или создать новый конвейер ADF с Azure-SSIS IR, выполнив пошаговые инструкции в статье [Подготовка среды выполнения интеграции Azure SSIS в Фабрике данных Azure с помощью PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Создание конвейера с действием "Выполнить пакет SSIS" 
На этом этапе создается конвейер с действием "Выполнить пакет SSIS". Это действие запускает пакет SSIS. 

1. Создайте JSON-файл **RunSSISPackagePipeline.json** в папке **C:\ADF\RunSSISPackage** с содержимым, как в приведенном ниже примере.

   > [!IMPORTANT]
   > Перед сохранением файла замените имена и описания объектов, пути, значения свойств и параметров, пароли и другие значения переменных. 

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

   Для выполнения пакетов, хранящихся в файловых системах, файловых ресурсах или файлах Azure, можно ввести значения свойств расположения пакета или журнала следующим образом.

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

   Для выполнения пакетов в проектах, хранящихся в файловых системах, файловых ресурсах или файлах Azure, можно ввести значения для свойства расположение пакета следующим образом.

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

2. В Azure PowerShell перейдите в папку `C:\ADF\RunSSISPackage`.

3. Чтобы создать конвейер **RunSSISPackagePipeline**, выполните командлет **Set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Пример выходных данных:

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

Запустите приведенный ниже скрипт PowerShell, чтобы проверять состояние выполнения, пока не закончится копирование данных. Скопируйте приведенный ниже скрипт в окно PowerShell и нажмите клавишу ВВОД. 

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

Вы также можете отслеживать конвейер с помощью портала Azure. Пошаговые инструкции см. в разделе [Мониторинг конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Запуск конвейера по расписанию с помощью триггера
На предыдущем шаге вы запустили конвейер по запросу. Вы также можете создать триггер расписания для запуска конвейера по расписанию (ежечасно, ежедневно и т. д.).

1. Создайте файл JSON с именем **MyTrigger.json** в папке **C:\ADF\RunSSISPackage** со следующим содержимым: 

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
2. В **Azure PowerShell** перейдите в папку **C:\ADF\RunSSISPackage**.
3. Выполните командлет **Set-AzDataFactoryV2Trigger** , который создает триггер. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. По умолчанию триггер находится в остановленном состоянии. Запустите триггер, выполнив командлет **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Убедитесь, что триггер запущен, выполнив командлет **Get-AzDataFactoryV2Trigger** . 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Через час выполните следующую команду. Например, если текущее время 15:25 (UTC), запустите команду в 16:00 (UTC). 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Вы можете запустить следующий запрос к базе данных SSISDB на своем сервере SQL Server Azure, чтобы убедиться, что пакет выполнен. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Следующие шаги
См. в следующей записи блога:
-   [Модернизация и расширение рабочих процессов ETL/ELT с помощью операций MSSQL Integration Services в конвейерах ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
