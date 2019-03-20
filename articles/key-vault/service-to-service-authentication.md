---
title: Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET
description: Использование библиотеки Microsoft.Azure.Services.AppAuthentication для выполнения проверки подлинности в Azure Key Vault с помощью .NET.
keywords: локальные учетные данные проверки подлинности azure key vault
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 03/05/2019
ms.topic: conceptual
ms.service: key-vault
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: b7532dc910fae84733c04d4e16ac93a5cd0f11c8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097912"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET

Чтобы выполнить проверку подлинности в Azure Key Vault, необходимо иметь учетные данные Azure Active Directory: в виде общего секрета или сертификата. Управление такими учетными данными может быть сложным. Очевидным решением может казаться объединить учетные данные в приложение, включив их в исходные данные или файлы конфигурации.

Пакет `Microsoft.Azure.Services.AppAuthentication` для библиотеки .NET упрощает эту проблему. Он использует учетные данные разработчика для выполнения проверки подлинности во время локальной разработки. Если решение позже развертывается в Azure, библиотека автоматически переключается на использование учетных данных приложения.  

Использовать учетные данные разработчика во время локального развертывания более безопасно, так как вам не нужно создавать учетные данные Azure AD или совместно использовать учетные данные с другими разработчиками.

Библиотека `Microsoft.Azure.Services.AppAuthentication` автоматически управляет проверкой подлинности, которая, в свою очередь, позволяет сконцентрироваться на решении, а не на учетных данных.

Библиотека `Microsoft.Azure.Services.AppAuthentication` поддерживает локальную разработку с Microsoft Visual Studio, Azure CLI и встроенную проверку подлинности Azure AD. При развертывании в ресурс Azure, поддерживающий управляемое удостоверение, библиотека автоматически использует [управляемые удостоверения для ресурсов Azure](../active-directory/msi-overview.md). Изменение кода или конфигурации не требуется. Библиотека также поддерживает непосредственное использование [учетных данных клиента](../azure-resource-manager/resource-group-authenticate-service-principal.md) Azure AD, если управляемое удостоверение недоступно или когда контекст безопасности разработчика не удается определить во время локальной разработки.

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

## <a name="samples"></a>Примеры

В следующих примерах показана библиотека `Microsoft.Azure.Services.AppAuthentication` в действии:

1. [Извлечение секрета из Azure Key Vault во время выполнения с помощью управляемого удостоверения](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Развертывание программным способом шаблона Azure Resource Manager из виртуальной машины Azure с помощью управляемого удостоверения](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)

3. [Вызов служб Azure из виртуальной машины Azure под управлением Linux с использованием примера .NET Core и управляемого удостоверения](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)

## <a name="local-development-authentication"></a>Проверка подлинности среды локальной разработки

Для локальной разработки существует два основных сценария проверки подлинности:

