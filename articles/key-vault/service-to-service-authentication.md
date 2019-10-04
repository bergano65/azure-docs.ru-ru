---
title: Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET
description: Использование библиотеки Microsoft.Azure.Services.AppAuthentication для выполнения проверки подлинности в Azure Key Vault с помощью .NET.
keywords: локальные учетные данные проверки подлинности azure key vault
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 0f8c8900782ec8beb9f9b11c8fe7f7651901c343
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958996"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET

Для проверки подлинности в Azure Key Vault требуются учетные данные Azure Active Directory (Azure AD), либо общий секрет, либо сертификат.

Управление такими учетными данными может быть трудной задачей. Это может привести к объединению учетных данных в приложение, включив их в файлы исходного кода или конфигурации. Пакет `Microsoft.Azure.Services.AppAuthentication` для библиотеки .NET упрощает эту проблему. Он использует учетные данные разработчика для выполнения проверки подлинности во время локальной разработки. Если решение позже развертывается в Azure, библиотека автоматически переключается на использование учетных данных приложения. Использование учетных данных разработчика во время локальной разработки является более безопасным, поскольку вам не нужно создавать учетные данные Azure AD или обмениваться учетными данными между разработчиками.

Библиотека `Microsoft.Azure.Services.AppAuthentication` управляет проверкой подлинности автоматически, что, в свою очередь, позволяет сосредоточиться на решении, а не на ваших учетных данных. Она поддерживает локальную разработку с использованием Microsoft Visual Studio, Azure CLI или встроенной проверки подлинности Azure AD. При развертывании в ресурс Azure, поддерживающий управляемое удостоверение, библиотека автоматически использует [управляемые удостоверения для ресурсов Azure](../active-directory/msi-overview.md). Изменение кода или конфигурации не требуется. Библиотека также поддерживает прямое использование [учетных данных клиента](../azure-resource-manager/resource-group-authenticate-service-principal.md) Azure AD, если управляемое удостоверение недоступно, или если контекст безопасности разработчика не может быть определен во время локальной разработки.

## <a name="prerequisites"></a>Предварительные требования

- [Visual studio 2019](https://www.visualstudio.com/downloads/) или [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Расширение проверки подлинности приложения для Visual Studio, которое доступно как отдельное расширение для Visual Studio 2017 с обновлением 5 и объединено с продуктом в обновлении 6 и более поздних версий. С обновлением 6 или более поздней версии можно проверить установку расширения проверки подлинности приложения, выбрав средства разработки Azure в установщике Visual Studio.

## <a name="using-the-library"></a>Использование библиотеки

Для приложений .NET для работы с управляемым удостоверением проще всего использовать пакет `Microsoft.Azure.Services.AppAuthentication`. Узнайте, как начать работу.

1. Выберите **инструменты** > **диспетчер пакетов NuGet** > **Управление пакетами NuGet для решения** , чтобы добавить ссылки на пакеты NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) и [Microsoft. Azure. KeyVault.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) Ваш проект.

1. Добавьте следующий код:

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

Класс `AzureServiceTokenProvider` кэширует токен в памяти и извлекает его из Azure AD прямо перед истечением срока действия. Поэтому больше не нужно проверять срок действия перед вызовом метода `GetAccessTokenAsync`. Просто вызовите метод, когда необходимо использовать токен.

