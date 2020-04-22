---
title: Учебник по ротации единого пароля пользователя
description: В этом учебнике описывается, как автоматизировать смену секрета для ресурсов, использующих проверку подлинности единого пароля пользователя.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 684bfccd698217acdafdaac8c33f1e5531cdb4bf
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998898"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>Автоматизация ротации секрета для ресурсов, использующих проверку на основе единого пароля пользователя

Лучшим способом проверки подлинности в службах Azure является использование [управляемого удостоверения](managed-identity.md), но иногда его использование невозможно. В этих случаях используют ключи доступа или секреты. Время от времени необходимо сменять эти ключи доступа или секреты.

В настоящем учебнике описан процесс автоматизации периодической ротации секретов для баз данных и служб, для которых выполняется проверка подлинности на основе единого пароля пользователя. В частности, в этом учебнике выполняется смена паролей SQL Server, хранящихся в Azure Key Vault. Для ротации используется функция, запускаемая уведомлением Сетки событий Azure:

![Схема решения ротации](./media/rotate1.png)

1. За 30 дней до истечения срока действия секрета Key Vault публикует в Сетке событий событие "Срок действия скоро истекает".
1. Сетка событий проверяет подписки на события и с помощью HTTP-запроса методом POST вызывает конечную точку приложения-функции, подписанную на это событие.
1. Приложение-функция получает сведения о секрете, генерирует случайный пароль и создает другую версию секрета с новым паролем в хранилище ключей.
1. Приложение-функция обновляет SQL Server, указав новый пароль.

> [!NOTE]
> Между шагами 3 и 4 может быть задержка. В течение этого времени секрет в Key Vault не сможет пройти проверку подлинности на SQL Server. В случае сбоя на любом из шагов Сетка событий повторит попытку в течение двух часов.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Создание хранилища ключей и экземпляра SQL Server

В самом начале необходимо создать хранилище ключей, экземпляр SQL Server и базу данных, а также сохранить пароль администратора SQL Server в Key Vault.

