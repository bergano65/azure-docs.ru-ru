---
title: Настройка настройки для Azure-SSIS Integration Runtime
description: В этой статье описывается, как использовать пользовательский интерфейс установки для Azure-SSIS Integration Runtime установки дополнительных компонентов или изменения параметров.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 02/01/2020
ms.openlocfilehash: c4502cc4a808b4a44f70c1f96fe38fd0ddeebdee
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187713"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Настройка настройки для Azure-SSIS Integration Runtime

Выборочная настройка Integration Runtime Azure-SQL Server Integration Services (Azure-SSIS IR) предоставляет интерфейс для добавления собственных шагов во время установки или перенастройки Azure-SSIS IR. 

С помощью пользовательской установки можно изменить операционную конфигурацию по умолчанию или среду, например, запустить дополнительные службы Windows или сохранить учетные данные доступа для файловых ресурсов. Или можно установить дополнительные компоненты, такие как сборки, драйверы или расширения, на каждом узле Azure-SSIS IR.

Вы можете выполнять пользовательские установки на Azure-SSIS IR одним из двух способов: 
* **Быстрая Настраиваемая установка без сценария**: запуск некоторых распространенных системных конфигураций и команд Windows или установка некоторых популярных или рекомендуемых дополнительных компонентов без использования скриптов.
* **Стандартная пользовательская установка со сценарием**. Подготовьте сценарий и связанные с ним файлы и отправьте их вместе в контейнер больших двоичных объектов в учетной записи хранения Azure. Затем вы предоставляете универсальный код ресурса (URI) подписанного URL-кода (SAS) для контейнера при настройке или повторной настройке Azure-SSIS IR. Затем каждый узел Azure-SSIS IR скачивает скрипт и связанные с ним файлы из контейнера и запускает пользовательскую установку с повышенными разрешениями. После завершения пользовательской установки каждый узел загружает стандартные выходные данные выполнения и другие журналы в контейнер.

