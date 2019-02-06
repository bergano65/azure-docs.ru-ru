---
title: Пользовательская настройка среды выполнения интеграции Azure–SSIS | Документация Майкрософт
description: В этой статье описано, как использовать интерфейс пользовательской установки для среды выполнения интеграции Azure–SSIS и установить дополнительные компоненты или изменить параметры
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 1/25/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 66f41ffef5d72f5d574bb78d3b810f4a4dc2c4c1
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098737"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Пользовательская установка для среды выполнения интеграции Azure–SSIS

С помощью пользовательских настроек можно добавить собственные шаги установки во время подготовки или перенастройки среды выполнения интеграции Azure–SSIS. Настройки пользователя позволяют изменить стандартную рабочую конфигурацию либо среду (например, запустить дополнительные службы Windows или сохранить учетные данные доступа для общих папок) или установить дополнительные компоненты (например, сборки, драйверы или расширения) на каждом узле среды выполнения интеграции Azure-SSIS.

Для настройки пользовательской установки вы подготавливаете скрипт и связанные файлы, а затем отправляете их в контейнер больших двоичных объектов в учетной записи хранения Azure. При подготовке или перенастройке среды выполнения интеграции Azure SSIS необходимо предоставить универсальный код ресурса (URI) SAS для контейнера. Затем каждый узел Azure SSIS IR скачивает скрипт и связанные с ним файлы из контейнера и выполняет пользовательскую установку с более высоким уровнем привилегий. После завершения пользовательской установки каждый узел передает стандартный поток вывода выполнения и другие журналы в контейнер.

Можно установить как бесплатные, так и нелицензированные компоненты, а также платные или лицензированные компоненты. Если вы являетесь независимым поставщиком ПО, ознакомьтесь со статьей [Develop paid or licensed custom components for the Azure SSIS integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md) (Разработка платных или лицензионных пользовательских компонентов для среды выполнения интеграции Azure SSIS).

> [!IMPORTANT]
> Узлы версии 2 Azure-SSIS IR не подходят для пользовательской установки, поэтому рекомендуется использовать вместо них узлы версии 3.  Если вы уже используете узлы версии 2, перейдите на узлы версии 3 как можно скорее.

## <a name="current-limitations"></a>Текущие ограничения

-   Если вы хотите использовать файл `gacutil.exe`, чтобы установить сборки в глобальном кэше сборок, необходимо предоставить `gacutil.exe` как часть пользовательской установки или воспользоваться его копией, доступной в контейнере общедоступной предварительной версии.

