---
title: Руководство. Использование Azure Key Vault в веб-приложении Azure (.NET) | Документация Майкрософт
description: Благодаря этому руководству вы настроите приложение ASP.NET Core для считывания секрета из хранилища ключей.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.openlocfilehash: a198456412c3146db2bc3e2a2483377e387f452d
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513338"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Руководство по использованию управляемого удостоверения для подключения Key Vault к веб-приложению в .NET

Azure Key Vault позволяет безопасно хранить учетные данные и другие секреты, но для их получения код должен выполнять аутентификацию в Key Vault. Руководство по [управляемым удостоверениям для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md) поможет решить эту проблему путем предоставления службам Azure автоматически управляемого удостоверения в Azure AD. Вы можете использовать это удостоверение для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, включая Key Vault, не отображая учетные данные в коде.

В инструкциях из этого руководства управляемое удостоверение используется для проверки подлинности веб-приложения Azure в Azure Key Vault. В этом процессе используются [клиентская библиотека Azure Key Vault версии 4 для .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) и [Azure CLI](/cli/azure/get-started-with-azure-cli), но все базовые принципы сохранятся и при использовании другого языка разработки, Azure PowerShell и (или) портала Azure.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее:

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Пакет SDK для .NET Core 3.1 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. Создайте группу ресурсов для размещения веб-приложения и хранилища ключей, выполнив команду [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create).

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Настройка хранилища ключей

Теперь создайте хранилище ключей и поместите в него секрет, который будет использоваться далее в этом руководстве.

Чтобы создать хранилище ключей, используйте команду [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create).

