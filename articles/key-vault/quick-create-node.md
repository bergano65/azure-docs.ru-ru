---
title: Краткое руководство по использованию клиентской библиотеки Azure Key Vault для Node.js (версии 4)
description: Сведения о создании, извлечении и удалении секретов в Azure Key Vault с помощью клиентской библиотеки Node.js.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e36e3ae9ce2e8ca116921de428b4dcc09b37afb6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978583"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>Краткое руководство. Использование клиентской библиотеки Azure Key Vault для Node.js (версии 4)

Приступите к работе с клиентской библиотекой Azure Key Vault для Node.js. Чтобы установить пакет и испробовать пример кода для выполнения базовых задач, выполните описанные ниже шаги.

Хранилище ключей Azure помогает защитить криптографические ключи и секреты, используемые облачными приложениями и службами. Клиентская библиотека Azure Key Vault для Node.js позволяет выполнять следующие задачи:

- Повысьте уровень безопасности и увеличьте контроль над ключами и паролями.
- Создавайте и импортируйте ключи шифрования за считаные минуты.
- Сократите время задержки с помощью масштабирования в облаке и глобального резервирования.
- Упрощение и автоматизация задач, связанных с SSL- и TLS-сертификатами.
- Используйте модули HSM, отвечающие стандартам FIPS 140-2 уровня 2.

[Справочная документация по API](/javascript/api/overview/azure/key-vault?view=azure-node-latest) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Пакет (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets).

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Последняя версия [Node.js](https://nodejs.org) для вашей операционной системы.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/overview).

В этом кратком руководстве предполагается, что вы используете [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) в окне терминала Linux.

## <a name="setting-up"></a>Настройка

### <a name="install-the-package"></a>Установка пакета

Через окно консоли установите клиентскую библиотеку секретов Azure Key Vault для Node.js.

```console
npm install @azure/keyvault-secrets
```

Для этого краткого руководства вам также понадобится установить пакет azure.identity.

```console
npm install @azure/identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Создание группы ресурсов и хранилища ключей

В этом кратком руководстве используется предварительно созданное хранилище ключей Azure. Хранилище ключей можно создать, выполнив действия, описанные в кратких руководствах по [Azure CLI](quick-create-cli.md), [Azure PowerShell](quick-create-powershell.md) или [порталу Azure](quick-create-portal.md). Можно также выполнить команду Azure CLI, которая предложена ниже:

> [!Important]
> Каждое хранилище ключей должно иметь уникальное имя. В следующих примерах замените <your-unique-keyvault-name> именем своего хранилища ключей.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Создание субъекта-службы

Самый простой способ проверить подлинность облачного приложения — с помощью управляемых удостоверений. Дополнительные сведения см. в статье [Provide Key Vault authentication with a managed identity](managed-identity.md) (Предоставление проверки подлинности Key Vault с помощью управляемого удостоверения). Но для простоты в этом кратком руководстве создается консольное приложение. Для проверки подлинности в Azure приложение рабочего стола должно использовать субъект-службу и политику управления доступом.

Создайте субъект-службу с помощью команды Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac):

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Эта операция возвращает ряд пар "ключ — значение". 

```azurecli
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Запишите значения clientId и clientSecret, так как они нам потребуются далее на шаге [настройки переменных среды](#set-environmental-variables).

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Предоставление субъекту-службе доступа к хранилищу ключей

Создайте политику доступа для хранилища ключей, которая предоставляет разрешение субъекту-службе, передавая clientId в команду [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Предоставьте субъекту-службе разрешения get, list и set для ключей и секретов.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Настройка переменных среды

Метод DefaultAzureCredential в нашем приложении использует три переменные среды: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` и `AZURE_TENANT_ID`. Присвойте этим переменным значения clientId, clientSecret и tenantId, записанные на шаге [создания субъекта-службы](#create-a-service-principal), используя формат `export VARNAME=VALUE`. (Здесь задаются только переменные для текущей оболочки и процессов, созданных в этой оболочке. Чтобы добавить эти переменные в среду на постоянной основе, измените файл `/etc/environment `.) 

Кроме того, необходимо будет сохранить имя хранилища ключей как переменную среды `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Объектная модель

Клиентская библиотека Azure Key Vault для Node.js позволяет управлять ключами и связанными с ними ресурсами, такими как сертификаты и секреты. В приведенных ниже примерах кода показано, как создать клиент, а также как задать, извлечь и удалить секрет.

Полный текст консольного приложения доступен по адресу https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Примеры кода

### <a name="add-directives"></a>Добавление директив

Добавьте в верхнюю часть кода следующие директивы:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Аутентификация и создание клиента

Проверка подлинности в хранилище ключей и создание клиента хранилища ключей зависят от переменных среды, указанных на шаге [настройки переменные среды](#set-environmental-variables), и [конструктора SecretClient](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-). 

Имя хранилища ключей расширяется до универсального кода ресурса (URI) хранилища ключей в формате `https://<your-key-vault-name>.vault.azure.net`. 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Сохранение секрета

Теперь, когда приложение прошло проверку подлинности, вы можете разместить секрет в хранилище ключей с помощью [метода client.setSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-). Для этого нужно указать имя секрета — в данном примере это mySecret.  

```javascript
await client.setSecret(secretName, secretValue);
```

Команда [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) позволяет убедиться, что секрет задан успешно:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Получение секрета

Теперь вы можете получить ранее заданное значение с помощью [метода client.getSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Секрет теперь сохраняется как `retrievedSecret.value`.

### <a name="delete-a-secret"></a>Удаление секрета.

Наконец, давайте удалим секрет из хранилища ключей с помощью [метода client.beginDeleteSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-).

```javascript
await client.beginDeleteSecret(secretName)
```

Команда [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) позволяет убедиться, что секрет успешно удален.

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы больше не нужны, вы можете использовать интерфейс командной строки Azure или Azure PowerShell, чтобы удалить хранилище ключей и соответствующую группу ресурсов.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Пример кода

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали хранилище ключей, сохранили в нем секрет и извлекли его. Дополнительные сведения о Key Vault и его интеграции в приложения см. в следующих статьях.

- [Обзор Azure Key Vault](key-vault-overview.md)
- [Руководство разработчика Azure Key Vault](key-vault-developers-guide.md)
- Сведения о [ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md)
- [Рекомендации по Azure Key Vault](key-vault-best-practices.md)