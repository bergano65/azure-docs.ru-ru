---
title: Настройка проверки подлинности
titleSuffix: Azure Machine Learning
description: Узнайте, как настроить проверку подлинности для различных ресурсов и рабочих процессов в Машинном обучении Azure. Существует несколько способов настройки и использования проверки подлинности в службе — от простой проверки подлинности на основе пользовательского интерфейса для разработки или тестирования до полной проверки подлинности субъекта-службы Azure Active Directory.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: has-adal-ref
ms.openlocfilehash: 6b2cfa85ea412a5ef8bda47a7ff6e99970ba6b0e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611846"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Настройка проверки подлинности для ресурсов и рабочих процессов Машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье рассказывается, как настроить проверку подлинности для различных ресурсов и рабочих процессов в Машинном обучении Azure. Существует несколько способов проверки подлинности в службе — от простой проверки подлинности на основе пользовательского интерфейса для разработки или тестирования до полной проверки подлинности субъекта-службы Azure Active Directory. В этой статье также объясняются различия в работе проверки подлинности веб-служб, а также то, как организовать проверку подлинности в REST API Машинного обучения Azure.

В этой статье рассматривается следующее:

* Использование интерактивной проверки подлинности на основе пользовательского интерфейса для тестирования и разработки
* Настройка проверки подлинности на основе субъекта-службы
* Проверка подлинности в рабочей области
* Получение маркеров носителя OAuth 2.0 для REST API Машинного обучения Azure
* Общие сведения о проверке подлинности веб-служб

Общие сведения о безопасности и проверке подлинности в Машинном обучении Azure см. в [основной статье](concept-enterprise-security.md).

## <a name="prerequisites"></a>Предварительные требования

