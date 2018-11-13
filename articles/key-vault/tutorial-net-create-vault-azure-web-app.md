---
title: Руководство по использованию Azure Key Vault с веб-приложениями Azure в .NET | Документация Майкрософт
description: Руководство по настройке веб-приложения ASP.NET Core для считывания секрета из Key Vault
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 9cc22e158a9473b7b60f7e8bcb57174abc1fb8cc
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218558"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>Руководство по использованию Azure Key Vault с веб-приложениями Azure в .NET

Azure Key Vault помогает защитить секреты, такие как ключи API, строки подключения к базам данных, необходимые для доступа к приложениям, службам и ИТ-ресурсам.

В этом руководстве описано, как в веб-приложении Azure настроить чтение данных из Azure Key Vault с помощью управляемых удостоверений для ресурсов Azure. Это руководство основано на [веб-приложениях Azure](../app-service/app-service-web-overview.md). Далее вы узнаете:

> [!div class="checklist"]
> * Создать хранилище ключей.
> * сохранение секрета в хранилище ключей;
> * получение секрета из хранилища ключей;
> * создание веб-приложения Azure;
> * включение [управляемого удостоверения](../active-directory/managed-identities-azure-resources/overview.md) для веб-приложения;
> * предоставление разрешений, необходимых веб-приложению для чтения данных из хранилища ключей.
> * Запуск веб-приложения в Azure

Прежде чем мы продолжим, ознакомьтесь с [основными понятиями](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Предварительные требования

* Действия для ОС Windows.
  * [Пакет SDK для .NET Core 2.1 или более поздней версии](https://www.microsoft.com/net/download/windows).

* Для компьютеров Mac.
  * Дополнительные сведения см. в статье [Новые возможности Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).

* Для всех платформ.
  * Git ([скачать](https://git-scm.com/downloads)).
  * Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) версии 2.0.4 или более поздней. Он доступен для Windows, Mac и Linux.
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Что такое управляемое удостоверение службы, и как это работает?
 Прежде чем мы продолжим, давайте разберем MSI. Azure Key Vault может безопасно хранить учетные данные, чтобы их не было в коде, но чтобы получить их, необходимо пройти проверку подлинности в хранилище ключей Azure. Для проверки подлинности в Key Vault необходимы учетные данные! Классическая задача начальной загрузки. Используя магию Azure и Azure AD, MSI предоставляет "загрузочное удостоверение", что значительно упрощает начало работы.

Вот как это работает При включении MSI для службы Azure, такой как виртуальные машины, служба приложений или Функции, Azure создает [субъект-службу](key-vault-whatis.md#basic-concepts) для экземпляра службы в Azure Active Directory и вводит учетные данные для субъекта-службы в экземпляр службы. 

![MSI](media/MSI.png)

Далее код вызывает локальную службу метаданных, доступную в ресурсе Azure, для получения маркера доступа.
Код использует маркер доступа, который он получает от локального MSI_ENDPOINT для проверки подлинности в службе Azure Key Vault. 

Теперь давайте приступим к изучению руководства.

## <a name="log-in-to-azure"></a>Вход в Azure

Чтобы войти в Azure с помощью Azure CLI, введите следующую команду:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Выберите имя группы ресурсов для заполнителя.
В следующем примере создается группа ресурсов в регионе "Западная часть США":

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Созданная группа ресурсов будет использоваться далее в этой статье.

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Теперь создайте хранилище ключей в группе ресурсов, созданной на предыдущем шаге. Введите следующие сведения:

* Имя хранилища ключей: это должна быть строка, состоящая из 3–24 таких символов: 0–9, a–z, A–Z и -.
* Имя группы ресурсов.
* Расположение: **Западная часть США**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

На этом этапе любые операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="add-a-secret-to-the-key-vault"></a>Добавление секрета в хранилище ключей

Теперь мы добавим секрет, чтобы продемонстрировать этот процесс. Вы можете хранить здесь строки подключения SQL и прочие сведения, которые должны храниться безопасно, но быть доступны для приложения.

Введите следующие команды, чтобы создать секрет с именем **AppSecret** в хранилище ключей. Этот секрет будет хранить значение **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Чтобы просмотреть содержащееся в секрете значение в виде обычного текста, введите следующее:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Эта команда отображает секретные сведения, включая URI. Выполнив эти действия, вы сохраните в хранилище ключей секрет со значением URI. Запишите эти сведения. Они потребуются вам на следующем шаге.

## <a name="create-a-net-core-web-app"></a>Создайте веб-приложение .NET Core.

Ознакомьтесь с шагами, приведенными в этом [руководстве](../app-service/app-service-web-get-started-dotnet.md) для создания веб-приложения .NET Core и его **публикации** в Azure **или** смотрите видео ниже.
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Открытие и изменение решения

1. Выберите "Страницы > Файл аbout.cshtml.cs".
2. Установите эти 2 пакета NuGet.
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [Хранилище ключей](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Импортируйте следующий файл About.cshtml.cs

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. Код в классе AboutModel должен выглядеть следующим образом.
    ```
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

В главном меню Visual Studio 2017 выберите **Отладка** > **Запуск** без или с отладкой. Когда откроется браузер, перейдите в нем на страницу **About** (Сведения). Отображается значение **AppSecret**.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Включение управляемого удостоверения для веб-приложения

Azure Key Vault позволяет обеспечить безопасное хранение учетных данных, а также других ключей и секретов, но для их получения код должен выполнять аутентификацию в Key Vault. [Управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) упрощают решение этой задачи, предоставляя службам Azure автоматически управляемое удостоверение в Azure Active Directory (Azure AD). Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, включая Key Vault, не храня какие-либо учетные данные в коде.

1. Вернитесь в окно командной строки Azure.
2. Выполните команду assign-identity, чтобы создать удостоверение для этого приложения. 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   Обратите внимание, что необходимо заменить <YourAppName> именем опубликованного приложения в Azure. Например, если опубликованное приложение называлось MyAwesomeapp.azurewebsites.net, замените <YourAppName> на MyAwesomeapp.
 
 Выходные данные приведенной выше команды выглядят следующим образом. Запишите PrincipalId при публикации приложения в Azure. Они должны быть представлены в следующем формате.
   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
  ```
>[!NOTE]
>Команда в данной процедуре выполняет те же действия, что и **включение** параметра [Identity / System assigned](https://portal.azure.com) (Назначаемое системой удостоверение) в свойствах веб-приложения **на портале**.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Назначение разрешений приложению для чтения секретов из Key Vault
        
Затем выполните следующую команду, используя имя вашего хранилища ключей и значение **PrincipalId**:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>Публикация веб-приложения в Azure

Опубликуйте это приложение в Azure, чтобы увидеть его в качестве живого веб-приложения, а также убедиться, что вы можете получить значение секрета.

1. В Visual Studio выберите проект **key-vault-dotnet-core-quickstart**.
2. Выберите **Опубликовать** > **Начало**.
3. Нажмите кнопку **Создать**.

В приведенной выше команде вы предоставляете удостоверению (MSI) разрешения службы приложений на операции **get** и **list** в Key Vault. <br />
Теперь при запуске приложения должно отображаться извлеченное значение секрета. 

Вот и все! Теперь вы успешно создали веб-приложение .NET, которое хранит и извлекает секреты из Key Vault.
