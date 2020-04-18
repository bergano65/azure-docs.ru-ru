---
title: Настройка настройки для runtime интеграции Azure-SSIS
description: В этой статье описывается, как использовать пользовательский интерфейс настройки для runtime интеграции Azure-SSIS для установки дополнительных компонентов или изменения настроек
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
ms.date: 04/15/2020
ms.openlocfilehash: ab2ba31d6b712bd3399bc8bf5b491337d462dac9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606206"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Настройка настройки для runtime интеграции Azure-SSIS

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Пользовательская настройка для интеграции служб интеграции серверов Azure-S'L (Azure-SSIS IR) обеспечивает интерфейс для добавления собственных шагов во время настройки или перенастройки ИК Azure-SSIS. 

Используя пользовательскую настройку, можно изменить конфигурацию или среду по умолчанию, например, запустить дополнительные службы Windows, сохранить учетные данные доступа к файловым долям или использовать сильную криптографию/более безопасный сетевой протокол (TLS 1.2). Или можно установить дополнительные компоненты, такие как сборки, драйверы или расширения, на каждом узеле ИК Azure-SSIS.

Пользовательские настройки на ИК Azure-SSIS можно выполнять двумя способами: 
* **Экспресс пользовательская настройка без скрипта**: Запустите некоторые общие системные конфигурации и команды Windows или установите некоторые популярные или рекомендуемые дополнительные компоненты без использования каких-либо скриптов.
* **Стандартная настройка с помощью скрипта:** Подготовка скрипта и связанных с ним файлов и загрузка их вместе в контейнер с каплями в учетную запись хранения Azure. Затем при настройке или перенастройке ИК Azure-SSIS для вашего контейнера предоставляется единый идентификатор единого ресурса общей подписи доступа (SAS). Затем каждый узел вашего ИК Azure-SSIS загружает скрипт и связанные с ним файлы из контейнера и запускает пользовательскую настройку с повышенными разрешениями. Когда пользовательская настройка закончена, каждый узла загружает стандартный выход выполнения и другие журналы в контейнер.

Вы можете установить как бесплатные, нелицензированные компоненты, так и платные, лицензированные компоненты с экспресс- и стандартными пользовательскими установками. Если вы независимый поставщик программного обеспечения (ISV), [см.](how-to-develop-azure-ssis-ir-licensed-components.md)

> [!IMPORTANT]
> Чтобы воспользоваться будущими усовершенствованиями, мы рекомендуем использовать v3 или более поздние серии узлов для вашего ИК Azure-SSIS с пользовательской настройкой.

## <a name="current-limitations"></a>Текущие ограничения

Следующие ограничения применяются только к стандартным пользовательским установкам:

- Если вы хотите использовать *gacutil.exe* в скрипте для установки сборок в кэш есобрание (GAC), вам необходимо предоставить *gacutil.exe* как часть пользовательской настройки. Или вы можете использовать копию, которая предоставляется в нашем контейнере *public Preview,* обсуждаемом позже в разделе "Инструкции".

