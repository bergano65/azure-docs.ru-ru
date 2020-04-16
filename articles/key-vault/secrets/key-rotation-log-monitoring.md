---
title: Настройка полной смены ключей и аудита в хранилище ключей Azure | Документация Майкрософт
description: Используйте это руководство, чтобы помочь вам настроить вращение ключей и контролировать журналы хранилища ключей.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d2981495a256ce5fb8f8f3584e68ac91541f9d62
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430257"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Настройка смены ключей и аудита в Azure Key Vault

## <a name="introduction"></a>Введение

Получив хранилище ключей, вы можете использовать его для хранения ключей и секретов. В приложениях больше не нужно сохранять ключи и секреты. При необходимости их можно получить из хранилища. Хранилище ключей позволяет обновлять ключи и секреты, не влияя на поведение вашего приложения, что открывает широту возможностей для вашего управления ключом и секретом.

В этой статье рассказывается о том, как реализовать запланированную ротацию ключей учетной записи хранилища, следить за журналами аудита хранилища ключей и повышать оповещения при выполнении неожиданных запросов. 

Сначала необходимо создать хранилище ключей, используя ваш выбор:

- [Настройка и получение секрета из Azure Key Vault с помощью Azure CLI](quick-create-cli.md)
- [Установите и извлеките секрет из Убежища ключей Azure с помощью Azure PowerShell](quick-create-powershell.md)
- [Установите и извлеките секрет из Убежища ключей Azure с помощью портала Azure](quick-create-portal.md)


## <a name="store-a-secret"></a>Храните секрет

Чтобы приложение могло получить секрет из хранилища ключей, секрет нужно создать и передать в хранилище.

Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:

```powershell
Connect-AzAccount
```

В окне всплывающих браузеров введите имя пользователя и пароль для учетной записи Azure. PowerShell получит все подписки, связанные с этой учетной записью. По умолчанию PowerShell будет использовать первую из них.

Если у вас есть несколько подписок, вы можете указать ту, с помощью которой вы создали хранилище ключей Azure. Чтобы просмотреть подписки для своей учетной записи, введите следующую команду:

```powershell
Get-AzSubscription
```

Чтобы указать подписку, связанную с хранилищем ключей, в который вы будете регистрироваться, введите:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Так как в этой статье в качестве секрета используется ключ учетной записи хранения, вам необходимо получить его.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

После получения секрета (в данном случае ключа учетной записи хранилища) необходимо преобразовать этот ключ в безопасную строку, а затем создать секрет с этим значением в хранилище ключей.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

После этого получите URI секрета, который вы создали. Вам понадобится этот URI на более позднем этапе, чтобы позвонить в хранилище ключей и получить ваш секрет. Выполнить следующую команду PowerShell и сделать к сведению значение ID, которое является URI секрета:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Настройка приложения

Теперь, когда у вас есть секрет хранится, вы можете использовать код для извлечения и использовать его после выполнения еще несколько шагов.

Во-первых, необходимо зарегистрировать приложение в Active Directory Azure. Затем сообщите Key Vault информацию о своем приложении, чтобы оно мог разрешить запросы от вашего приложения.

> [!NOTE]
> Приложение и хранилище ключей необходимо создать в одном и том же клиенте Azure Active Directory.

1. Открыть **активный каталог Azure**.
2. Выберите **регистрацию приложений**. 
3. Выберите **новую регистрацию приложений,** чтобы добавить приложение в Active Directory Azure.

    ![Вкладка "Приложения" в Azure Active Directory](../media/keyvault-keyrotation/azure-ad-application.png)

4. Под **Create**оставьте тип приложения в виде **веб-приложения / API** и назовите ваше приложение именем. Дайте вашему приложению **URL-адрес.** Этот URL может быть все, что вы хотите для этого демо.

    ![Создание регистрации приложения](../media/keyvault-keyrotation/create-app.png)

5. После добавления приложения в Active Directory Azure страница приложения открывается. Выберите **настройки,** а затем выберите **Свойства.** Скопируйте значение **ИД приложения**. Он вам понадобится в более поздних шагах.

