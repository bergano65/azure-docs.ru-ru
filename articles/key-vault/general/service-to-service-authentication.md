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
ms.subservice: general
ms.openlocfilehash: d6ac5961cbecf4e81c0b6bcc25c39aad42b18416
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429841"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET

Для проверки подлинности в Хранилище ключей Azure необходимо учетные данные Active Directory (Azure AD), общий секрет или сертификат.

Управление такими учетных данных может быть затруднено. Заманчиво укомплектовать учетные данные в приложение, включив их в исходные или файлы конфигурации. Пакет `Microsoft.Azure.Services.AppAuthentication` для библиотеки .NET упрощает эту проблему. Он использует учетные данные разработчика для выполнения проверки подлинности во время локальной разработки. Если решение позже развертывается в Azure, библиотека автоматически переключается на использование учетных данных приложения. Использование учетных данных разработчика во время локальной разработки является более безопасным, поскольку не требуется создавать учетные данные Azure AD или обмениваться учетными данными между разработчиками.

Библиотека `Microsoft.Azure.Services.AppAuthentication` управляет аутентификацией автоматически, что, в свою очередь, позволяет сосредоточиться на своем решении, а не на учетных данных. Он поддерживает локальную разработку с помощью Microsoft Visual Studio, Azure CLI или Azure AD Integrated Authentication. При развертывании в ресурс Azure, поддерживающий управляемое удостоверение, библиотека автоматически использует [управляемые удостоверения для ресурсов Azure](../../active-directory/msi-overview.md). Изменение кода или конфигурации не требуется. Библиотека также поддерживает прямое использование [учетных данных клиентов](../../azure-resource-manager/resource-group-authenticate-service-principal.md) Azure AD, когда управляемая идентификация недоступна или когда контекст безопасности разработчика не может быть определен во время локальной разработки.

## <a name="prerequisites"></a>Предварительные требования