- [проверка подлинности в службах Azure](#authenticating-to-azure-services);
- [проверка подлинности в пользовательских службах](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Проверка подлинности в службах Azure

Локальные компьютеры не поддерживают управляемые удостоверения для ресурсов Azure.  В результате библиотека `Microsoft.Azure.Services.AppAuthentication` использует учетные данные разработчика для запуска в среде локальной разработки. При развертывании решения в Azure библиотека использует управляемое удостоверение для переключения на поток предоставления учетных данных клиента OAuth 2.0.  Это значит, что вы можете без проблем тестировать один и тот же код локально и удаленно.

Для локальной разработки `AzureServiceTokenProvider` получает токены с помощью **Visual Studio**, **интерфейса командной строки Azure** (CLI) или **встроенной проверки подлинности Azure AD**. Каждый вариант применяется последовательно, и библиотека использует первый вариант, который завершается успешно. Если ни один вариант не работает, выводится исключение `AzureServiceTokenProviderException` с подробными сведениями.

### <a name="authenticating-with-visual-studio"></a>Проверка подлинности с помощью Visual Studio

Проверка подлинности с помощью Visual Studio необходимы следующие компоненты:

1. [Visual Studio 2017 версии 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) или более поздней версии.

2. [Расширение проверки подлинности приложения для Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354), доступны в виде отдельного расширения для Visual Studio 2017 с обновлением 5, так и связанные с продуктом в обновлении 6 и более поздних версий. С обновлением 6 или более поздней версии можно проверить установку расширение проверки подлинности приложения, выбрав средств разработки Azure в программе установки Visual Studio.
 
Войдите в Visual Studio и использовать **средства**&nbsp;>&nbsp;**параметры**&nbsp;>&nbsp;**службы Azure Проверка подлинности** выберите учетную запись для локальной разработки. 

Если возникли проблемы при использовании Visual Studio, например ошибки с файлом поставщика токенов, внимательно просмотрите указанные ниже шаги. 

Вам также может потребоваться повторно выполнить проверку подлинности токена разработчика. Для этого выберите **Сервис**&nbsp;>&nbsp;**Параметры**>**Azure&nbsp;Service&nbsp;Authentication** (Проверка подлинности в службе Azure) и найдите ссылку **Re-authenticate** (Повторно выполнить проверку подлинности) в выбранной учетной записи.  Щелкните ее, чтобы выполнить проверку подлинности. 

### <a name="authenticating-with-azure-cli"></a>Проверка подлинности с помощью Azure CLI

Чтобы использовать Azure CLI для локальной разработки, сделайте следующее:

1. Установите [Azure CLI версии 2.0.12](/cli/azure/install-azure-cli) или более поздней. Обновите более ранние версии. 

2. Выполните команду **az login** для входа в Azure.

Для проверки доступа выполните команду `az account get-access-token`.  Если появится ошибка, убедитесь в успешном завершении шага 1. 

Если Azure CLI не установлен в каталоге по умолчанию, вы можете получить ошибку, что `AzureServiceTokenProvider` не может найти путь для Azure CLI.  С помощью переменной среды **AzureCLIPath** можно определить папку установки Azure CLI. При необходимости `AzureServiceTokenProvider` добавляет каталог, указанный в переменной среды **AzureCLIPath**, в переменную среды **Path**.

Если вы вошли в Azure CLI с помощью нескольких учетных записей или учетная запись имеет доступ к нескольким подпискам, необходимо указать конкретную подписку для использования.  Для этого выполните следующую команду:

```
az account set --subscription <subscription-id>
```

Эта команда создает выходные данные только при сбое.  Чтобы проверить параметры текущей учетной записи, выполните следующую команду:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Использование встроенной проверки подлинности Azure AD

Чтобы использовать проверку подлинности Azure AD, убедитесь, что:

- локальная служба Active Directory [синхронизируется с Azure AD](../active-directory/connect/active-directory-aadconnect.md);

- код выполняется на компьютере, присоединенном к домену.


### <a name="authenticating-to-custom-services"></a>Проверка подлинности в пользовательских службах

Когда служба вызывает службы Azure, можно выполнить предыдущие действия, так как службы Azure предоставляют доступ пользователям и приложениям.  

При создании службы, которая вызывает пользовательскую службу, выполните проверку подлинности среды локальной разработки с помощью учетных данных клиента Azure AD.  Существуют два варианта: 

1.  Войдите в Azure, используя субъект-службу:

    1.  [Создайте субъект-службу](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Выполните вход с помощью Azure CLI:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Так как субъект-служба может не иметь доступа к подписке, используйте аргумент `--allow-no-subscriptions`.

2.  Укажите сведения о субъекте-службе с помощью переменных среды.  Дополнительные сведения см. в разделе [Запуск приложения с помощью переменной среды](#running-the-application-using-a-service-principal).

После входа в Azure `AzureServiceTokenProvider` использует субъект-службу, чтобы извлечь токен для локальной разработки.

Это относится только к локальной разработке. При развертывании решения в Azure библиотека переключается на выполнение проверки подлинности с использованием управляемого удостоверения.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Запуск приложения с помощью управляемого удостоверения или назначаемого пользователем удостоверения 

При выполнении кода в Службе приложений Azure или на виртуальной машине Azure с включенным управляемым удостоверением библиотека автоматически использует это удостоверение. Изменения кода не требуются. 

Кроме того можно проверять подлинность с помощью назначаемого пользователем удостоверения. Дополнительные сведения о назначенных пользователям удостоверений см. в разделе [о удостоверений, управляемых для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Строка подключения указана в [поддержка строки подключения](#connection-string-support) разделе ниже.

## <a name="running-the-application-using-a-service-principal"></a>Запуск приложения с помощью субъекта-службы 

Возможно, может понадобиться создать учетные данные клиента Azure AD для проверки подлинности. Ниже приведены распространенные примеры.

1. Ваш код выполняется в среде локальной разработки, но не использует удостоверение разработчика.  Service Fabric, например, использует [учетную запись NetworkService](../service-fabric/service-fabric-application-secret-management.md) для локальной разработки.
 
2. Ваш код выполняется в среде локальной разработки и выполняется проверка подлинности в пользовательской службе, поэтому вы не можете использовать удостоверение разработчика. 
 
3. Ваш код выполняется в вычислительном ресурсе Azure, который еще не поддерживает управляемые удостоверения для ресурсов Azure, например в пакетной службе Azure.

Чтобы войти в Azure AD с помощью сертификата, сделайте следующее:

1. Создайте [сертификат субъекта-службы](../azure-resource-manager/resource-group-authenticate-service-principal.md). 

2. Разверните сертификат в хранилище *LocalMachine* или *CurrentUser*. 

3. Задайте переменную среды с именем **AzureServicesAuthConnectionString**, как показано в следующем примере:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Замените *{AppId}*, *{TenantId}* и *{Thumbprint}* идентификатором приложения, идентификатором клиента и отпечатком, созданными на шаге 1. Замените *{CertificateStore}* на `LocalMachine` или `CurrentUser` в зависимости от плана развертывания.

4. Запустите приложение. 

Чтобы выполнить вход с помощью учетных данных в виде общего секрета Azure AD, сделайте следующее:

1. Создайте [субъект-службу с паролем](../azure-resource-manager/resource-group-authenticate-service-principal.md) и предоставьте ей доступ к Key Vault. 

2. Задайте переменную среды с именем **AzureServicesAuthConnectionString**, как показано в следующем примере:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Замените _{AppId}_, _{TenantId}_ и _{ClientSecret}_ идентификатором приложения, идентификатором клиента и секретом клиента, созданными на шаге 1.

3. Запустите приложение. 

Если установка выполнена правильно, дальнейшие изменения в коде не требуются.  `AzureServiceTokenProvider` использует переменную среды и сертификат для выполнения проверки подлинности в Azure AD. 

## <a name="connection-string-support"></a>Поддержка строки подключения

По умолчанию `AzureServiceTokenProvider` использует несколько способов извлечения токена. 

Для управления процессом используйте строку подключения, переданную в конструктор `AzureServiceTokenProvider` или указанную в переменной среды *AzureServicesAuthConnectionString*. 

Поддерживаются следующие варианты.

| Вариант&nbsp;строки&nbsp;подключения | Сценарий | Комментарии|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Локальная разработка | AzureServiceTokenProvider использует Azure CLI для получения токена. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Локальная разработка | AzureServiceTokenProvider использует Visual Studio для получения токена. |
| `RunAs=CurrentUser` | Локальная разработка | AzureServiceTokenProvider использует встроенную проверку подлинности Azure AD для получения токена. |
| `RunAs=App` | [Управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider использует управляемое удостоверение для получения токена. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Назначаемое пользователем удостоверение для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | AzureServiceTokenProvider использует назначаемое пользователем удостоверение для получения токена. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`   | Субъект-служба | `AzureServiceTokenProvider` использует сертификат для получения токена из Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Субъект-служба | `AzureServiceTokenProvider` использует сертификат для получения токена из Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Субъект-служба |`AzureServiceTokenProvider` использует секрет для получения токена из Azure AD. |



## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. в статье [Использование управляемых удостоверений в Службе приложений и Функциях Azure](../active-directory/managed-identities-azure-resources/index.yml).
- [Что такое проверка подлинности?](../active-directory/develop/active-directory-authentication-scenarios.md)
