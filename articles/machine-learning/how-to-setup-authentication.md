---
title: Настройка проверки подлинности
titleSuffix: Azure Machine Learning
description: Узнайте, как настроить проверку подлинности для различных ресурсов и рабочих процессов в Машинном обучении Azure. Существует несколько способов настройки и использования проверки подлинности в службе — от простой проверки подлинности на основе пользовательского интерфейса для разработки или тестирования до полной проверки подлинности субъекта-службы Azure Active Directory.
services: machine-learning
author: larryfr
ms.author: larryfr
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref
ms.openlocfilehash: 653ca578e9fafd245c22bcfd7db038d5c23da016
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326959"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Настройка проверки подлинности для ресурсов и рабочих процессов Машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Сведения о проверке подлинности в рабочей области Машинное обучение Azure и о моделях, развернутых в виде веб-служб.

В общем случае существует два типа проверки подлинности, которые можно использовать с Машинное обучение Azure:

* __Интерактивный__: ваша учетная запись используется в Azure Active Directory для непосредственной проверки подлинности или для получения токена, используемого для проверки подлинности. Интерактивная проверка подлинности используется во время экспериментов и итеративной разработки. Или там, где требуется управлять доступом к ресурсам (например, веб-службе) отдельно для каждого пользователя.
* __Субъект-служба__. вы создаете учетную запись субъекта службы в Azure Active Directory и используете ее для проверки подлинности или получения маркера. Субъект-служба используется, когда требуется автоматизированный процесс для проверки подлинности в службе без вмешательства пользователя. Например, сценарий непрерывной интеграции и развертывания, который проводит обучение и тестирование модели при каждом изменении кода обучения. Вы также можете использовать субъект-службу для получения маркера для проверки подлинности в веб-службе, если не требуется, чтобы конечный пользователь службы прошел проверку подлинности. Или если проверка подлинности пользователя не выполняется напрямую с помощью Azure Active Directory.

Независимо от типа проверки подлинности, используется управление доступом на основе ролей (RBAC) для определения уровня доступа к ресурсам. Например, учетной записи, используемой для получения маркера доступа для развернутой модели, требуется доступ только для чтения к рабочей области. Дополнительные сведения о RBAC см. в статье [Управление доступом к рабочей области машинное обучение Azure](how-to-assign-roles.md).

## <a name="prerequisites"></a>Предварительные требования