Вы можете установить как бесплатные, так и нелицензированные компоненты, а также платные лицензированные компоненты с помощью Экспресс и стандартных настраиваемых настроек. Если вы являетесь независимым поставщиком программного обеспечения (ISV), см. статью [Разработка платных или лицензированных компонентов для Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Так как узлы серии v2 Azure-SSIS IR не подходят для настраиваемой установки, используйте вместо них узлы серии v3. Если вы уже используете узлы серии v2, переключитесь на узлы серии v3 как можно скорее.

## <a name="current-limitations"></a>Текущие ограничения

Следующие ограничения применяются только к стандартным пользовательским настройкам.

- Если вы хотите использовать *gacutil. exe* в скрипте для установки сборок в глобальный кэш сборок (GAC), необходимо предоставить *gacutil. exe* в рамках пользовательской установки. Кроме того, вы можете использовать копию, указанную в нашем *общедоступном контейнере предварительной версии* , как описано далее в разделе "инструкции".

- Если вы хотите сослаться на вложенную папку в скрипте, программа *msiexec. exe* не поддерживает `.\` нотацию для ссылки на корневую папку. Используйте команду, например `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`, а не `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- В этом Azure-SSIS IR в настоящее время не поддерживаются административные общие папки или скрытые сетевые ресурсы, которые автоматически создаются Windows.

- Драйвер ODBC для IBM iSeries Access не поддерживается на Azure-SSIS IR. Вы можете столкнуться с ошибками установки во время пользовательской установки. Если вы сделаете это, обратитесь за помощью в службу поддержки IBM.

## <a name="prerequisites"></a>предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы настроить Azure-SSIS IR, вам потребуются следующие элементы:

- [Подписка Azure](https://azure.microsoft.com/)

- [Подготовка Azure SSIS IR.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Учетная запись хранения Azure.](https://azure.microsoft.com/services/storage/) Не требуется для Экспресс пользовательских настроек. Для стандартных пользовательских настроек вы отправляете и сохраняете пользовательский скрипт установки и связанные с ним файлы в контейнере больших двоичных объектов. Процесс пользовательской установки отправляет свои журналы выполнения в тот же контейнер больших двоичных объектов.

## <a name="instructions"></a>Instructions

1. Если вы хотите настроить или перенастроить Azure-SSIS IR с помощью PowerShell, скачайте и установите [Azure PowerShell](/powershell/azure/install-az-ps). Для быстрых пользовательских настроек перейдите к шагу 4.

1. Подготовьте свой собственный скрипт пользовательской установки и связанные с ним файлы (например, файлы BAT, CMD, EXE, DLL, MSI или PS1).

   * Необходимо иметь файл скрипта с именем *Main. cmd*, который является точкой входа пользовательской установки.  
   * Чтобы убедиться в том, что скрипт может быть выполнен автоматически, рекомендуется сначала проверить его на локальном компьютере.  
   * Если требуется, чтобы дополнительные журналы, созданные другими инструментами (например, *msiexec. exe*), передавались в контейнер, укажите предопределенную переменную среды `CUSTOM_SETUP_SCRIPT_LOG_DIR`в качестве папки журнала в скриптах (например, *msiexec/i XXX. msi/quiet/лв% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. Скачайте, установите и откройте [Обозреватель службы хранилища Azure](https://storageexplorer.com/). Для этого:

   а. В разделе **(локальный и присоединенный)** щелкните правой кнопкой мыши элемент **учетные записи хранения**, а затем выберите **подключиться к службе хранилища Azure**.

      ![Подключение к службе хранилища Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Установите флажок **использовать имя и ключ учетной записи хранения**, а затем нажмите кнопку **Далее**.

      ![Использование имени и ключа учетной записи хранения](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Введите имя и ключ учетной записи хранения Azure, нажмите кнопку **Далее**, а затем выберите **подключить**.

      ![Укажите имя и ключ учетной записи хранения](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. В подключенной учетной записи хранения Azure щелкните правой кнопкой мыши **контейнеры больших двоичных объектов**, выберите **создать контейнер больших двоичных объектов**и назовите новый контейнер.

      ![Создание контейнера BLOB-объектов](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   д) Выберите новый контейнер и отправьте пользовательский скрипт установки и связанные с ним файлы. Обязательно отправьте *Main. cmd* на верхний уровень контейнера, а не в папку. Кроме того, убедитесь, что контейнер содержит только необходимые файлы пользовательской установки, поэтому загрузка этих файлов в Azure-SSIS IR позже не займет много времени. Максимальная продолжительность пользовательской установки в данный момент задается в течение 45 минут до истечения времени ожидания. Сюда входит время загрузки всех файлов из контейнера и их установка на Azure-SSIS IR. Если программе установки требуется больше времени, создайте запрос в службу поддержки.

      ![Отправка файлов в контейнер больших двоичных объектов](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   е) Щелкните контейнер правой кнопкой мыши и выберите **получить подпись общего доступа**.

      ![Получение подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   ж. Создайте URI SAS для контейнера с достаточным временем истечения срока действия и разрешением на чтение, запись или список. URI SAS необходим для скачивания и запуска пользовательского скрипта установки и связанных с ним файлов каждый раз при повторном создании образа или перезапуске любого узла Azure-SSIS IR. Разрешение на запись необходимо для отправки журналов выполнения программы установки.

      > [!IMPORTANT]
      > Убедитесь, что URI SAS не заканчивается, и ресурсы пользовательской установки всегда доступны во время всего жизненного цикла Azure-SSIS IR, от создания до удаления, особенно в том случае, когда вы регулярно останавливаете и запускаете Azure-SSIS IR в течение этого периода.

      ![Создание подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Скопируйте и сохраните URI SAS контейнера.

      ![Копирование и сохранение подписанного URL-адреса](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. При настройке или повторной настройке Azure-SSIS IR с помощью пользовательского интерфейса фабрики данных можно добавить или удалить пользовательские настройки, установив флажок **настроить Azure-SSIS Integration Runtime с дополнительными конфигурациями системы или установками компонентов** в разделе **Дополнительные параметры** панели **настройки среды выполнения интеграции** . 

   Если вы хотите добавить стандартные пользовательские настройки, введите URI SAS контейнера в поле **URI SAS контейнера пользовательской установки** . 
   
   Если требуется добавить Экспресс-настраиваемые настройки, выберите **создать** , чтобы открыть панель " **Добавить Экспресс-настраиваемую настройку** ", а затем выберите тип в раскрывающемся списке **Быстрая Настраиваемая установка тип установки** :

   * Если выбрать тип **команды Run cmdkey** , можно сохранить учетные данные доступа для общих файловых ресурсов или файлов Azure в Azure-SSIS IR, указав имя целевого компьютера или имя домена, имя учетной записи или имя пользователя, ключ учетной записи или пароль в полях **/Add**, **/User**и **/Пасс** . Это похоже на выполнение команды Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) на локальном компьютере.
   
   * Если выбрать тип **переменной среды добавить** , можно добавить переменные среды Windows, которые будут использоваться в пакетах, выполняемых в Azure-SSIS IR, введя имя переменной среды и значение в полях **имя переменной** и **значение переменной** . Это похоже на выполнение команды Windows [Set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) на локальном компьютере.

   * Если выбран тип **компонента установить лицензированный** , можно выбрать интегрированный компонент от наших партнеров ISV в раскрывающемся списке **имя компонента** :

     * При выборе компонента **фабрики задач SentryOne** можно установить набор компонентов [фабрики задач](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) из SentryOne на Azure-SSIS IR, введя лицензионный ключ продукта, который вы приобрели в поле **лицензионный ключ** . Текущая интегрированная версия — **2019.4.3**.

     * При выборе **OH22'S Хедда. Компонент ввода-вывода** можно установить [Хедда. ](https://hedda.io/ssis-component/)Компонент качества или очистки данных ввода-вывода из oh22 на Azure-SSIS IR после приобретения службы. Текущая интегрированная версия — **1.0.13**.

      * Если вы выберете компонент **oh22's SQLPhonetics.NET** , можно установить компонент качества и сопоставления данных [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) из oh22 на Azure-SSIS IR, введя лицензионный ключ продукта, который вы приобрели в поле **лицензионный ключ** . Текущая интегрированная версия — **1.0.43**.
   
   Добавленные Экспресс пользовательские настройки будут отображаться в разделе **Дополнительные параметры** . Чтобы удалить их, установите соответствующие флажки и нажмите кнопку **Удалить**.

   ![Дополнительные параметры с пользовательскими установками](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. При настройке или повторной настройке Azure-SSIS IR с помощью PowerShell можно добавить или удалить пользовательские настройки, выполнив командлет `Set-AzDataFactoryV2IntegrationRuntime` перед началом Azure-SSIS IR.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   После завершения стандартной пользовательской установки и запуска Azure-SSIS IR можно найти стандартные выходные данные файла *Main. cmd* и другие журналы выполнения в папке *Main. cmd. log* контейнера хранилища.

1. Чтобы просмотреть примеры стандартных пользовательских настроек, подключитесь к нашему общедоступному контейнеру предварительной версии с помощью Обозреватель службы хранилища Azure.

   а. В узле **(Local and Attached)** (Локальные и присоединенные) щелкните правой кнопкой мыши **Учетные записи хранения**, выберите пункт **Connect to Azure storage** (Подключиться к службе хранилища Azure), а затем — **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения) и нажмите кнопку **Далее**.

      ![Подключение к хранилищу Azure с помощью подписанного URL-адреса](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Выберите **использовать URI SAS** , а затем в поле **универсальный код** ресурса (URI) введите следующий URI SAS:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Предоставление подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Нажмите кнопку **Далее**, а затем выберите **подключить**.

   d. В левой области выберите подключенный контейнер **публикпревиев** , а затем дважды щелкните папку *кустомсетупскрипт* . Внутри этой папки находятся следующие элементы:

      * *Пример* папки, которая содержит выборочную установку для установки базовой задачи на каждом узле Azure-SSIS IR. Задача не делает ничего, только несколько секунд находится в спящем режиме. Папка также содержит папку *gacutil* , все содержимое которой (*gacutil. exe*, *gacutil. exe. config*и *1033 \ гакутлрк. dll*) можно скопировать в контейнер.

      * Папка *усерсценариос* , которая содержит несколько пользовательских примеров установки из сценариев реальных пользователей.

        ![Содержимое контейнера общедоступной предварительной версии](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   д) Дважды щелкните папку *усерсценариос* , чтобы найти следующие элементы:

      * Папка *.NET FRAMEWORK 3,5* , которая содержит пользовательскую установку для установки более ранней версии .NET Framework, которая может потребоваться для пользовательских компонентов на каждом узле Azure-SSIS IR.

      * Папка *bcp* , содержащая пользовательскую программу установки для установки SQL Server служебных программ командной строки (*мссклкмдлнутилс. msi*), включая программу копирования (*bcp*), на каждом узле Azure-SSIS IR.

      * Папка *Excel* , содержащая пользовательский сценарий установки (*Main. cmd*) для установки C# сборок и библиотек, которые можно использовать в задачах скриптов для динамического чтения и записи файлов Excel на каждом узле Azure-SSIS IR. 
      
        Сначала скачайте [*ексцелдатареадер. dll*](https://www.nuget.org/packages/ExcelDataReader/) и [*DocumentFormat. OPENXML. dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), а затем отправьте их вместе с помощью *Main. cmd* в контейнер. Кроме того, если вы хотите использовать только стандартный диспетчер соединений с Excel, источник Excel и назначение Excel, необходимый распространяемый компонент Access уже предварительно установлен на Azure-SSIS IR, поэтому пользовательская настройка не требуется.
      
      * Папка *ODBC MySQL* , которая содержит пользовательский сценарий установки (*Main. cmd*) для установки драйверов ODBC MYSQL в каждом узле Azure-SSIS IR. Эта программа установки позволяет использовать диспетчер соединений ODBC, источник и назначение для подключения к серверу MySQL. 
     
        Сначала [скачайте последние 64-разрядные и 32-разрядные версии установщиков драйверов для MySQL ODBC](https://dev.mysql.com/downloads/connector/odbc/) (например, *мискл-коннектор-ОДБК-8.0.13-winx64. msi* и *мискл-коннектор-ОДБК-8.0.13-Win32. msi*), а затем отправьте их вместе с файлом *Main. cmd* в контейнер.

      * Папка *Oracle Enterprise* , которая содержит пользовательский сценарий установки (*Main. cmd*) и файл конфигурации автоматической установки (*Client. rsp*) для установки соединителей Oracle и драйвера OCI в каждом узле Azure-SSIS IR Enterprise Edition. Эта программа установки позволяет использовать диспетчер соединений Oracle, источник и назначение для подключения к серверу Oracle. 
      
        Сначала скачайте Microsoft Connectors версии 5.0 для Oracle (*аттунитиссисораадаптерссетуп. msi* и *AttunitySSISOraAdaptersSetup64. msi*) из [центра загрузки Майкрософт](https://www.microsoft.com/en-us/download/details.aspx?id=55179) и последней версии клиента Oracle (например, *winx64_12102_client. zip*) из [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), а затем отправьте все вместе с помощью *Main. cmd* и *Client. rsp* в контейнер. Если для подключения к Oracle используется TNS, необходимо также скачать *tnsnames. ORA*, изменить его и передать в контейнер, чтобы его можно было скопировать в папку установки Oracle во время установки.

      * *Стандартная папка oracle ADO.NET* , которая содержит пользовательский сценарий установки (*Main. cmd*) для установки драйвера Oracle ODP.NET на каждом узле Azure-SSIS IR. Эта программа установки позволяет использовать диспетчер соединений ADO.NET, источник и назначение для подключения к серверу Oracle. 
      
        Сначала [скачайте последнюю версию драйвера Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (например, *ODP. NET_Managed_ODAC122cR1. zip*), а затем отправьте ее вместе с *Main. cmd* в контейнер.
       
      * *Стандартная папка ODBC Oracle* , которая содержит пользовательский сценарий установки (*Main. cmd*) для установки драйвера Oracle ODBC и настройки имени источника данных (DSN) на каждом узле Azure-SSIS IR. Эта программа установки позволяет использовать диспетчер соединений ODBC, источник и назначение или диспетчер соединений Power Query и источник с типом источника данных ODBC для подключения к серверу Oracle. 
      
        Сначала скачайте последний клиент Oracle Instant Client (базовый пакет или базовый пакет Lite) и пакет ODBC, а затем отправьте все вместе с помощью *Main. cmd* в контейнер:
        * [Скачать 64-разрядные пакеты](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (базовый пакет: *инстантклиент-Басик-Виндовс. x64-18.3.0.0.0 дбру. zip*; Базовый пакет Lite: *инстантклиент-басиклите-Виндовс. x64-18.3.0.0.0 дбру. zip*; Пакет ODBC: *инстантклиент-ОДБК-Виндовс. x64-18.3.0.0.0 дбру. zip.* 
        * [Скачать 32-разрядные пакеты](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (базовый пакет: *инстантклиент-Басик-НТ-18.3.0.0.0 дбру. zip*; Базовый пакет Lite: *инстантклиент-басиклите-НТ-18.3.0.0.0 дбру. zip*; Пакет ODBC: *инстантклиент-ОДБК-НТ-18.3.0.0.0 дбру. zip*)

      * Папка *Oracle Standard OLEDB* , которая содержит пользовательский сценарий установки (*Main. cmd*) для установки драйвера Oracle OLEDB на каждом узле Azure-SSIS IR. Эта программа установки позволяет использовать диспетчер соединений OLEDB, источник и назначение для подключения к серверу Oracle. 
     
        Сначала [скачайте последнюю версию драйвера Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (например, *ODAC122010Xcopy_x64. zip*), а затем отправьте его вместе с *Main. cmd* в контейнер.

      * Папка *ODBC PostgreSQL* , которая содержит пользовательский сценарий установки (*Main. cmd*) для установки драйверов PostgreSQL ODBC в каждом узле Azure-SSIS IR. Эта программа установки позволяет использовать диспетчер соединений ODBC, источник и назначение для подключения к серверу PostgreSQL. 
     
        Сначала [скачайте последние 64-разрядные и 32-разрядные версии установщиков драйверов ODBC PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (например, *psqlodbc_x64. msi* и *psqlodbc_x86. msi*), а затем отправьте их вместе с файлом *Main. cmd* в контейнер.

      * Папка *SAP BW* , содержащая пользовательский сценарий установки (*Main. cmd*) для установки сборки соединителя SAP .NET (*librfc32. dll*) на каждом узле Azure-SSIS IR Enterprise Edition. Эта настройка позволяет подключиться к серверу SAP BW с помощью диспетчера соединений SAP Business Warehouse (BW), источника и назначения. 
      
        Сначала загрузите 64-или 32-разрядную версию *librfc32. dll* из папки установки SAP вместе с *Main. cmd* в контейнер. Затем сценарий копирует сборку SAP в папку *%WINDIR%\SysWow64* или *%WINDIR%\System32* во время установки.

      * Папка *хранилища* , которая содержит выборочную установку Azure PowerShell на каждом узле Azure-SSIS IR. Эта программа установки позволяет развертывать и запускать пакеты служб SSIS, в которых выполняются [сценарии PowerShell, для управления учетной записью хранения Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Скопируйте файл *Main. cmd*, пример *азуреповершелл. msi* (или используйте последнюю версию), а файл *Storage. ps1* — в контейнер. Используйте *PowerShell. dtsx* в качестве шаблона для своих пакетов. Шаблон пакета сочетает [задачу скачивания больших двоичных объектов Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), которая скачивает файл *Storage. ps1* в качестве изменяемого скрипта PowerShell и [задачу «Выполнение процесса](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)», которая выполняет скрипт на каждом узле.

      * Папка *Teradata* , содержащая пользовательский сценарий установки (*Main. cmd*), связанный файл (*install. cmd*) и пакеты установщика (*MSI*). Эти файлы устанавливают соединители Teradata, API параллельного транспорта Teradata (TPT) и драйвер ODBC для каждого узла Azure-SSIS IR Enterprise Edition. Эта программа установки позволяет использовать диспетчер соединений Teradata, источник и назначение для подключения к серверу Teradata. 
      
        Сначала [скачайте zip-файл средств Teradata и служебные программы 15. x](http://partnerintelligence.teradata.com) (например, *TeradataToolsAndUtilitiesBase__windows_indep. 15.10.22.00. zip*), а затем отправьте его вместе с ранее упомянутыми файлами *. cmd* и *. msi* в контейнер.

      * Папка *Zulu OPENJDK* , которая содержит пользовательский сценарий установки (*Main. cmd*) и файл PowerShell (*install_openjdk. ps1*) для установки Zulu OPENJDK на каждом узле Azure-SSIS IR. Эта программа установки позволяет использовать Azure Data Lake Store и гибкие соединители файлов для обработки файлов ORC и Parquet. Дополнительные сведения см. в статье [пакет дополнительных компонентов Azure для Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Сначала [скачайте последнюю версию Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (например, *Zulu 8.33.0.1-jdk 8.0.192-win_x64. zip*), а затем отправьте ее вместе с *Main. cmd* и *install_openjdk. ps1* в контейнер.

        ![Папки в папке сценариев пользователя](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   е) Чтобы попробовать эти образцы пользовательской установки, скопируйте содержимое из выбранной папки в контейнер.
   
      При настройке или повторной настройке Azure-SSIS IR с помощью пользовательского интерфейса фабрики данных установите флажок **настроить Azure-SSIS Integration Runtime с дополнительными конфигурациями системы или установками компонентов** в разделе **Дополнительные параметры** , а затем введите универсальный код ресурса (URI) SAS контейнера в поле **URI SAS контейнера пользовательской установки** .
   
      При настройке или повторной настройке Azure-SSIS IR с помощью PowerShell выполните командлет `Set-AzDataFactoryV2IntegrationRuntime` с URI SAS контейнера в качестве значения параметра `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка выпуска Enterprise Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Разработка платных или лицензированных пользовательских компонентов для Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