* Создайте [рабочую область Машинного обучения Azure](how-to-manage-workspace.md).
* [Настройте среду разработки](how-to-configure-environment.md) для установки пакета SDK для Машинного обучения Azure или используйте [виртуальную машину записной книжки Машинного обучения Azure](concept-azure-machine-learning-architecture.md#compute-instance) с уже установленным пакетом SDK.

## <a name="interactive-authentication"></a>Интерактивная проверка подлинности

В большинстве примеров в документации по этой службе используется интерактивная проверка подлинности в записных книжках Jupyter, которая служит простым методом для тестирования и демонстрации. Это упрощенный способ проверки того, что вы создаете. Существует два вызова функций, которые будут автоматически запрашивать поток проверки подлинности на основе пользовательского интерфейса.

Вызов функции `from_config()` приведет к выдаче запроса.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Функция `from_config()` выполняет поиск файла JSON, который содержит сведения о подключении к рабочей области. Можно также явно указать сведения о подключении с помощью конструктора `Workspace`, который также будет запрашивать интерактивную проверку подлинности. Оба вызова эквивалентны.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

При наличии доступа к нескольким клиентам может потребоваться импортировать класс и явно определить целевой клиент. При вызове конструктора для `InteractiveLoginAuthentication` также будет предложено выполнить вход, аналогичный приведенным выше вызовам.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Интерактивная проверка подлинности подходит для тестирования и изучения, однако она не поможет вам в создании автоматизированных рабочих процессов. Настройка проверки подлинности на основе субъекта-службы лучше подходит для автоматизированных процессов, использующих пакет SDK.

## <a name="set-up-service-principal-authentication"></a>Настройка проверки подлинности на основе субъекта-службы

Этот процесс необходим для включения проверки подлинности, которая отличается от проверки подлинности с использованием конкретного имени входа, что позволяет выполнять проверку подлинности в пакете SDK для Python Машинного обучения Azure в автоматизированных рабочих процессах. Проверка подлинности на основе субъекта-службы также позволит [пройти проверку подлинности в REST API](#azure-machine-learning-rest-api-auth).

Чтобы настроить проверку подлинности на основе субъекта-службы, сначала создайте регистрацию приложения в Azure Active Directory, а затем предоставьте доступ к рабочей области ML на основе ролей приложения. Самый простой способ выполнить эту настройку — [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) на портале Azure. После входа на портал щелкните значок `>_` в правом верхнем углу страницы рядом с именем, чтобы открыть оболочку.

Если вы еще не использовали Cloud Shell в учетной записи Azure, вам потребуется создать ресурс учетной записи хранения для хранения всех записанных файлов. Ежемесячное использование этой учетной записи хранения стоит незначительную сумму. Кроме того, установите расширение машинного обучения, если оно не использовалось ранее, с помощью следующей команды.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Для выполнения следующих действий требуются разрешения администратора подписки.

Затем выполните следующую команду, чтобы создать субъект-службу. Присвойте ему имя, в данном случае **ml-auth**.

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

Затем выполните следующую команду, чтобы получить сведения о только что созданном субъекте-службе, используя значение `clientId` выше в качестве входных данных для параметра `--id`.

```azurecli-interactive
az ad sp show --id your-client-id
```

Ниже приведен упрощенный пример выходных данных JSON из команды. Запишите значение в поле `objectId`, поскольку оно понадобится на следующем шаге.

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

Затем используйте следующую команду, чтобы назначить субъекту-службе доступ к рабочей области машинного обучения. Вам потребуется имя рабочей области и имя группы ресурсов для параметров `-w` и `-g` соответственно. Для параметра `--user` используйте значение `objectId` из предыдущего шага. Параметр `--role` позволяет задать роль доступа для субъекта-службы, и в общем случае вы будете использовать либо роль **владельца**, либо роль **участника**. Обе роли имеют доступ на запись к существующим ресурсам, таким как вычислительные кластеры и хранилища данных, но подготавливать эти ресурсы может только **владелец**.

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Этот вызов не создает никаких выходных данных, но теперь для вашей рабочей области настроена проверка подлинности на основе субъекта-службы.

## <a name="authenticate-to-your-workspace"></a>Проверка подлинности в рабочей области

Теперь, когда включена проверка подлинности на основе субъекта-службы, вы можете пройти проверку подлинности в рабочей области в пакете SDK без физического входа в систему в качестве пользователя. Используйте конструктор класса `ServicePrincipalAuthentication` и используйте значения, полученные на предыдущих шагах в качестве параметров. Параметр `tenant_id` сопоставляется с `tenantId` выше, `service_principal_id` сопоставляется с `clientId`, а `service_principal_password` сопоставляется с `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Переменная `sp` теперь содержит объект проверки подлинности, который вы используете непосредственно в пакете SDK. Как правило, идентификаторы и секреты, используемые выше, рекомендуется хранить в переменных среды, как показано в следующем коде.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Для автоматизированных рабочих процессов, которые выполняются в Python и используют пакет SDK, этот объект в большинстве случаев можно использовать как есть для проверки подлинности. Следующий код выполняет проверку подлинности в рабочей области, используя только что созданный объект проверки подлинности.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Проверка подлинности REST API Машинного обучения Azure

Субъект-службу, созданную в описанных выше шагах, также можно использовать для проверки подлинности [REST API](https://docs.microsoft.com/rest/api/azureml/) Машинного обучения Azure. Используется [поток предоставления учетных данных клиента](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) Azure Active Directory, который позволяет выполнять вызовы из одной службы к другой для проверки подлинности в автоматизированных рабочих процессах без графического пользовательского интерфейса. Примеры реализуются с помощью библиотеки [ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) как в Python, так и в Node.js, но также можно использовать любую библиотеку с открытым исходным кодом, поддерживающую OpenID Connect 1.0.

> [!NOTE]
> MSAL.js — это более новая библиотека, чем ADAL, однако она не позволяет выполнять проверку подлинности между службами с помощью учетных данных клиента, поскольку, в первую очередь, это клиентская библиотека, предназначенная для интерактивной проверки подлинности или на базе пользовательского интерфейса, привязанной к конкретному пользователю. Мы рекомендуем использовать ADAL, как показано ниже, чтобы создать автоматические рабочие процессы с REST API.

### <a name="nodejs"></a>Node.js

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

Переменная `tokenResponse` — это объект, включающий маркер и связанные метаданные, такие как время истечения срока действия. Маркеры действительны в течение 1 часа. Обновить их можно путем повторного выполнения того же вызова для получения нового маркера. Пример ответа приведен ниже:

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

### <a name="python"></a>Python

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

Переменная `token_response` — это словарь, включающий маркер и связанные метаданные, такие как время истечения срока действия. Маркеры действительны в течение 1 часа. Обновить их можно путем повторного выполнения того же вызова для получения нового маркера. Пример ответа приведен ниже:

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

Веб-службы в Машинном обучении Azure используют другой шаблон проверки подлинности, отличный от описанного выше. Самый простой способ проверки подлинности в развернутых веб-службах — использовать **проверку подлинности на основе ключей**, которая создает статические ключи проверки подлинности типа носителя, не требующие обновления. Если требуется только проверка подлинности в развернутой веб-службе, не нужно настраивать проверку подлинности на основе субъекта-службы, как показано выше.

В веб-службах, развернутых в службе Azure Kubernetes, проверка подлинности на основе ключей *включена* по умолчанию. В службах, развернутых в экземплярах контейнеров Azure, проверка подлинности на основе ключей по умолчанию *отключена*, однако ее можно включить, установив `auth_enabled=True` при создании веб-службы ACI. Ниже приведен пример создания конфигурации развертывания ACI с включенной проверкой подлинности на основе ключей.

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

Веб-службы также поддерживают проверку подлинности на основе маркеров, но только для развертываний службы Kubernetes Azure. Дополнительные сведения о проверке подлинности см. в [инструкциях](how-to-consume-web-service.md) по использованию веб-служб.

### <a name="token-based-web-service-authentication"></a>Проверка подлинности веб-служб на основе маркеров

При включении проверки подлинности с помощью маркеров для веб-службы пользователи должны предоставить веб-службе Машинного обучения Azure маркер JWT для доступа к ней. Срок действия маркера безопасности истекает через заданное время, и его необходимо обновить, чтобы продолжить выполнение вызовов.

* Проверка подлинности на основе маркеров безопасности **включена по умолчанию** при развертывании в Службе Azure Kubernetes.
* При развертывании в Экземплярах контейнеров Azure проверка подлинности на основе маркеров безопасности **не поддерживается**.

Чтобы управлять аутентификацией на основе маркеров безопасности, используйте параметр `token_auth_enabled` при создании или обновлении развертывания.

Если проверка подлинности на основе токенов включена, можно использовать метод `get_token` для получения маркера JWT и его срока действия:

```python
token, refresh_by = service.get_token()
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

* [Обучение модели классификации изображений](tutorial-train-models-with-aml.md).
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md).