* Создайте [рабочую область Машинного обучения Azure](how-to-manage-workspace.md).
* [Настройте среду разработки](how-to-configure-environment.md) для установки пакета SDK для Машинного обучения Azure или используйте [виртуальную машину записной книжки Машинного обучения Azure](concept-azure-machine-learning-architecture.md#compute-instance) с уже установленным пакетом SDK.

## <a name="interactive-authentication"></a>Интерактивная проверка подлинности

В большинстве примеров в документации и примерах используется интерактивная проверка подлинности. Например, при использовании пакета SDK есть два вызова функций, которые автоматически запрашивают поток проверки подлинности на основе пользовательского интерфейса:

* Вызов функции `from_config()` приведет к выдаче запроса.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    Функция `from_config()` выполняет поиск файла JSON, который содержит сведения о подключении к рабочей области.

* При использовании `Workspace` конструктора для предоставления сведений о подписке, группе ресурсов и рабочей области будет также предложено выполнить интерактивную проверку подлинности.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> При наличии доступа к нескольким клиентам может потребоваться импортировать класс и явно определить целевой клиент. При вызове конструктора для `InteractiveLoginAuthentication` также будет предложено выполнить вход, аналогичный приведенным выше вызовам.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

## <a name="service-principal-authentication"></a>Проверка подлинности субъекта-службы

Чтобы использовать проверку подлинности субъекта-службы (SP), необходимо сначала создать пакет обновления и предоставить ему доступ к рабочей области. Как упоминалось ранее, управление доступом на основе ролей (RBAC) в Azure используется для управления доступом, поэтому необходимо также решить, какой доступ должен предоставить пакет обновления.

> [!IMPORTANT]
> При использовании субъекта-службы предоставьте ему __минимальный доступ, необходимый для задачи__ , для которой он используется. Например, владелец субъекта-службы или доступ к участнику не могут быть предоставлены, если все они используются для чтения маркера доступа для развертывания в Интернете.
>
> Причина предоставления минимального доступа заключается в том, что субъект-служба использует пароль для проверки подлинности, а пароль может храниться как часть скрипта автоматизации. При утечке пароля наличие минимального доступа, необходимого для выполнения конкретных задач, позволяет избежать вредоносного использования пакета обновления.

Самый простой способ создать пакет обновления и предоставить доступ к рабочей области — с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Чтобы создать субъект-службу и предоставить ему доступ к рабочей области, выполните следующие действия.

> [!NOTE]
> Для выполнения всех этих действий необходимо быть администратором подписки.

1. Пройдите аутентификацию в подписке Azure.

    ```azurecli-interactive
    az login
    ```

    Если CLI сможет запустить браузер по умолчанию, он откроет в браузере страницу входа. Или откройте браузер и выполните инструкции из командной строки. В инструкции входит переход к [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и ввод кода авторизации.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    Другие методы аутентификации см. в статье [Вход с помощью Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

1. Установите расширение Машинное обучение Azure:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Создайте субъект-службу. В следующем примере создается SP с именем **ML-auth** :

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    Результатом будет файл JSON, который будет выглядеть примерно так: Запишите значения в полях `clientId`, `clientSecret` и `tenantId`, поскольку они понадобятся для выполнения других действий в этой статье.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Получите сведения для субъекта-службы, используя `clientId` значение, возвращенное на предыдущем шаге:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    Следующий JSON является упрощенным примером выходных данных команды. Запишите значение в поле `objectId`, поскольку оно понадобится на следующем шаге.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Разрешить пакету обновления доступ к рабочей области Машинное обучение Azure. Вам потребуется имя рабочей области и имя группы ресурсов для параметров `-w` и `-g` соответственно. Для параметра `--user` используйте значение `objectId` из предыдущего шага. `--role`Параметр позволяет задать роль доступа для субъекта-службы. В следующем примере для роли **владельца** назначается SP. 

    > [!IMPORTANT]
    > Доступ владельца позволяет субъекту-службе выполнять практически любую операцию в рабочей области. Он используется в этом документе, чтобы продемонстрировать, как предоставить доступ. в рабочей среде Корпорация Майкрософт рекомендует предоставить субъекту-службе минимальный доступ, необходимый для выполнения роли. Дополнительные сведения см. [в статье Управление доступом к рабочей области машинное обучение Azure](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Этот вызов не создает никаких выходных данных при успешном выполнении.

### <a name="use-a-service-principal-from-the-sdk"></a>Использование субъекта-службы из пакета SDK

Чтобы выполнить аутентификацию в рабочей области из пакета SDK с помощью субъекта-службы, используйте `ServicePrincipalAuthentication` конструктор класса. Используйте значения, полученные при создании поставщика услуг в качестве параметров. Параметр `tenant_id` сопоставляется с `tenantId` выше, `service_principal_id` сопоставляется с `clientId`, а `service_principal_password` сопоставляется с `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Переменная `sp` теперь содержит объект проверки подлинности, который вы используете непосредственно в пакете SDK. Как правило, идентификаторы и секреты, используемые выше, рекомендуется хранить в переменных среды, как показано в следующем коде. Хранение в переменных среды предотвращает случайную проверку данных в репозитории GitHub.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Для автоматизированных рабочих процессов, которые выполняются в Python и используют пакет SDK, этот объект в большинстве случаев можно использовать как есть для проверки подлинности. Следующий код выполняет проверку подлинности в рабочей области с помощью созданного объекта auth.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Использование субъекта-службы из Azure CLI

Для Azure CLIных команд можно использовать субъект-службу. Дополнительные сведения см. [в разделе Вход с использованием субъекта-службы](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Использование субъекта-службы с REST API (Предварительная версия)

Субъект-служба также может использоваться для проверки подлинности Машинное обучение Azure [REST API](https://docs.microsoft.com/rest/api/azureml/) (Предварительная версия). Используется [поток предоставления учетных данных клиента](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) Azure Active Directory, который позволяет выполнять вызовы из одной службы к другой для проверки подлинности в автоматизированных рабочих процессах без графического пользовательского интерфейса. Примеры реализуются с помощью библиотеки [ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) как в Python, так и в Node.js, но также можно использовать любую библиотеку с открытым исходным кодом, поддерживающую OpenID Connect 1.0.

> [!NOTE]
> MSAL.js — это более новая библиотека, чем ADAL, однако она не позволяет выполнять проверку подлинности между службами с помощью учетных данных клиента, поскольку, в первую очередь, это клиентская библиотека, предназначенная для интерактивной проверки подлинности или на базе пользовательского интерфейса, привязанной к конкретному пользователю. Мы рекомендуем использовать ADAL, как показано ниже, чтобы создать автоматические рабочие процессы с REST API.

#### <a name="nodejs"></a>Node.js

Чтобы создать маркер проверки подлинности с помощью Node.js, выполните указанные ниже действия. В среде выполните `npm install adal-node`. Затем используйте `tenantId`, `clientId` и `clientSecret` из субъекта-службы, созданного в приведенных выше шагах, в качестве значений для соответствующих переменных в следующем сценарии.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

Переменная `tokenResponse` — это объект, включающий маркер и связанные метаданные, такие как время истечения срока действия. Маркеры действительны в течение 1 часа. Обновить их можно путем повторного выполнения того же вызова для получения нового маркера. В следующем фрагменте кода приведен пример ответа.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Используйте свойство `accessToken` для получения маркера проверки подлинности. Примеры использования маркера для выполнения вызовов API см. в [документации по REST API](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API).

#### <a name="python"></a>Python

Чтобы создать маркер проверки подлинности с помощью Python, выполните указанные ниже действия. В среде выполните `pip install adal`. Затем используйте `tenantId`, `clientId` и `clientSecret` из субъекта-службы, созданного в приведенных выше шагах, в качестве значений для соответствующих переменных в следующем сценарии.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

Переменная `token_response` — это словарь, включающий маркер и связанные метаданные, такие как время истечения срока действия. Маркеры действительны в течение 1 часа. Обновить их можно путем повторного выполнения того же вызова для получения нового маркера. В следующем фрагменте кода приведен пример ответа.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Чтобы получить маркер проверки подлинности, используйте `token_response["accessToken"]`. Примеры использования маркера для выполнения вызовов API см. в [документации по REST API](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API).

## <a name="web-service-authentication"></a>Проверка подлинности веб-служб

Развертывания модели, созданные Машинное обучение Azure предоставляют два метода проверки подлинности:

* **на основе ключа**: статический ключ используется для проверки подлинности веб-службы.
* **на основе токенов**: необходимо получить временный маркер из рабочей области и использовать его для проверки подлинности в веб-службе. Срок действия маркера истекает через определенный период времени, и его необходимо обновить, чтобы продолжить работу с веб-службой.

    > [!NOTE]
    > Проверка подлинности на основе маркеров доступна только при развертывании в службе Kubernetes Azure.

### <a name="key-based-web-service-authentication"></a>Проверка подлинности веб-службы на основе ключей

Веб-службы, развернутые в службе Azure Kubernetes Service (AKS), по умолчанию *включены* в проверку подлинности на основе ключей. Развернутые службы "экземпляры контейнеров Azure" (ACI) по умолчанию *отключили* проверку подлинности на основе ключей, но ее можно включить, задав `auth_enabled=True` при создании веб-службы ACI. Следующий код является примером создания конфигурации развертывания ACI с включенной проверкой подлинности на основе ключей.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

После этого можно будет использовать настраиваемую конфигурацию ACI в развертывании с помощью класса `Model`.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Чтобы получить ключи проверки подлинности, используйте `aci_service.get_keys()`. Чтобы повторно создать ключ, используйте функцию `regen_key()` и передайте значение **Primary** или **Secondary**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Дополнительные сведения о проверке подлинности в развернутой модели см. в разделе [Создание клиента для модели, развернутой в качестве веб-службы](how-to-consume-web-service.md).

### <a name="token-based-web-service-authentication"></a>Проверка подлинности веб-служб на основе маркеров

При включении проверки подлинности с помощью маркеров для веб-службы пользователи должны предоставить веб-службе Машинного обучения Azure маркер JWT для доступа к ней. Срок действия маркера безопасности истекает через заданное время, и его необходимо обновить, чтобы продолжить выполнение вызовов.

* Проверка подлинности на основе маркеров безопасности **включена по умолчанию** при развертывании в Службе Azure Kubernetes.
* При развертывании в Экземплярах контейнеров Azure проверка подлинности на основе маркеров безопасности **не поддерживается**.
* Аутентификация по маркеру **не может использоваться одновременно с проверкой подлинности на основе ключей**.

Чтобы управлять проверкой подлинности на маркере, используйте `token_auth_enabled` параметр при создании или обновлении развертывания:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Если проверка подлинности на основе токенов включена, можно использовать метод `get_token` для получения маркера JWT и его срока действия:

> [!TIP]
> Если вы используете субъект-службу для получения токена и хотите, чтобы он имел минимальный необходимый доступ для получения маркера, назначьте его роли **читателя** для рабочей области.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> После истечения срока действия маркера безопасности `refresh_by` необходимо запрашивать новый маркер. Если необходимо обновить маркеры за пределами пакета SDK для Python, можно использовать REST API с проверкой подлинности на основе субъекта-службы, чтобы периодически выполнять вызов `service.get_token()`, как обсуждалось ранее.
>
> Настоятельно рекомендуется создать рабочую область Машинного обучения Azure в том же регионе, что и кластер службы Azure Kubernetes.
>
> Для проверки подлинности с помощью маркера веб-служба выполняет вызов в регион, в котором создана рабочая область Машинного обучения Azure. Если регион рабочей области недоступен, вы не сможете получить маркер для веб-службы, даже если ваш кластер находится в другом регионе, отличном от региона вашей рабочей области. В результате проверка подлинности Azure AD недоступна, пока регион рабочей области снова не будет доступен.
>
> Кроме того, чем больше расстояние между регионом кластера и регионом рабочей области, тем дольше будет получение маркера.

## <a name="next-steps"></a>Дальнейшие действия

* [Использование секретов в обучении](how-to-use-secrets-in-runs.md).
* [Обучение модели классификации изображений](tutorial-train-models-with-aml.md).
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md).
