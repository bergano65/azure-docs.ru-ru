---
title: Руководство. Начало работы с Always Encrypted и безопасными анклавами в Базе данных SQL Azure
description: Из этого руководства вы узнаете, как создать базовую среду Always Encrypted с безопасными анклавами в Базе данных SQL Azure, а также как шифровать данные на месте и выдавать полнофункциональные конфиденциальные запросы к зашифрованным столбцам с помощью SQL Server Management Studio (SSMS).
keywords: шифрование данных, шифрование SQL, шифрование базы данных, конфиденциальные данные, Always Encrypted, безопасные анклавы, SGX, аттестация
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 94923b13181290a290f13339da5b05f6fdddff38
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252163"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>Руководство. Начало работы с Always Encrypted и безопасными анклавами в Базе данных SQL Azure

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted с безопасными анклавами для Базы данных SQL Azure в настоящее время предоставляется в **общедоступной предварительной версии**.

Из этого руководства вы узнаете, как приступить к работе с [Always Encrypted с безопасными анклавами](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) в Базе данных SQL Azure. Буду рассмотрены следующие темы.

> [!div class="checklist"]
> - Создание среды для тестирования и оценки Always Encrypted с безопасными анклавами.
> - Шифрование данных на месте и выдача полнофункциональных конфиденциальных запросов к зашифрованным столбцам с помощью SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством требуются Azure PowerShell и [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="powershell-requirements"></a>Требования для PowerShell

Сведения о том, как установить и запустить Azure PowerShell, см. в статье [Общие сведения об Azure PowerShell](https://docs.microsoft.com/powershell/azure). 

Минимально необходимые версии модулей Az для поддержки операций с аттестацией:

- Az 4.5.0;
- Az.Accounts 1.9.2;
- Az.Attestation 0.1.8.

Выполните приведенную ниже команду, чтобы проверить версии всех установленных модулей Az:

```powershell
Get-InstalledModule
```

Если какие-то версии не соответствуют минимальным требованиям, выполните команду `Update-Module`.

Для коллекции PowerShell не рекомендуется использовать протокол TLS (Transport Layer Security) версий 1.0 и 1.1. Используйте TLS версии 1.2 или более поздней. Если используется TLS версии ниже 1.2, могут возникнуть следующие ошибки:

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

Чтобы продолжить взаимодействие с коллекцией PowerShell Gallery, выполните следующие команды перед командами Install-Module.

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>Требования для SSMS

Сведения о том, как скачать SSMS, см. на странице [Скачивание SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

Требуемая минимальная версия SSMS — 18.8.


## <a name="step-1-create-a-server-and-a-dc-series-database"></a>Шаг 1. Создание сервера и базы данных серии DC

 На этом шаге вы создадите логический сервер Базы данных SQL Azure и новую базу данных, используя аппаратную конфигурацию серии DC. Always Encrypted с безопасными анклавами в Базе данных SQL Azure использует анклавы Intel SGX, которые поддерживаются в аппаратной конфигурации серии DC. Подробные сведения см. в разделе о [серии DC](service-tiers-vcore.md#dc-series).

1. Откройте консоль PowerShell и войдите в Azure. При необходимости [перейдите в подписку](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps), используемую для работы с этим руководством.

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = <your subscription ID>
  Set-AzContext -Subscription $serverSubscriptionId
  ```

2. Создайте группу ресурсов для сервера базы данных. 

  ```powershell
  $serverResourceGroupName = "<server resource group name>"
  $serverLocation = "<Azure region that supports DC-series in SQL Database>"
  New-AzResourceGroup -Name $serverResourceGroupName -Location $serverLocation 
  ```

  > [!IMPORTANT]
  > Вам потребуется создать группу ресурсов в регионе, который поддерживает аппаратную конфигурацию серии DC. Список поддерживаемых в настоящее время регионов см. в разделе со сведениями о [доступности серии DC](service-tiers-vcore.md#dc-series-1).

3. Создайте сервер базы данных. При появлении запроса введите имя администратора сервера и пароль.

  ```powershell
  $serverName = "<server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -Location $serverLocation
  ```

4. Создайте правило брандмауэра сервера, разрешающее доступ из указанного диапазона IP-адресов.
  
  ```powershell
  # The ip address range that you want to allow to access your server
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

5. Назначьте серверу удостоверение управляемой системы. Оно потребуется позже, чтобы предоставить серверу доступ к Аттестации Microsoft Azure.

  ```powershell
  Set-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -AssignIdentity 
  ```

6. Получите идентификатор объекта удостоверения, назначенного серверу. Сохраните итоговый идентификатор объекта. Идентификатор потребуется при работе со следующим разделом.

  > [!NOTE]
  > Для появления только что назначенного удостоверения управляемой системы в Azure Active Directory может потребоваться несколько секунд. Если приведенный ниже скрипт возвращает пустой результат, выполните его повторно.

  ```PowerShell
  $server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
  $serverObjectId = $server.Identity.PrincipalId
  $serverObjectId
  ```

7. Создайте базу данных серии DC.

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $serverResourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $edition -Vcore $vCore -ComputeGeneration $generation
  ```

## <a name="step-2-configure-an-attestation-provider"></a>Шаг 2. Настройка поставщика аттестации

На этом шаге вы создадите и настроите поставщик аттестации в Аттестации Microsoft Azure. Это необходимо для аттестации безопасного анклава на сервере базы данных.

1. Скопируйте приведенную ниже политику аттестации и сохраните ее в текстовом файле (TXT). Дополнительные сведения о приведенной ниже политике см. в разделе [Создание и настройка поставщика аттестации](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. Импортируйте нужные версии `Az.Accounts` и `Az.Attestation`.  

  ```powershell
  Import-Module "Az.Accounts" -MinimumVersion "1.9.2"
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```

3. Создайте группу ресурсов для поставщика аттестации.

  ```powershell
  $attestationLocation = $serverLocation
  $attestationResourceGroupName = "<attestation provider resource group name>"
  New-AzResourceGroup -Name $attestationResourceGroupName -Location $location  
  ```

4. Создайте поставщика аттестации. 

  ```powershell
  $attestationProviderName = "<attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Location $attestationLocation
  ```

5. Настройте политику аттестации.
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Tee $teeType -Policy $policy -PolicyFormat  $policyFormat
  ```

6. Предоставьте логическому серверу Azure SQL доступ к своему поставщику аттестации. На этом шаге мы используем идентификатор объекта удостоверения управляемой службы, назначенного серверу ранее.

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName  
  ```

7. Получите URL-адрес аттестации.

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host "Your attestation URL is: " $attestationUrl 
  ```

8.  Сохраните полученный URL-адрес аттестации, указывающий на политику аттестации, которая была настроена для анклава SGX. Он понадобится вам позднее. URL-адрес аттестации всегда должен выглядеть так: `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`.

## <a name="step-3-populate-your-database"></a>Шаг 3. Заполнение базы данных

На этом шаге вы создадите таблицу и заполните ее данными, которые позже будут зашифрованы и запрошены.

1. Откройте среду SSMS и подключитесь к базе данных **ContosoHR** на логическом сервере Azure SQL, созданном **без** включения Always Encrypted для подключения к базе данных.
    1. В диалоговом окне **Подключение к серверу** укажите имя сервера (например, *myserver123.database.windows.net*) и введите имя пользователя и пароль, настроенные ранее.
    2. Щелкните **Параметры >>** и откройте вкладку **Свойства подключения**. Обязательно выберите базу данных **ContosoHR** (а не базу данных master по умолчанию). 
    3. Выберите вкладку **Always Encrypted**.
    4. Убедитесь, что флажок **Включить Always Encrypted (шифрование столбцов)** **не** установлен.

        ![Подключение без Always Encrypted](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. Щелкните **Подключить**.

2. Создайте новую таблицу с именем **Employees**.

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. Добавьте несколько записей о сотрудниках в таблицу **Employees**.

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>Шаг 4. Подготовка ключей с поддержкой анклава

На этом шаге вы создадите главный ключ столбца и ключ шифрования столбца, который разрешает вычисления анклава.

1. С помощью экземпляра SSMS из предыдущего шага в **обозревателе объектов** разверните базу данных и перейдите к пункту **Безопасность** > **Ключи Always Encrypted**.
1. Подготовьте новый главный ключ столбца с поддержкой анклава:
    1. Щелкните правой кнопкой мыши **Ключи Always Encrypted** и выберите **Создать главный ключ столбца...** .
    2. Выберите имя главного ключа столбца: **CMK1**.
    3. Убедитесь, что выбрано значение **Хранилище сертификатов Windows (текущий пользователь или локальный компьютер)** или **Azure Key Vault**.
    4. Выберите **Разрешить вычисления анклава**.
    5. Если вы выбрали Azure Key Vault, войдите в Azure и выберите хранилище ключей. Дополнительные сведения о создании хранилища ключей для Always Encrypted см. в статье [Управление хранилищами ключей на портале Azure](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal).
    6. Выберите сертификат или ключ Azure Key Vault, если он существует, или нажмите кнопку **Создать сертификат**, чтобы создать новый сертификат.
    7. Щелкните **ОК**.

        ![Разрешение вычислений анклава](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. Создайте ключ шифрования столбцов с поддержкой анклава:

    1. Щелкните правой кнопкой мыши **Ключи Always Encrypted** и выберите **Создать ключ шифрования столбца**.
    2. Введите имя для нового ключа шифрования столбцов: **CEK1**.
    3. В раскрывающемся списке **Главный ключ столбца** выберите ключ, созданный на предыдущих шагах.
    4. Щелкните **ОК**.

## <a name="step-5-encrypt-some-columns-in-place"></a>Шаг 5. Шифрование некоторых столбцов на месте

На этом шаге вы выполните шифрование данных, хранящихся в столбцах **SSN** и **Salary** в анклаве на стороне сервера, а затем протестируете запрос SELECT к данным.

1. Откройте новый экземпляр SSMS и подключитесь к базе данных **без** включенной функции Always Encrypted для подключения к базе данных.
    1. Создайте новый экземпляр SSMS.
    2. В диалоговом окне **Соединение с сервером** укажите имя сервера, выберите метод аутентификации и введите учетные данные.
    3. Щелкните **Параметры >>** и откройте вкладку **Свойства подключения**. Обязательно выберите базу данных **ContosoHR** (а не базу данных master по умолчанию). 
    4. Выберите вкладку **Always Encrypted**.
    5. Убедитесь, что флажок **Включить Always Encrypted (шифрование столбцов)** установлен.
    6. Укажите URL-адрес аттестации анклава, полученный при выполнении инструкций на [шаге 2 "Настройка поставщика аттестации"](#step-2-configure-an-attestation-provider). См. снимок экрана ниже.

        ![Подключение с аттестацией](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. Щелкните **Подключить**.
    8. Если отобразится запрос на включение параметризации для запросов Always Encrypted, нажмите кнопку **Включить**.



1. Используя тот же экземпляр SSMS (с включенной функцией Always Encrypted), откройте новое окно запроса и выполните шифрование столбцов **SSN** и **Salary** с помощью приведенных ниже инструкций.

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > Обратите внимание на инструкцию ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE для очистки кэша планов запросов в базе данных в приведенном выше скрипте. Изменив таблицу, необходимо очистить планы всех пакетов и хранимых процедур, которые обращаются к таблице, чтобы обновить сведения о параметрах шифрования. 

1. Чтобы убедиться в том, что столбцы **SSN** и **Salary** зашифрованы, откройте новое окно запроса в экземпляре SSMS **без** включенной функции Always Encrypted для подключения к базе данных и выполните приведенную ниже инструкцию. Окно запроса должно возвратить зашифрованные значения столбцов **SSN** и **Salary**. Если вы выполните тот же запрос с помощью экземпляра SSMS с включенной функцией Always Encrypted, данные будут расшифрованы.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>Шаг 6. Выполнение полнофункциональных запросов к зашифрованным столбцам

Вы можете выполнить полнофункциональные запросы к зашифрованным столбцам. Некоторая обработка запросов будет выполняться в анклаве на стороне сервера. 

1. В экземпляре SSMS **с** включенной функцией Always Encrypted убедитесь, что включена параметризация для Always Encrypted.
    1. Выберите пункт **Инструменты** в главном меню SSMS.
    2. Выберите **Параметры**.
    3. Выберите **Выполнение запроса** > **SQL Server** > **Дополнительно**.
    4. Убедитесь, что установлен флажок **Включить параметризацию для Always Encrypted**.
    5. Щелкните **ОК**.
2. Откройте новое окно запроса, а затем вставьте и выполните приведенный ниже запрос. Запрос должен возвратить соответствующие заданным условиям поиска значения и строки в виде открытого текста.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Попробуйте выполнить тот же запрос еще раз в экземпляре SSMS с отключенной функцией Always Encrypted. Произойдет сбой.
 
## <a name="next-steps"></a>Следующие шаги

После завершения работы с этим учебником вы можете обратиться к одному из следующих учебников:
- [Руководство. Разработка приложения .NET с помощью Always Encrypted с безопасными анклавами](https://docs.microsoft.com/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [Руководство. Разработка приложения .NET Framework с помощью Always Encrypted с безопасными анклавами](https://docs.microsoft.com/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [Руководство. Создание и использование индексов в столбцах с поддержкой анклава с помощью случайного шифрования](https://docs.microsoft.com/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>См. также:

- [Настройка и использование Always Encrypted с безопасными анклавами](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)