---
title: Учебник по ротации единого пароля пользователя
description: В этом учебнике представлены сведения о ротации единого пароля пользователя
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223361"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Автоматизируйте ротацию секрета для ресурсов с помощью проверки единого пароля пользователя

Лучшим способом пройти проверку подлинности в службах Azure является использование [управляемого удостоверения](managed-identity.md), но иногда его использование невозможно. В этих случаях используют ключи доступа или секреты. Время от времени необходимо выполнять ротацию этих ключей доступа или секретов.

В настоящем учебнике описан процесс автоматизации периодической ротации секретов для баз данных и служб, выполняемой с помощью проверки единого пароля пользователя. В частности, в этом сценарии выполняется ротация паролей SQL Server, хранящихся в хранилище ключей. Для ротации используется функция, запускаемая уведомлением Сетки событий:

![Схема ротации](./media/rotate1.png)

1. За 30 дней до истечения срока действия секрета хранилище ключей публикует в Сетке событий событие "Срок действия скоро истекает".
1. Сетка событий проверяет подписки на события и с помощью HTTP-запроса методом POST вызывает конечную точку приложения-функции, подписанную на это событие.
1. Приложение-функция получает сведения о секрете, генерирует случайный пароль и создает другую версию секрета с новым паролем в хранилище ключей.
1. Приложение-функция обновляет SQL, указав новый пароль.

> [!NOTE]
> Между шагами 3 и 4 может возникнуть задержка, в течение которой секрет в хранилище ключей нельзя будет использовать для проверки подлинности в SQL. В случае сбоя на любом из шагов Сетка событий повторит попытку в течение 2 часов.

## <a name="setup"></a>Настройка

## <a name="create-a-key-vault-and-sql-server"></a>Создание хранилища ключей и SQL Server

В самом начале необходимо создать хранилище ключей, SQL Server и базу данных, а также сохранить пароль администратора SQL Server в хранилище ключей.

В рамках этого учебника для создания компонентов используется предварительно созданный шаблон Azure Resource Manager. Весь код можно найти на этой странице: [Пример шаблона базовой ротации секретов](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Щелкните ссылку для развертывания шаблона в Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Под полем "Группа ресурсов" щелкните ссылку "Создать" и присвойте ей имя simplerotation.
1. Нажмите кнопку "Приобрести".

    ![Создание группы ресурсов](./media/rotate2.png)

Выполнив эти шаги, вы создадите хранилище ключей, SQL Server и базу данных SQL. Их наличие можно проверить в терминале Azure CLI, выполнив следующую команду:

```azurecli
az resource list -o table
```

Должен отобразиться примерно следующий результат:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Создание приложения-функции

Создайте приложение-функцию с управляемым системой удостоверением и другими обязательными компонентами: 

Для приложения-функции требуются следующие компоненты и конфигурации:
- План обслуживания приложения
- Учетная запись хранения
- Политика доступа для получения доступа к секретам в хранилище ключей с использованием управляемого удостоверения приложения-функции.

1. Щелкните ссылку для развертывания шаблона в Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. В поле "Группа ресурсов" выберите simplerotation.
1. Нажмите кнопку "Приобрести".

   ![Снимок экрана с кнопкой "Приобрести"](./media/rotate3.png)

Выполнив описанные выше действия, вы создадите учетную запись хранения, ферму серверов и приложение-функцию.  Их наличие можно проверить в терминале Azure CLI, выполнив следующую команду:

```azurecli
az resource list -o table
```

Должен отобразиться примерно следующий результат:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

Сведения о создании приложения-функции и использовании управляемого удостоверения для доступа к хранилищу ключей см. в статьях [Создание приложения-функции на портале Azure](../azure-functions/functions-create-function-app-portal.md) и [Выполнение проверки подлинности Key Vault с использованием управляемого удостоверения](managed-identity.md).

### <a name="rotation-function-and-deployment"></a>Функция ротации и развертывание
Функция использует событие в качестве триггера и выполняет ротацию секретов, обновляя хранилище ключей и базу данных SQL.

#### <a name="function-event-trigger-handler"></a>Обработчик триггеров событий функции

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
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Весь код можно найти на этой странице: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Развертывание функции

1. Скачайте ZIP-файл приложения-функции: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Загрузите файл simplerotationsample-fn.zip в Azure Cloud Shell.
 
1. Выполните приведенную ниже команду CLI, чтобы развернуть ZIP-файл в приложение-функцию.

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Снимок экрана с кнопкой "Приобрести"](./media/rotate4.png)

После развертывания в разделе simplerotation-fn должны отобразиться две функции:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Добавление подписки на событие для события SecretNearExpiry

Скопируйте ключ приложения-функции: eventgrid_extension.

![Azure Cloud Shell](./media/rotate6.png)

![Тестирование и проверка](./media/rotate7.png)

Укажите скопированный ключ расширения eventgrid и идентификатор своей подписки в приведенной ниже команде, чтобы создать подписку сетки событий для событий SecretNearExpiry.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Добавление секрета в хранилище ключей
Настройте политику доступа, предоставив пользователям разрешение на управление секретами.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Теперь создайте секрет с тегом для источника данных базы данных SQL и для идентификатора пользователя и укажите завтрашний день в качестве даты окончания срока действия.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

При создании секрета с коротким сроком действия немедленно публикуется событие SecretNearExpiry, которое вызывает функцию для ротации секрета.

### <a name="test-and-verify"></a>Тестирование и проверка
Через несколько минут должна выполниться автоматическая ротация секрета sqluser.

Чтобы проверить ротацию секрета, выберите элементы "хранилище ключей > Секреты".

![Тестирование и проверка](./media/rotate8.png)

Откройте секрет sqluser и просмотрите исходную и измененную версию.

![Тестирование и проверка](./media/rotate9.png)

## <a name="create-web-app"></a>Создание веб-приложения

Чтобы проверить учетные данные SQL, создайте веб-приложение. Это веб-приложение получит секрет из хранилища ключей, извлечет из секрета сведения о базе данных и учетные данные SQL, а также выполнит проверку подключения к SQL.

Для веб-приложения требуются следующие компоненты и конфигурация:
- веб-приложение с управляемым системой удостоверением;
- политика доступа для получения доступа к секретам в хранилище ключей с использованием управляемого удостоверения веб-приложения.

1. Щелкните ссылку для развертывания шаблона в Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Выберите группу ресурсов **simplerotation**.
1. Нажатие кнопки "Приобрести"

### <a name="deploy-web-app"></a>Развертывание веб-приложения

Исходный код веб-приложения можно найти, перейдя по этой ссылке: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp. Разверните веб-приложение, выполнив следующие действия:

1. Скачайте ZIP-файл приложения-функции, перейдя по этой ссылке: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Передайте файл `simplerotationsample-app.zip` в Azure Cloud Shell.
1. Выполните приведенную ниже команду Azure CLI, чтобы развернуть ZIP-файл в приложение-функцию:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Открытие веб-приложения

Перейдите к развернутому приложению и щелкните URL-адрес.
 
![Тестирование и проверка](./media/rotate10.png)

Сгенерированное значение секрета должно отображаться с подключенной базой данных, для которой задано значение true.

## <a name="learn-more"></a>Дополнительные сведения:

- Общие сведения. [Мониторинг Key Vault с помощью службы "Сетка событий Azure" (предварительная версия)](event-grid-overview.md)
- Руководство. [Получение сообщения электронной почты при изменении секрета в хранилище ключей](event-grid-logicapps.md)
- [Схема событий службы "Сетка событий Azure" для Azure Key Vault (предварительная версия)](../event-grid/event-schema-key-vault.md)