-   Если в скрипт нужно добавить ссылку на вложенную папку, не используйте `msiexec.exe`. Эта команда не поддерживает нотацию `.\` для ссылки на корневую папку. Вместо `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` используйте такую команду, как `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`.

-   Если во время пользовательской настройки среды выполнения интеграции Azure–SSIS необходимо подключить к виртуальной сети, это можно сделать только с помощью виртуальной сети Azure Resource Manager. Классическая виртуальная сеть не поддерживается.

-   Административная общая папка в Azure-SSIS IR в настоящее время не поддерживается.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить Azure SSIS IR, необходимо следующее:

-   [Подписка Azure.](https://azure.microsoft.com/)

-   [База данных SQL Azure или сервер управляемого экземпляра.](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Подготовка Azure SSIS IR.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Учетная запись хранения Azure.](https://azure.microsoft.com/services/storage/) Чтобы выполнить пользовательскую установку, отправьте и сохраните свой скрипт пользовательской установки и связанные с ним файлы в контейнер больших двоичных объектов. Процесс пользовательской установки отправляет свои журналы выполнения в тот же контейнер больших двоичных объектов.

## <a name="instructions"></a>Указания

1.  Загрузите и установите [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) версии 5.4 или более поздней.

1.  Подготовьте свой собственный скрипт пользовательской установки и связанные с ним файлы (например, файлы BAT, CMD, EXE, DLL, MSI или PS1).

    1.  Необходимо иметь файл скрипта с именем `main.cmd`, который является точкой входа в пользовательской установке.

    1.  Если необходимо, чтобы в контейнер отправлялись дополнительные журналы, генерируемые другими инструментами (например, `msiexec.exe`), укажите предопределенную переменную среды `CUSTOM_SETUP_SCRIPT_LOG_DIR` в качестве папки журнала в ваших скриптах (например, `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1.  Загрузите, установите и запустите [Обозреватель службы хранилища Azure](http://storageexplorer.com/).

    1.  В узле **(Local and Attached)** (Локальные и присоединенные) щелкните правой кнопкой мыши **Учетные записи хранения**, а затем — **Connect to Azure storage** (Подключиться к службе хранилища Azure).

       ![Подключение к службе хранилища Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    1.  Выберите **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения), а затем нажмите кнопку **Далее**.

       ![Использование имени и ключа учетной записи хранения](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    1.  Введите имя и ключ учетной записи хранения Azure, нажмите кнопку **Далее**, а затем — **Подключить**.

       ![Укажите имя и ключ учетной записи хранения](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    1.  В разделе подключенной учетной записи хранения Azure щелкните правой кнопкой мыши **Контейнеры больших двоичных объектов**, выберите **Создать контейнер BLOB-объектов** и присвойте имя новому контейнеру.

       ![Создание контейнера BLOB-объектов](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    1.  Выберите новый контейнер и передайте скрипт пользовательской установки и связанные с ним файлы. Убедитесь, что `main.cmd` отправлен в верхний уровень вашего контейнера, а не в любую другую папку. Кроме того, убедитесь, что контейнер содержит только необходимые для пользовательской установки файлы. Таким образом, их дальнейшая загрузка на ваш Azure-SSIS IR не займет много времени.

       ![Отправка файлов в контейнер больших двоичных объектов](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    1.  Щелкните правой кнопкой мыши контейнер и выберите **Get Shared Access Signature** (Получить подписанный URL-адрес).

       ![Получение подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    1.  Создайте URI SAS для своего контейнера с достаточно длительным сроком действия, а также разрешениями чтения, записи и вывода списка. URI SAS нужен для скачивания и выполнения сценария выборочной установки и связанных с ним файлов всякий раз, когда пересоздается или перезапускается образ какого-либо узла Azure-SSIS IR. Разрешение на запись необходимо для отправки журналов выполнения программы установки.

        > [!IMPORTANT]
        > Убедитесь, что не истек срок действия URI SAS, а ресурсы выборочной установки всегда доступны в течение всего жизненного цикла вашей Azure-SSIS IR — от создания до удаления, особенно в том случае, если вы регулярно останавливаете и запускаете вашу Azure-SSIS IR в течение этого периода.

       ![Создание подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    1.  Скопируйте и сохраните URI SAS контейнера.

       ![Копирование и сохранение подписанного URL-адреса](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    1.  При подготовке или перенастройке Azure-SSIS IR с помощью пользовательского интерфейса фабрики данных, перед запуском Azure-SSIS IR введите значение URI подписанного URL-адреса контейнера в соответствующее поле на панели **Дополнительные параметры**.

       ![Ввод подписанного URL-адреса.](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

       При подготовке или перенастройке Azure SSIS IR с помощью PowerShell, прежде чем запустить Azure SSIS IR, выполните командлет `Set-AzureRmDataFactoryV2IntegrationRuntime` с URI SAS контейнера в качестве значения нового параметра `SetupScriptContainerSasUri`. Например: 

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    1.  После завершения пользовательской установки и запуска Azure SSIS IR можно найти стандартные выходные данные `main.cmd` и другие журналы выполнения в папке `main.cmd.log` контейнера хранилища.

1.  Чтобы просмотреть другие примеры пользовательской установки, подключитесь к контейнеру общедоступной предварительной версии с помощью Обозревателя службы хранилища Azure.

    a.  В узле **(Local and Attached)** (Локальные и присоединенные) щелкните правой кнопкой мыши **Учетные записи хранения**, выберите пункт **Connect to Azure storage** (Подключиться к службе хранилища Azure), а затем — **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения) и нажмите кнопку **Далее**.

       ![Подключение к хранилищу Azure с помощью подписанного URL-адреса](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Выберите **Use a SAS URI** (Использовать URI SAS) и введите следующий URI SAS для контейнера общедоступной предварительной версии. Нажмите кнопку **Далее**, а затем — **Подключить**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Предоставление подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Выберите подключенный контейнер общедоступной предварительной версии и дважды щелкните папку `CustomSetupScript`. Внутри этой папки находятся следующие элементы:

       1. Папка `Sample`, где содержится файл пользовательской установки для установки базовой задачи на каждом узле Azure SSIS IR. Задача не делает ничего, только несколько секунд находится в спящем режиме. Папка также содержит папку `gacutil`, все содержимое которой (`gacutil.exe`, `gacutil.exe.config` и `1033\gacutlrc.dll`) можно скопировать в контейнер. Кроме того, `main.cmd` содержит комментарии для сохранения учетных данных доступа к общим папкам.

       1. Папка `UserScenarios`, содержащая несколько конфигураций пользовательской установки для реальных сценариев.

    ![Содержимое контейнера общедоступной предварительной версии](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Дважды щелкните папку `UserScenarios`. Внутри этой папки находятся следующие элементы:

       1. Папка `.NET FRAMEWORK 3.5`, в которой содержится конфигурация пользовательской установки для установки более ранней версии .NET Framework, которая может потребоваться для пользовательских компонентов на каждом узле вашей Azure SSIS IR.

       1. Папка `BCP`, которая содержит файл пользовательской установки для установки служебных программ командной строки SQL Server (`MsSqlCmdLnUtils.msi`), включая программу массового копирования (`bcp`), на каждом узле Azure SSIS IR.

       1. Папка `EXCEL`, где содержится файл пользовательской установки для установки сборок с открытым исходным кодом (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` и `ExcelDataReader.dll`) на каждом узле Azure SSIS IR.

       1. Папка `ORACLE ENTERPRISE`, которая содержит пользовательский скрипт установки (`main.cmd`) и файл конфигурации автоматической установки (`client.rsp`), позволяющие установить соединители Oracle и драйвер OCI на каждом узле Azure-SSIS IR Enterprise Edition. Эта установка предоставляет возможность использовать диспетчер подключений, источник и назначение Oracle. Прежде всего скачайте соединители Microsoft версии 5.0 для Oracle (`AttunitySSISOraAdaptersSetup.msi` и `AttunitySSISOraAdaptersSetup64.msi`) из [центра загрузки Майкрософт](https://www.microsoft.com/en-us/download/details.aspx?id=55179) и последнюю версию клиента Oracle — например, версию `winx64_12102_client.zip` с сайта [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), и отправьте их в контейнер вместе с `main.cmd` и `client.rsp`. Если TNS используется для подключения к Oracle, необходимо загрузить файл `tnsnames.ora`, изменить его и отправить в контейнер, чтобы его можно было скопировать в папку установки Oracle во время установки.

       1. Папка `ORACLE STANDARD ADO.NET`, где содержится скрипт пользовательской установки (`main.cmd`) для установки драйвера Oracle ODP.NET на каждом узле Azure SSIS IR. Эта установка дает возможность использовать диспетчер подключений, источник и назначение ADO.NET. Сначала загрузите последнюю версию драйвера Oracle ODP.NET (например, `ODP.NET_Managed_ODAC122cR1.zip`) из [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) и затем передайте его вместе с `main.cmd` в свой контейнер.
       
       1. Папка `ORACLE STANDARD ODBC`, где содержится сценарий пользовательской установки (`main.cmd`) для установки драйвера Oracle ODBC и настройки имени источника данных на каждом узле Azure-SSIS IR. Эта установка позволяет использовать диспетчер подключений ODBC, источник и назначение или диспетчер подключений Power Query и источник с источником данных ODBC для подключения к серверу Oracle. Сначала загрузите последнюю версию Oracle Instant Client (базовый пакет или базовый пакет Lite) и пакет ODBC, например 64-битные пакеты можно скачать [здесь](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (базовый пакет — `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, базовый пакет — `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, пакет ODBC — `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`), а 32-битные пакеты [здесь](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (базовый пакет — `instantclient-basic-nt-18.3.0.0.0dbru.zip`, базовый пакет Lite — `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, пакет ODBC — `instantclient-odbc-nt-18.3.0.0.0dbru.zip`), а затем отправьте их вместе с файлом `main.cmd` в свой контейнер.

       1. Папка `SAP BW`, которая содержит скрипт пользовательской установки (`main.cmd`), чтобы установить сборку соединителя SAP .NET (`librfc32.dll`) на каждом узле среды выполнения интеграции Azure SSIS IR Enterprise Edition. Эта установка предоставляет возможность использовать диспетчер подключений, источник и назначение SAP BW. Сначала передайте в контейнер 64- или 32-разрядную версию `librfc32.dll` из папки установки SAP вместе с `main.cmd`. Затем скрипт скопирует сборку SAP в папку `%windir%\SysWow64` или `%windir%\System32` во время установки.

       1. Папка `STORAGE`, где содержится файл пользовательской установки для установки Azure PowerShell на каждом узле Azure SSIS IR. Эта установка позволяет развертывать и запускать пакеты SSIS, которые запускают [скрипты PowerShell для работы с учетной записью хранения Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Скопируйте `main.cmd`, пример `AzurePowerShell.msi` (или установите последнюю версию) и `storage.ps1` в контейнер. Используйте PowerShell.dtsx в качестве шаблона для пакетов. Шаблон пакета объединяет [задачу загрузки большого двоичного объекта Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), которая загружает `storage.ps1` в качестве изменяемого скрипта PowerShell, и [задачу обработки выполнения](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), которая выполняет скрипт на каждом узле.

       1. Папка `TERADATA`, которая содержит скрипт пользовательской установки (`main.cmd`), связанный файл (`install.cmd`) и пакеты установщика (`.msi`). Эти файлы устанавливают соединители Teradata, API TPT и драйвер ODBC на каждый узел среды выполнения интеграции Azure SSIS IR Enterprise Edition. Эта установка предоставляет возможность использовать диспетчер подключений, источник и назначение Teradata. Сначала загрузите ZIP-файл со средствами и служебными программами Teradata 15.х (например, `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) из [Teradata](http://partnerintelligence.teradata.com), а затем отправьте их вместе с файлами `.cmd` и `.msi` в свой контейнер.

    ![Папки в папке сценариев пользователя](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    д. Чтобы попробовать эти примеры пользовательской установки, скопируйте, а затем вставьте содержимое из выбранной папки в свой контейнер. При подготовке или перенастройке Azure SSIS IR с помощью PowerShell выполните командлет `Set-AzureRmDataFactoryV2IntegrationRuntime` с URI SAS контейнера в качестве значения нового параметра `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Дополнительная информация

-   [Выпуск Enterprise Edition среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Разработка платных или лицензионных пользовательских компонентов для среды выполнения интеграции Azure SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
