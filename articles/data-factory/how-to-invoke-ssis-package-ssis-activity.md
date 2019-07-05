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
ms.date: 07/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f33259fff17633cc4864a342609f747ebb9902ba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484839"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Запуск пакета Integration Services с помощью действия выполнения пакета SSIS в Фабрике данных Azure
В этой статье описывается выполнение пакета служб SQL Server Integration Services (SSIS) в конвейере фабрики данных Azure (ADF) с помощью действия выполнения пакета служб SSIS. 

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Создайте среду выполнения интеграции Azure SSIS (IR), если у вас ее еще нет. Для этого выполните пошаговую инструкцию из статьи [Подготовка Integration Runtime Azure–SSIS Подготовка Azure SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

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

4. На **параметры** вкладке для действия выполнение пакета служб SSIS, выберите среду IR Azure SSIS, где вы хотите выполнить пакет. Если пакет использует проверку подлинности Windows для доступа к хранилищам данных, например SQL серверы и общие файлы на локальном компьютере, файлы Azure и др., проверьте **проверки подлинности Windows** флажок и введите значения для выполнения пакета учетные данные (**Домена**/**Username**/**пароль**). Кроме того можно использовать секреты, хранящиеся в ваш Azure Key Vault (AKV) в качестве их значения. Для этого нужно щелкнуть **AZURE KEY VAULT** флажок рядом с соответствующие учетные данные, select и изменение существующей службы связаны с хранилищем ключей AZURE или создайте новую, а затем выберите имя и версию секрета для значения учетных данных.  При Создание или изменение AKV связанной службы, select или изменить ваш существующий AKV или создайте новую, то можно предоставьте вашей AKV ADF управляемых идентификации доступ, если вы еще не сделали. Секреты можно также ввести непосредственно в следующем формате: `<AKV linked service name>/<secret name>/<secret version>`. Если для пакета требуется 32-разрядная среда выполнения, установите флажок **32-Bit runtime** (32-разрядная среда выполнения). 

   Для **размещение пакета**выберите **SSISDB**, **файловой системы (пакет)** , или **файловой системы (проект)** . При выборе **SSISDB** как расположение пакета, который автоматически выбирается, если ваша среда выполнения Интеграции Azure SSIS, были предоставлены каталога служб SSIS (SSISDB), расположенных на сервере базы данных SQL Azure или управляемого экземпляра, необходимо указать пакет для запуска, который был развернут в SSISDB. Если ваша среда выполнения Интеграции Azure SSIS запущена и **вручную записей** флажок снят, можно просмотреть и выбрать существующие папки/проекты и пакеты и среды из SSISDB. Нажмите кнопку **Обновить**, чтобы получить только что добавленные папки, проекты, пакеты или среды из SSISDB, и они станут доступны для просмотра и выбора. 
   
   Для **уровня ведения журнала** выберите предопределенную область ведения журнала для выполнения пакета. Установите флажок **Настроено**, если вы хотите ввести имя настраиваемого ведения журнала. 

   ![Автоматическая установка свойств на вкладке "Параметры"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Если ваша среда выполнения Интеграции Azure SSIS не выполняется или **вручную записей** флажок установлен, можно ввести путь пакета и среды из SSISDB непосредственно в следующих форматах: `<folder name>/<project name>/<package name>.dtsx` и `<folder name>/<environment name>`.

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   При выборе **файловой системы (пакет)** как расположение пакета, который автоматически выбирается, если ваша среда выполнения Интеграции Azure SSIS была подготовлена без SSISDB, необходимо указать пакет для запуска, предоставляя (Universal Naming Convention) Путь UNC) к файлу пакета (`.dtsx`) в **путь к пакету**. Например, если вы храните ваш пакет в службе файлов Azure, его путь к пакету будет `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Если вы настраиваете ваш пакет в отдельном файле, необходимо также указать UNC-путь к файлу конфигурации (`.dtsConfig`) в **путь конфигурации**. Например, если вы храните конфигурацию в службе файлов Azure, его путь конфигурации будет `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   При выборе **файловой системы (проект)** как расположение пакета, необходимо указать пакет для запуска, указав UNC-путь к файлу проекта (`.ispac`) в **путь к проекту** и пакет файлов () `.dtsx`) из проекта в **имя пакета**. Например, если проект хранится в службе файлов Azure, его путь к проекту будет `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Установка свойств на вкладке "Параметры" вручную](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Далее необходимо указать учетные данные для доступа к файлам конфигурации/пакета/проекта. Если ранее вы ввели значения учетных данных выполнение пакета (см. выше), можно повторно использовать их, проверив **так же, как упаковать учетные данные для выполнения** флажок. В противном случае необходимо ввести значения для доступа учетные данные пакета (**домена**/**Username**/**пароль**). Например, если вы храните/пакета/конфигурации проекта в службе файлов Azure **домена** — `Azure`; **Username** — `<storage account name>`; и **пароль**является `<storage account key>`. Кроме того можно использовать секреты, хранящиеся в вашей AKV, как их значения (см. выше). Эти учетные данные будут использоваться для доступа к пакета и дочерних пакетов в задаче "Выполнение пакета", все из собственные путь и тот же проект, а также конфигурации, включая теми, которые указаны в пакетах. 
   
   Если вы использовали **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** защиты уровня, при создании пакета с помощью SQL Server Data Tools (SSDT), необходимо ввести значение пароль по **пароль шифрования**. Кроме того можно использовать секретов, хранящихся в вашей AKV в качестве его значения (см. выше). Если вы использовали **EncryptSensitiveWithUserKey** уровень защиты, необходимо повторно ввести ваш конфиденциальных данных в файлах конфигурации или на **параметры служб SSIS** /  **Диспетчеры соединений**/**переопределяет свойство** вкладок (см. ниже). Если вы использовали **EncryptAllWithUserKey** уровень защиты, он не поддерживается, поэтому необходимо перенастроить пакет использовал другой уровень защиты с помощью SSDT или `dtutil` служебной программы командной строки. 
   
   Для **уровня ведения журнала** выберите предопределенную область ведения журнала для выполнения пакета. Установите флажок **Настроено**, если вы хотите ввести имя настраиваемого ведения журнала. Если вы хотите входа вашей выполнявшихся других страницах с помощью стандартных регистраторы, которые могут быть указаны в пакет, необходимо указать папку журнала, указав его UNC-путь в **путь для ведения журнала**. Например, если для хранения журналов в службе файлов Azure, путь для ведения журнала будет `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Вложенную папку создается в этом пути для каждого запуска отдельного пакета, с именем после выполнения действия выполнение пакета служб SSIS, идентификатор, в которой файлы журнала создаются каждые пять минут. 
   
   Наконец необходимо также указать учетные данные для доступа к папке журнала. Если ранее вы ввели значения учетных данных доступа пакета (см. выше), можно повторно использовать их, проверив **так же, как упаковать учетные данные для доступа** флажок. В противном случае необходимо ввести значения для доступа учетные данные ведения журнала (**домена**/**Username**/**пароль**). Например, если вы сохраните свои журналы в службе файлов Azure **домена** — `Azure`; **Username** — `<storage account name>`; и **пароль** является `<storage account key>`. Кроме того можно использовать секреты, хранящиеся в вашей AKV, как их значения (см. выше). Эти учетные данные будут использоваться для хранения журналов. 
   
   Для всех путей UNC, упомянутых выше полное имя файла должно быть короче 260 символов, и имя каталога должно быть короче 248 символов.

