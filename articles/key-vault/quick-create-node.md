---
title: Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения Node | Документация Майкрософт
description: В этом кратком руководстве вы настроите и получите секрет из Azure Key Vault с помощью веб-приложения Node
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: quickstart
ms.date: 09/03/2010
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 4bea46e62f90a41b566781457a39718849ee0e15
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259239"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения Node 

В этом кратком руководстве описывается, как хранить секрет в Azure Key Vault и как его получать с помощью веб-приложения. С помощью Key Vault можно защитить данные. Чтобы просмотреть значение секрета, необходимо выполнить это краткое руководство в Azure. Это краткое руководство использует Node.js и управляемые удостоверения для ресурсов Azure. Вы узнаете, как выполнять следующие задачи:

* Создать хранилище ключей.
* сохранение секрета в хранилище ключей;
* получение секрета из хранилища ключей;
* создание веб-приложения Azure;
* включение [управляемого удостоверения](../active-directory/managed-service-identity/overview.md) для веб-приложения;
* предоставление разрешений, необходимых веб-приложению для чтения данных из хранилища ключей.

Прежде чем продолжить, убедитесь, что вы ознакомлены с [основными понятиями Key Vault](key-vault-whatis.md#basic-concepts).

> [!NOTE]
> Key Vault — это центральный репозиторий для хранения секретов программным способом. Но чтобы воспользоваться возможностями хранилища ключей, приложения и пользователи должны сначала пройти в нем аутентификацию, т. е. предоставить секрет. В соответствии с рекомендациями по безопасности первый секрет должен периодически меняться. 
>
> Благодаря [управляемым удостоверениям служб для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) приложения, работающие в Azure, получают удостоверение, которым автоматически управляет Azure. Это помогает устранить *проблему введения секрета*, чтобы пользователи и приложения могли следовать рекомендациям и не беспокоиться об изменении первого секрета.

## <a name="prerequisites"></a>Предварительные требования

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 или более поздней версии. Для этого краткого руководства требуется запустить Azure CLI локально. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо установить или обновить CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="log-in-to-azure"></a>Вход в Azure

Чтобы войти в Azure с помощью Azure CLI, введите следующую команду:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Выберите имя группы ресурсов для заполнителя.
В следующем примере создается группа ресурсов в регионе "Восточная часть США".

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Созданная группа ресурсов будет использоваться далее в этой статье.

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Теперь с помощью группы ресурсов, созданной на предыдущем шаге, создайте хранилище ключей. Хотя в этой статье в качестве имени используется ContosoKeyVault, вам необходимо использовать уникальное имя. Введите следующие сведения:

* Имя хранилища ключей.
* Имя группы ресурсов. это должна быть строка, состоящая из 3–24 символов, которые содержат: 0–9, a–z, A–Z и дефис (-).
* Расположение. **Восточная часть США**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

На этом этапе любые операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="add-a-secret-to-the-key-vault"></a>Добавление секрета в хранилище ключей

Теперь мы добавим секрет, чтобы продемонстрировать этот процесс. Вы можете хранить здесь строки подключения SQL и прочие сведения, которые должны храниться безопасно, но быть доступны для приложения. В этом руководстве мы сохраним пароль с именем **AppSecret** и значением **MySecret**.

Введите следующие команды, чтобы создать секрет с именем **AppSecret** в хранилище ключей. Этот секрет будет хранить значение **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Чтобы просмотреть содержащееся в секрете значение в виде обычного текста, введите следующее:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Эта команда отображает секретные сведения, включая URI. Выполнив эти действия, вы сохраните в хранилище ключей секрет со значением URI. Запишите эти сведения. Они потребуются вам на следующем шаге.

## <a name="clone-the-repo"></a>Клонирование репозитория

Клонируйте репозиторий, чтобы создать локальную копию, в которой можно изменить источник. Выполните следующую команду:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Установка зависимостей

Для установки зависимостей введите следующие команды.

```
cd key-vault-node-quickstart
npm install
```

В этом проекте используются два модуля Node: [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) и [azure-keyvault](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>Публикация веб-приложения в Azure

Создание плана [службы приложений Azure](https://azure.microsoft.com/services/app-service/). В этом плане можно хранить несколько веб-приложений.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Затем создайте веб-приложение. В следующем примере замените `<app_name>` глобальным уникальным именем приложения (допустимые символы: a-z, 0-9 и -). Для среды выполнения установлено значение NODE|6.9. Список всех поддерживаемых сред выполнения можно получить с помощью команды `az webapp list-runtimes`.

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:

    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
Перейдите к только что созданному веб-приложению. Оно должно работать. Замените `<app_name>` уникальным именем приложения.

    ```
    http://<app name>.azurewebsites.net
    ```
Предыдущая команда также создает приложение с поддержкой Git, что позволяет развертывать Azure из локального репозитория Git. Локальный репозиторий Git настроен с таким URL-адресом: `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`.

После выполнения предыдущей команды в локальный репозиторий Git можно добавить удаленное приложение Azure. Замените `<url>` на URL-адрес репозитория Git.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Включение управляемого удостоверения для веб-приложения

Azure Key Vault позволяет обеспечить безопасное хранение учетных данных, а также других ключей и секретов, но для их получения код должен выполнять аутентификацию в Key Vault. [Управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) упрощают решение этой задачи, предоставляя службам Azure автоматически управляемое удостоверение в Azure Active Directory (Azure AD). Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, включая Key Vault, не храня какие-либо учетные данные в коде.

Выполните команду assign-identity, чтобы создать удостоверение для этого приложения.

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Эта команда выполняет те же действия, что и **включение** параметра **назначаемого свойством удостоверения** в свойствах веб-приложения на портале.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Назначение разрешений приложению для чтения секретов из Key Vault

Обратите внимание на выходные данные предыдущей команды. Они должны быть представлены в следующем формате.
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Затем выполните следующую команду, используя имя вашего хранилища ключей и значение **principalId**.

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Развертывание приложения Node в Azure и получение секретного значения

Чтобы развернуть приложение в Azure, выполните следующую команду.

```
git push azure master
```

После этого вы можете перейти по адресу `https://<app_name>.azurewebsites.net`, чтобы просмотреть значение секрета. Убедитесь, что вы заменили имя `<YourKeyVaultName>` именем своего хранилища.

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы создали Key Vault и сохранили в нем секрет. Дополнительные сведения о Key Vault и его интеграции в приложения см. в следующих статьях.

- [Обзор Azure Key Vault](key-vault-overview.md)
- [Руководство разработчика Azure Key Vault](key-vault-developers-guide.md)
- Сведения о [ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md)
- [Рекомендации по Azure Key Vault](key-vault-best-practices.md)