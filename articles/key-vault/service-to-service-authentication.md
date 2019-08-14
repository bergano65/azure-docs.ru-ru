---
title: Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET
description: Использование библиотеки Microsoft.Azure.Services.AppAuthentication для выполнения проверки подлинности в Azure Key Vault с помощью .NET.
keywords: локальные учетные данные проверки подлинности azure key vault
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 07/06/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: f6a95f56b7b617b42c1cec9f64aae73b88b813da
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934336"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET

Для проверки подлинности в Azure Key Vault требуются учетные данные Azure Active Directory (AD) либо общий секрет, либо сертификат. 

Управление такими учетными данными может быть сложным. Очевидным решением может казаться объединить учетные данные в приложение, включив их в исходные данные или файлы конфигурации.  Пакет `Microsoft.Azure.Services.AppAuthentication` для библиотеки .NET упрощает эту проблему. Он использует учетные данные разработчика для выполнения проверки подлинности во время локальной разработки. Если решение позже развертывается в Azure, библиотека автоматически переключается на использование учетных данных приложения.    Использовать учетные данные разработчика во время локального развертывания более безопасно, так как вам не нужно создавать учетные данные Azure AD или совместно использовать учетные данные с другими разработчиками.

Библиотека `Microsoft.Azure.Services.AppAuthentication` автоматически управляет проверкой подлинности, которая, в свою очередь, позволяет сконцентрироваться на решении, а не на учетных данных.  Она поддерживает локальную разработку с использованием Microsoft Visual Studio, Azure CLI или встроенной проверки подлинности Azure AD. При развертывании в ресурс Azure, поддерживающий управляемое удостоверение, библиотека автоматически использует [управляемые удостоверения для ресурсов Azure](../active-directory/msi-overview.md). Изменение кода или конфигурации не требуется. Библиотека также поддерживает непосредственное использование [учетных данных клиента](../azure-resource-manager/resource-group-authenticate-service-principal.md) Azure AD, если управляемое удостоверение недоступно или когда контекст безопасности разработчика не удается определить во время локальной разработки.

## <a name="using-the-library"></a>Использование библиотеки

Для приложений .NET для работы с управляемым удостоверением проще всего использовать пакет `Microsoft.Azure.Services.AppAuthentication`. Узнайте, как начать работу.

1. Добавьте ссылки на пакеты NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) и [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) в приложение. 

2. Добавьте следующий код:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

Класс `AzureServiceTokenProvider` кэширует токен в памяти и извлекает его из Azure AD прямо перед истечением срока действия. Следовательно, вам больше не нужно проверять срок действия перед вызовом метода `GetAccessTokenAsync`. Просто вызовите метод, когда необходимо использовать токен. 

