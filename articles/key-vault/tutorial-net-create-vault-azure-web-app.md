---
title: Руководство. Использование Azure Key Vault в веб-приложении Azure (.NET) — Azure Key Vault | Документация Майкрософт
description: Руководство. Настройка веб-приложения ASP.NET Core для считывания секрета из Key Vault
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: b6dbae0f721983920c2073927fff74100528678e
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998801"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Руководство. Использование Azure Key Vault в веб-приложении Azure (.NET)

Служба Azure Key Vault помогает защищать секреты, например ключи API и строки подключения к базе данных. Она позволяет осуществлять доступ к приложениям, службам и ИТ-ресурсам.

В этом руководстве описано, как создать веб-приложение Azure, которое может считывать информацию из хранилища ключей Azure. При этом используются управляемые удостоверения для ресурсов Azure. См. дополнительные сведения о [Службе приложений Azure](../app-service/overview.md).

В этой статье описаны следующие операции:

> [!div class="checklist"]
> * Создать хранилище ключей.
> * сохранение секрета в хранилище ключей;
> * получение секрета из хранилища ключей;
> * создание веб-приложения Azure;
> * включение [управляемого удостоверения](../active-directory/managed-identities-azure-resources/overview.md) для веб-приложения;
> * предоставление разрешений, необходимых веб-приложению для чтения данных из хранилища ключей.
> * запуск веб-приложения в Azure.

Прежде чем продолжить, ознакомьтесь с [основными понятиями службы Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Предварительные требования

* Действия для ОС Windows.
  * [Пакет SDK для .NET Core 2.1 или более поздней версии](https://www.microsoft.com/net/download/windows).

* Для компьютеров Mac.
  * [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/)

* Для всех платформ.
  * [Git](https://git-scm.com/downloads)
  * Подписка Azure <br />(Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.)
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) версии 2.0.4 и выше для Windows, Mac или Linux
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="managed-service-identity-and-how-it-works"></a>Управляемое удостоверение службы и принцип его работы

Azure Key Vault надежно хранит учетные данные, исключая их передачу в код. Но для получения ключей нужно выполнить проверку подлинности в Azure Key Vault. А для проверки подлинности в Key Vault требуются учетные данные. Это классическая дилемма. Управляемое удостоверение службы (MSI) решает эту проблему, предоставляя _соответствующее удостоверение_, которое упрощает процесс.

Когда вы включаете MSI для службы Azure (например, Виртуальные машины, Службу приложений или Функции), в Azure создается [субъект-служба](key-vault-whatis.md#basic-concepts). MSI делает это для экземпляра службы в Azure Active Directory (Azure AD) и добавляет учетные данные для субъекта-службы в этот экземпляр.

![Схема MSI](media/MSI.png)

Далее код вызывает локальную службу метаданных, доступную в ресурсе Azure, чтобы получить маркер доступа. Код использует маркер доступа, который он получает от локального MSI_ENDPOINT для проверки подлинности в службе Azure Key Vault.

## <a name="sign-in-to-azure"></a>Вход в Azure

Чтобы войти в Azure с помощью Azure CLI, введите следующую команду:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

1. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create).
1. Выберите имя группы ресурсов для заполнителя. В следующем примере создается группа ресурсов в регионе "Западная часть США":

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Работая с этим руководством, вы будете использовать созданную группу ресурсов.

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Чтобы создать хранилище ключей в группе ресурсов, укажите следующие сведения:

* Имя хранилища ключей: строка длиной от 3 до 24 символов, которая может содержать только цифры, буквы и дефисы (например, 0–9, a–z, A–Z, и - ).
* Имя группы ресурсов
* Расположение. **Западная часть США**

Выполните следующую команду в Azure CLI:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

На этом этапе любые операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="add-a-secret-to-the-key-vault"></a>Добавление секрета в хранилище ключей

Теперь можно добавить секрет. Это может быть строка подключения с серверу SQL или другие сведения, которые должны быть защищены и при этом доступны приложению.

Введите следующую команду, чтобы создать секрет с именем **AppSecret** в хранилище ключей. В этом секрете хранится значение **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Для просмотра хранящегося в секрете значения в виде обычного текста введите следующую команду:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Эта команда отображает секретные сведения, включая URI. Выполнив эти действия, вы сохраните в хранилище ключей секрет со значением URI. Запишите эти сведения. Они потребуются вам на следующем шаге.

## <a name="create-a-net-core-web-app"></a>Создание веб-приложения .NET Core

Выполните описанные действия, чтобы [создать](../app-service/app-service-web-get-started-dotnet.md) веб-приложение .NET Core и **опубликовать** его в Azure. Вы также можете просмотреть это видео:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Открытие и изменение решения

1. Откройте файл **Pages** > **About.cshtml.cs**.
2. Установите следующие пакеты NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [Хранилище ключей](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Импортируйте следующий код в файл About.cshtml.cs:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

4. Код класса AboutModel должен выглядеть следующим образом:

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
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-app"></a>Запуск приложения

1. В главном меню Visual Studio 2017 выберите **Отладка** > **Запуск** (с отладкой или без нее). 
1. Когда откроется браузер, перейдите в нем на страницу **About** (Сведения).
1. Отображается значение **AppSecret**.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Включение управляемого удостоверения для веб-приложения

Azure Key Vault позволяет безопасно хранить учетные данные и другие секреты, но для их получения код должен выполнять аутентификацию в Key Vault. Руководство по [управляемым удостоверениям для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) поможет решить эту проблему путем предоставления службам Azure автоматически управляемого удостоверения в Azure AD. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, включая Key Vault, не храня какие-либо учетные данные в коде.

1. В Azure CLI выполните команду assign-identity, чтобы создать удостоверение для приложения:

   ```azurecli

   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"

   ```

   >[!NOTE]
   >Нужно заменить \<YourAppName\> именем приложения, опубликованного в Azure. Например, если имя опубликованного приложения — **MyAwesomeapp.azurewebsites.net**, измените \<YourAppName\> на **MyAwesomeapp**.

1. При публикации приложения в Azure запишите значение `PrincipalId`. Выходные данные команды, выполненной на шаге 1, должны иметь следующий формат:

   ```json
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
   ```

>[!NOTE]
>Команда в данной процедуре выполняет те же действия, что и **включение** параметра [Identity / System assigned](https://portal.azure.com) (Назначаемое системой удостоверение) в свойствах веб-приложения **на портале**.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Назначение разрешений приложению для чтения секретов из Key Vault

В следующей команде замените \<YourKeyVaultName\> именем хранилища ключей и \<PrincipalId\> значением **PrincipalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Эта команда предоставляет удостоверению (MSI) службы приложений разрешения на выполнение операций **get** и **list** в хранилище ключей.

## <a name="publish-the-web-application-to-azure"></a>Публикация веб-приложения в Azure

Снова опубликуйте свое веб-приложение в Azure, чтобы убедиться, что работающее веб-приложение может получить значение секрета.

1. В Visual Studio выберите проект **key-vault-dotnet-core-quickstart**.
2. Выберите **Опубликовать** > **Начало**.
3. Нажмите кнопку **Создать**.

После запуска приложения вы должны увидеть, что оно может получать значение секрета.

Вы успешно создали веб-приложение .NET, которое хранит секреты в Key Vault и получает их оттуда.

## <a name="next-steps"></a>Дополнительная информация

>[!div class="nextstepaction"]
>[Руководство разработчика хранилища ключей Azure](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
