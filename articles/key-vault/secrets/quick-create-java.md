---
title: Краткое руководство по использованию клиентской библиотеки Azure Key Vault для Java
description: Сведения о критериях, применяемых к формату и содержимому для создания кратких руководств по клиентским библиотекам Azure SDK.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 6c29141a2e255588ffa581b84ffeb4ddd7fdb703
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324715"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Краткое руководство. Клиентская библиотека Azure Key Vault для Java

Начало работы с клиентской библиотекой Azure Key Vault для Java. Чтобы установить пакет и испробовать пример кода для выполнения базовых задач, выполните описанные ниже шаги.

Хранилище ключей Azure помогает защитить криптографические ключи и секреты, используемые облачными приложениями и службами. Клиентская библиотека Azure Key Vault для Java позволяет выполнять следующие задачи:

- Повысьте уровень безопасности и увеличьте контроль над ключами и паролями.
- Создавайте и импортируйте ключи шифрования за считаные минуты.
- Сократите время задержки с помощью масштабирования в облаке и глобального резервирования.
- Упрощение и автоматизация задач, связанных с TLS- и SSL-сертификатами.
- Используйте модули HSM, отвечающие стандартам FIPS 140-2 уровня 2.

Дополнительные ресурсы:

* [Исходный код](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault).
* [Справочная документация по API](https://azure.github.io/azure-sdk-for-java)
* [Документация по продукту](index.yml)
* [Примеры](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Пакет разработчиков Java (JDK)](/java/azure/jdk/?view=azure-java-stable) версии 8 или более поздней версии.
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/).

В этом кратком руководстве предполагается, что вы используете [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) и [Apache Maven](https://maven.apache.org) в окне терминала Linux.

## <a name="setting-up"></a>Настройка

### <a name="create-new-java-console-app"></a>Создание консольного приложения Java

В окне консоли выполните команду `mvn`, чтобы создать консольное приложение Java с именем `akv-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Примерный результат создания проекта показан ниже:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Измените каталог на созданную папку akv-java/.

```console
cd akv-java
```

### <a name="install-the-package"></a>Установка пакета

Откройте файл *pom.xml* в текстовом редакторе. Добавьте приведенные ниже элементы зависимости в группу зависимостей.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.0.0</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Создание группы ресурсов и хранилища ключей

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Создание субъекта-службы

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Предоставление субъекту-службе доступа к хранилищу ключей

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Настройка переменных среды

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Объектная модель

Клиентская библиотека Azure Key Vault для Java позволяет управлять ключами и связанными с ними ресурсами, такими как сертификаты и секреты. В приведенных ниже примерах кода показано, как создать клиент, а также как задать, извлечь и удалить секрет.

Полная версия консольного приложения представлена [ниже](#sample-code).

## <a name="code-examples"></a>Примеры кода

### <a name="add-directives"></a>Добавление директив

Добавьте в верхнюю часть кода следующие директивы:

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Аутентификация и создание клиента

Аутентификация в хранилище ключей и создание клиента хранилища ключей зависят от переменных среды, приведенных на шаге [настройки переменные среды](#set-environmental-variables) выше. Имя хранилища ключей расширяется до универсального кода ресурса (URI) хранилища ключей в формате `https://<your-key-vault-name>.vault.azure.net`.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Сохранение секрета

Теперь, когда приложение прошло проверку подлинности, можно добавить секрет в хранилище ключей с помощью метода `secretClient.setSecret`. Для этого требуется имя секрета. Мы присвоили значение mySecret переменной `secretName` в этом примере.  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Команда [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) позволяет убедиться, что секрет задан успешно:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Получение секрета

Теперь вы можете получить ранее заданное значение с помощью метода `secretClient.getSecret`.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Теперь можно получить доступ к значению полученного секрета с помощью `retrievedSecret.getValue()`.

### <a name="delete-a-secret"></a>Удаление секрета.

Наконец, давайте удалим секрет из хранилища ключей с помощью метода `secretClient.beginDeleteSecret`.

```java
secretClient.beginDeleteSecret(secretName);
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

## <a name="sample-code"></a>Образец кода

```java
package com.keyvault.quickstart;

import java.io.Console;   

import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;

public class App {

    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String kvUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, kvUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(kvUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();


        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Input the value of your secret > ");
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");

        System.out.println("Forgetting your secret.");
        secretValue = "";
        System.out.println("Your secret is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        secretClient.beginDeleteSecret(secretName);

        System.out.println("done.");


    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали хранилище ключей, сохранили в нем секрет и извлекли его. Дополнительные сведения о Key Vault и его интеграции в приложения см. в следующих статьях.

- [Обзор Azure Key Vault](../general/overview.md)
- [Руководство разработчика Azure Key Vault](../general/developers-guide.md)
- [Рекомендации по Azure Key Vault](../general/best-practices.md)