- [Визуальная студия 2019](https://www.visualstudio.com/downloads/) или [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Расширение аутентификации приложения для Visual Studio, доступное в качестве отдельного расширения для Visual Studio 2017 Update 5 и в комплекте с продуктом в обновлении 6 и позже. С обновлением 6 или позже вы можете проверить установку расширения аутентификации приложений, выбрав инструменты разработки Azure из установки Visual Studio.

## <a name="using-the-library"></a>Использование библиотеки

Для приложений .NET для работы с управляемым удостоверением проще всего использовать пакет `Microsoft.Azure.Services.AppAuthentication`. Узнайте, как начать работу.

1. Выберите **Набор инструментов** > **NuGet Package Manager** > **Manage NuGet Packages для решения** для добавления ссылок на пакеты [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) и [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet в ваш проект.

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

Класс `AzureServiceTokenProvider` кэширует токен в памяти и извлекает его из Azure AD прямо перед истечением срока действия. Таким образом, вам больше не нужно проверять `GetAccessTokenAsync` истечение срока действия перед вызовом метода. Просто вызовите метод, когда необходимо использовать токен.

Для метода `GetAccessTokenAsync` требуется наличие идентификатора ресурса. Подробнее об службах Microsoft Azure читайте в отделе поиска [ресурсов Azure.](../../active-directory/msi-overview.md)

## <a name="local-development-authentication"></a>Проверка подлинности среды локальной разработки

Для локальной разработки существует два основных сценария проверки подлинности: [аутентификация служб Azure](#authenticating-to-azure-services)и [аутентификация пользовательских служб.](#authenticating-to-custom-services)

### <a name="authenticating-to-azure-services"></a>Проверка подлинности в службах Azure

Локальные машины не поддерживают управляемые идентификаторы для ресурсов Azure. В результате библиотека `Microsoft.Azure.Services.AppAuthentication` использует учетные данные разработчика для запуска в среде локальной разработки. При развертывании решения в Azure библиотека использует управляемое удостоверение для переключения на поток предоставления учетных данных клиента OAuth 2.0. Этот подход означает, что вы можете протестировать один и тот же код локально и удаленно, не беспокоясь.

Для локальной разработки `AzureServiceTokenProvider` получает токены с помощью **Visual Studio**, **интерфейса командной строки Azure** (CLI) или **встроенной проверки подлинности Azure AD**. Каждый вариант применяется последовательно, и библиотека использует первый вариант, который завершается успешно. Если ни один вариант не работает, выводится исключение `AzureServiceTokenProviderException` с подробными сведениями.

#### <a name="authenticating-with-visual-studio"></a>Проверка подлинности с помощью Visual Studio

Для проверки подлинности с помощью Visual Studio:

1. Вопиюсь в Visual Studio и используйте>&nbsp;**параметры инструментов,** **Tools**&nbsp;чтобы открыть **опции.**

1. Выберите **аутентацию службы Azure,** выберите учетную запись для локальной разработки и выберите **OK.**

Если у вас возникли проблемы с использованием Visual Studio, такие как ошибки, связанные с файлом поставщика токенов, внимательно просмотрите предыдущие шаги.

Возможно, потребуется повторное аутентификации маркера разработчика. Для этого выберите>&nbsp;**параметры инструментов,** **Tools**&nbsp;а затем выберите **аутентификацию службы&nbsp;&nbsp;Azure.** Ищите ссылку **re-authenticate** под выбранной учетной записью. Щелкните ее, чтобы выполнить проверку подлинности.

#### <a name="authenticating-with-azure-cli"></a>Проверка подлинности с помощью Azure CLI

Чтобы использовать Azure CLI для локальной разработки, убедитесь, что у вас есть версия [Azure CLI v2.0.12](/cli/azure/install-azure-cli) или позже.

Для использования Azure CLI:

1. Поиск Azure CLI в панели задач Windows для открытия **запроса Microsoft Azure.**

1. Войдите на портал Azure: *az login* для входа в Azure.

1. Проверить доступ, введя *учетную запись az, получить-доступ-токен --ресурс https://vault.azure.net *. Если вы получили ошибку, убедитесь, что правильная версия Azure CLI правильно установлена.

   Если Azure CLI не установлен в каталоге по умолчанию, `AzureServiceTokenProvider` вы можете получить сообщение об ошибке, которое не может найти путь для Azure CLI. Используйте переменную среды **AzureCLIPath** для определения папки установки Azure CLI. При необходимости `AzureServiceTokenProvider` добавляет каталог, указанный в переменной среды **AzureCLIPath**, в переменную среды **Path**.

1. Если вы вписались в Azure CLI с использованием нескольких учетных записей или ваша учетная запись имеет доступ к нескольким подпискам, необходимо указать подписку для использования. Введите *набор учетных записей --подписка <подписка-id>. *

Эта команда создает выходные данные только при сбое. Чтобы проверить настройки текущего счета, введите команду `az account list`.

#### <a name="authenticating-with-azure-ad-authentication"></a>Проверка подлинности с помощью аутентификации Azure AD

Чтобы использовать проверку подлинности Azure AD, убедитесь, что:

- Ваш предприимчивый Active Directory синхронизируется с Azure AD. Для получения дополнительной информации см. [Что такое гибридная идентичность с Azure Active Directory?](../../active-directory/connect/active-directory-aadconnect.md).

- Ваш код работает на компьютере, работаемом на домене.

### <a name="authenticating-to-custom-services"></a>Проверка подлинности в пользовательских службах

Когда служба вызывает службы Azure, можно выполнить предыдущие действия, так как службы Azure предоставляют доступ пользователям и приложениям.

При создании службы, которая вызывает пользовательскую службу, выполните проверку подлинности среды локальной разработки с помощью учетных данных клиента Azure AD. Имеются две возможности:

- Войдите в Azure, используя субъект-службу:

    1. Создание субъекта-службы. Дополнительные сведения см. в статье [Создание субъекта-службы Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Используйте Azure CLI для вхверсии со следующей командой:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Так как субъект-служба может не иметь доступа к подписке, используйте аргумент `--allow-no-subscriptions`.

- Укажите сведения о субъекте-службе с помощью переменных среды. Для получения дополнительной информации [см. Запуск приложения с помощью основного сервиса](#running-the-application-using-a-service-principal).

После вхлечения в Azure `AzureServiceTokenProvider` используется основной службы для получения маркера для локальной разработки.

Этот подход применяется только к местному развитию. При развертывании решения в Azure библиотека переключается на выполнение проверки подлинности с использованием управляемого удостоверения.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Запуск приложения с использованием управляемой идентификации или удостоверения, назначенного пользователем

При выполнении кода в Службе приложений Azure или на виртуальной машине Azure с включенным управляемым удостоверением библиотека автоматически использует это удостоверение. Изменения кода не требуются, но управляемый идентатор должен *иметь* разрешения на хранилище ключей. Вы можете предоставить управляемой идентификации *получить* разрешения через политики доступа хранилища *ключей.*

Кроме того, можно проверить подлинность с помощью установленного пользователем удостоверения личности. Для получения дополнительной информации о пользовательских идентификаторах [см.](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) Чтобы проверить подлинность с помощью установленного пользователем удостоверения личности, необходимо указать идентификатор клиента назначенного пользователя в строке соединения. Строка соединения указана в [поддержке строки соединения.](#connection-string-support)

## <a name="running-the-application-using-a-service-principal"></a>Запуск приложения с помощью субъекта-службы

Возможно, может понадобиться создать учетные данные клиента Azure AD для проверки подлинности. Такая ситуация может произойти в следующих примерах:

- Ваш код выполняется в среде локальной разработки, но не использует удостоверение разработчика. Service Fabric, например, использует [учетную запись NetworkService](../../service-fabric/service-fabric-application-secret-management.md) для локальной разработки.

- Ваш код выполняется в среде локальной разработки и выполняется проверка подлинности в пользовательской службе, поэтому вы не можете использовать удостоверение разработчика.

- Код работает на ресурсе вычислений Azure, который еще не поддерживает управляемые идентификаторы для ресурсов Azure, такие как Azure Batch.

Существует три основных метода использования директора службы для запуска приложения. Чтобы использовать любой из них, необходимо сначала создать директор службы. Дополнительные сведения см. в статье [Создание субъекта-службы Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Используйте сертификат в локальном магазине ключей для входного в Azure AD

1. Создайте основной сертификат службы, используя команду Azure CLI [az ad sp create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Эта команда создает файл .pem (частный ключ), который хранится в вашем домашнем каталоге. Развернуть этот сертификат в магазине *LocalMachine* или *CurrentUser.*

    > [!Important]
    > Команда CLI генерирует файл .pem, но Windows предоставляет только нативную поддержку для сертификатов PFX. Вместо этого воспользуйтесь приведенными здесь командами PowerShell: [Создайте основной сервис с сертификатом с самостоятельной подписью.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) Эти команды автоматически развертывают сертификат.

1. Установите переменную среды под названием **AzureServicesAuthConnectionString** на следующее значение:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Замените *{AppId}*, *{TenantId}* и *{Thumbprint}* идентификатором приложения, идентификатором клиента и отпечатком, созданными на шаге 1. Замените *«CertificateStore»* на *LocalMachine*' или *CurrentUser,* основываясь на плане развертывания.

1. Запустите приложение.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Используйте общие секретные учетные данные для входной регистрации в Azure AD

1. Создайте основной сертификат службы с паролем, используя команду Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) с параметром --sdk-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Установите переменную среды под названием **AzureServicesAuthConnectionString** на следующее значение:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Замените _{AppId}_, _{TenantId}_ и _{ClientSecret}_ идентификатором приложения, идентификатором клиента и секретом клиента, созданными на шаге 1.

1. Запустите приложение.

Если установка выполнена правильно, дальнейшие изменения в коде не требуются. `AzureServiceTokenProvider` использует переменную среды и сертификат для выполнения проверки подлинности в Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Используйте сертификат в Key Vault для входного в Azure AD

Эта опция позволяет хранить клиентский сертификат директора службы в Key Vault и использовать его для основной аутентификации службы. Эту опцию можно использовать для следующих сценариев:

- Локальная аутентификация, где требуется аутентифицировать с помощью явного принципа службы, и хотите надежно сохранить учетные данные службы в хранилище ключей. Учетная запись разработчика должна иметь доступ к хранилищу ключей.

- Аутентификация из Azure, где требуется использовать явные учетные данные и хотите надежно сохранить учетные данные службы в хранилище ключей. Эту опцию можно использовать для сценария кросс-арендатора. Управляемая идентификация должна иметь доступ к хранилищу ключей.

Управляемая личность или идентатор разработчика должны иметь разрешение на получение сертификата клиента из Key Vault. Библиотека AppAuthentication использует извлеченный сертификат в качестве клиентского учетных данных директора службы.

Для использования сертификата клиента для основной аутентификации службы:

1. Создайте основной сертификат службы и автоматически храните его в Key Vault. Используйте команду Azure CLI [az ad sp create-for-rbac --keyvault \<keyvault keyvault> --cert \<certificatename> --create-cert --skip-assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) команды:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    Идентификатор сертификата будет URL в формате`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Заменить `{KeyVaultCertificateSecretIdentifier}` в этой связи строку идентификатором сертификата:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Например, если ваш свод ключей получил *название myKeyVault* и вы создали сертификат под названием *myCert,* идентификатор сертификата будет:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Поддержка строки подключения

По умолчанию `AzureServiceTokenProvider` использует несколько способов извлечения токена.

Для управления процессом используйте строку подключения, переданную в конструктор `AzureServiceTokenProvider` или указанную в переменной среды *AzureServicesAuthConnectionString*.

Поддерживаются следующие варианты.

| Опция строки подключения | Сценарий | Комментарии|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Локальная разработка | `AzureServiceTokenProvider`использует AzureCli для получения токена. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Локальная разработка | `AzureServiceTokenProvider`использует Visual Studio, чтобы получить токен. |
| `RunAs=CurrentUser` | Локальная разработка | `AzureServiceTokenProvider`использует интегрированную аутентификацию Azure AD для получения токена. |
| `RunAs=App` | [Управляемые удостоверения для ресурсов Azure](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`использует управляемый итог для получения токена. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Иноеудостоверения, назначенные пользователем для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`использует установленное пользователем удостоверение для получения маркера. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Проверка подлинности пользовательских служб | `KeyVaultCertificateSecretIdentifier`является секретным идентификатором сертификата. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Субъект-служба | `AzureServiceTokenProvider` использует сертификат для получения токена из Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Субъект-служба | `AzureServiceTokenProvider` использует сертификат для получения токена из Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Субъект-служба |`AzureServiceTokenProvider` использует секрет для получения токена из Azure AD. |

## <a name="samples"></a>Примеры

Чтобы увидеть `Microsoft.Azure.Services.AppAuthentication` библиотеку в действии, обратитесь к следующим образцам кода.

- [Извлечение секрета из Azure Key Vault во время выполнения с помощью управляемого удостоверения](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Развертывание программным способом шаблона Azure Resource Manager из виртуальной машины Azure с помощью управляемого удостоверения](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)

- [Вызов служб Azure из виртуальной машины Azure под управлением Linux с использованием примера .NET Core и управляемого удостоверения](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)

## <a name="appauthentication-troubleshooting"></a>Устранение проблем AppAuthentication

### <a name="common-issues-during-local-development"></a>Общие проблемы в ходе местного развития

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLI не установлен, вы не вошли в систему, или у вас нет последней версии

Запустите маркер доступа *к учетной записи,* чтобы узнать, если Azure CLI показывает токен для вас. Если он **говорит, что такая программа не найдена,** установите [последнюю версию Azure CLI.](/cli/azure/install-azure-cli?view=azure-cli-latest) Возможно, вам будет предложено войти в систему.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider не может найти путь для Azure CLI

AzureServiceTokenProvider ищет Azure CLI в своих местах установки по умолчанию. Если он не может найти Azure CLI, установите переменную среды **AzureCLIPath** в папку установки Azure CLI. AzureServiceTokenProvider добавит переменную среды в переменную среды Пути.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Вы вошли в Azure CLI с использованием нескольких учетных записей, один и тот же аккаунт имеет доступ к подпискам у нескольких арендаторов, или вы получаете ошибку доступа, отрицаемую при попытке совершать звонки во время локальной разработки

Используя Azure CLI, установите подписку по умолчанию на подписку по умолчанию на подписку, которая имеет учетную запись, которую вы хотите использовать. Подписка должна быть в том же наемном ресурсе, к который вы хотите получить доступ: **набор учетных записей az --подписка (подписка-id).** Если выход не виден, это удается. Проверить правильную учетную запись теперь по умолчанию с помощью **списка учетных записей az**.

### <a name="common-issues-across-environments"></a>Общие проблемы в разных средах

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Несанкционированный доступ, отказ в доступе, запрещенный или аналогичный погрешность

У используемого субъекта нет доступа к ресурсу, к которому он пытается получить доступ. Предоставьте доступ к ресурсу учетной записи пользователя или MSI службы AppI. Какой из них зависит от того, работаете ли вы с образцом на локальном компьютере или развернуты в Azure в службу приложений. Некоторые ресурсы, такие как хранилища ключей, также имеют свои собственные [политики доступа,](https://docs.microsoft.com/azure/key-vault/secure-your-key-vault#data-plane-and-access-policies) которые используются для предоставления доступа к принципам, таким как пользователи, приложения и группы.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Общие проблемы при развертывании в службе приложений Azure

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Управляемая идентификация не настроена в службе приложений

Проверьте переменные среды MSI_ENDPOINT и MSI_SECRET существуют с помощью [консоли отладки Kudu.](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/) Если эти переменные среды не существуют, управляемая идентификация не включена в службе приложения.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Общие проблемы при локальном развертывании с IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Не уклонить токены при отладке приложения в IIS

По умолчанию AppAuth работает в другом контексте пользователя в IIS. Вот почему он не имеет доступа к использованию идентификатора разработчика для получения токенов доступа. Можно настроить IIS для выполнения контекста пользователя следующими двумя шагами:
- Назначьте пул приложений для веб-приложения для запуска в качестве текущей учетной записи пользователя. Дополнительные сведения см. [здесь](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities).
- Настроили "setProfileEnvironment" на "True". Дополнительная информация [смотрите здесь](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Перейдите на вопрос :windir% -System32-inetsrv-config-applicationHost.config
    - Поиск "setProfileEnvironment". Если он настроен на "Ложь", измените его на "True". Если его нет, добавьте его в качестве атрибута/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironmentэлемента processModel (), и установите его на "True".

- Дополнительные сведения см. в статье [Использование управляемых удостоверений в Службе приложений и Функциях Azure](../../active-directory/managed-identities-azure-resources/index.yml).
- [Что такое проверка подлинности?](../../active-directory/develop/active-directory-authentication-scenarios.md)
