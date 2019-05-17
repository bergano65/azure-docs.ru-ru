---
title: Настройка полной смены ключей и аудита в хранилище ключей Azure | Документация Майкрософт
description: Используйте в этом практическом руководстве помогут вам настроить смену ключей и отслеживать журналы хранилища ключей.
services: key-vault
author: barclayn
manager: barbkess
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 20a170963ff4a8ff9cb69d3397e66e12c1047d16
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561192"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Настройка смены ключей и аудита в Azure Key Vault

## <a name="introduction"></a>Общие сведения

Получив хранилище ключей, вы можете использовать его для хранения ключей и секретов. В приложениях больше не нужно сохранять ключи и секреты. При необходимости их можно получить из хранилища. Хранилище ключей позволяет обновлять ключи и секреты, не затрагивая работу приложения, который открывает разнообразные возможности для управления секретами и ключ.

>[!IMPORTANT]
> Примеры в этой статье приведены исключительно для демонстрации. Они не предназначены для использования в рабочей среде. 

В этой статье описаны следующие операции.

- Пример использования Azure Key Vault для хранения секрета. В этой статье секретный код, хранимый является ключ учетной записи хранения Azure, обращается приложение. 
- Инструкции по реализации плановой смены этого ключа учетной записи хранения.
- Мониторинг ключ хранилища журналов аудита и настройку оповещений о непредвиденных запросах.