Для метода `GetAccessTokenAsync` требуется наличие идентификатора ресурса. Дополнительные сведения о службах Microsoft Azure см. в статье [что такое управляемые удостоверения для ресурсов Azure](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Проверка подлинности среды локальной разработки

Для локальной разработки существует два основных сценария проверки подлинности: проверка подлинности [в службах Azure](#authenticating-to-azure-services)и [Проверка подлинности пользовательских служб](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Проверка подлинности в службах Azure

Локальные компьютеры не поддерживают управляемые удостоверения для ресурсов Azure. В результате библиотека `Microsoft.Azure.Services.AppAuthentication` использует учетные данные разработчика для запуска в среде локальной разработки. При развертывании решения в Azure библиотека использует управляемое удостоверение для переключения на поток предоставления учетных данных клиента OAuth 2.0. Такой подход означает, что вы можете протестировать один и тот же код локально и удаленно, не беспокоясь.

Для локальной разработки `AzureServiceTokenProvider` получает токены с помощью **Visual Studio**, **интерфейса командной строки Azure** (CLI) или **встроенной проверки подлинности Azure AD**. Каждый вариант применяется последовательно, и библиотека использует первый вариант, который завершается успешно. Если ни один вариант не работает, выводится исключение `AzureServiceTokenProviderException` с подробными сведениями.

#### <a name="authenticating-with-visual-studio"></a>Проверка подлинности с помощью Visual Studio

Для проверки подлинности с помощью Visual Studio:

1. Войдите в Visual Studio и используйте **инструменты**&nbsp; @ no__t-2 @ no__t-3,**чтобы открыть** **Параметры**.

1. Выберите **Проверка подлинности службы Azure**, выберите учетную запись для локальной разработки и нажмите кнопку **ОК**.

Если при использовании Visual Studio возникают проблемы, например ошибки, связанные с файлом поставщика маркеров, внимательно проверьте предыдущие шаги.

Может потребоваться повторная проверка подлинности токена разработчика. Для этого выберите **инструменты**&nbsp; @ no__t-2 @ no__t **-3,** а затем выберите **Azure @ no__t-6Service @ no__t-7Authentication**. Найдите ссылку **повторной проверки подлинности** в выбранной учетной записи. Щелкните ее, чтобы выполнить проверку подлинности.

#### <a name="authenticating-with-azure-cli"></a>Проверка подлинности с помощью Azure CLI

Чтобы использовать Azure CLI для локальной разработки, убедитесь, что у вас установлена версия [Azure CLI v 2.0.12](/cli/azure/install-azure-cli) или более поздняя.

Чтобы использовать Azure CLI, выполните следующие действия.

1. Выполните поиск Azure CLI на панели задач Windows, чтобы открыть **Microsoft Azure командной строке**.

1. Войдите в портал Azure: *AZ login* , чтобы войти в Azure.

1. Проверьте доступ, введя команду *AZ Account Get-Access-Token*. Если появляется сообщение об ошибке, убедитесь, что правильно установлена правильная версия Azure CLI.

   Если Azure CLI не установлен в каталог по умолчанию, может появиться сообщение об ошибке, которое `AzureServiceTokenProvider` не может найти путь для Azure CLI. Используйте переменную среды **азуреклипас** , чтобы определить папку установки Azure CLI. При необходимости `AzureServiceTokenProvider` добавляет каталог, указанный в переменной среды **AzureCLIPath**, в переменную среды **Path**.

1. Если вы вошли в Azure CLI с использованием нескольких учетных записей или ваша учетная запись имеет доступ к нескольким подпискам, необходимо указать подписку для использования. Введите команду *AZ Account Set--subscription < Subscription-id >* .

Эта команда создает выходные данные только при сбое. Чтобы проверить текущие параметры учетной записи, введите команду `az account list`.

#### <a name="authenticating-with-azure-ad-authentication"></a>Проверка подлинности с помощью аутентификации Azure AD

Чтобы использовать проверку подлинности Azure AD, убедитесь, что:

- Локальные Active Directory синхронизируются с Azure AD. Дополнительные сведения см. [в статье что такое гибридное удостоверение с Azure Active Directory?](../active-directory/connect/active-directory-aadconnect.md).

- Ваш код работает на компьютере, присоединенном к домену.

### <a name="authenticating-to-custom-services"></a>Проверка подлинности в пользовательских службах

Когда служба вызывает службы Azure, можно выполнить предыдущие действия, так как службы Azure предоставляют доступ пользователям и приложениям.

При создании службы, которая вызывает пользовательскую службу, выполните проверку подлинности среды локальной разработки с помощью учетных данных клиента Azure AD. Существуют два варианта:

- Войдите в Azure, используя субъект-службу:

    1. Создание субъекта-службы. Дополнительные сведения см. в статье [Создание субъекта-службы Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Используйте Azure CLI для входа с помощью следующей команды:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Так как субъект-служба может не иметь доступа к подписке, используйте аргумент `--allow-no-subscriptions`.

- Укажите сведения о субъекте-службе с помощью переменных среды. Дополнительные сведения см. в разделе [Запуск приложения с помощью субъекта-службы](#running-the-application-using-a-service-principal).

Войдя в Azure, `AzureServiceTokenProvider` использует субъект службы для получения маркера для локальной разработки.

Этот подход применим только к локальной разработке. При развертывании решения в Azure библиотека переключается на выполнение проверки подлинности с использованием управляемого удостоверения.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Запуск приложения с использованием управляемого удостоверения или удостоверения, назначенного пользователем

При выполнении кода в Службе приложений Azure или на виртуальной машине Azure с включенным управляемым удостоверением библиотека автоматически использует это удостоверение. Изменения кода не требуются, но управляемое удостоверение должно иметь разрешения *Get* для хранилища ключей. Вы можете предоставить управляемому удостоверению разрешения на *Получение* разрешений через *политики доступа*хранилища ключей.

Кроме того, вы можете пройти проверку подлинности с помощью назначенного пользователем удостоверения. Дополнительные сведения о назначенных пользователем удостоверениях см. в статье [об управляемых удостоверениях для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Чтобы выполнить проверку подлинности с помощью назначенного пользователем удостоверения, необходимо указать идентификатор клиента, которому назначено пользовательское удостоверение, в строке подключения. Строка подключения указывается в поле [Поддержка строки подключения](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Запуск приложения с помощью субъекта-службы

Возможно, может понадобиться создать учетные данные клиента Azure AD для проверки подлинности. Эта ситуация может возникнуть в следующих примерах:

- Ваш код выполняется в среде локальной разработки, но не использует удостоверение разработчика. Service Fabric, например, использует [учетную запись NetworkService](../service-fabric/service-fabric-application-secret-management.md) для локальной разработки.

- Ваш код выполняется в среде локальной разработки и выполняется проверка подлинности в пользовательской службе, поэтому вы не можете использовать удостоверение разработчика.

- Ваш код выполняется в ресурсе вычислений Azure, который еще не поддерживает управляемые удостоверения для ресурсов Azure, таких как пакетная служба Azure.

Существует три основных способа использования субъекта-службы для запуска приложения. Чтобы использовать их, необходимо сначала создать субъект-службу. Дополнительные сведения см. в статье [Создание субъекта-службы Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Использование сертификата в локальном хранилище ключей для входа в Azure AD

1. Создайте сертификат субъекта-службы с помощью команды Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) .

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Эта команда создает PEM-файл (закрытый ключ), хранящийся в домашнем каталоге. Разверните этот сертификат в хранилище *LocalMachine* или *CurrentUser* .

    > [!Important]
    > Команда CLI создает PEM-файл, но Windows обеспечивает только собственную поддержку сертификатов PFX. Чтобы создать PFX-сертификат, используйте команды PowerShell, показанные здесь: [Создайте субъект-службу с самозаверяющим сертификатом](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Эти команды также автоматически развертывают сертификат.

1. Задайте для переменной среды с именем **AzureServicesAuthConnectionString** следующее значение:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Замените *{AppId}* , *{TenantId}* и *{Thumbprint}* идентификатором приложения, идентификатором клиента и отпечатком, созданными на шаге 1. Замените *{CertificateStore}* на *LocalMachine*или *CurrentUser*в соответствии с планом развертывания.

1. Запустите приложение.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Использование учетных данных общего секрета для входа в Azure AD

1. Создайте сертификат субъекта-службы с паролем, используя команду Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) с параметром--SDK-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Задайте для переменной среды с именем **AzureServicesAuthConnectionString** следующее значение:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Замените _{AppId}_ , _{TenantId}_ и _{ClientSecret}_ идентификатором приложения, идентификатором клиента и секретом клиента, созданными на шаге 1.

1. Запустите приложение.

Если установка выполнена правильно, дальнейшие изменения в коде не требуются. `AzureServiceTokenProvider` использует переменную среды и сертификат для выполнения проверки подлинности в Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Использование сертификата в Key Vault для входа в Azure AD

Этот параметр позволяет хранить сертификат клиента субъекта-службы в Key Vault и использовать его для проверки подлинности субъекта-службы. Этот вариант можно использовать в следующих сценариях:

- Локальная проверка подлинности, в которой вы хотите пройти проверку подлинности с помощью явного субъекта-службы и хотите безопасно хранить учетные данные субъекта-службы в хранилище ключей. Учетная запись разработчика должна иметь доступ к хранилищу ключей.

- Аутентификация из Azure, где вы хотите использовать явные учетные данные и хотите безопасно хранить учетные данные субъекта-службы в хранилище ключей. Этот параметр можно использовать для сценария с несколькими клиентами. Управляемое удостоверение должно иметь доступ к хранилищу ключей.

Управляемое удостоверение или удостоверение разработчика должно иметь разрешение на получение сертификата клиента из Key Vault. Библиотека AppAuthentication использует полученный сертификат в качестве учетных данных клиента субъекта-службы.

Чтобы использовать сертификат клиента для проверки подлинности субъекта-службы:

1. Создайте сертификат субъекта-службы и автоматически сохраните его в Key Vault. Используйте Azure CLI [AZ AD SP Create-for-RBAC--keyvault \<keyvaultname >--cert \<certificatename >--Create-CERT--Skip-присваивание](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    Идентификатор сертификата будет URL-адресом в формате `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`.

1. Замените `{KeyVaultCertificateSecretIdentifier}` в этой строке подключения на идентификатор сертификата:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Например, если хранилище ключей называлось *myKeyVault* и вы создали сертификат с именем *MyCert*, идентификатор сертификата будет выглядеть так:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Поддержка строки подключения

По умолчанию `AzureServiceTokenProvider` использует несколько способов извлечения токена.

Для управления процессом используйте строку подключения, переданную в конструктор `AzureServiceTokenProvider` или указанную в переменной среды *AzureServicesAuthConnectionString*.

Поддерживаются следующие варианты.

| Параметр строки подключения | Сценарий | Комментарии|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Локальная разработка | `AzureServiceTokenProvider` использует Azure CLI для получения токена. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Локальная разработка | `AzureServiceTokenProvider` использует Visual Studio для получения токена. |
| `RunAs=CurrentUser` | Локальная разработка | `AzureServiceTokenProvider` использует встроенную проверку подлинности Azure AD для получения токена. |
| `RunAs=App` | [Управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider` использует управляемое удостоверение для получения токена. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Удостоверение, назначенное пользователем для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider` использует назначенное пользователем удостоверение для получения маркера. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Проверка подлинности настраиваемых служб | `KeyVaultCertificateSecretIdentifier` — это идентификатор секрета сертификата. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Субъект-служба | `AzureServiceTokenProvider` использует сертификат для получения токена из Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Субъект-служба | `AzureServiceTokenProvider` использует сертификат для получения токена из Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Субъект-служба |`AzureServiceTokenProvider` использует секрет для получения токена из Azure AD. |

## <a name="samples"></a>Примеры

Чтобы просмотреть библиотеку `Microsoft.Azure.Services.AppAuthentication` в действии, см. следующие примеры кода.

- [Извлечение секрета из Azure Key Vault во время выполнения с помощью управляемого удостоверения](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Развертывание программным способом шаблона Azure Resource Manager из виртуальной машины Azure с помощью управляемого удостоверения](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)

- [Вызов служб Azure из виртуальной машины Azure под управлением Linux с использованием примера .NET Core и управляемого удостоверения](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)

## <a name="appauthentication-troubleshooting"></a>Устранение неполадок AppAuthentication

### <a name="common-issues-during-local-development"></a>Распространенные проблемы при локальной разработке

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLI не установлен, вы не вошли в систему или у вас не установлена последняя версия

Выполните команду *AZ Account Get-Access-Token* , чтобы узнать, покажет ли Azure CLI маркер. Если это не говорит о том, что эта **Программа найдена**, установите [последнюю версию Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Возможно, вам будет предложено войти в систему.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider не удается найти путь для Azure CLI

AzureServiceTokenProvider ищет Azure CLI в его расположениях установки по умолчанию. Если не удается найти Azure CLI, задайте для переменной среды **азуреклипас** значение Azure CLI папке установки. AzureServiceTokenProvider добавит переменную среды в переменную среды PATH.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Вы выполнили вход в Azure CLI с использованием нескольких учетных записей, одна учетная запись имеет доступ к подпискам в нескольких клиентах или при попытке вызвать вызовы во время локальной разработки возникает ошибка отказа в доступе.

Используя Azure CLI, задайте подписку по умолчанию с учетной записью, которую вы хотите использовать. Подписка должна находиться в том же клиенте, что и ресурс, к которому вы хотите получить доступ: **AZ Account Set--Subscription [Subscription-ID]** . Если выходные данные не отображаются, это означает, что она была успешной. Убедитесь, что правильная учетная запись теперь используется по умолчанию с помощью команды **AZ Account List**.

### <a name="common-issues-across-environments"></a>Распространенные проблемы в средах

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Несанкционированный доступ, отказ в доступе, запрещение или подобная ошибка

У используемого субъекта нет доступа к ресурсу, к которому он пытается получить доступ. Предоставьте учетной записи пользователя или MSI-участнику службы приложений доступ к ресурсу. Какой из них зависит от того, вы запускаете пример на локальном компьютере или разворачиваете в Azure в службе приложений. Некоторые ресурсы, такие как хранилища ключей, также имеют собственные [политики доступа](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) , которые используются для предоставления доступа участникам, таким как пользователи, приложения и группы.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Распространенные проблемы при развертывании в службе приложений Azure

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Управляемое удостоверение не настроено в службе приложений

Проверьте, существуют ли переменные среды MSI_ENDPOINT и MSI_SECRET с помощью [консоли отладки KUDU](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Если эти переменные среды не существуют, управляемое удостоверение не включено в службе приложений.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Распространенные проблемы при развертывании локально с IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Не удается получить токены при отладке приложения в IIS

По умолчанию AppAuth выполняется в другом контексте пользователя в службах IIS. Поэтому у него нет доступа к использованию удостоверения разработчика для получения маркеров доступа. Вы можете настроить службы IIS для работы с контекстом пользователя, выполнив следующие два действия:
- Настройте пул приложений для запуска веб-приложения от имени текущей учетной записи пользователя. Дополнительные сведения см. [здесь](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities).
- Задайте для "Сетпрофилинвиронмент" значение "true". Дополнительные сведения см. [здесь](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Перейдите по адресу%windir%\System32\inetsrv\config\applicationHost.config
    - Выполните поиск по запросу "Сетпрофилинвиронмент". Если задано значение "false", измените его на "true". Если он отсутствует, добавьте его в качестве атрибута в элемент processModel (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) и задайте для него значение "true".

- Дополнительные сведения см. в статье [Использование управляемых удостоверений в Службе приложений и Функциях Azure](../active-directory/managed-identities-azure-resources/index.yml).
- [Что такое проверка подлинности?](../active-directory/develop/active-directory-authentication-scenarios.md)
