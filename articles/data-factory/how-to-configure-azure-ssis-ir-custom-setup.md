---
title: Настройка среды выполнения интеграции Azure Integration Services
description: В этой статье описано, как использовать интерфейс пользовательской установки для среды выполнения интеграции Azure-SSIS и установить дополнительные компоненты или изменить параметры
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
ms.date: 07/09/2020
ms.openlocfilehash: c3f3e7540f837722d4dd730c5635ac30c699ccd3
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184367"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Настройка среды выполнения интеграции Azure Integration Services

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Пользовательская установка Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) в фабрике данных Azure (ADF) предоставляет интерфейс для добавления собственных шагов во время подготовки или перенастройки Azure-SSIS IR. 

С помощью пользовательской установки можно изменить рабочую конфигурацию или среду по умолчанию, например, запустить дополнительные службы Windows, сохранить учетные данные для доступа к общим папкам или использовать стойкое шифрование или более безопасный сетевой протокол (TLS 1.2). Или можно установить дополнительные пользовательские или сторонние компоненты, такие как сборки, драйверы или расширения, на каждом узле Azure-SSIS IR. Дополнительные сведения о встроенных и предварительно установленных компонентах см. в статье [Встроенные и предварительно установленные компоненты в Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime).

Вы можете выполнять пользовательскую установку в Azure-SSIS IR одним из двух способов: 
* **Стандартная пользовательская установка со скриптами**: подготовьте скрипт и связанные файлы, а затем отправьте их в контейнер больших двоичных объектов в учетной записи хранения Azure. После этого для установки или перенастройки Azure-SSIS IR необходимо предоставить универсальный код ресурса (URI) SAS для контейнера. Затем каждый узел Azure-SSIS IR скачивает скрипт и связанные с ним файлы из контейнера и выполняет пользовательскую установку с более высоким уровнем разрешений. После завершения пользовательской установки каждый узел передает стандартный поток вывода выполнения и другие журналы в контейнер.
* **Быстрая пользовательская установка без скриптов**: запустите некоторые распространенные системные конфигурации и команды Windows или установите некоторые популярные или рекомендуемые дополнительные компоненты без использования скриптов.

