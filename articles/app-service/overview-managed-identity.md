---
title: Управляемые удостоверения
description: Узнайте, как управляемые идентификаторы работают в службах приложений Azure и функциях Azure, как настроить управляемый идентификатор и создать маркер для резервного ресурса.
author: mattchenderson
ms.topic: article
ms.date: 03/04/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 6e3169f2bfcba0a02af1490f875cbab8a14d02f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280031"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Использование управляемых удостоверений в Службе приложений и Функциях Azure

> [!Important] 
> При переносе приложения между подписками или клиентами управляемые удостоверения для Службы приложений и Функций Azure не будут работать в надлежащем режиме. Для приложения нужно получить новое удостоверение, отключив службу и включив ее снова. См. раздел [Удаление удостоверения](#remove) ниже. Также нужно обновить политики доступа во всех зависимых ресурсах, чтобы они использовали новое удостоверение.

В этой статье показано, как создавать управляемое удостоверение для приложений Службы приложений и Функций Azure, а также как их использовать для получения доступа к другим ресурсам. Управляемый имитатор из Azure Active Directory (AAD) позволяет приложению легко получить доступ к другим защищенным AAD ресурсам, таким как Azure Key Vault. Удостоверения управляются платформой Azure, и для них не нужно подготавливать или изменять секреты. Дополнительные сведения об управляемых удостоверениях в Azure Active Directory см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Приложению можно предоставить два типа удостоверений: 
- **Назначаемое системой удостоверение** привязывается к приложению и удаляется при удалении приложения. Приложение может иметь только одно назначаемое системой удостоверение.
- **Идентификационная личность, назначенная пользователем,** является автономным ресурсом Azure, который может быть назначен вашему приложению. Приложение может иметь несколько назначаемых пользователем удостоверений.

## <a name="add-a-system-assigned-identity"></a>Добавление установленного системой удостоверения

Чтобы создать приложение с назначаемым системой удостоверением, необходимо задать дополнительное свойство в приложении.

### <a name="using-the-azure-portal"></a>Использование портала Azure

Чтобы настроить управляемое удостоверение на портале, сначала необходимо создать обычное приложение, а затем активировать соответствующую функцию.

1. Создайте приложение на портале обычным образом. Перейдите к нему на портале.

2. Если используется приложение-функция, перейдите к **функциям платформы**. Для других типов приложений прокрутите вниз до группы **параметров** в левой области навигации.

3. Выберите **идентификацию**.

4. На вкладке **Назначено системой** для параметра **Состояние** установите значение **Вкл**. Нажмите **Сохранить**.

    ![Управляемое удостоверение в Службе приложений](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>Использование Azure CLI

Чтобы настроить управляемое удостоверение с помощью Azure CLI, используйте команду `az webapp identity assign` для имеющегося приложения. Примеры из этого раздела можно выполнять тремя способами:

- использовать [Azure Cloud Shell](../cloud-shell/overview.md) с портала Azure;
- Используйте встроенную облачную оболочку Azure через кнопку "Попробуй это", расположенную в правом верхнем углу каждого блока кода ниже.
- [установить последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 или выше), если вы предпочитаете использовать локальную консоль CLI. 

Ниже описаны действия по созданию веб-приложения и присвоению удостоверения ему с помощью CLI:

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az-login). Используйте учетную запись, связанную с подпиской Azure, в соответствии с которой вы хотели бы развернуть приложение:

    ```azurecli-interactive
    az login
    ```
2. Создайте веб-приложение с помощью CLI. Дополнительные примеры использования CLI со службой приложений см. в статье [Примеры Azure CLI](../app-service/samples-cli.md).

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Выполните команду `identity assign`, чтобы создать удостоверение для этого приложения.

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Использование Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ниже описаны действия по созданию веб-приложения и присвоению удостоверения ему с помощью Azure PowerShell:

1. При необходимости установите Azure PowerShell, используя инструкции, найденные в `Login-AzAccount` [руководстве Azure PowerShell,](/powershell/azure/overview)а затем запустите для создания соединения с Azure.

2. Создайте веб-приложение с помощью Azure PowerShell. Дополнительные примеры применения Azure PowerShell со службой приложений см. в статье [Примеры сценариев Azure PowerShell](../app-service/samples-powershell.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Выполните команду `Set-AzWebApp -AssignIdentity`, чтобы создать удостоверение для этого приложения.

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager

Шаблон Azure Resource Manager можно использовать для автоматизации развертывания ресурсов Azure. Дополнительные сведения о развертывании в службе приложений и Функциях см. в статьях [Предсказуемые подготовка и развертывание микрослужб в Azure](../app-service/deploy-complex-application-predictably.md) и [Автоматизация развертывания ресурсов приложения-функции для службы "Функции Azure"](../azure-functions/functions-infrastructure-as-code.md).

Любой ресурс типа `Microsoft.Web/sites` можно создать с помощью удостоверения, добавив следующее свойство в определение ресурса:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Приложение может иметь назначаемое системой и назначаемое пользователем удостоверения одновременно. В этом случае для свойства `type` будет задано значение `SystemAssigned,UserAssigned`.

При добавлении назначаемого системой удостоверения Azure создает удостоверение для приложения и управляет им.

Например, веб-приложение может выглядеть следующим образом:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Созданный сайт будет иметь следующие дополнительные свойства:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Свойство tenantId определяет, к какому клиенту AAD относится это удостоверение. principalId — это уникальный идентификатор для нового удостоверения приложения. В AAD субъект-служба имеет то же имя, которое было присвоено экземпляру Службы приложений или Функций Azure.


## <a name="add-a-user-assigned-identity"></a>Добавление удостоверения, назначенного пользователем

Для создания приложения с назначаемым пользователем удостоверением необходимо создать удостоверение, а затем добавить его идентификатор ресурса в конфигурацию приложения.

### <a name="using-the-azure-portal"></a>Использование портала Azure

Сначала необходимо создать ресурс назначаемого пользователем удостоверения.

1. Создайте ресурс назначаемого пользователем управляемого удостоверения в соответствии с [этими инструкциями](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Создайте приложение на портале обычным образом. Перейдите к нему на портале.

3. Если используется приложение-функция, перейдите к **функциям платформы**. Для других типов приложений прокрутите вниз до группы **параметров** в левой области навигации.

4. Выберите **идентификацию**.

5. В **назначаемых пользователем** вкладке нажмите **Добавить**.

6. Найдите созданное ранее удостоверение и выберите его. Нажмите кнопку **Добавить**.

    ![Управляемое удостоверение в Службе приложений](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager

Шаблон Azure Resource Manager можно использовать для автоматизации развертывания ресурсов Azure. Дополнительные сведения о развертывании в службе приложений и Функциях см. в статьях [Предсказуемые подготовка и развертывание микрослужб в Azure](../app-service/deploy-complex-application-predictably.md) и [Автоматизация развертывания ресурсов приложения-функции для службы "Функции Azure"](../azure-functions/functions-infrastructure-as-code.md).

Любой ресурс типа `Microsoft.Web/sites` можно создать с помощью удостоверения, добавив следующий блок в определение ресурса, заменив `<RESOURCEID>` идентификатором ресурса необходимого удостоверения:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Приложение может иметь назначаемое системой и назначаемое пользователем удостоверения одновременно. В этом случае для свойства `type` будет задано значение `SystemAssigned,UserAssigned`.

Добавление заданного пользователем типа говорит Azure использовать установленный пользователем иственную личность, указанную для приложения.

Например, веб-приложение может выглядеть следующим образом:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Созданный сайт будет иметь следующие дополнительные свойства:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

principalId является уникальным идентификатором для идентификации, которая используется для администрирования AAD. ClientId — это уникальный идентификатор для нового удостоверения приложения, который используется для определения того, какой идентификатор использовать во время выполнения вызовов.


## <a name="obtain-tokens-for-azure-resources"></a>Получение токенов для ресурсов Azure

Приложение может использовать управляемое удостоверение для получения токенов для доступа к другим ресурсам, защищенным AAD, таким как Azure Key Vault. Эти маркеры представляют приложение, получающее доступ к ресурсам, а не конкретного пользователя приложения. 

Чтобы разрешить доступ из приложения, необходимо настроить целевой ресурс. Например, если вы запрашиваете маркер для доступа к Key Vault, необходимо убедиться, что вы добавили политику доступа, которая включает в себя идентификацию приложения. В противном случае вызовы Key Vault будут отклонены, даже если они включают маркеры. Чтобы узнать больше о том, какие ресурсы поддерживают маркеры Azure Active Directory, см. сведения в разделе о [службах Azure, поддерживающих аутентификацию Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> Службы бэк-энда для управляемых идентификаторов поддерживают кэш на ресурсе URI в течение примерно 8 часов. Если вы обновите политику доступа определенного целевого ресурса и немедленно изымаете маркер для этого ресурса, можно продолжить получать кэшированный маркер с устаревшими разрешениями до истечения срока действия этого маркера. В настоящее время нет способа заставить маркер обновления.

Существует простой протокол REST для получения маркера в службе приложений и Функциях Azure. Это может быть использовано для всех приложений и языков. Для .NET и Java SDK Azure предоставляет абстракцию по этому протоколу и облегчает локальную разработку.

### <a name="using-the-rest-protocol"></a>Использование протокола REST

Приложение с управляемым удостоверением содержит две заданные переменные среды:

- MSI_ENDPOINT — URL-адрес локальной службы токенов.
- MSI_SECRET — заголовок, который используется при устранении атак с подделкой серверных запросов (SSRF). Это значение меняется платформой.

**MSI_ENDPOINT** — это локальный URL-адрес, из которого приложение может запрашивать маркеры. Чтобы получить маркер для ресурса, отправьте запрос HTTP GET к этой конечной точке, задав следующие параметры:

> |Имя параметра|В|Описание|
> |-----|-----|-----|
> |ресурс|Запрос|Универсальный код ресурса (URI) AAD, для которого нужно получить маркер. Это может быть URI одной из [служб Azure, которая поддерживает аутентификацию Azure AD,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) или любой другой URI ресурса.|
> |api-version|Запрос|Версия API маркеров, которая будет использоваться. Сейчас поддерживается только одна версия: 2017-09-01.|
> |secret|Заголовок|Значение переменной среды MSI_SECRET. Заголовок, который используется при устранении атак с подделкой серверных запросов (SSRF).|
> |clientid|Запрос|(Необязательно для назначенных пользователем) Идентификатор присваиваемого пользователем удостоверения, который будет использоваться. Если этот параметр опущен, используется назначаемое системой удостоверение.|

> [!IMPORTANT]
> Если вы пытаетесь получить токены для удостоверений, назначенных пользователем, вы должны включить свойство. `clientid` В противном случае служба токенов попытается получить токен для установленного системой удостоверения, которое может существовать или не существовать.

Успешный ответ 200 — OK включает текст JSON со следующими свойствами:

> |Имя свойства|Описание|
> |-------------|----------|
> |access_token|Запрашиваемый маркер доступа. Вызывающая веб-служба может использовать этот маркер для проверки подлинности принимающей веб-службы.|
> |expires_on|Время истечения срока действия маркера доступа. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC до истечения срока действия. Это значение используется для определения времени существования кэшированных маркеров.|
> |ресурс|URI идентификатора приложения принимающей вызов веб-службы.|
> |token_type|Указывает значение типа маркера. Единственный тип, поддерживаемый Azure AD — носитель. Для получения дополнительной информации о [The OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)токенах предъявителя см.|

Этот ответ совпадает с [ответом для запроса маркера взаимного доступа между службами AAD](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#get-a-token).

> [!NOTE]
> Переменные среды настраиваются при первом запуске процесса. Поэтому после включения управляемого удостоверения может потребоваться перезапустить приложение или повторно развернуть код, чтобы для него стали доступны `MSI_ENDPOINT` и `MSI_SECRET`.

### <a name="rest-protocol-examples"></a>Примеры протокола REST

Пример запроса может выглядеть следующим образом:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Пример ответа может выглядеть следующим образом:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Примеры кода

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> Для языков .NET можно также использовать пакет [Microsoft.Azure.Services.AppAuthentication](#asal), вместо того чтобы создавать этот запрос самостоятельно.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2017-09-01", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource));
    request.Headers.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=2017-09-01`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

msi_endpoint = os.environ["MSI_ENDPOINT"]
msi_secret = os.environ["MSI_SECRET"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{msi_endpoint}?resource={resource_uri}&api-version=2017-09-01"
    head_msi = {'Secret':msi_secret}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=2017-09-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Использование библиотеки Microsoft.Azure.Services.AppAuthentication для .NET

Самый простой способ для приложений и функций .NET работать с управляемыми удостоверениями заключается в использовании пакета Microsoft.Azure.Services.AppAuthentication. Эта библиотека также позволяет локально тестировать код на компьютере разработки с использованием учетной записи пользователя из Visual Studio, [Azure CLI](/cli/azure) или встроенной проверки подлинности Active Directory. Дополнительные сведения о параметрах локальной разработки с помощью этой библиотеки см. в [справочнике Microsoft.Azure.Services.AppAuthentication]. В этом разделе показано, как начать работу с библиотекой в коде.

1. Добавьте ссылки на пакет [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) и другие пакеты NuGet в приложение. В примерах ниже также используется [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Добавьте приведенный ниже код в приложение, указав необходимый целевой ресурс. В этом примере показано два способа работы с Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Дополнительные сведения о пакете Microsoft.Azure.Services.AppAuthentication и операциях, которые он предоставляет, см. в [справочнике Microsoft.Azure.Services.AppAuthentication ] и [примерах службы приложений и хранилищах ключей с управляемым удостоверением службы .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Использование SDK Azure для Java

Для Java-приложений и функций самый простой способ работы с управляемой идентификацией — это [SDK Azure для Java.](https://github.com/Azure/azure-sdk-for-java) В этом разделе показано, как начать работу с библиотекой в коде.

1. Добавьте ссылку на [библиотеку Azure SDK.](https://mvnrepository.com/artifact/com.microsoft.azure/azure) Для проектов Maven можно добавить этот `dependencies` фрагмент в раздел POM-файл проекта:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Используйте `AppServiceMSICredentials` объект для проверки подлинности. В этом примере показано, как этот механизм может быть использован для работы с Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Удаление удостоверения

Чтобы удалить назначаемое системой удостоверение, нужно отключить возможность с помощью портала, PowerShell или интерфейса командной строки точно так же, как и при его создании. Назначаемые пользователем удостоверения можно удалить по отдельности. Чтобы удалить все идентификаторы, установите тип "Нет" в [шаблоне ARM:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

Такое удаление назначаемого системой удостоверения приводит к удалению из AAD. Назначаемые системой удостоверения также удаляются из AAD автоматически, когда удаляется ресурс приложения.

> [!NOTE]
> Также вы можете установить параметр приложения WEBSITE_DISABLE_MSI, который отключает локальную службу маркеров. Но при этом само удостоверение сохранится, а управляемое удостоверение будет отображаться как "включенное". По этой причине мы рекомендуем не использовать такой параметр.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Безопасное подключение Базы данных Azure SQL с использованием управляемого удостоверения службы](app-service-web-tutorial-connect-msi.md)

[Справочник Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