5. На **параметры служб SSIS** вкладку для действия, выполнение пакета служб SSIS, если выполняется IR Azure SSIS, **SSISDB** выбран как расположение пакета и **вручную записей** флажок на **параметры** вкладки не установлен, то существующие параметры служб SSIS в выбранный проект или пакет из SSISDB. отображаются позволяет присвоить им значения. В противном случае можно ввести их по очереди, чтобы присвоить им значения вручную. Убедитесь, что они существуют и введены правильно для успешного выполнения пакета. 
   
   Если вы использовали **EncryptSensitiveWithUserKey** уровень защиты при создании пакета с помощью SSDT и **файловой системы (пакет)** /**файловой системы (проект)** выбран как расположение пакета, необходимо также повторно введите ваши конфиденциальные параметры для присвоения значений их в файлах конфигурации или на этой вкладке. 
   
   При присвоении значений параметров, можно добавить динамическое содержимое с помощью выражения, функции, системные переменные фабрики данных AZURE и конвейера ADF параметры или переменные. Кроме того можно использовать секреты, хранящиеся в вашей AKV, как их значения (см. выше).

   ![Установка свойств на вкладке SSIS Parameters (Параметры SSIS)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. На **диспетчеры соединений** вкладку для действия, выполнение пакета служб SSIS, если выполняется IR Azure SSIS, **SSISDB** выбран как расположение пакета и **вручную записей**флажок на **параметры** вкладка флажок снят, существующих диспетчеров соединений в выбранный проект или пакет из SSISDB отображаются автоматически для присвоения значений свойствам. В противном случае можно ввести их по одной для присвоения значений свойствам вручную – убедитесь, что они существуют и правильность ввода для успешного выполнения пакета. 
   
   Если вы использовали **EncryptSensitiveWithUserKey** уровень защиты при создании пакета с помощью SSDT и **файловой системы (пакет)** /**файловой системы (проект)** выбран как расположение пакета, необходимо также повторно введите свои свойства диспетчера соединения конфиденциальные для присвоения значений их в файлах конфигурации или на этой вкладке. 
   
   При присвоении значений свойств диспетчера подключения, можно добавить динамическое содержимое с помощью выражения, функции, системные переменные фабрики данных AZURE и конвейера ADF параметры или переменные. Кроме того можно использовать секреты, хранящиеся в вашей AKV, как их значения (см. выше).

   ![Установка свойств на вкладке Connection Managers (Диспетчеры подключений)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. На **переопределяет свойство** вкладку для действия, выполнение пакета служб SSIS, можно ввести пути существующих свойств в один за другим, чтобы присвоить им значения вручную — убедитесь, что они существуют и будут правильно выбранный пакет для вашего пакета для успешного выполнения, например чтобы переопределить значение переменной вашего пользователя, введите его путь в следующем формате: `\Package.Variables[User::<variable name>].Value`. 
   
   Если вы использовали **EncryptSensitiveWithUserKey** уровень защиты при создании пакета с помощью SSDT и **файловой системы (пакет)** /**файловой системы (проект)** выбран как расположение пакета, необходимо также повторно введите свои конфиденциальные свойства для присвоения значений их в файлах конфигурации или на этой вкладке. 
   
   При присвоении значений свойств, можно добавить динамическое содержимое с помощью выражения, функции, системные переменные фабрики данных AZURE и конвейера ADF параметры или переменные.

   ![Установка свойств на вкладке Property Overrides (Переопределения свойств)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Значения, назначенные в файлах конфигурации и на *параметры служб SSIS* вкладки могут быть переопределены с помощью **диспетчеры соединений**/**переопределяет свойство** вкладки, тогда как запросы, назначенные на **диспетчеры соединений** вкладке также могут быть переопределены с помощью **переопределяет свойство** вкладки.

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

   ![Выполнение действия](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Вы можете запустить следующий **запрос** к базе данных SSISDB на своем сервере SQL Server Azure, чтобы убедиться, что пакет выполнен. 

   ```sql
   select * from catalog.executions
   ```

   ![Проверка выполнения пакета](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Можно также получить идентификатор выполнения SSISDB из выходных данных действия выполнение конвейера и использовать идентификатор для проверки более полные журналы выполнения и сообщения об ошибках в SQL Server Management Studio (SSMS).

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
   > Замените имена объектов, описания и пути, свойства и значения параметров, пароли и другие значения переменных перед сохранением файла. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "mySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "myAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
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
                               "secretName": "MySecret"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "abc"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "userName": {
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
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

2. В Azure PowerShell перейдите в папку `C:\ADF\RunSSISPackage`.

3. Чтобы создать конвейер **RunSSISPackagePipeline**, запустите **AzDataFactoryV2Pipeline набора** командлета.

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
Используйте **Invoke AzDataFactoryV2Pipeline** командлет для запуска конвейера. Командлет позволяет получить идентификатор выполнения конвейера для дальнейшего мониторинга.

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
3. Запустите **AzDataFactoryV2Trigger набора** командлета, который создает триггер. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. По умолчанию триггер находится в остановленном состоянии. Запустите триггер с управлением **AzDataFactoryV2Trigger начала** командлета. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Убедитесь, что триггер запущен, выполнив **Get AzDataFactoryV2Trigger** командлета. 

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

## <a name="next-steps"></a>Дальнейшие действия
См. в следующей записи блога:
-   [Модернизация и расширение рабочих процессов ETL/ELT с помощью операций MSSQL Integration Services в конвейерах ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