- Если вы хотите ссылаться на субфолдер в скрипте, *msiexec.exe* не поддерживает `.\` обозначение для ссылки на корневую папку. Используйте команду, `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` такую `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`как вместо .

- Административные акции или скрытые сетевые акции, автоматически создаваемые Windows, в настоящее время не поддерживаются в ИК Azure-SSIS.

- Драйвер IBM iSeries Access ODBC не поддерживается на ИК Azure-SSIS. Вы можете увидеть ошибки установки во время пользовательской настройки. Если вы это сделаете, обратитесь за помощью в службу поддержки IBM.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы настроить ИК Azure-SSIS, вам нужны следующие элементы:

- [Подписка на Azure](https://azure.microsoft.com/)

- [Подготовка Azure SSIS IR.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Учетная запись хранения Azure](https://azure.microsoft.com/services/storage/). Не требуется для экспресс-настройки. Для стандартных пользовательских настроек вы загружаете и храните пользовательский сценарий настройки и связанные с ним файлы в контейнере с каплями. Процесс пользовательской установки отправляет свои журналы выполнения в тот же контейнер больших двоичных объектов.

## <a name="instructions"></a>Instructions

1. Если вы хотите настроить или перенастроить ИК Azure-SSIS с помощью PowerShell, загрузите и установите [Azure PowerShell.](/powershell/azure/install-az-ps) Для экспресс пользовательских установк, перейти к шагу 4.

1. Подготовьте свой собственный скрипт пользовательской установки и связанные с ним файлы (например, файлы BAT, CMD, EXE, DLL, MSI или PS1).

   * Вы должны иметь файл скрипта под названием *main.cmd*, который является точкой входа вашей пользовательской установки.  
   * Чтобы убедиться, что скрипт может быть выполнен в режиме бесшумно, мы рекомендуем сначала протестировать его на локальной машине.  
   * Если вы хотите, чтобы дополнительные журналы, генерируемые другими инструментами (например, *msiexec.exe),* были загружены в ваш контейнер, укажите предопределенную переменную среды, `CUSTOM_SETUP_SCRIPT_LOG_DIR`как папку журнала в скриптах (например, *msiexec /i xxx.msi/quiet/lv% CUSTOM_SETUP_SCRIPT_LOG_DIR% install.log).*

1. Скачать, установить и открыть [Explorer хранения Azure](https://storageexplorer.com/). Для этого:

   а. В **соответствии с (местные и прикрепленные)**, правой кнопкой мыши **учетных записей хранения**, а затем выберите **Подключение к лазурному хранению**.

      ![Подключение к службе хранилища Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Выберите **Используйте имя учетной записи и ключ для хранения,** а затем выберите **Следующий.**

      ![Использование имени и ключа учетной записи хранения](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Введите имя и ключ учетной записи хранилища Azure, выберите **«Следующий»,** а затем выберите **Connect.**

      ![Укажите имя учетной записи и ключ хранения](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. В рамках подключенной учетной записи хранения Azure, правой кнопкой мыши **Blob Контейнеры**, выберите **Создать Blob контейнера**, и имя нового контейнера.

      ![Создание контейнера BLOB-объектов](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   д) Выберите новый контейнер и загрузите пользовательский сценарий настройки и связанные с ним файлы. Убедитесь, что вы загружаете *main.cmd* на верхнем уровне контейнера, а не в любой папке. Кроме того, убедитесь, что контейнер содержит только необходимые файлы настройки, так что загрузка их в Azure-SSIS ИК позже не займет много времени. Максимальная продолжительность пользовательской установки в настоящее время установлена на 45 минут до его время. Это включает в себя время загрузки всех файлов из контейнера и установки их на ИК Azure-SSIS. Если настройка требует больше времени, поднимите билет на поддержку.

      ![Отправка файлов в контейнер больших двоичных объектов](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   е) Нажмите справа на контейнер, а затем выберите **Получить общую подпись доступа.**

      ![Получение подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   ж. Создайте SAS URI для вашего контейнера с достаточно длительным сроком действия и с разрешением на чтение/запись/список. Вам необходимо SAS URI для загрузки и запуска пользовательского сценария настройки и связанных с ним файлов всякий раз, когда любой узел вашего ИК Azure-SSIS перезапускается или перезапускается. Разрешение на запись необходимо для отправки журналов выполнения программы установки.

      > [!IMPORTANT]
      > Убедитесь, что срок действия SAS URI не истекает, а пользовательские ресурсы настройки всегда доступны в течение всего жизненного цикла иС Azure-SSIS, от создания до удаления, особенно если вы регулярно останавливаетесь и начинаете ИК Azure-SSIS в течение этого периода.

      ![Создание подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Скопируйте и сохраните URI SAS контейнера.

      ![Копирование и сохранение подписанного URL-адреса](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. При настройке или перенастройке ИК Azure-SSIS с помощью пользовательского пакета данных можно добавить или удалить пользовательские настройки, выбрав **настройку Azure-SSIS Integration Runtime с дополнительными системными конфигурациями/компонентными установками,** проверяя окно в разделе **Расширенные настройки** панели **настройки.** 

   Если вы хотите добавить стандартные пользовательские настройки, введите SAS URI вашего контейнера в **контейнере пользовательской настройки SAS URI.** 
   
   Если вы хотите добавить пользовательские настройки экспресса, выберите **Новый,** чтобы открыть **пользовательское панель настройки Add Express,** а затем выберите тип в списке выпадающих пользовательских наборов **Express:**

   * При выборе типа **команды Run cmdkey** можно сохранить доступ к учетным данным для ваших файлов или акций Azure Files в ИК Azure-SSIS, введя целевое имя компьютера или доменное имя, имя учетной записи или имя пользователя, а также ключ учетной записи или пароль в **/Add**, **/User**, и **/Pass** коробки. Это похоже на запуск команды Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) на локальной машине.
   
   * При выборе переменного типа **среды Добавить** можно добавить переменные среды Windows для использования в пакетах, которые работают **Variable name** на ИК Azure-SSIS, введя переменное имя и значение среды в переменные и **переменные** значения. Это похоже на запуск команды [набора](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) Windows на локальной машине.

   * При выборе типа **лицензированного компонента Install** можно выбрать интегрированный компонент из наших партнеров ISV в списке выпадающих **имен компонента:**

     * При выборе **компонента SentryOne** Task Factory можно установить набор компонентов Task [Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) из SentryOne на ваш ИК Azure-SSIS, введя ключ лицензии продукта, приобретенный у них в поле **ключа лицензии.** Текущая интегрированная версия **2019.4.3**.

     * Если вы выберете **HEDDA oh22. IO** компонент, вы можете установить [HEDDA. ](https://hedda.io/ssis-component/)Компонент качества/очистки данных io-класса от oh22 на ИК Azure-SSIS после покупки их сервиса. Текущая интегрированная версия **1.0.13**.

     * Если вы выберете **компонент SQLPhonetics.NET oh22,** можно установить компонент качества/сопоставления [данных SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) из oh22 на икс-иК Azure-SSIS, введя ключ лицензии продукта, приобретенный у них в поле ключа **лицензии.** Текущая интегрированная версия **1.0.43**.

     * Если вы выберете компонент **SSIS Integration Toolkit от KingswaySoft,** вы можете установить набор разъемов [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) для приложений CRM/ERP/marketing/marketing/collaboration, таких как Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud **License key** и т.д. от KingswaySoft на вашем Azure-SSIS IR, введя в них ключ лицензии. Текущая интегрированная версия **2019.2**.

     * Если вы выберете компонент **пакета производительности KingswaySoft SSIS,** вы можете установить набор компонентов [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) от KingswaySoft на ваш ИК Azure-SSIS, введя ключ лицензии продукта, который вы приобрели у них в поле ключа **лицензии.** Текущая интегрированная версия **10.0**.

     * Если вы выберете компонент **Xtract IS от Theobald Software,** вы можете установить набор разъемов [Xtract IS](https://theobald-software.com/en/xtract-is/) для системы SAP (ERP, S/4HANA, BW) из программного обеспечения Theobald на вашем ИК Azure-SSIS путем перетаскивания & удаления/загрузки файла лицензии продукта, который вы приобрели у них, в файл **лицензии.** Текущая интегрированная версия **6.1.1.3**.

   Дополнительные пользовательские настройки экспресс-экспресса будут отображаться в разделе **Расширенные настройки.** Чтобы удалить их, выберите их флажки, а затем выберите **Удалить**.

   ![Расширенные настройки с пользовательскими настройками](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. При настройке или перенастройке ИК Azure-SSIS с помощью PowerShell можно добавить `Set-AzDataFactoryV2IntegrationRuntime` или удалить пользовательские настройки, запустив cmdlet перед запуском ИК Azure-SSIS.
   
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
   
   После завершения стандартной настройки и запуска ИК Azure-SSIS в папке main.cmd и других журналов исполнения в папке *main.cmd.log контейнера main.cmd.log* можно найти стандартный выход *main.cmd.log.*

1. Чтобы просмотреть некоторые образцы стандартных пользовательских настроек, подключитесь к нашему контейнеру Public Preview с помощью Azure Storage Explorer.

   а. В узле **(Local and Attached)** (Локальные и присоединенные) щелкните правой кнопкой мыши **Учетные записи хранения**, выберите пункт **Connect to Azure storage** (Подключиться к службе хранилища Azure), а затем — **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения) и нажмите кнопку **Далее**.

      ![Подключение к хранилищу Azure с помощью подписанного URL-адреса](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Выберите **Используйте SAS URI,** а затем, в поле **URI,** введите следующие SAS URI:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Предоставление подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Выберите **следующий,** а затем выберите **Connect**.

   d. В левой панели выберите подключенный контейнер **publicpreview,** а затем дважды щелкните папку *CustomSetupScript.* Внутри этой папки находятся следующие элементы:

      * Папка *«Пример»,* содержащая пользовательскую настройку для установки основной задачи на каждом узеле ИК Azure-SSIS. Задача не делает ничего, только несколько секунд находится в спящем режиме. Папка также содержит папку *gacutil,* все содержимое которой *(gacutil.exe*, *gacutil.exe.config*, и *1033'gacutlrc.dll*) могут быть скопированы как в контейнер.

      * Папка *UserScenarios,* которая содержит несколько пользовательских образцов настройки из реальных сценариев пользователя.

        ![Содержимое контейнера общедоступной предварительной версии](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   д) Дважды щелкните папку *UserScenarios,* чтобы найти следующие элементы:

      * *Папка .NET FRAMEWORK 3.5,* содержащая пользовательский скрипт настройки *(main.cmd),* для установки более ранней версии рамочной программы .NET, которая может потребоваться для пользовательских компонентов на каждом узеле вашего ИК Azure-SSIS.

      * Папка *BCP,* которая содержит пользовательский скрипт настройки *(main.cmd*) для установки утилит командной строки S'L Server *(MsSqlCmdLnUtils.msi),* включая программу копирования навалом *(bcp),* на каждом узеле вашего Azure-SSIS IR.

      * Папка *EXCEL,* содержащая пользовательский сценарий настройки *(main.cmd)* для установки сборок и библиотек, которые можно использовать в задачах скрипта для динамического чтения и записи файлов Excel на каждом узле вашего ИК Azure-SSIS. 
      
        Во-первых, скачать [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) и [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), а затем загрузить их все вместе с *main.cmd* в контейнер. Кроме того, если вы хотите использовать только стандартный менеджер подключения Excel, источник Excel и пункт назначения Excel, необходимый доступ, перемотанный доступ, уже предустановлен на ИК Azure-SSIS, так что вам не нужна настройка.
      
      * Папка *MYS'L ODBC,* содержащая пользовательский сценарий настройки *(main.cmd),* для установки драйверов MyS'L ODBC на каждом узеле вашего ИК Azure-SSIS. Эта настройка позволяет использовать диспетчер, источник и пункт назначения ODBC для подключения к серверу MyS'L. 
     
        Во-первых, [скачать последние 64-разрядные и 32-битные версии myS'L ODBC драйвера установки](https://dev.mysql.com/downloads/connector/odbc/) (например, *mysql-разъем-odbc-8.0.13-winx64.msi* и *mysql-разъем-odbc-8.0.13-win32.msi),* а затем загрузить их все вместе с *main.cmd* к вашему контейнеру.

      * Папка *ORACLE ENTERPRISE,* которая содержит пользовательский сценарий настройки *(main.cmd)* и файл конфигурации безмолвной установки *(client.rsp)* для установки разъемов Oracle и драйвера OCI на каждом узеле вашего ИК-enterprise Edition Azure-SSIS. Эта настройка позволяет использовать Oracle Connection Manager, источник и пункт назначения для подключения к серверу Oracle. 
      
        Во-первых, скачать Microsoft Connectors v5.0 для Oracle (*AttunitySISSIsOraAdaptersSetup.msi* и *AttunitySISIsOraAdaptersSetup64.msi*) от [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) и последний клиент Oracle (например, *winx64_12102_client.zip*) от [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), а затем загрузить их все вместе с *main.cmd* и *client.rsp* в ваш контейнер. Если вы используете TNS для подключения к Oracle, вам также необходимо скачать *tnsnames.ora,* отредконить его и загрузить в свой контейнер, чтобы его можно было скопировать в папку установки Oracle во время настройки.

      * Папка *ORACLE STANDARD ADO.NET,* которая содержит пользовательский скрипт настройки *(main.cmd)* для установки драйвера Oracle ODP.NET на каждом узеле вашего ИК Azure-SSIS. Эта настройка позволяет использовать ADO.NET менеджер подключения, источник и пункт назначения для подключения к серверу Oracle. 
      
        Во-первых, [скачать последний Oracle ODP.NET драйвер](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (например, *ODP.NET_Managed_ODAC122cR1.zip*), а затем загрузить его вместе с *main.cmd* в контейнер.
       
      * Папка *ORACLE STANDARD ODBC,* содержащая пользовательский скрипт настройки *(main.cmd)* для установки драйвера Oracle ODBC и настройки имени источника данных (DSN) на каждом узеле вашего ИК Azure-SSIS. Эта настройка позволяет использовать диспетчера подключения ODBC, источник и пункт назначения или менеджер подключения к энергоресурсаи и источник с типом источника данных ODBC для подключения к серверу Oracle. 
      
        Во-первых, скачать последние Oracle Мгновенный клиент (базовый пакет или базовый пакет Lite) и ODBC пакет, а затем загрузить их все вместе с *main.cmd* в контейнер:
        * [Скачать 64-битные пакеты](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Базовый пакет: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip;* Базовый lite Package: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip;* OdBC Пакет: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Скачать 32-битные пакеты](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Базовый пакет: *instantclient-basic-nt-18.3.0.0.0dbru.zip;* Базовый Lite Package: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip;* OdBC Пакет: *мгновенный клиент-odbc-nt-18.0.0.0dbru.zip*)

      * Папка *ORACLE STANDARD OLEDB,* содержащая пользовательский сценарий настройки *(main.cmd)* для установки драйвера Oracle OLEDB на каждом узеле вашего ИК Azure-SSIS. Эта настройка позволяет использовать диспетчерсоединения OLEDB, источник и пункт назначения для подключения к серверу Oracle. 
     
        Во-первых, [скачать последний oracle OLEDB драйвер](https://www.oracle.com/partners/campaign/index-090165.html) (например, *ODAC122010Xcopy_x64.zip*), а затем загрузить его вместе с *main.cmd* в контейнер.

      * Папка *POSTGRES'L ODBC,* содержащая пользовательский скрипт настройки *(main.cmd)* для установки драйверов PostgreS'L ODBC на каждом узеле вашего ИК Azure-SSIS. Эта настройка позволяет использовать диспетчер, источник и пункт назначения ODBC для подключения к серверу PostgreS'L. 
     
        Во-первых, [загрузите последние 64-разрядные и 32-битные версии установщиков драйверов PostgreS'L ODBC](https://www.postgresql.org/ftp/odbc/versions/msi/) (например, *psqlodbc_x64.msi* и *psqlodbc_x86.msi),* а затем загрузите их все вместе с *main.cmd* в контейнер.

      * Папка *SAP BW,* содержащая пользовательский сценарий настройки *(main.cmd)* для установки сборки разъема SAP .NET *(librfc32.dll)* на каждом узле вашего ИК-enterprise Edition Azure-SSIS. Эта настройка позволяет использовать sAP Business Warehouse (BW) менеджер подключения, источник и пункт назначения для подключения к серверу SAP BW. 
      
        Во-первых, загрузите 64-битную или 32-битную версию *librfc32.dll* из папки установки SAP вместе с *main.cmd* в контейнер. Затем скрипт копирует сборку SAP в *папку %windir%-SysWow64* или *%windir%-System32* во время настройки.

      * Папка *STORAGE,* содержащая пользовательский скрипт настройки *(main.cmd)* для установки Azure PowerShell на каждом узеле вашего ИК Azure-SSIS. Эта настройка позволяет развертывать и запускать пакеты SSIS, запускающие [скрипты PowerShell для управления учетной записью хранения Azure.](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell) 
      
        *Скопируйте main.cmd,* образец *AzurePowerShell.msi* (или используйте последнюю версию) и *storage.ps1* в контейнере. Используйте *PowerShell.dtsx* в качестве шаблона для пакетов. Шаблон пакета сочетает в себе [задачу загрузки Azure Blob,](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)которая загружает *storage.ps1* в качестве модифицируемого скрипта PowerShell, и [задачу выполнения процесса,](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)которая выполняет скрипт на каждом узлу.

      * Папка *TERADATA,* которая содержит пользовательский сценарий настройки *(main.cmd),* связанный с ним файл *(install.cmd),* и установщик пакеты (*.msi*). Эти файлы устанавливают разъемы Teradata, API Teradata Parallel Transporter (TPT) и драйвер ODBC на каждом узеле вашего ИК-корпоративного издания Azure-SSIS. Эта настройка позволяет использовать диспетчер, источник и пункт назначения Teradata для подключения к серверу Teradata. 
      
        Во-первых, [загрузите файл Teradata Tools and Utilities 15.x zip](http://partnerintelligence.teradata.com) (например, *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip),* а затем загрузите его вместе с ранее упомянутыми файлами *.cmd* и *.msi* в ваш контейнер.

      * Папка *TLS 1.2,* содержащая пользовательский сценарий настройки *(main.cmd),* для использования сильной криптографии/более безопасного сетевого протокола (TLS 1.2) и отключения старых версий SSL/TLS на каждом узле вашего Azure-SSIS IR.

      * Папка *ЗУЛУ ОПЕНДжДК,* содержащая пользовательский скрипт настройки *(main.cmd)* и файл PowerShell *(install_openjdk.ps1)* для установки зулу с открытым икобом на каждом узле вашего ИК Azure-SSIS. Эта настройка позволяет использовать azure Data Lake Store и гибкие разъемы файлов для обработки файлов ORC и Parquet. Для получения дополнительной [Azure Feature Pack for Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)информации см. 
      
        Во-первых, [скачать последние Зулу OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (например, *zulu8.33.0.1-jdk8.0.192-win_x64.zip*), а затем загрузить его вместе с *main.cmd* и *install_openjdk.ps1* в контейнер.

        ![Папки в папке сценариев пользователя](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   е) Чтобы попробовать эти пользовательские образцы настройки, скопируйте содержимое из выбранной папки в контейнер.
   
      При настройке или настройке ИК Azure-SSIS с помощью пользовательского центра Data Factory выберите **настройку часов интеграции Azure-SSIS с дополнительными системными конфигурациями/компонентными установками,** проверяйте поле в разделе **Расширенные настройки,** а затем введите SAS URI в **контейнере SAS URI.**
   
      При настройке или перенастройке ИК Azure-SSIS с `Set-AzDataFactoryV2IntegrationRuntime` PowerShell запустите cmdlet с SAS `SetupScriptContainerSasUri` URI вашего контейнера в качестве значения для параметра.

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка корпоративного издания часов интеграции Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Разработка платных или лицензированных пользовательских компонентов для Runtime интеграции Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