В рамках этого учебника для создания компонентов используется предварительно созданный шаблон Azure Resource Manager. Исходный код можно найти здесь: [Пример шаблона базовой ротации секретов](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Щелкните ссылку для развертывания шаблона в Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Для параметра **Группа ресурсов** выберите **Создать**. Присвойте группе имя **simplerotation**.
1. Щелкните **Приобрести**.

    ![Создание группы ресурсов](./media/rotate2.png)

Теперь у вас есть хранилище ключей, экземпляр SQL Server и база данных SQL. Вы можете проверить эту установку в Azure CLI, выполнив следующую команду:

```azurecli
az resource list -o table
```

Результат будет выглядеть примерно следующим образом:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Создание приложения-функции

Затем создайте приложение-функцию с управляемым системой удостоверением и другими обязательными компонентами.

Для приложения-функции требуются следующие компоненты:
- План службы приложений Azure.
- Учетная запись хранения.
- Политика доступа для получения доступа к секретам в Key Vault с использованием управляемого удостоверения приложения-функции.

1. Щелкните ссылку для развертывания шаблона в Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. В поле **Группа ресурсов** выберите **simplerotation**.
1. Щелкните **Приобрести**.

   ![Нажмите кнопку "Приобрести".](./media/rotate3.png)

Выполнив описанные выше действия, вы создадите учетную запись хранения, ферму серверов и приложение-функцию. Вы можете проверить эту установку в Azure CLI, выполнив следующую команду:

```azurecli
az resource list -o table
```

Результат будет выглядеть примерно следующим образом:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

Сведения о создании приложения-функции и использовании управляемого удостоверения для доступа к Key Vault см. в статьях [Создание приложения-функции на портале Azure](../azure-functions/functions-create-function-app-portal.md) и [Выполнение проверки подлинности Key Vault с использованием управляемого удостоверения](managed-identity.md).

### <a name="rotation-function"></a>Функция ротации
Функция использует событие, чтобы активировать ротацию секрета путем обновления Key Vault и базы данных SQL.

#### <a name="function-trigger-event"></a>Событие, вызывающее функцию

Приведенная ниже функция считывает данные события и выполняет логику ротации.

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>Логика ротации секретов
Этот метод ротации считывает сведения о базе данных из секрета, создает другую версию секрета и обновляет базу данных, указав новый секрет.

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Полный пример кода можно найти на сайте [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Развертывание функции

1. Скачайте ZIP-файл приложения-функции на [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Загрузите файл simplerotationsample-fn.zip в Azure Cloud Shell.

   ![Отправка файла](./media/rotate4.png)
1. Выполните приведенную ниже команду Azure CLI, чтобы развернуть ZIP-файл в приложение-функцию:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

После развертывания функции вы должны увидеть две функции в репозитории simplerotation-fn:

![Функции SimpleRotation и SimpleRotationHttpTest](./media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Добавление подписки на событие для события SecretNearExpiry

Скопируйте ключ `eventgrid_extension` приложения-функции.

   ![Выбор параметров приложения-функции](./media/rotate6.png)

   ![Ключ eventgrid_extension](./media/rotate7.png)

Укажите скопированный ключ `eventgrid_extension` и идентификатор своей подписки в приведенной ниже команде, чтобы создать подписку сетки событий для событий `SecretNearExpiry`.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Добавление секрета в хранилище ключей
Настройте политику доступа, предоставив пользователям разрешение на *управление секретами*.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Создайте секрет с тегами, содержащими источник данных базы данных SQL и идентификатор пользователя. Включите дату истечения срока действия, которая назначена на завтра.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

При создании секрета с коротким сроком действия немедленно публикуется событие `SecretNearExpiry`, которое вызывает функцию для ротации секрета.

## <a name="test-and-verify"></a>Тестирование и проверка
Через несколько минут должна выполниться автоматическая ротация секрета `sqluser`.

Чтобы убедиться в ротации секрета, перейдите в раздел **Key Vault** > **Секреты**:

![Переход к секретам](./media/rotate8.png)

Откройте секрет **sqluser** и просмотрите исходную и измененную версию.

![Открытие секрета sqluser](./media/rotate9.png)

### <a name="create-a-web-app"></a>Создание веб-приложения

Чтобы проверить учетные данные SQL, создайте веб-приложение. Это веб-приложение получит секрет из Key Vault, извлечет из секрета сведения о базе данных и учетные данные SQL, а также выполнит проверку подключения к SQL.

Для веб-приложения требуются следующие компоненты:
- веб-приложение с управляемым системой удостоверением;
- политика доступа для получения доступа к секретам в хранилище ключей с использованием управляемого удостоверения веб-приложения.

1. Щелкните ссылку для развертывания шаблона в Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Выберите группу ресурсов **simplerotation**.
1. Щелкните **Приобрести**.

### <a name="deploy-the-web-app"></a>Развертывание веб-приложения

Исходный код для веб-приложения можно найти на [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Чтобы развернуть веб-приложение, выполните следующие действия:

1. Скачайте ZIP-файл приложения-функции на [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Загрузите файл simplerotationsample-app.zip в Azure Cloud Shell.
1. Выполните приведенную ниже команду Azure CLI, чтобы развернуть ZIP-файл в приложение-функцию:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Открытие веб-приложения

Перейдите к развернутому приложению и щелкните URL-адрес.
 
![Выбор URL-адреса](./media/rotate10.png)

Для созданного значения секрета следует установить значение true для параметра Database Connected (Подключено к базе данных).

## <a name="learn-more"></a>Дополнительные сведения

- Общие сведения. [Мониторинг Key Vault с помощью службы "Сетка событий Azure" (предварительная версия)](event-grid-overview.md)
- Руководство. [Получение сообщения электронной почты при изменении секрета в хранилище ключей](event-grid-logicapps.md)
- [Схема событий службы "Сетка событий Azure" для Azure Key Vault (предварительная версия)](../event-grid/event-schema-key-vault.md)