Вы можете установить бесплатные, нелицензированные компоненты и платные лицензированные компоненты с помощью стандартных и Экспресс пользовательских настроек. Если вы являетесь независимым поставщиком программного обеспечения (ISV), см. статью [Разработка платных или лицензированных компонентов для Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Чтобы воспользоваться преимуществами будущих улучшений, мы рекомендуем использовать серию узлов версии 3 или более поздней для Azure-SSIS IR с помощью пользовательской установки.

## <a name="current-limitations"></a>Текущие ограничения

Следующие ограничения применяются только к стандартным пользовательским установкам:

- Если вы хотите использовать файл *gacutil.exe* в своем скрипте, чтобы установить сборки в глобальном кэше сборок, необходимо предоставить *gacutil.exe* как часть пользовательской установки. Кроме того, вы можете использовать копию, указанную в контейнере *общедоступной предварительной версии*, которая обсуждается далее в разделе "Инструкции".

- Если в скрипт нужно добавить ссылку на вложенную папку, не используйте *msiexec.exe*. Эта команда не поддерживает нотацию `.\` для ссылки на корневую папку. Используйте команду `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` вместо `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- В Azure-SSIS IR в настоящее время не поддерживаются административные общие папки или скрытые сетевые ресурсы, которые автоматически создаются Windows.

- Драйвер ODBC для IBM iSeries Access не поддерживается в Azure-SSIS IR. Вы можете столкнуться с ошибками установки во время пользовательской установки. Если это случится, обратитесь за помощью в службу поддержки IBM.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы настроить Azure-SSIS IR, понадобятся:

- [Подписка Azure](https://azure.microsoft.com/)

- [Подготовка Azure SSIS IR.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Учетная запись хранения Azure.](https://azure.microsoft.com/services/storage/) Не требуется для быстрой пользовательской установки. Чтобы выполнить стандартную пользовательскую установку, отправьте и сохраните свой скрипт пользовательской установки и связанные с ним файлы в контейнер больших двоичных объектов. Процесс пользовательской установки отправляет свои журналы выполнения в тот же контейнер больших двоичных объектов.

## <a name="instructions"></a>Instructions

Вы можете подготавливать или перенастраивать Azure-SSIS IR с помощью настраиваемых настроек в пользовательском интерфейсе ADF. Если вы хотите сделать то же самое с помощью PowerShell, скачайте и установите [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Стандартная Настраиваемая установка

Чтобы подготавливать или перенастроить Azure-SSIS IR со стандартными пользовательскими настройками, выполните следующие действия.

1. Подготовьте свой собственный скрипт пользовательской установки и связанные с ним файлы (например, файлы BAT, CMD, EXE, DLL, MSI или PS1).

   * Необходимо иметь файл скрипта с именем *main.cmd*, который является точкой входа в пользовательской установке.  
   * Чтобы проверить фоновое выполнение скрипта, рекомендуется сначала запустить его на локальном компьютере.  
   * Если необходимо, чтобы в контейнер отправлялись дополнительные журналы, генерируемые другими инструментами (например, *msiexec.exe*), укажите предопределенную переменную среды `CUSTOM_SETUP_SCRIPT_LOG_DIR` в качестве папки журнала в ваших скриптах (например, *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Скачайте, установите и откройте [Обозреватель службы хранилища Azure](https://storageexplorer.com/).

   а. В узле **(Локальные и присоединенные)** щелкните правой кнопкой мыши **Учетные записи хранения**, а затем выберите **Подключиться к службе хранилища Azure**.

      ![Подключение к службе хранилища Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Выберите **Использовать имя и ключ учетной записи хранения**, а затем нажмите кнопку **Далее**.

      ![Использование имени и ключа учетной записи хранения](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Введите имя и ключ учетной записи хранения Azure, нажмите кнопку **Далее**, а затем — **Подключить**.

      ![Предоставление имени и ключа учетной записи хранения](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. В разделе подключенной учетной записи хранения Azure щелкните правой кнопкой мыши **Контейнеры больших двоичных объектов**, выберите **Создать контейнер BLOB-объектов** и присвойте имя новому контейнеру.

      ![Создание контейнера BLOB-объектов](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Выберите новый контейнер и передайте скрипт пользовательской установки и связанные с ним файлы. Убедитесь, что файл *main.cmd* отправлен в верхний уровень вашего контейнера, а не в любую другую папку. Кроме того, убедитесь, что контейнер содержит только необходимые для пользовательской установки файлы. Таким образом, их дальнейшее скачивание на ваш Azure-SSIS IR не займет много времени. Максимальная продолжительность пользовательской установки в настоящее время настроена на 45 минут, а затем истекает время ее ожидания. Сюда входит время скачивания всех файлов из контейнера и их установка в Azure-SSIS IR. Если программе установки требуется больше времени, создайте запрос в службу поддержки.

      ![Отправка файлов в контейнер больших двоичных объектов](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Щелкните правой кнопкой мыши контейнер и выберите **Получить подписанный URL-адрес**.

      ![Получение подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Создайте URI SAS для своего контейнера с достаточно длительным сроком действия, а также разрешениями чтения, записи и вывода списка. URI SAS нужен для скачивания и выполнения сценария пользовательской установки и связанных с ним файлов всякий раз, когда пересоздается или перезапускается образ какого-либо узла Azure-SSIS IR. Разрешение на запись необходимо для отправки журналов выполнения программы установки.

      > [!IMPORTANT]
      > Убедитесь, что не истек срок действия URI SAS, а ресурсы пользовательской установки всегда доступны в течение всего жизненного цикла вашей Azure-SSIS IR — от создания до удаления, особенно в том случае, если вы регулярно останавливаете и запускаете вашу Azure-SSIS IR в течение этого периода.

      ![Создание подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Скопируйте и сохраните URI SAS контейнера.

      ![Копирование и сохранение подписанного URL-адреса](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. При подготовке или повторной настройке Azure-SSIS IR в пользовательском интерфейсе ADF установите флажок **настроить Azure-SSIS Integration Runtime с дополнительными конфигурациями системы или установками компонентов** на странице **Дополнительные параметры** панели **настройки среды выполнения интеграции** и введите универсальный код ресурса (URI) SAS контейнера в поле **URI SAS контейнера пользовательской установки** .

   ![Расширенные параметры для пользовательских установок](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

### <a name="express-custom-setup"></a>Быстрая Настраиваемая установка

Чтобы подготавливать или перенастроить Azure-SSIS IR с помощью Экспресс настраиваемых настроек, выполните следующие действия.

1. При подготовке или повторной настройке Azure-SSIS IR в пользовательском интерфейсе ADF установите флажок **настроить Azure-SSIS Integration Runtime с дополнительными конфигурациями системы или установками компонентов** на странице **Дополнительные параметры** панели **настройки среды выполнения интеграции** . 

1. Выберите **создать** , чтобы открыть панель " **Добавить Экспресс-настраиваемую настройку** ", а затем выберите тип в раскрывающемся списке Экспресс-настраиваемый **тип установки** :

   * Если выбран тип **Выполнение команды cmdkey**, можно сохранить учетные данные доступа для общих файловых ресурсов или общих файлов Azure в Azure-SSIS IR, введя целевое имя компьютера или имя домена, имя учетной записи или пользователя, ключ учетной записи или пароль в полях **/Add**, **/User** и **/Pass**. Это похоже на выполнение команды Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) на локальном компьютере.
   
   * Если выбран тип **Добавление переменной среды**, можно добавить переменные среды Windows, которые будут использоваться в пакетах, выполняемых в Azure-SSIS IR, введя имя переменной среды и значение в полях **Имя переменной** и **Значение переменной**. Это похоже на выполнение команды Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) на локальном компьютере.

   * Если выбран тип **Установка лицензированного компонента**, можно выбрать интегрированный компонент от наших партнеров независимых поставщиков программных продуктов в раскрывающемся списке **Название компонента**:

     * Если выбран компонент **SentryOne's Task Factory**, можно установить набор компонентов [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) от SentryOne в Azure-SSIS IR, введя лицензионный ключ продукта, который вы приобрели у этой компании и указали в поле **Ключ лицензии**. Текущая интегрированная версия — **2019.4.3**.

     * Если выбран компонент **oh22's HEDDA.IO**, можно установить компонент качества и очистки данных [HEDDA.IO](https://hedda.io/ssis-component/) от oh22 в Azure-SSIS IR после приобретения их службы. Текущая интегрированная версия — **1.0.13**.

     * Если выбран компонент **oh22's SQLPhonetics.NET**, можно установить компонент качества и очистки данных [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) от oh22 в Azure-SSIS IR, введя лицензионный ключ продукта, который вы приобрели у этой компании и указали в поле **Ключ лицензии**. Текущая интегрированная версия — **1.0.43**.

     * Если выбран компонент **KingswaySoft's SSIS Integration Toolkit**, можно установить набор соединителей [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) для приложений CRM/ERP/маркетинга/совместной работы, таких как Microsoft Dynamics, SharePoint, Project Server, Oracle/Salesforce Marketing и т. п. от KingswaySoft в Azure-SSIS IR, введя лицензионный ключ продукта, который вы приобрели у этой компании и указали в поле **Ключ лицензии**. Текущая интегрированная версия — **2019.2**.

     * Если выбран компонент **KingswaySoft's SSIS Productivity Pack**, можно установить набор компонентов [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) от KingswaySoft в Azure-SSIS IR, введя лицензионный ключ продукта, который вы приобрели у этой компании и указали в поле **Ключ лицензии**. Текущая интегрированная версия — **10.0**.

     * Если выбран компонент **Theobald Software's Xtract IS**, можно установить набор компонентов [Xtract IS](https://theobald-software.com/en/xtract-is/) для систем SAP (ERP, S/4HANA, BW) от Theobald Software в Azure-SSIS IR, перетянув или отправив файл лицензии продукта, который вы приобрели у этой компании и ввели в поле **Файл лицензии**. Текущая интегрированная версия — **6.1.1.3**.

Добавленные экспресс-установки будут отображаться на странице **Дополнительные параметры** . Чтобы удалить их, установите соответствующие флажки, а затем выберите **Удалить**.

### <a name="azure-powershell"></a>Azure PowerShell

Чтобы подготавливать или перенастроить Azure-SSIS IR с помощью настраиваемых настроек, используя Azure PowerShell, выполните следующие действия.

1. Если Azure-SSIS IR уже запущена или запущена, сначала прервите ее.

1. Затем можно добавить или удалить пользовательские настройки, выполнив `Set-AzDataFactoryV2IntegrationRuntime` командлет перед началом Azure-SSIS IR.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
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

1. После завершения стандартной пользовательской установки и запуска Azure-SSIS IR можно найти стандартные выходные данные файла *Main. cmd* и другие журналы выполнения в папке *Main. cmd. log* контейнера.

### <a name="standard-custom-setup-samples"></a>Стандартные примеры выборочной установки

Для просмотра и повторного использования некоторых примеров стандартных пользовательских настроек выполните следующие действия.

1. Подключитесь к нашему общедоступному контейнеру предварительной версии с помощью Обозреватель службы хранилища Azure.

   а. В узле **(Local and Attached)** (Локальные и присоединенные) щелкните правой кнопкой мыши **Учетные записи хранения**, выберите пункт **Connect to Azure storage** (Подключиться к службе хранилища Azure), а затем — **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения) и нажмите кнопку **Далее**.

      ![Подключение к хранилищу Azure с помощью подписанного URL-адреса](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Выберите **Использовать URI SAS** а затем в поле **URI** введите следующий URI SAS:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Предоставление подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Нажмите кнопку **Далее**, а затем — **Подключить**.

   d. В левой области выберите подключенный контейнер **publicpreview**, а затем дважды щелкните папку *CustomSetupScript*. Внутри этой папки находятся следующие элементы:

      * Папка *Sample*, которая содержит файл пользовательской установки для установки базовой задачи на каждом узле Azure-SSIS IR. Задача не делает ничего, только несколько секунд находится в спящем режиме. Папка также содержит папку *gacutil*, все содержимое которой (*gacutil.exe*, *gacutil.exe.config* и *1033\gacutlrc.dll*) можно скопировать в ваш контейнер.

      * Папка *UserScenarios*, содержащая несколько примеров конфигураций пользовательской установки из реальных ситуаций.

        ![Содержимое контейнера общедоступной предварительной версии](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Дважды щелкните папку *UserScenarios*, чтобы найти следующие элементы:

      * Папка *.NET FRAMEWORK 3.5*, которая содержит скрипт пользовательской установки (*main.cmd*) для установки более ранней версии .NET Framework, которая может потребоваться для пользовательских компонентов на каждом узле вашей Azure-SSIS IR.

      * Папка *BCP*, которая содержит скрипт пользовательской установки (*main.cmd*) для установки служебных программ командной строки SQL Server (*MsSqlCmdLnUtils.msi*), включая программу массового копирования (*bcp*), на каждом узле Azure-SSIS IR.

      * Папка *EXCEL*, которая содержит скрипт пользовательской установки (*main.cmd*) для установки сборок и библиотек C#, которые можно использовать в задачах скриптов для динамического чтения и записи файлов Excel на каждом узле Azure-SSIS IR. 
      
        Сначала скачайте [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) и [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), а затем отправьте их вместе с *main.cmd* в ваш контейнер. Если же вы хотите использовать только стандартный диспетчер соединений Excel, источник Excel и назначение Excel, необходимый распространяемый компонент доступа уже предварительно установлен у вас в Azure-SSIS IR, поэтому пользовательская установка не требуется.
      
      * Папка *MYSQL ODBC*, которая содержит скрипт пользовательской установки (*main.cmd*) для установки драйвера MySQL ODBC на каждом узле Azure-SSIS IR. Эта установка предоставляет возможность использовать диспетчер подключений ODBC, источник и назначение для подключения к серверу MySQL. 
     
        Сначала [скачайте последние 64- и 32-разрядные версии установщиков драйвера ODBC для MySQL](https://dev.mysql.com/downloads/connector/odbc/) (например, *mysql-connector-odbc-8.0.13-winx64.msi* и *mysql-connector-odbc-8.0.13-win32.msi*), а затем отправьте их вместе с *main.cmd* в контейнер.

      * Папка *ORACLE ENTERPRISE*, которая содержит пользовательский скрипт установки (*main.cmd*) и файл конфигурации автоматической установки (*client.rsp*), позволяющие установить соединители Oracle и драйвер OCI на каждом узле Azure-SSIS IR Enterprise Edition. Эта установка предоставляет возможность использовать диспетчер подключений Oracle, источник и назначение для подключения к серверу Oracle. 
      
        Сначала скачайте Microsoft Connectors версии 5.0 для Oracle (*AttunitySSISOraAdaptersSetup.msi* и *AttunitySSISOraAdaptersSetup64.msi*) из [Центр загрузок Майкрософт](https://www.microsoft.com/en-us/download/details.aspx?id=55179) и последнюю версию клиента Oracle (например, *winx64_12102_client.zip*) с сайта [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), а затем отправьте их все вместе с *main.cmd* и *client.rsp* в ваш контейнер. Если TNS используется для подключения к Oracle, необходимо скачать файл *tnsnames.ora*, изменить его и отправить в контейнер, чтобы его можно было скопировать в папку установки Oracle во время установки.

      * Папка *ORACLE STANDARD ADO.NET*, которая содержит скрипт пользовательской установки (*main.cmd*) для установки драйвера Oracle ODP.NET на каждом узле Azure-SSIS IR. Эта установка предоставляет возможность использовать диспетчер подключений ADO.NET, источник и назначение для подключения к серверу Oracle. 
      
        Сначала [скачайте последнюю версию драйвера Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (например, *ODP.NET_Managed_ODAC122cR1.zip*) из Oracle и затем передайте его вместе с *main.cmd* в свой контейнер.
       
      * Папка *ORACLE STANDARD ODBC*, которая содержит сценарий пользовательской установки (*main.cmd*) для установки драйвера Oracle ODBC и настройки имени источника данных на каждом узле Azure-SSIS IR. Эта установка позволяет использовать диспетчер подключений ODBC, источник и назначение или диспетчер подключений Power Query и источник с источником данных ODBC для подключения к серверу Oracle. 
      
        Сначала скачайте последнюю версию клиента Oracle Instant Client (пакет Basic или Basic Lite) и пакет ODBC, а затем отправьте их вместе с *main.cmd* в контейнер:
        * [Скачайте 64-разрядные пакеты](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (пакет Basic: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; пакет Basic Lite: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; пакет ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Скачайте 32-разрядные пакеты](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (пакет Basic: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; пакет Basic Lite: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; пакет ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Папка *ORACLE STANDARD OLEDB*, которая содержит скрипт пользовательской установки (*main.cmd*) для установки драйвера Oracle OLEDB на каждом узле Azure-SSIS IR. Эта установка предоставляет возможность использовать диспетчер подключений OLEDB, источник и назначение для подключения к серверу Oracle. 
     
        Сначала [скачайте последнюю версию драйвера Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (например, *ODAC122010Xcopy_x64.zip*) из Oracle, а затем отправьте его вместе с *main.cmd* в свой контейнер.

      * Папка *POSTGRESQL ODBC*, которая содержит скрипт пользовательской установки (*main.cmd*) для установки драйвера PostgreSQL ODBC на каждом узле Azure-SSIS IR. Эта установка предоставляет возможность использовать диспетчер подключений ODBC, источник и назначение для подключения к серверу PostgreSQL. 
     
        Сначала [скачайте последние 64- и 32-разрядные версии установщиков драйвера ODBC для PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (например, *psqlodbc_x64.msi* и *psqlodbc_x86.msi*), а затем отправьте их вместе с *main.cmd* в ваш контейнер.

      * Папка *SAP BW*, которая содержит скрипт пользовательской установки (*main.cmd*), чтобы установить сборку соединителя SAP .NET (*librfc32.dll*) на каждом узле среды выполнения интеграции Azure-SSIS IR Enterprise Edition. Эта установка предоставляет возможность использовать диспетчер подключений SAP Business Warehouse (BW), источник и назначение для подключения к серверу SAP BW. 
      
        Сначала передайте в контейнер 64- или 32-разрядную версию *librfc32.dll* из папки установки SAP вместе с *main.cmd* в ваш контейнер. Затем скрипт скопирует сборку SAP в папку *%windir%\SysWow64* или *%windir%\System32* во время установки.

      * Папка *STORAGE*, которая содержит скрипт пользовательской установки (*main.cmd*) для установки Azure PowerShell на каждом узле Azure-SSIS IR. Эта установка позволяет развертывать и запускать пакеты SSIS, которые запускают [скрипты PowerShell для работы с учетной записью хранения Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Скопируйте *main.cmd*, пример *AzurePowerShell.msi* (или используйте последнюю версию) и *storage.ps1* в ваш контейнер. Используйте *PowerShell.dtsx* в качестве шаблона для пакетов. Шаблон пакета объединяет [задачу загрузки большого двоичного объекта Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), которая загружает *storage.ps1* в качестве изменяемого скрипта PowerShell, и [задачу обработки выполнения](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), которая выполняет скрипт на каждом узле.

      * Папка *TERADATA*, которая содержит скрипт пользовательской установки (*main.cmd*), связанный файл (*install.cmd*) и пакеты установщика ( *.msi*). Эти файлы устанавливают соединители Teradata, API Teradata Parallel Transporter (TPT) и драйвер ODBC на каждый узел среды выполнения интеграции Azure-SSIS IR Enterprise Edition. Эта установка предоставляет возможность использовать диспетчер подключений Teradata, источник и назначение для подключения к серверу Teradata. 
      
        Сначала [скачайте ZIP-файл Teradata Tools and Utilities 15.x](http://partnerintelligence.teradata.com) (например, *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*), а затем отправьте его вместе с ранее упомянутыми файлами *.cmd* и *.msi* в ваш контейнер.

      * Папка *TLS 1.2*, которая содержит скрипт пользовательской установки (*main.cmd*) для использования надежного шифрования с более защищенным сетевым протоколом (TLS 1.2) и отключения старых версий SSL/TLS на каждом узле Azure-SSIS IR.

      * Папка *ZULU OPENJDK*, которая содержит скрипт пользовательской установки (*main.cmd*) и файл PowerShell (*install_openjdk.ps1*) для установки Zulu OpenJDK на каждом узле Azure-SSIS IR. Эта программа установки позволяет использовать Azure Data Lake Store и гибкие соединители файлов для обработки файлов ORC и Parquet. Дополнительные сведения см. в статье [Пакет дополнительных компонентов Azure для служб Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Сначала [скачайте последнюю версию Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (например, *zulu8.33.0.1-jdk8.0.192-win_x64.zip*), а затем отправьте этот файл вместе с *main.cmd* и *install_openjdk.ps1* в ваш контейнер.

        ![Папки в папке сценариев пользователя](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Чтобы повторно использовать эти стандартные образцы пользовательской установки, скопируйте содержимое выбранной папки в контейнер.

1. При подготовке или повторной настройке Azure-SSIS IR в пользовательском интерфейсе ADF установите флажок **настроить Azure-SSIS Integration Runtime с дополнительными конфигурациями системы или установками компонентов** на странице **Дополнительные параметры** панели **настройки среды выполнения интеграции** и введите универсальный код ресурса (URI) SAS контейнера в поле **URI SAS контейнера пользовательской установки** .
   
1. При подготовке или повторной настройке Azure-SSIS IR с помощью Azure PowerShell, если она уже запущена или запущена, запустите `Set-AzDataFactoryV2IntegrationRuntime` командлет с URI SAS контейнера в качестве значения `SetupScriptContainerSasUri` параметра, а затем запустите Azure-SSIS IR.

1. После завершения стандартной пользовательской установки и запуска Azure-SSIS IR можно найти стандартные выходные данные файла *Main. cmd* и другие журналы выполнения в папке *Main. cmd. log* контейнера.

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка выпуска Enterprise Azure-SSIS IR](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Разработка платных или лицензированных компонентов для Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
