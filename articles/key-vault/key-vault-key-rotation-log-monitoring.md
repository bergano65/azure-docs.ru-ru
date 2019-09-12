---
title: Настройка полной смены ключей и аудита в хранилище ключей Azure | Документация Майкрософт
description: Это руководство поможет вам настроить смену ключей и отслеживать журналы хранилища ключей.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 1f60ce3a23882a48e6008b76c0eedcab99e013b2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883449"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Настройка смены ключей и аудита в Azure Key Vault

## <a name="introduction"></a>Введение

Получив хранилище ключей, вы можете использовать его для хранения ключей и секретов. В приложениях больше не нужно сохранять ключи и секреты. При необходимости их можно получить из хранилища. Хранилище ключей позволяет обновлять ключи и секреты, не влияя на поведение приложения, что открывает широкий спектр возможностей для управления ключами и секретами.

>[!IMPORTANT]
> Примеры в этой статье приведены исключительно для демонстрации. Они не предназначены для использования в рабочей среде. 

В этой статье описаны следующие операции.

- Пример использования Azure Key Vault для хранения секрета. В этой статье хранится секретный ключ учетной записи хранения Azure, к которому обращается приложение. 
- Как реализовать запланированный поворот этого ключа учетной записи хранения.
- Как отслеживать журналы аудита хранилища ключей и создавать оповещения при возникновении непредвиденных запросов.