> [!NOTE]
> В этой статье не приведено подробное описание начальной настройки хранилища ключей. Дополнительные сведения см. в статье [Что такое хранилище ключей Azure?](key-vault-overview.md) Инструкции кросс платформенного интерфейса командной строки, см. в разделе [Управление хранилищем ключей с помощью Azure CLI](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Настройка хранилища ключей

Чтобы приложение могло получить секрет из хранилища ключей, секрет нужно создать и передать в хранилище.

Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:

```powershell
Connect-AzAccount
```

В всплывающем окне браузера введите имя пользователя и пароль для учетной записи Azure. PowerShell получит все подписки, связанные с этой учетной записью. По умолчанию PowerShell будет использовать первую из них.

Если у вас есть несколько подписок, вы можете указать ту, с помощью которой вы создали хранилище ключей Azure. Чтобы просмотреть подписки для своей учетной записи, введите следующую команду:

```powershell
Get-AzSubscription
```

Чтобы указать подписку, связанную с хранилищем ключей, с которого будут регистрироваться, введите следующую команду:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Так как в этой статье в качестве секрета используется ключ учетной записи хранения, вам необходимо получить его.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Полученный секрет (в данном случае ключ учетной записи хранения), необходимо преобразовать этот ключ в защищенную строку и затем создайте секрет с этим значением в хранилище ключей.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

После этого получите URI секрета, который вы создали. Вам потребуется этот URI в дальнейшем для вызова хранилища ключей и получать секрет. Выполните следующую команду PowerShell и запишите значение идентификатора, которое используется в URI секрета:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Настройка приложения

Теперь, когда у вас есть сохранили секрет, можно использовать код для извлечения и использовать его, выполнив несколько дополнительных действий.

Во-первых необходимо зарегистрировать приложение с Azure Active Directory. Затем укажите хранилище ключей информации о приложениях, чтобы он сможет принимать запросы от приложения.

> [!NOTE]
> Приложение и хранилище ключей необходимо создать в одном и том же клиенте Azure Active Directory.

1. Откройте **Azure Active Directory**.
2. Щелкните **Регистрация приложений**. 
3. Выберите **Регистрация нового приложения** Добавление приложения в Azure Active Directory.

    ![Вкладка "Приложения" в Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. В разделе **создать**, оставьте тип **веб-приложение или API** и введите имя приложения. Присвойте приложению **URL-адрес входа**. Этот URL-адрес может быть любым способом для этого демонстрационного проекта.

    ![Создание регистрации приложения](./media/keyvault-keyrotation/create-app.png)

5. После добавления приложения в Azure Active Directory, откроется страница приложения. Выберите **параметры**, а затем выберите **свойства**. Скопируйте значение **ИД приложения**. Он понадобится на следующих шагах.

Далее создайте ключ приложения, чтобы оно могло взаимодействовать с Azure Active Directory. Чтобы создать ключ, выберите **ключи** под **параметры**. Запишите созданный ключ для приложения Azure Active Directory. Они потребуются вам на следующем шаге. Ключ не будет доступно после выхода из этого раздела. 

![Ключи приложения Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Перед установкой все вызовы из приложения в хранилище ключей, хранилище ключей необходимо сообщить о приложении и его разрешения. Следующая команда использует имя хранилища и идентификатор приложения из приложения Azure Active Directory для предоставления приложения **получить** доступ к хранилищу ключей.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Теперь вы готовы приступить к созданию вызовов из приложения. В приложении необходимо установить пакеты NuGet, необходимые для взаимодействия с хранилищем ключей Azure и Azure Active Directory. Для этого введите приведенные ниже команды в консоли диспетчера пакетов Visual Studio. На момент написания этой статьи, текущая версия пакета Azure Active Directory — 3.10.305231913, поэтому последнюю версию и при необходимости обновите.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

В коде приложения создайте класс, который будет использоваться при проверке подлинности в Azure Active Directory. В этом примере ему присвоено имя **Utils**. Добавьте следующую инструкцию `using` :

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Добавьте приведенный ниже метод для получения маркера JWT из Azure Active Directory. Для удобства обслуживания может потребоваться переместить жестко заданные строковые значения в конфигурацию или веб-приложении.

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

Добавьте необходимый код для обращения к хранилищу ключей и получения значения секрета. Во-первых, необходимо добавить следующие `using` инструкции:

```csharp
using Microsoft.Azure.KeyVault;
```

Добавьте метод, который будет обращаться к хранилищу ключей и получать секрет. Добавьте в этот метод URI секрета, сохраненный на предыдущем шаге. Обратите внимание на использование **GetToken** метода из **Utils** ранее созданного класса.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Запустив приложение, вы можете выполнить проверку подлинности в Azure Active Directory и извлечь значение секрета из хранилища ключей Azure.

## <a name="key-rotation-using-azure-automation"></a>Смена ключей с помощью службы автоматизации Azure

> [!IMPORTANT]
> Модули Runbook службы автоматизации Azure по-прежнему требуется использовать `AzureRM` модуля.

Теперь вы готовы настроить смену значения, которые хранятся в виде секретов Key Vault. Можно сменить несколькими способами:

- Как часть вручную
- Программно с помощью вызовов API
- Через сценарий автоматизации Azure

В рамках этой статьи будет использоваться в сочетании со службой автоматизации Azure PowerShell для изменения ключа доступа учетной записи хранения Azure. Затем обновим секрет хранилища ключей с помощью этого нового ключа.

Чтобы разрешить службе автоматизации Azure могла задавать значения секретов в хранилище ключей, необходимо получить идентификатор клиента для подключения с именем **AzureRunAsConnection**. Это подключение была создана при установке экземпляра службы автоматизации Azure. Чтобы найти этот идентификатор, выберите **активы** из экземпляра службы автоматизации Azure. После этого выберите **подключений**, а затем выберите **AzureRunAsConnection** субъекта-службы. Запишите **ApplicationId** значение.

![Идентификатор клиента службы автоматизации Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

В **активы**выберите **модули**. Выберите **коллекции**, а затем найдите и импортируйте обновленные версии каждого из следующих модулей:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> На момент написания этой статьи требовалось обновить только перечисленные выше модули, чтобы запустить следующий скрипт. При сбое задание по автоматизации убедитесь, что вы импортировали все необходимые модули и их зависимости.

После получения идентификатора приложения для подключения к службе автоматизации Azure, необходимо сообщить хранилище ключей, что это приложение имеет разрешение на обновление секретов в хранилище. Используйте следующую команду PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Затем выберите **ранбуки** в экземпляре службы автоматизации Azure, а затем выберите **добавления модуля Runbook**. Выберите **Быстрое создание**. Имя модуля runbook и выберите **PowerShell** тип runbook. Можно добавить описание. Наконец, выберите **Создать**.

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

В области редактора выберите **область тестирования** для тестирования сценария. Когда скрипт выполняется без ошибок, вы можете выбрать **публикации**, и затем можно применить расписания для runbook в области конфигурации модуля runbook.

## <a name="key-vault-auditing-pipeline"></a>Конвейер аудита для хранилища ключей

При настройке хранилища ключей вы можете включить аудит, чтобы собирать журналы запросов на доступ к этому хранилищу. Эти журналы хранятся в учетной записи указанного хранилища Azure и можно извлекать, отслеживать и анализировать. Следующий сценарий использует функции Azure, Azure logic apps и журналы аудита хранилища ключей для создания конвейера, который отправляет сообщение электронной почты, когда приложение, которое не соответствует Идентификатору веб-приложение получает секреты из хранилища.

Сначала нужно включить ведение журнала в хранилище ключей. Используйте следующие команды PowerShell. (Вы увидите подробные сведения в [этой статьи, посвященной ведение журнала key vault](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

После включения ведения журнала аудита занесение в журнал, хранящихся в назначенной учетной записи хранения. В этих журналах содержатся сведения о том, кто, как и когда осуществлял доступ к хранилищам ключей.

> [!NOTE]
> Регистрируемые в журналах сведения становятся доступны через 10 минут после выполнения операции с хранилищем ключей. Он часто будут доступны раньше.

Теперь необходимо [создать очередь служебной шины Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Эта очередь используется будут помещаться журналы аудита хранилища ключей. Когда в очереди сообщений журнала аудита, приложение логики извлекает их и работает с ними. Создайте экземпляр служебной шины, сделав следующее:

1. Создание пространства имен служебной шины (если у вас уже есть один, который вы хотите использовать, перейдите к шагу 2).
2. Перейдите к экземпляру шины обслуживания на портале Azure и выберите пространство имен, необходимо создать очередь.
3. Выберите **создать ресурс** > **интеграция с предприятием** > **служебной шины**, а затем введите необходимые сведения.
4. Найти сведения о соединении служебной шины, выборе пространства имен, а затем выбрав **сведения о подключении**. Эта информация потребуется в следующем разделе.

Далее, [создать функцию Azure](../azure-functions/functions-create-first-azure-function.md) опрашивать журналы хранилища ключей в учетной записи хранения и извлекать новые события. Эта функция будет запускаться по расписанию.

Для создания приложения-функции Azure, выберите **создать ресурс**, найдите в marketplace **приложения-функции**, а затем выберите **создать**. Можно использовать для этого действующий план размещения или создать новый. Вы можете также использовать динамическое размещение. Дополнительные сведения о возможности размещения для функций Azure, см. в разделе [масштабирование функций Azure](../azure-functions/functions-scale.md).

После создания приложения-функции Azure перейдите к нему и выберите **таймера** сценарий и **C\#**  для языка. Затем выберите **создайте эту функцию**.

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
> Изменение переменных в приведенном выше коде, чтобы связать вашу учетную запись хранения, куда записываются журналы хранилища ключей, к экземпляру шины обслуживания, созданную ранее и в конкретном пути к журналам хранилища ключей хранилища.

Функция получает из учетной записи хранения, в которою записываются журналы хранилища ключей, последний файл журнала, извлекает из него последние события и передает их в очередь служебной шины. 

Так как один файл может иметь несколько событий, необходимо создать файл sync.txt, функция также сканирует для определения отметки времени последнего извлеченного события. С помощью этого файла гарантирует, что вы не будет отправлено того же события несколько раз. 

В файле sync.txt содержится отметку времени для события обнаружил последнего. Когда журналы загружаются, они должны быть отсортированы на основе их отметок времени, чтобы убедиться, что они все в неправильном порядке.

Для этой функции мы ссылаться на несколько дополнительных библиотек, которые недоступны по умолчанию в функциях Azure. Эти библиотеки, библиотеки необходимо добавить функции Azure с их с помощью NuGet. В разделе **кода** выберите **Просмотр файлов**.

![Параметр «Просмотр файлов»](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Добавьте новый файл Project.JSON со следующим содержимым:

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

После выбора **Сохранить**, функции Azure скачают необходимые двоичные файлы.

Перейдите на вкладку **Интеграция** и введите для параметра "Таймер" понятное имя, которое будет использоваться в функции. В приведенном выше коде функция ожидает таймер, чтобы вызвать *myTimer*. Укажите [выражение CRON](../app-service/webjobs-create.md#CreateScheduledCRON) таймера следующим образом: `0 * * * * *`. Это выражение вызовет функцию для выполнения каждую минуту.

На том же **интегрировать** вкладке, Добавление входных данных типа **хранилище BLOB-объектов Azure**. Эти входные данные будут указывать на файл sync.txt, содержащий метку времени последнего события, просмотренного функцией. Эти входные данные будут осуществляться в функции, используя имя параметра. В приведенном выше коде входные данные хранилища BLOB-объектов Azure ожидает, что имя параметра, чтобы быть *inputBlob*. Выберите учетную запись хранения, где будет находиться файл sync.txt (возможно, в том же или другую учетную запись хранения). В поле "путь", укажите путь к файлу в формате `{container-name}/path/to/sync.txt`.

Добавление выходных данных типа **хранилище BLOB-объектов Azure**. Этот результат будет указывать на файл sync.txt, определенный во входных данных. Этот выход используется функцией для записи отметки времени последнего просмотренного события. В приведенном выше коде ожидается, что этому параметру будет присвоено имя *outputBlob*.

Теперь функция готова. Перейдите на вкладку **Разработка** и сохраните код. Проверьте окно вывода появились ошибки компиляции и исправьте их при необходимости. Если код компилируется, затем код должна теперь проверка журналов хранилища ключей каждую минуту и передавать новые события в определенную очередь служебной шины. При каждом запуске функции в окне журнала будут отображаться данные журнала.

### <a name="azure-logic-app"></a>Приложение логики Azure

Далее необходимо создать приложение логики Azure, который выбирает события, что функция помещает в очередь служебной шины, анализирует содержимое и отправляет сообщение электронной почты, в зависимости от соответствующего условия.

[Создайте приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md) , выбрав **создать ресурс** > **интеграции** > **приложение логики**.

После создания приложения логики, перейдите к нему и выберите **изменить**. В редакторе приложений логики выберите **очереди служебной шины** и введите свои учетные данные служебной шины, чтобы подключиться к очереди.

![Служебная шина приложения логики Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Выберите **Добавить условие**. В условии переключитесь в расширенный редактор и введите следующий код. Замените *строку* идентификатором фактического приложения веб-приложения:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

По сути, это выражение возвращает **false** Если *appid* из входящего события (текст сообщения служебной шины) не *appid* приложения.

Теперь создайте действие в разделе **Если нет, НИЧЕГО не**.

![Служба Azure Logic Apps выберите действие](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Действие, выберите **Office 365 — отправить сообщение электронной почты**. Заполните поля, чтобы создать электронное письмо, которое будет отправляться, когда определенное условие возвращает значение **false**. Если у вас нет Office 365, попробуйте найти альтернативные варианты для достижения тех же результатов.

Теперь у вас есть конвейер end-to-end, который ищет новые журналы аудита хранилища ключей каждую минуту. Новые журналы, найденные помещает в очередь служебной шины. Приложение логики запускается каждый раз при появлении в очереди нового сообщения. Если *appid* внутри события не соответствует Идентификатору вызывающего приложения, он отправляет сообщение электронной почты.