Затем создайте ключ для приложения, чтобы оно могли взаимодействовать с Active Directory Azure. Чтобы создать ключ, выберите **Ключи** под **настройками.** Обратите внимание на недавно созданный ключ для приложения Azure Active Directory. Они потребуются вам на следующем шаге. Ключ не будет доступен после выхода из этого раздела. 

![Ключи приложения Active Directory Azure](../media/keyvault-keyrotation/create-key.png)

Прежде чем установить какие-либо звонки из приложения в хранилище ключей, вы должны сообщить хранилище ключей о вашем приложении и его разрешениях. Следующая команда использует имя хранилища и идентификатор приложения из приложения Azure Active Directory, чтобы предоставить приложению **Доступ** к хранилищу ключей.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Теперь вы готовы приступить к созданию вызовов приложений. В приложении необходимо установить пакеты NuGet, необходимые для взаимодействия с Azure Key Vault и Active Directory Azure. Для этого введите приведенные ниже команды в консоли диспетчера пакетов Visual Studio. При написании этой статьи текущая версия пакета Active Directory Azure составляет 3.10.305231913, поэтому подтверждайте последнюю версию и обновляйте по мере необходимости.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

В коде приложения создайте класс, который будет использоваться при проверке подлинности в Azure Active Directory. В этом примере этот класс называется **Utils.** Добавьте следующую инструкцию `using` :

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Добавьте приведенный ниже метод для получения маркера JWT из Azure Active Directory. Для обслуживания может потребоваться переместить значения строки с жестким кодом в конфигурацию веб-страниц или приложений.

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

Добавьте необходимый код для обращения к хранилищу ключей и получения значения секрета. Во-первых, необходимо `using` добавить следующее утверждение:

```csharp
using Microsoft.Azure.KeyVault;
```

Добавьте метод, который будет обращаться к хранилищу ключей и получать секрет. Добавьте в этот метод URI секрета, сохраненный на предыдущем шаге. Обратите внимание на использование метода **GetToken** из созданного ранее класса **Utils.**

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Запустив приложение, вы можете выполнить проверку подлинности в Azure Active Directory и извлечь значение секрета из хранилища ключей Azure.

## <a name="key-rotation-using-azure-automation"></a>Смена ключей с помощью службы автоматизации Azure

> [!IMPORTANT]
> Runbooks Azure Automation по-прежнему `AzureRM` требуют использования модуля.

Теперь вы готовы настроить стратегию вращения для значений, которые вы храните в качестве секретов Key Vault. Секреты могут быть повернуты несколькими способами:

- В рамках ручного процесса
- Программно используя вызовы API
- С помощью сценария автоматизации Azure

Для целей этой статьи вы будете использовать PowerShell в сочетании с Azure Automation для изменения ключа доступа учетной записи хранилища Azure. Затем вы обновите секрет хранилища ключей с помощью этого нового ключа.

Чтобы позволить Azure Automation установить секретные значения в хранилище ключей, необходимо получить идентификатор клиента для соединения под названием **AzureRunAsConnection.** Это соединение было создано при создании экземпляра Azure Automation. Чтобы найти этот идентификатор, выберите **ресурсы** из экземпляра Azure Automation. Оттуда выберите **соединения,** а затем выберите принцип службы **AzureRunAsConnection.** Обратите внимание на значение **ApplicationId.**

