---
title: Руководство. Использование Azure Key Vault в веб-приложении Azure (.NET) | Документация Майкрософт
description: Благодаря этому руководству вы настроите приложение ASP.NET Core для считывания секрета из хранилища ключей.
services: key-vault
author: mbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 42c8d863a58c5f5d8f47f6686aa9a5b8f80277d2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64710502"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Руководство по Использование Azure Key Vault в веб-приложении Azure (.NET)

Служба Azure Key Vault помогает защищать секреты, например ключи API и строки подключения к базе данных. Она позволяет осуществлять доступ к приложениям, службам и ИТ-ресурсам.

В этом руководстве описано, как создать веб-приложение Azure, которое может считывать информацию из хранилища ключей Azure. При этом используются управляемые удостоверения для ресурсов Azure. См. дополнительные сведения о [Службе приложений Azure](../app-service/overview.md).

В этом руководстве описаны следующие процедуры.

> [!div class="checklist"]
> * Создать хранилище ключей.
> * Добавление секрета в хранилище ключей.
> * получение секрета из хранилища ключей;
> * создание веб-приложения Azure;
> * включение управляемого удостоверения для веб-приложения;
> * назначение разрешения для веб-приложения;
> * запуск веб-приложения в Azure.

Перед началом работы ознакомьтесь с [основными понятиями службы Key Vault](key-vault-whatis.md#basic-concepts). 

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Предварительные требования

* Для Windows: [пакет SDK для .NET Core 2.1 или более поздней версии](https://www.microsoft.com/net/download/windows).
* Для Mac: [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/)
* Для Windows, Mac и Linux:
  * [Git](https://git-scm.com/downloads)
  * Для этого руководства требуется запустить Azure CLI локально. Необходимо установить Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо установить или обновить CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Основные сведения об MSI

Azure Key Vault надежно хранит учетные данные, исключая их отображение в коде. Но для получения ключей нужно выполнить проверку подлинности в Azure Key Vault. А для проверки подлинности в Key Vault требуются учетные данные. Это классическая дилемма. Управляемое удостоверение службы (MSI) решает эту проблему, предоставляя _соответствующее удостоверение_, которое упрощает процесс.

При включении MSI для службы Azure (например, виртуальных машин, Службы приложений Azure или Функций Azure), Azure создает [субъект-службу](key-vault-whatis.md#basic-concepts). MSI делает это для экземпляра службы в Azure Active Directory (Azure AD) и добавляет учетные данные субъекта-службы в этот экземпляр.

![Схема MSI](media/MSI.png)

Далее, чтобы получить маркер доступа, код вызывает локальную службу метаданных, доступную в ресурсе Azure. Код использует маркер доступа, который получает от локальной конечной точки MSI для проверки подлинности в службе Azure Key Vault.

## <a name="log-in-to-azure"></a>Вход в Azure

Чтобы войти в Azure с помощью Azure CLI, введите следующую команду:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create).

Затем выберите имя группы ресурсов для заполнителя. В следующем примере создается группа ресурсов в регионе "Западная часть США":

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Работая с этим руководством, вы будете использовать созданную группу ресурсов.

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Чтобы создать хранилище ключей в группе ресурсов, укажите следующие сведения:

* Имя хранилища ключей: строка длиной от 3 до 24 символов, которая может содержать только цифры (0–9), буквы (a-z, A-Z) и дефисы (-).
* Имя группы ресурсов
* Расположение. **Западная часть США**

В интерфейсе командной строки Azure введите следующую команду.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

На этом этапе операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="add-a-secret-to-the-key-vault"></a>Добавление секрета в хранилище ключей

Теперь можно добавить секрет. Это может быть строка подключения с серверу SQL или другие сведения, которые должны быть защищены и при этом доступны приложению.

Введите следующую команду, чтобы создать секрет с именем **AppSecret** в хранилище ключей. 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

В этом секрете хранится значение **MySecret**.

Для просмотра хранящегося в секрете значения в виде обычного текста введите следующую команду.

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Эта команда отображает секретные сведения, в том числе URI. 

Выполнив эти действия, вы сохраните в хранилище ключей секрет со значением URI. Запишите эти сведения для дальнейшего использования в этом руководстве. 

## <a name="create-a-net-core-web-app"></a>Создание веб-приложения .NET Core

Чтобы создать веб-приложение .NET Core и опубликовать его в Azure, следуйте инструкциям, указанным в руководстве [Создание веб-приложения ASP.NET Core в Azure](../app-service/app-service-web-get-started-dotnet.md). 

Вы также можете посмотреть этот видеоролик:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Открытие и изменение решения

1. Откройте файл **Pages** > **About.cshtml.cs**.

1. Установите следующие пакеты NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [Хранилище ключей](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Импортируйте следующий код в файл *About.cshtml.cs*.

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   Код в классе AboutModel должен выглядеть следующим образом.

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>Запуск веб-приложения

1. В главном меню Visual Studio 2017 выберите **Отладка** > **Запуск** (с отладкой или без нее). 
1. В браузере перейдите на страницу **О программе**.  
    Отображается значение **AppSecret**.

## <a name="enable-a-managed-identity"></a>Включение управляемого удостоверения

Azure Key Vault позволяет безопасно хранить учетные данные и другие секреты, но для их получения код должен выполнять аутентификацию в Key Vault. Руководство по [управляемым удостоверениям для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) поможет решить эту проблему путем предоставления службам Azure автоматически управляемого удостоверения в Azure AD. Вы можете использовать это удостоверение для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, включая Key Vault, не отображая учетные данные в коде.

В Azure CLI выполните команду assign-identity, чтобы создать удостоверение для приложения.

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Нужно заменить \<YourAppName> именем приложения, опубликованного в Azure.  
    Например, если имя опубликованного приложения — **MyAwesomeapp.azurewebsites.net**, измените \<YourAppName> на **MyAwesomeapp**.

При публикации приложения в Azure запишите значение `PrincipalId`. Выходные данные команды, выполненной на шаге 1, должны иметь следующий формат:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>Команда в данной процедуре выполняет те же действия, что и переход на [портал Azure](https://portal.azure.com) и включение параметра **Удостоверение/Назначено системой** в состояние **Включено** в свойствах веб-приложения.

## <a name="assign-permissions-to-your-app"></a>Назначение разрешений приложению

В следующей команде замените \<YourKeyVaultName> именем хранилища ключей, а \<PrincipalId> — значением **PrincipalId**.

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Эта команда предоставляет удостоверению (MSI) службы приложений разрешение на выполнение операций **get** и **list** в хранилище ключей.

## <a name="publish-the-web-app-to-azure"></a>Публикация веб-приложения в Azure

Снова опубликуйте свое веб-приложение в Azure, чтобы убедиться, что работающее веб-приложение может получить значение секрета.

1. В Visual Studio выберите проект **key-vault-dotnet-core-quickstart**.
2. Выберите **Опубликовать** > **Начало**.
3. Нажмите кнопку **Создать**.

После запуска приложения вы должны увидеть, что оно может получать значение секрета.

Теперь вы успешно создали веб-приложение .NET, которое хранит секреты в хранилище ключей и получает их оттуда.

## <a name="clean-up-resources"></a>Очистка ресурсов
Вы можете удалить виртуальную машину и хранилище ключей, если они больше не нужны.

## <a name="next-steps"></a>Дополнительная информация

>[!div class="nextstepaction"]
>[Руководство разработчика хранилища ключей Azure](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