Для метода `GetAccessTokenAsync` требуется наличие идентификатора ресурса. Дополнительные сведения см. в статье об [управляемых удостоверениях для ресурсов Azure](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Проверка подлинности среды локальной разработки

Для локальной разработки существует два основных сценария проверки подлинности: проверка подлинности [в службах Azure](#authenticating-to-azure-services)и [Проверка подлинности пользовательских служб](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Проверка подлинности в службах Azure

Локальные компьютеры не поддерживают управляемые удостоверения для ресурсов Azure.  В результате библиотека `Microsoft.Azure.Services.AppAuthentication` использует учетные данные разработчика для запуска в среде локальной разработки. При развертывании решения в Azure библиотека использует управляемое удостоверение для переключения на поток предоставления учетных данных клиента OAuth 2.0.  Это значит, что вы можете без проблем тестировать один и тот же код локально и удаленно.

Для локальной разработки `AzureServiceTokenProvider` получает токены с помощью **Visual Studio**, **интерфейса командной строки Azure** (CLI) или **встроенной проверки подлинности Azure AD**. Каждый вариант применяется последовательно, и библиотека использует первый вариант, который завершается успешно. Если ни один вариант не работает, выводится исключение `AzureServiceTokenProviderException` с подробными сведениями.

### <a name="authenticating-with-visual-studio"></a>Проверка подлинности с помощью Visual Studio

Проверка подлинности в Visual Studio имеет следующие предварительные требования.

1. [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) или более поздней версии.

2. [Расширение проверки подлинности приложения для Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354), которое доступно как отдельное расширение для visual Studio 2017 с обновлением 5 и объединено с продуктом в обновлении 6 и более поздних версий. С обновлением 6 или более поздней версии можно проверить установку расширения проверки подлинности приложения, выбрав средства разработки Azure в установщике Visual Studio.
 
Войдите в Visual Studio и используйте **Сервис**&nbsp;&nbsp;>**Параметры**&nbsp;Проверкаподлинности**службы Azure** , чтобы выбрать учетную запись для локальной разработки.>&nbsp; 

Если возникли проблемы при использовании Visual Studio, например ошибки с файлом поставщика токенов, внимательно просмотрите указанные ниже шаги. 

Вам также может потребоваться повторно выполнить проверку подлинности токена разработчика. Для этого выберите **Сервис**&nbsp;>&nbsp;**Параметры**>**Azure&nbsp;Service&nbsp;Authentication** (Проверка подлинности в службе Azure) и найдите ссылку **Re-authenticate** (Повторно выполнить проверку подлинности) в выбранной учетной записи.  Щелкните ее, чтобы выполнить проверку подлинности. 

### <a name="authenticating-with-azure-cli"></a>Проверка подлинности с помощью Azure CLI

Чтобы использовать Azure CLI для локальной разработки, сделайте следующее:

1. Установите [Azure CLI версии 2.0.12](/cli/azure/install-azure-cli) или более поздней. Обновите более ранние версии. 

2. Выполните команду **az login** для входа в Azure.

Для проверки доступа выполните команду `az account get-access-token`.  Если появится ошибка, убедитесь в успешном завершении шага 1. 

Если Azure CLI не установлен в каталоге по умолчанию, вы можете получить ошибку, что `AzureServiceTokenProvider` не может найти путь для Azure CLI.  Используйте переменную среды **азуреклипас** , чтобы определить папку установки Azure CLI. При необходимости `AzureServiceTokenProvider` добавляет каталог, указанный в переменной среды **AzureCLIPath**, в переменную среды **Path**.

Если вы вошли в Azure CLI с помощью нескольких учетных записей или учетная запись имеет доступ к нескольким подпискам, необходимо указать конкретную подписку для использования.  Для этого выполните следующую команду:

```
az account set --subscription <subscription-id>
```

Эта команда создает выходные данные только при сбое.  Чтобы проверить параметры текущей учетной записи, выполните следующую команду:

```
az account list
```

### <a name="authenticating-with-azure-ad-authentication"></a>Проверка подлинности с помощью аутентификации Azure AD

Чтобы использовать проверку подлинности Azure AD, убедитесь, что:

- локальная служба Active Directory [синхронизируется с Azure AD](../active-directory/connect/active-directory-aadconnect.md);

- код выполняется на компьютере, присоединенном к домену.


### <a name="authenticating-to-custom-services"></a>Проверка подлинности в пользовательских службах

Когда служба вызывает службы Azure, можно выполнить предыдущие действия, так как службы Azure предоставляют доступ пользователям и приложениям.  

При создании службы, которая вызывает пользовательскую службу, выполните проверку подлинности среды локальной разработки с помощью учетных данных клиента Azure AD.  Существуют два варианта: 

1.  Войдите в Azure, используя субъект-службу:

    1.  [Создайте субъект-службу](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Выполните вход с помощью Azure CLI:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Так как субъект-служба может не иметь доступа к подписке, используйте аргумент `--allow-no-subscriptions`.

2.  Укажите сведения о субъекте-службе с помощью переменных среды.  Дополнительные сведения см. в разделе [Запуск приложения с помощью переменной среды](#running-the-application-using-a-service-principal).

После входа в Azure `AzureServiceTokenProvider` использует субъект-службу, чтобы извлечь токен для локальной разработки.

Это относится только к локальной разработке. При развертывании решения в Azure библиотека переключается на выполнение проверки подлинности с использованием управляемого удостоверения.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Запуск приложения с использованием управляемого удостоверения или удостоверения, назначенного пользователем 

При выполнении кода в Службе приложений Azure или на виртуальной машине Azure с включенным управляемым удостоверением библиотека автоматически использует это удостоверение. 

Кроме того, вы можете пройти проверку подлинности с помощью назначенного пользователем удостоверения. Дополнительные сведения о назначенных пользователем удостоверениях см. в статье [об управляемых удостоверениях для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Чтобы выполнить проверку подлинности с помощью назначенного пользователем удостоверения, необходимо указать идентификатор клиента, которому назначено пользовательское удостоверение, в строке подключения. Строка подключения указывается в разделе [Поддержка строки подключения](#connection-string-support) ниже.

## <a name="running-the-application-using-a-service-principal"></a>Запуск приложения с помощью субъекта-службы 

Возможно, может понадобиться создать учетные данные клиента Azure AD для проверки подлинности. Ниже приведены распространенные примеры.

- Ваш код выполняется в среде локальной разработки, но не использует удостоверение разработчика.  Service Fabric, например, использует [учетную запись NetworkService](../service-fabric/service-fabric-application-secret-management.md) для локальной разработки.
 
- Ваш код выполняется в среде локальной разработки и выполняется проверка подлинности в пользовательской службе, поэтому вы не можете использовать удостоверение разработчика. 
 
- Ваш код выполняется в вычислительном ресурсе Azure, который еще не поддерживает управляемые удостоверения для ресурсов Azure, например в пакетной службе Azure.

Существует три основных способа использования субъекта-службы для запуска приложения. Чтобы использовать их, необходимо сначала [создать субъект-службу](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Использование сертификата в локальном хранилище ключей для входа в Azure AD

1. Создайте сертификат субъекта-службы с помощью команды Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) . 

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    При этом будет создан PEM-файл (закрытый ключ), который будет храниться в домашнем каталоге. Разверните этот сертификат в хранилище *LocalMachine* или *CurrentUser* . 

    > [!Important]
    > Команда CLI создает PEM-файл, но Windows обеспечивает только собственную поддержку сертификатов PFX. Чтобы создать PFX-сертификат, используйте команды PowerShell, показанные здесь: [Создайте субъект-службу с самозаверяющим сертификатом](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Эти команды также автоматически развертывают сертификат.

1. Задайте переменную среды с именем **AzureServicesAuthConnectionString**, как показано в следующем примере:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Замените *{AppId}* , *{TenantId}* и *{Thumbprint}* идентификатором приложения, идентификатором клиента и отпечатком, созданными на шаге 1. Замените *{CertificateStore}* на `LocalMachine` или `CurrentUser` в зависимости от плана развертывания.

1. Запустите приложение. 

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Использование учетных данных общего секрета для входа в Azure AD

1. Создайте сертификат субъекта-службы с паролем, выполнив команду [AZ AD SP Create-for-RBAC--Password](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac). 

2. Задайте переменную среды с именем **AzureServicesAuthConnectionString**, как показано в следующем примере:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Замените _{AppId}_ , _{TenantId}_ и _{ClientSecret}_ идентификатором приложения, идентификатором клиента и секретом клиента, созданными на шаге 1.

3. Запустите приложение. 

Если установка выполнена правильно, дальнейшие изменения в коде не требуются.  `AzureServiceTokenProvider` использует переменную среды и сертификат для выполнения проверки подлинности в Azure AD. 

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Использование сертификата в Key Vault для входа в Azure AD

Этот параметр позволяет хранить сертификат клиента субъекта-службы в Key Vault и использовать его для проверки подлинности субъекта-службы. Это можно использовать в следующих сценариях:

* Локальная проверка подлинности, в которой вы хотите пройти проверку подлинности с помощью явного субъекта-службы и хотите безопасно хранить учетные данные субъекта-службы в хранилище ключей. Учетная запись разработчика должна иметь доступ к хранилищу ключей. 
* Аутентификация из Azure, где вы хотите использовать явные учетные данные (например, для сценариев с несколькими клиентами) и хотите безопасно хранить учетные данные субъекта-службы в хранилище ключей. Управляемое удостоверение должно иметь доступ к хранилищу ключей. 

Управляемое удостоверение или удостоверение разработчика должно иметь разрешение на получение сертификата клиента из Key Vault. Библиотека AppAuthentication использует полученный сертификат в качестве учетных данных клиента субъекта-службы.

Использование сертификата клиента для проверки подлинности субъекта-службы

1. Создайте сертификат субъекта-службы и автоматически сохраните его в keyvault с помощью команды Azure CLI [AZ AD SP Create-for-RBAC-- <keyvaultname> keyvault-- <certificatename> CERT--Create-CERT--Skip-присваивания](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```
    
    Идентификатор сертификата будет URL-адресом в формате`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Замените `{KeyVaultCertificateSecretIdentifier}` в этой строке подключения на идентификатор сертификата:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Если, например, хранилище ключей называлось "myKeyVault" и создан сертификат с именем "myCert", идентификатор сертификата будет выглядеть следующим образом:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```


## <a name="connection-string-support"></a>Поддержка строки подключения

По умолчанию `AzureServiceTokenProvider` использует несколько способов извлечения токена. 

Для управления процессом используйте строку подключения, переданную в конструктор `AzureServiceTokenProvider` или указанную в переменной среды *AzureServicesAuthConnectionString*. 

Поддерживаются следующие варианты.

| Параметр строки подключения | Сценарий | Комментарии|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Локальная разработка | AzureServiceTokenProvider использует Azure CLI для получения токена. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Локальная разработка | AzureServiceTokenProvider использует Visual Studio для получения токена. |
| `RunAs=CurrentUser` | Локальная разработка | AzureServiceTokenProvider использует встроенную проверку подлинности Azure AD для получения токена. |
| `RunAs=App` | [Управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider использует управляемое удостоверение для получения токена. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Удостоверение, назначенное пользователем для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | AzureServiceTokenProvider использует назначенное пользователем удостоверение для получения маркера. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Проверка подлинности настраиваемых служб | Кэйваултцертификатесекретидентифиер = идентификатор секрета сертификата. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Субъект-служба | `AzureServiceTokenProvider` использует сертификат для получения токена из Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Субъект-служба | `AzureServiceTokenProvider` использует сертификат для получения токена из Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Субъект-служба |`AzureServiceTokenProvider` использует секрет для получения токена из Azure AD. |

## <a name="samples"></a>Примеры

Чтобы просмотреть `Microsoft.Azure.Services.AppAuthentication` библиотеку в действии, обратитесь к следующим примерам кода.

1. [Извлечение секрета из Azure Key Vault во время выполнения с помощью управляемого удостоверения](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Развертывание программным способом шаблона Azure Resource Manager из виртуальной машины Azure с помощью управляемого удостоверения](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)

3. [Вызов служб Azure из виртуальной машины Azure под управлением Linux с использованием примера .NET Core и управляемого удостоверения](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)

## <a name="appauthentication-troubleshooting"></a>Устранение неполадок AppAuthentication

### <a name="common-issues-during-local-development"></a>Распространенные проблемы при локальной разработке

#### <a name="azure-cli-is-not-installed-you-are-not-logged-in-or-you-do-not-have-the-latest-version"></a>Azure CLI не установлен, вы не вошли в систему или у вас не установлена последняя версия

Выполните команду **AZ Account Get-Access-Token** , чтобы узнать, покажет ли Azure CLI маркер. Если это не говорит о том, что такая программа найдена, установите [последнюю версию Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Если вы установили его, может появиться запрос на вход. 
 
#### <a name="azureservicetokenprovider-cannot-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider не удается найти путь для Azure CLI

AzureServiceTokenProvider ищет Azure CLI в его расположениях установки по умолчанию. Если не удается найти Azure CLI, укажите в качестве значения переменной среды **азуреклипас** папку установки Azure CLI. AzureServiceTokenProvider добавит переменную среды в переменную среды PATH.
 
#### <a name="you-are-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Вы выполнили вход в Azure CLI с использованием нескольких учетных записей, одна учетная запись имеет доступ к подпискам в нескольких клиентах или при попытке вызвать вызовы во время локальной разработки возникает ошибка отказа в доступе.

Используя Azure CLI, задайте подписку по умолчанию, которая имеет учетную запись, которую вы хотите использовать, и укажите в том же клиенте, что и ресурс, к которому вы хотите получить доступ: **AZ Account Set--Subscription [Subscription-ID]** . Если выходные данные не отображаются, то она была успешной. Убедитесь, что правильная учетная запись теперь используется по умолчанию с помощью команды **AZ Account List**.

### <a name="common-issues-across-environments"></a>Распространенные проблемы в средах

#### <a name="unauthorized-access-access-denied-forbidden-etc-error"></a>Несанкционированный доступ, отказ в доступе, запрещение и т. д. ошибка
 
Используемый участник не имеет доступа к ресурсу, к которому он пытается получить доступ. Предоставьте учетной записи пользователя или MSI "участник" службы приложений доступ к нужному ресурсу в зависимости от того, выполняется ли пример на локальном компьютере разработки или он развернут в Azure в службе приложений. Некоторые ресурсы, такие как хранилища ключей, также имеют собственные [политики доступа](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) , которые используются для предоставления доступа участникам (пользователям, приложениям, группам и т. д.).

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Распространенные проблемы при развертывании в службе приложений Azure

#### <a name="managed-identity-is-not-setup-on-the-app-service"></a>Управляемое удостоверение не настроено в службе приложений
 
Проверьте, существуют ли переменные среды MSI_ENDPOINT и MSI_SECRET с помощью [консоли отладки KUDU](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Если эти переменные среды не существуют, управляемое удостоверение не включено в службе приложений. 
 
### <a name="common-issues-when-deployed-locally-with-iis"></a>Распространенные проблемы при развертывании локально с IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Не удается получить токены при отладке приложения в IIS

По умолчанию AppAuth выполняется в другом контексте пользователя в IIS и поэтому не имеет доступа к использованию удостоверения разработчика для получения маркеров доступа. Вы можете настроить службы IIS для работы с контекстом пользователя, выполнив следующие два действия:
- Настройте пул приложений для запуска веб-приложения от имени текущей учетной записи пользователя. Дополнительные сведения см. [здесь](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities).
- Задайте для "Сетпрофилинвиронмент" значение "true". Дополнительные сведения см. [здесь](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Перейдите по адресу%windir%\System32\inetsrv\config\applicationHost.config
    - Выполните поиск по запросу "Сетпрофилинвиронмент". Если задано значение "false", измените его на "true". Если он отсутствует, добавьте его в качестве атрибута в элемент processModel (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) и присвойте ему значение true.

- Дополнительные сведения см. в статье [Использование управляемых удостоверений в Службе приложений и Функциях Azure](../active-directory/managed-identities-azure-resources/index.yml).
- [Что такое проверка подлинности?](../active-directory/develop/active-directory-authentication-scenarios.md)