![Идентификатор клиента службы автоматизации Azure](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

В **активах**выберите **Модули.** Выберите **Gallery**, а затем поиск и импорт обновленных версий каждого из следующих модулей:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> На момент написания этой статьи требовалось обновить только перечисленные выше модули, чтобы запустить следующий скрипт. Если работа автоматизации не удается, подтвердите, что вы импортировали все необходимые модули и их зависимости.

После того как вы извлекли идентификатор приложения для подключения Azure Automation, вы должны сообщить хранилище мгновений ключа, что у этого приложения есть разрешение на обновление секретов в хранилище. Используйте следующую команду PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Далее выберите **Runbooks** в экземпляре Azure Automation, а затем выберите **Добавить Runbook.** Выберите **Быстрое создание**. Назовите свой runbook и выберите **PowerShell** в качестве типа runbook. Вы можете добавить описание. Наконец, выберите **Создать**.

![Создание модуля Runbook](../media/keyvault-keyrotation/Create_Runbook.png)

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

В панели редактора выберите **панель тестирования** для тестирования скрипта. После того, как скрипт работает без ошибок, вы можете выбрать **Publish,** а затем вы можете применить расписание для runbook в панели конфигурации runbook.

## <a name="key-vault-auditing-pipeline"></a>Конвейер аудита для хранилища ключей

При настройке хранилища ключей вы можете включить аудит, чтобы собирать журналы запросов на доступ к этому хранилищу. Эти журналы хранятся в специально отведенной учетной записи хранения Azure и могут быть извлечены, проверены и проанализированы. В следующем сценарии используются функции Azure, логические приложения Azure и журналы аудита ключей для создания конвейера, который отправляет электронное письмо, когда приложение, не совпадаящее с идентификатором приложения веб-приложения, извлекает секреты из хранилища.

Сначала нужно включить ведение журнала в хранилище ключей. Используйте следующие команды PowerShell. (Вы можете увидеть полную информацию в [этой статье о ключе-vault-logging](../general/logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

После включения журнала журналы аудита начинают храниться в назначенном учете хранилища. В этих журналах содержатся сведения о том, кто, как и когда осуществлял доступ к хранилищам ключей.

> [!NOTE]
> Регистрируемые в журналах сведения становятся доступны через 10 минут после выполнения операции с хранилищем ключей. Он часто будет доступен раньше, чем это.

Теперь необходимо [создать очередь служебной шины Azure](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Эта очередь — это место, где выталкиваются журналы аудита ключей. Когда сообщения аудиторского журнала находятся в очереди, приложение логики подбирает их и действует на них. Создайте экземпляр «Автобус обслуживания» со следующими шагами:

1. Создайте пространство имен sbus (если у вас уже есть то, что вы хотите использовать, перейдите к шагу 2).
2. Просмотрите экземпляр «Сервис-бус» на портале Azure и выберите пространство имен, в которое вы хотите создать очередь.
3. Выберите **Создать ресурс** > **Enterprise Integration** > **Service Bus,** а затем введите необходимые сведения.
4. Найдите информацию о подключении к сервисному автобусу, выбрав пространство имен, а затем выбрав **информацию о**подключении. Эта информация понадобится для следующего раздела.

Затем [создайте функцию Azure](../../azure-functions/functions-create-first-azure-function.md) для опроса журналов хранилища ключей в учетной записи хранилища и поиска новых событий. Эта функция будет срабатывать по расписанию.

Чтобы создать приложение функции Azure, выберите **Создать ресурс,** поиск на рынке **функциональных приложений,** а затем выберите **Создать.** Можно использовать для этого действующий план размещения или создать новый. Вы также можете выбрать динамический хостинг. Для получения дополнительной информации о вариантах [How to scale Azure Functions](../../azure-functions/functions-scale.md)хостинга для функций Azure см.

После создания приложения функции Azure перейдите к нему и выберите сценарий **Таймер** и **C\# ** для языка. Затем выберите **Создайте эту функцию.**

![Начальная колонка функций Azure](../media/keyvault-keyrotation/Azure_Functions_Start.png)

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
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
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
> Измените переменные в предыдущем коде, чтобы указать на учетную запись хранилища, где написаны журналы хранилища ключей, на экземпляр Service Bus, созданный ранее, и на конкретный путь к журналам хранения ключей.

Функция получает из учетной записи хранения, в которою записываются журналы хранилища ключей, последний файл журнала, извлекает из него последние события и передает их в очередь служебной шины. 

Поскольку один файл может иметь несколько событий, следует создать файл sync.txt, который функция также рассматривает, чтобы определить отметку времени последнего события, которое было выбрано. Использование этого файла гарантирует, что вы не нажимаете одно и то же событие несколько раз. 

Файл sync.txt содержит отметку времени для последнего события. Когда журналы загружаются, они должны быть отсортированы на основе их временных штампов, чтобы убедиться, что они заказаны правильно.

Для этой функции мы ссылаемся на несколько дополнительных библиотек, которые не доступны из коробки в Функциях Azure. Чтобы включить эти библиотеки, нам нужны функции Azure, чтобы вытащить их с помощью NuGet. Под окне **кода** выберите **Файлы просмотра.**

![Опция "Просмотр файлов"](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Добавьте файл под названием project.json со следующим содержанием:

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

После выбора **Сохранить**функции Azure будут загружать необходимые файлы.

Перейдите на вкладку **Интеграция** и введите для параметра "Таймер" понятное имя, которое будет использоваться в функции. В предыдущем коде функция ожидает, что таймер будет называться *myTimer*. Укажите [выражение CRON](../../app-service/webjobs-create.md#CreateScheduledCRON) для таймера следующим образом: `0 * * * * *`. Это выражение приведет к тому, что функция будет выполняться один раз в минуту.

На той же вкладке **Integrate** добавьте входный ввод **хранилища Типа Azure Blob.** Этот вход укажет на файл sync.txt, содержащий отметку времени последнего события, просматриваемого функцией. Этот вход будет доступен в функции с помощью имени параметра. В предыдущем коде ввод хранилища Azure Blob предполагает, что имя параметра *будет введаннымBlob.* Выберите учетную запись хранения, где будет находиться файл sync.txt (это может быть тот же или другой счет хранения). В поле пути предоставьте путь к файлу в формате. `{container-name}/path/to/sync.txt`

Добавьте выход из **типа хранилища Azure Blob.** Этот вывод укажет на файл sync.txt, определенный в входе. Этот вывод используется функцией для написания отметки времени последнего из рассмотренных событий. В приведенном выше коде ожидается, что этому параметру будет присвоено имя *outputBlob*.

Функция готова. Перейдите на вкладку **Разработка** и сохраните код. Проверьте выходное окно на наличие ошибок компиляции и исправьте их по мере необходимости. Если код компилируется, то код должен теперь проверять журналы хранилища ключей каждую минуту и вставлять любые новые события в определенную очередь Service Bus. При каждом запуске функции в окне журнала будут отображаться данные журнала.

### <a name="azure-logic-app"></a>Приложение логики Azure

Далее необходимо создать приложение логики Azure, которое подхватывает события, которые функция толкает в очередь Service Bus, разбирает содержимое и отправляет электронное письмо на основе соответствия условия.

[Создайте логическое приложение,](../../logic-apps/quickstart-create-first-logic-app-workflow.md) выбрав **приложение «Создай логику** > **интеграции** > **Logic App**ресурса».

После создания приложения логики перейдите к нему и выберите **Edit**. В редакторе приложения логики выберите **очередь в автобус службы** и введите учетные данные Service Bus, чтобы подключить его к очереди.

![Служебная шина приложения логики Azure](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Выберите **Добавить условие**. В этом состоянии переключитесь на продвинутый редактор и введите следующий код. Замените *APP_ID* фактическим идентификатором приложения вашего веб-приложения:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Это выражение по существу **возвращаетложное,** если *приложение* из входящего события (которое является телом сообщения Service Bus) не является *приложением* приложения.

Теперь, создать действие в соответствии с **ЕСЛИ НЕТ, ничего .**

![Приложения логики Azure выбирают действие](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Для акции выберите **Office 365 - отправьте электронное письмо.** Заполните поля, чтобы создать электронное письмо, которое будет отправляться, когда определенное условие возвращает значение **false**. Если у вас нет Office 365, ищите альтернативы для достижения тех же результатов.

Теперь у вас есть сквозной конвейер, который ищет новые журналы аудита ключей раз в минуту. Он толкает новые журналы, которые он находит, в очередь Service Bus. Приложение логики запускается каждый раз при появлении в очереди нового сообщения. Если *приложение* в событии не совпадает с идентификатором приложения вызывающей приложения, оно отправляет электронное письмо.