> [!NOTE]
> В этой статье подробно описывается начальная настройка хранилища ключей. Дополнительные сведения см. в статье [Что такое хранилище ключей Azure?](key-vault-overview.md) Инструкции по использованию кросс-платформенных интерфейсов командной строки см. [в разделе управление Key Vault с помощью Azure CLI](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Настройка хранилища ключей

Чтобы приложение могло получить секрет из хранилища ключей, секрет нужно создать и передать в хранилище.

Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:

```powershell
Connect-AzAccount
```

Во всплывающем окне браузера введите имя пользователя и пароль для своей учетной записи Azure. PowerShell получит все подписки, связанные с этой учетной записью. По умолчанию PowerShell будет использовать первую из них.

Если у вас есть несколько подписок, вы можете указать ту, с помощью которой вы создали хранилище ключей Azure. Чтобы просмотреть подписки для своей учетной записи, введите следующую команду:

```powershell
Get-AzSubscription
```

Чтобы указать подписку, связанную с хранилищем ключей, которое вы будете регистрировать, введите:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Так как в этой статье в качестве секрета используется ключ учетной записи хранения, вам необходимо получить его.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

После получения секрета (в данном случае — ключа учетной записи хранения) необходимо преобразовать этот ключ в защищенную строку, а затем создать секрет с этим значением в хранилище ключей.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

После этого получите URI секрета, который вы создали. Этот URI потребуется позже для вызова хранилища ключей и получения секрета. Выполните следующую команду PowerShell и запишите значение идентификатора, которое является URI секрета:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Настройка приложения

Теперь, когда у вас есть секрет, можно использовать код для его извлечения и использования после выполнения нескольких дополнительных действий.

Сначала необходимо зарегистрировать приложение в Azure Active Directory. Затем укажите Key Vault сведения о приложении, чтобы они могли разрешать запросы из приложения.

> [!NOTE]
> Приложение и хранилище ключей необходимо создать в одном и том же клиенте Azure Active Directory.

1. Откройте **Azure Active Directory**.
2. Щелкните **Регистрация приложений**. 
3. Выберите **Регистрация нового приложения** , чтобы добавить приложение в Azure Active Directory.

    ![Вкладка "Приложения" в Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. В разделе **создать**оставьте тип приложения **веб-приложение или API** и присвойте приложению имя. Предоставьте приложению URL- **адрес для входа**. Этот URL-адрес может быть любым для этой демонстрации.

    ![Создание регистрации приложения](./media/keyvault-keyrotation/create-app.png)

5. После добавления приложения в Azure Active Directory открывается страница приложения. Выберите **Параметры**, а затем щелкните **свойства**. Скопируйте значение **ИД приложения**. Он понадобится вам в последующих шагах.

Затем создайте ключ для приложения, чтобы он мог взаимодействовать с Azure Active Directory. Чтобы создать ключ, выберите **раздел "ключи** " в разделе " **Параметры**". Запишите созданный ключ для приложения Azure Active Directory. Они потребуются вам на следующем шаге. После выхода из этого раздела ключ будет недоступен. 

![Ключи приложения Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Прежде чем устанавливать вызовы из приложения в хранилище ключей, необходимо сообщить хранилищу ключей о приложении и его разрешениях. Следующая команда использует имя хранилища и идентификатор приложения из приложения Azure Active Directory, чтобы предоставить приложению доступ к вашему хранилищу ключей.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Теперь вы готовы приступить к созданию вызовов приложений. В приложении необходимо установить пакеты NuGet, необходимые для взаимодействия с Azure Key Vault и Azure Active Directory. Для этого введите приведенные ниже команды в консоли диспетчера пакетов Visual Studio. На момент написания этой статьи текущая версия Azure Active Directory пакета — 3.10.305231913, поэтому при необходимости подтвердите последнюю версию и обновите ее.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

В коде приложения создайте класс, который будет использоваться при проверке подлинности в Azure Active Directory. В этом примере ему присвоено имя **Utils**. Добавьте следующую инструкцию `using` :

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Добавьте приведенный ниже метод для получения маркера JWT из Azure Active Directory. Для удобства обслуживания может потребоваться переместить жестко запрограммированные строковые значения в конфигурацию веб-приложения или приложений.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Добавьте необходимый код для обращения к хранилищу ключей и получения значения секрета. Сначала необходимо добавить следующую `using` инструкцию:

```csharp
using Microsoft.Azure.KeyVault;
```

Добавьте метод, который будет обращаться к хранилищу ключей и получать секрет. Добавьте в этот метод URI секрета, сохраненный на предыдущем шаге. Обратите внимание на использование метода CoClass из созданного ранее класса **utils** .

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Запустив приложение, вы можете выполнить проверку подлинности в Azure Active Directory и извлечь значение секрета из хранилища ключей Azure.

## <a name="key-rotation-using-azure-automation"></a>Смена ключей с помощью службы автоматизации Azure

> [!IMPORTANT]
> Модули Runbook службы автоматизации Azure по-прежнему нуждаются в `AzureRM` использовании модуля.

Теперь вы можете настроить стратегию ротации для значений, которые вы храните в качестве Key Vault секретов. Секреты можно поворачивать несколькими способами:

- Как часть ручного процесса
- Программно с помощью вызовов API
- С помощью скрипта службы автоматизации Azure

В рамках этой статьи вы будете использовать PowerShell вместе со службой автоматизации Azure для изменения ключа доступа учетной записи хранения Azure. Затем вы обновите секрет хранилища ключей с помощью нового ключа.

Чтобы разрешить службе автоматизации Azure задавать секретные значения в хранилище ключей, необходимо получить идентификатор клиента для подключения с именем **AzureRunAsConnection**. Это подключение было создано при установке экземпляра службы автоматизации Azure. Чтобы найти этот идентификатор, выберите **активы** из экземпляра службы автоматизации Azure. В нем выберите **подключения**, а затем выберите субъект-службу **AzureRunAsConnection** . Запишите значение **applicationId** .

![Идентификатор клиента службы автоматизации Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

В окне **ресурсы**выберите **модули**. Выберите **коллекция**, а затем найдите и импортируйте обновленные версии каждого из следующих модулей:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> На момент написания этой статьи требовалось обновить только перечисленные выше модули, чтобы запустить следующий скрипт. Если задание службы автоматизации завершается сбоем, убедитесь, что импортированы все необходимые модули и их зависимости.

После получения идентификатора приложения для подключения службы автоматизации Azure необходимо сообщить хранилищу ключей, что это приложение имеет разрешение на обновление секретов в вашем хранилище. Используйте следующую команду PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Затем выберите **модули Runbook** в экземпляре службы автоматизации Azure, а затем щелкните **Добавить Runbook**. Выберите **Быстрое создание**. Присвойте имя модулю Runbook и выберите **PowerShell** в качестве типа Runbook. Можно добавить описание. Наконец, выберите **Создать**.

![Создание модуля Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

В области редактора нового модуля Runbook вставьте следующий сценарий PowerShell.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

В области редактора выберите **Тестовая область** для тестирования скрипта. После выполнения сценария без ошибок можно выбрать вариант **опубликовать**, после чего можно применить расписание для модуля Runbook в области конфигурации Runbook.

## <a name="key-vault-auditing-pipeline"></a>Конвейер аудита для хранилища ключей

При настройке хранилища ключей вы можете включить аудит, чтобы собирать журналы запросов на доступ к этому хранилищу. Эти журналы хранятся в назначенной учетной записи хранения Azure и могут быть извлечены, отслеживаться и анализироваться. В следующем сценарии используются функции Azure, Azure Logic Apps и журналы аудита хранилища ключей для создания конвейера, который отправляет сообщение электронной почты, когда приложение, не совпадающее с ИДЕНТИФИКАТОРом веб-приложения, извлекает секреты из хранилища.

Сначала нужно включить ведение журнала в хранилище ключей. Используйте следующие команды PowerShell. (Подробные сведения, приведенные в этой статье, см. в разделе [Регистрация в хранилище ключей](key-vault-logging.md)).

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

После включения ведения журнала журналы аудита начинают храниться в указанной учетной записи хранения. В этих журналах содержатся сведения о том, кто, как и когда осуществлял доступ к хранилищам ключей.

> [!NOTE]
> Регистрируемые в журналах сведения становятся доступны через 10 минут после выполнения операции с хранилищем ключей. Как правило, оно будет доступно раньше.

Теперь необходимо [создать очередь служебной шины Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). В эту очередь помещаются журналы аудита хранилища ключей. Когда сообщения журнала аудита находятся в очереди, приложение логики берет их и взаимодействует с ними. Создайте экземпляр служебной шины, выполнив следующие действия.

1. Создайте пространство имен служебной шины (если у вас уже есть такой объект, перейдите к шагу 2).
2. Перейдите к экземпляру служебной шины в портал Azure и выберите пространство имен, в котором нужно создать очередь.
3. Выберите **создать ресурс** > **Корпоративная интеграция** > **служебная шина**, а затем введите необходимые сведения.
4. Найдите сведения о подключении к служебной шине, выбрав пространство имен и выбрав **сведения о подключении**. Эти сведения понадобятся вам в следующем разделе.

Затем [Создайте функцию Azure](../azure-functions/functions-create-first-azure-function.md) , чтобы опросить журналы хранилища ключей в учетной записи хранения и выбрать новые события. Эта функция будет запускаться по расписанию.

Чтобы создать приложение-функцию Azure, выберите **создать ресурс**, выполните поиск по **приложение-функция**в Marketplace, а затем щелкните **создать**. Можно использовать для этого действующий план размещения или создать новый. Также можно выбрать динамическое размещение. Дополнительные сведения о вариантах размещения для функций Azure см. [в статье масштабирование функций Azure](../azure-functions/functions-scale.md).

После создания приложения-функции Azure перейдите к нему и выберите сценарий таймера и **C\#**  для языка. Затем выберите **создать эту функцию**.

![Начальная колонка функций Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

На вкладке **Разработка** замените код run.csx приведенным ниже.

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Измените переменные в приведенном выше коде так, чтобы они указывали на учетную запись хранения, в которую записываются журналы хранилища ключей, на созданный ранее экземпляр служебной шины и на конкретный путь к журналам хранилища ключей.

Функция получает из учетной записи хранения, в которою записываются журналы хранилища ключей, последний файл журнала, извлекает из него последние события и передает их в очередь служебной шины. 

Поскольку один файл может иметь несколько событий, следует создать файл Sync. txt, который также будет использоваться функцией для определения метки времени последнего события, которое было выбрано. Использование этого файла гарантирует, что одно и то же событие не будет отправляться несколько раз. 

Файл Sync. txt содержит метку времени для последнего произошедшего события. После загрузки журналов их необходимо отсортировать в соответствии с временными метками, чтобы убедиться, что они упорядочены правильно.

Для этой функции мы будем ссылаться на пару дополнительных библиотек, которые недоступны в функциях Azure. Чтобы включить эти библиотеки, нам нужны функции Azure для их извлечения с помощью NuGet. В поле **код** выберите **Просмотреть файлы**.

![Параметр «Просмотр файлов»](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Добавьте файл с именем Project. JSON со следующим содержимым:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

После того как вы наберете **сохранить**, функции Azure будут скачивать необходимые двоичные файлы.

Перейдите на вкладку **Интеграция** и введите для параметра "Таймер" понятное имя, которое будет использоваться в функции. В приведенном выше коде функция ждет, что таймер будет называться *myTimer*. Укажите [выражение cron](../app-service/webjobs-create.md#CreateScheduledCRON) для таймера следующим образом: `0 * * * * *`. Это выражение приведет к запуску функции один раз в минуту.

На той же вкладке **интеграции** добавьте входные данные типа **хранилище BLOB-объектов Azure**. Этот ввод указывает на файл Sync. txt, содержащий метку времени последнего события, просмотренного функцией. Эти входные данные будут доступны в функции с помощью имени параметра. В приведенном выше коде входные данные хранилища BLOB-объектов Azure предполагают, что имя параметра должно быть *inputBlob*. Выберите учетную запись хранения, в которой будет расположен файл Sync. txt (это может быть та же или другая учетная запись хранения). В поле путь укажите путь к файлу в формате `{container-name}/path/to/sync.txt`.

Добавьте выходные данные типа **хранилище BLOB-объектов Azure**. Эти выходные данные будут указывать на файл Sync. txt, определенный во входных данных. Этот выход используется функцией для записи метки времени последнего события, рассмотренного в. В приведенном выше коде ожидается, что этому параметру будет присвоено имя *outputBlob*.

Теперь функция готова. Перейдите на вкладку **Разработка** и сохраните код. Проверьте окно вывода на наличие ошибок компиляции и при необходимости исправьте их. Если код компилируется, теперь код должен проверять журналы хранилища ключей каждую минуту и отправлять новые события в определенную очередь служебной шины. При каждом запуске функции в окне журнала будут отображаться данные журнала.

### <a name="azure-logic-app"></a>Приложение логики Azure

Далее необходимо создать приложение логики Azure, которое выбирает события, которые функция отправляет в очередь служебной шины, анализирует содержимое и отправляет сообщение электронной почты на основе соответствующего условия.

[Создайте приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md) , выбрав создать**Интеграция** >  **с ресурсом** > **приложение логики**.

После создания приложения логики перейдите к нему и выберите **изменить**. В редакторе приложения логики выберите **очередь служебной шины** и введите учетные данные служебной шины, чтобы подключить ее к очереди.

![Служебная шина приложения логики Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Выберите **Добавить условие**. В условии перейдите в расширенный редактор и введите следующий код. Замените *APP_ID* ФАКТИЧЕСКИм идентификатором приложения вашего веб-приложения:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Это выражение фактически возвращает **значение false** , если *идентификатор AppID* из входящего события (который является телом сообщения служебной шины) не является *AppID* приложения.

Теперь создайте действие в **случае, если нет, ничего не делать**.

![Azure Logic Apps выберите действие](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Для этого действия выберите **Office 365 — отправить сообщение электронной почты**. Заполните поля, чтобы создать электронное письмо, которое будет отправляться, когда определенное условие возвращает значение **false**. Если у вас нет Office 365, найдите альтернативы, чтобы получить те же результаты.

Теперь у вас есть сквозной конвейер, который ищет новые журналы аудита ключей в хранилище, один раз в минуту. Он отправляет новые журналы, которые он находит, в очередь служебной шины. Приложение логики запускается каждый раз при появлении в очереди нового сообщения. Если *AppID* в событии не совпадает с идентификатором приложения вызывающего приложения, отправляется сообщение электронной почты.