> [!Important]
> Каждое хранилище ключей должно иметь уникальное имя. В следующих примерах замените заполнитель <your-keyvault-name> именем своего хранилища ключей.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Запишите полученное значение `vaultUri` в формате "https://&lt;your-keyvault-name&gt;.vault.azure.net". Оно вам пригодится на шаге [Обновление кода](#update-the-code).

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-net-web-app"></a>Создание веб-приложения .NET

### <a name="create-a-local-app"></a>Создание локального приложения

В окне терминала на компьютере создайте каталог `akvwebapp` и перейдите в него.

```bash
mkdir akvwebapp
cd akvwebapp
```

Теперь создайте приложение .NET Core с помощью команды [dotnet new web](/dotnet/core/tools/dotnet-new).

```bash
dotnet new web
```

Запустите приложение локально, чтобы увидеть, как оно будет выглядеть после развертывания в Azure. 

```bash
dotnet run
```

Откройте веб-браузер и перейдите к приложению в `http://localhost:5000`.

На странице отобразится сообщение **Hello World**, созданное примером приложения.

### <a name="initialize-the-git-repository"></a>Инициализация репозитория Git

В окне терминала нажмите клавиши **CTRL+C**, чтобы выйти из веб-сервера.  Инициализируйте репозиторий Git для проекта .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

Для развертывания в веб-приложение Azure из FTP и локального репозитория Git можно использовать *пользователя развертывания*. Настроив один раз пользователя развертывания, вы сможете использовать его для всех последующих развертываний в Azure. Имя пользователя и пароль учетной записи развертывания отличаются от учетных данных подписки Azure. 

Чтобы настроить пользователя для развертывания, выполните команду [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Выберите имя пользователя и пароль, которые соответствуют следующим рекомендациям: 

- Имя пользователя должно быть уникальным в Azure. Кроме того, чтобы отправка в локальный репозиторий Git работала, имя пользователя не должно содержать символ @. 
- Пароль должен содержать не менее восьми символов и включать два из трех следующих элементов: буквы, цифры и символы. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

В выходных данных JSON пароль отображается как `null`. Если появляется сообщение об ошибке `'Conflict'. Details: 409`, измените имя пользователя. Если появляется сообщение об ошибке `'Bad Request'. Details: 400`, используйте более надежный пароль. 

Запишите имя пользователя и пароль и используйте их для развертывания веб-приложений.

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

Создайте план службы приложений, выполнив команду Azure CLI [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest). В следующем примере создается план службы приложений с именем `myAppServicePlan` и ценовой категорией **Бесплатный**.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

После создания плана службы приложений в Azure CLI отображается информация следующего вида:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>


### <a name="create-a-remote-web-app"></a>Создание удаленного веб-приложения

Создайте [веб-приложение Azure](../../app-service/containers/app-service-linux-intro.md) в плане службы приложений `myAppServicePlan`. 

> [!Important]
> Как и хранилище ключей, веб-приложение Azure должно иметь уникальное имя. В следующих примерах замените \<your-webapp-name\> именем своего веб-приложения.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


URL-адрес удаленного репозитория Git отображается в свойстве `deploymentLocalGitUrl` в формате `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Сохраните этот URL-адрес для дальнейшего использования.

Перейдите к только что созданному веб-приложению. Замените _&lt;your-webapp-name>_ именем своего приложения.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Вы увидите веб-страницу по умолчанию для только что созданного веб-приложения Azure.

### <a name="deploy-your-local-app"></a>Развертывание локального приложения

Вернитесь к окну локального терминала и добавьте удаленное хранилище Azure в локальный репозиторий Git, заменив *\<deploymentLocalGitUrl-from-create-step>* URL-адресом удаленного хранилища Git, который вы сохранили на предыдущем шаге [Создание удаленного веб-приложения](#create-a-remote-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Отправьте код в удаленное приложение Azure, чтобы развернуть приложение. Когда диспетчер учетных данных Git отобразит запрос на ввод учетных данных, введите соответствующие значения, созданные на шаге [Настройка пользователя развертывания](#configure-a-deployment-user).

```bash
git push azure master
```

Выполнение этой команды может занять несколько минут. При выполнении эта команда выводит приблизительно следующие сведения:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Откройте или обновите в браузере страницу развернутого приложения.

```bash
http://<your-webapp-name>.azurewebsites.net
```

Там отобразится сообщение "Hello World!", которое вы уже видели при посещении `http://localhost:5000`.

## <a name="create-and-assign-a-managed-identity"></a>Создание и назначение управляемого удостоверения

В Azure CLI выполните команду [az webapp-identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign), чтобы создать удостоверение для этого приложения.

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Эта операция вернет следующий фрагмент JSON:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Чтобы предоставить веб-приложению разрешение на выполнение операций **get** и **list** в хранилище ключей, передайте значение principalID в команду Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```


## <a name="modify-the-app-to-access-your-key-vault"></a>Изменение приложения для доступа к хранилищу ключей

### <a name="install-the-packages"></a>Установка пакетов

В окне терминала установите пакеты клиентской библиотеки Azure Key Vault для .NET.

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

### <a name="update-the-code"></a>Обновление кода

Найдите и откройте файл Startup.cs в проекте akvwebapp. 

Добавьте эти две строки в заголовок:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Эти строки следует поместить перед вызовом `app.UseEndpoints`, включив в URI значение `vaultUri` для вашего хранилища ключей. Для проверки подлинности в хранилище ключей в следующем примере кода используется функция [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet), которая использует токен из управляемого удостоверения приложения. В ней также реализована экспоненциальная задержка для повторных попыток на случай регулирования запросов к хранилищу ключей.

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("mySecret");

string secretValue = secret.Value;
```

Обновите строку `await context.Response.WriteAsync("Hello World!");`, чтобы она выглядела так:

```csharp
await context.Response.WriteAsync(secretValue);
```

Не забудьте сохранить внесенные изменения, прежде чем перейти к следующему шагу.

### <a name="redeploy-your-web-app"></a>Повторное развертывание веб-приложения

После обновления кода повторно разверните его в Azure с помощью следующих команд Git:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="visit-your-completed-web-app"></a>Открытие страницы готового веб-приложения

```bash
http://<your-webapp-name>.azurewebsites.net
```

Вместо надписи **Hello World**, которую мы видели раньше, отобразится значение секрета: **Готово!**

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. в статье [Использование управляемых удостоверений для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md).
- Дополнительные сведения об использовании управляемых удостоверений в Службе приложений см. в [этой статье](../../app-service/overview-managed-identity.md?tabs=dotnet).
- Изучите [справочник по API клиентской библиотеки Azure Key Vault для .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).
- Изучите [исходный код клиентской библиотеки Azure Key Vault для .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault).
- Просмотрите сведения о [пакете NuGet клиентской библиотеки Azure Key Vault для .NET версии 4](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/).


