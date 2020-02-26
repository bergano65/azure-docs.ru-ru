---
title: Настройка проверки подлинности
titleSuffix: Azure Machine Learning
description: Узнайте, как настроить и настроить проверку подлинности для различных ресурсов и рабочих процессов в Машинное обучение Azure. Существует несколько способов настройки и использования проверки подлинности в службе, в зависимости от простой проверки подлинности на основе пользовательского интерфейса в целях разработки или тестирования, до полной Azure Active Directory субъекта-службы.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602588"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Настройка проверки подлинности для ресурсов Машинное обучение Azure и рабочих процессов
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как настроить и настроить проверку подлинности для различных ресурсов и рабочих процессов в Машинное обучение Azure. Существует несколько способов проверки подлинности в службе, от простой проверки подлинности на основе пользовательского интерфейса для разработки или тестирования до полной Azure Active Directory проверки подлинности субъекта-службы. В этой статье также объясняются различия в работе проверки подлинности веб-служб, а также проверка подлинности в Машинное обучение Azure REST API.

В этом пошаговом окне показано, как выполнить следующие задачи.

* Использование интерактивной проверки подлинности пользовательского интерфейса для тестирования и разработки
* Настройка проверки подлинности субъекта-службы
* Проверка подлинности в рабочей области
* Получение токенов типа носителя OAuth 2.0 для Машинное обучение Azure REST API
* Общие сведения о проверке подлинности веб-служб

Общие сведения о безопасности и проверке подлинности в Машинное обучение Azure см. в [статье концепции](concept-enterprise-security.md) .

## <a name="prerequisites"></a>Предварительные требования

* Создайте [рабочую область Машинного обучения Azure](how-to-manage-workspace.md).
* [Настройте среду разработки](how-to-configure-environment.md) , чтобы установить пакет sdk для машинное обучение Azure, или используйте [виртуальную машину машинное обучение Azure Notebook](concept-azure-machine-learning-architecture.md#compute-instance) с уже установленным пакетом SDK.

## <a name="interactive-authentication"></a>Интерактивная проверка подлинности

Большинство примеров в документации по этой службе используют интерактивную проверку подлинности в записных книжках Jupyter в качестве простого метода для тестирования и демонстрации. Это упрощенный способ проверки того, что вы собираете. Существует два вызова функций, которые будут автоматически запрашивать поток проверки подлинности на основе пользовательского интерфейса.

Вызов функции `from_config()` приведет к выдаче запроса.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Функция `from_config()` ищет JSON-файл, содержащий сведения о подключении к рабочей области. Можно также явно указать сведения о соединении с помощью конструктора `Workspace`, который также будет запрашивать интерактивную проверку подлинности. Оба вызова эквивалентны.

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

Полезно для тестирования и обучения, но Интерактивная проверка подлинности не поможет вам создавать автоматизированные рабочие процессы. Настройка проверки подлинности субъекта-службы является лучшим подходом для автоматизированных процессов, использующих пакет SDK.

## <a name="set-up-service-principal-authentication"></a>Настройка проверки подлинности субъекта-службы

Этот процесс необходим для включения проверки подлинности, которая отделяется от конкретного имени входа пользователя, что позволяет выполнять проверку подлинности в пакете SDK для Машинное обучение Azure Python в автоматизированных рабочих процессах. Проверка подлинности субъекта-службы также позволит выполнить [проверку подлинности в REST API](#azure-machine-learning-rest-api-auth).

Чтобы настроить проверку подлинности субъекта-службы, сначала создайте регистрацию приложения в Azure Active Directory, а затем предоставьте доступ к рабочей области ML на основе ролей приложения. Самый простой способ завершить эту установку — выполнить [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) в портал Azure. После входа на портал щелкните значок `>_` в правом верхнем углу страницы рядом с именем, чтобы открыть оболочку.

Если вы еще не использовали Cloud Shell в учетной записи Azure, вам потребуется создать ресурс учетной записи хранения для хранения всех записанных файлов. В общем, эта учетная запись хранения повлечет за собой незначительное ежемесячное выставление стоимости. Кроме того, установите расширение машинного обучения, если оно не использовалось ранее с помощью следующей команды.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Для выполнения следующих действий необходимо быть администратором подписки.

Затем выполните следующую команду, чтобы создать субъект-службу. Присвойте ему имя, в данном случае это **ML-auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

Выходные данные будут выглядеть как JSON, как показано ниже. Запишите поля `clientId`, `clientSecret`и `tenantId`, так как они понадобятся для выполнения других действий в этой статье.

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

Ниже приведен упрощенный пример выходных данных JSON из команды. Запишите поле `objectId`, так как оно понадобится для следующего шага.

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

Затем используйте следующую команду, чтобы назначить субъекту-службе доступ к рабочей области машинного обучения. Вам потребуется имя рабочей области и имя группы ресурсов для параметров `-w` и `-g` соответственно. Для параметра `--user` используйте значение `objectId` из предыдущего шага. Параметр `--role` позволяет задать роль доступа для субъекта-службы, и в общем случае вы будете использовать либо **владельца** , либо **участника**. Оба имеют доступ на запись к существующим ресурсам, таким как COMPUTE CLUSTERS и хранилища данных, но только **владелец** может подготавливать эти ресурсы. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Этот вызов не создает никаких выходных данных, но теперь для вашей рабочей области настроена проверка подлинности субъекта-службы.

## <a name="authenticate-to-your-workspace"></a>Аутентификация в рабочей области

Теперь, когда включена проверка подлинности субъекта-службы, вы можете пройти проверку подлинности в рабочей области в пакете SDK без физического входа в систему в качестве пользователя. Используйте конструктор класса `ServicePrincipalAuthentication` и используйте значения, полученные из предыдущих шагов в качестве параметров. `tenant_id` параметр сопоставляется с `tenantId` выше, `service_principal_id` сопоставляется с `clientId`, а `service_principal_password` сопоставляется `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Переменная `sp` теперь содержит объект проверки подлинности, который вы используете непосредственно в пакете SDK. Как правило, рекомендуется хранить идентификаторы и секреты, используемые выше, в переменных среды, как показано в следующем коде.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Для автоматических рабочих процессов, которые выполняются в Python и используют пакет SDK, этот объект можно использовать как есть в большинстве случаев для проверки подлинности. Следующий код выполняет проверку подлинности в рабочей области, используя только что созданный объект auth.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Проверка подлинности Машинное обучение Azure REST API

Субъект-службу, созданную в описанных выше шагах, также можно использовать для проверки подлинности Машинное обучение Azure [REST API](https://docs.microsoft.com/rest/api/azureml/). Вы используете [поток предоставления учетных данных клиента](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)Azure Active Directory, который разрешает вызовы между службами для бездисплейной проверки подлинности в автоматизированных рабочих процессах. Примеры реализуются с помощью [библиотеки ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) в Python и Node. js, но также можно использовать любую библиотеку с открытым исходным кодом, поддерживающую openid connect Connect 1,0. 

> [!NOTE]
> MSAL. js — это более новая библиотека, чем ADAL, но нельзя выполнять проверку подлинности между службами с помощью учетных данных клиента с MSAL. js, так как это, в первую очередь, клиентская библиотека, предназначенная для интерактивной проверки подлинности или пользовательского интерфейса, привязанной к конкретному пользователю. Мы рекомендуем использовать ADAL, как показано ниже, чтобы создать автоматические рабочие процессы с REST API.

### <a name="nodejs"></a>Node.js

Чтобы создать токен проверки подлинности с помощью Node. js, выполните следующие действия. В среде выполните `npm install adal-node`. Затем используйте `tenantId`, `clientId`и `clientSecret` от субъекта-службы, созданного в приведенных выше шагах, в качестве значений для соответствующих переменных в следующем скрипте.

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

Переменная `tokenResponse` — это объект, включающий маркер и связанные метаданные, такие как время истечения срока действия. Токены допустимы в течение 1 часа и могут быть обновлены путем повторного выполнения одного вызова для получения нового маркера. Ниже приведен пример ответа.

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

Используйте свойство `accessToken` для выборки токена проверки подлинности. Примеры использования маркера для выполнения вызовов API см. в [документации по REST API](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) .

### <a name="python"></a>Python 

Чтобы создать маркер проверки подлинности с помощью Python, выполните следующие действия. В среде выполните `pip install adal`. Затем используйте `tenantId`, `clientId`и `clientSecret` от субъекта-службы, созданного в приведенных выше шагах, в качестве значений для соответствующих переменных в следующем скрипте.

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

Переменная `token_response` — это словарь, включающий маркер и связанные метаданные, такие как время истечения срока действия. Токены допустимы в течение 1 часа и могут быть обновлены путем повторного выполнения одного вызова для получения нового маркера. Ниже приведен пример ответа.

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

Чтобы получить маркер проверки подлинности, используйте `token_response["accessToken"]`. Примеры использования маркера для выполнения вызовов API см. в [документации по REST API](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) .

## <a name="web-service-authentication"></a>Проверка подлинности веб-служб

Веб-службы в Машинное обучение Azure используют другой шаблон проверки подлинности, чем описано выше. Самый простой способ проверки подлинности в развернутых веб-службах — использовать **проверку подлинности на основе ключей**, которая создает статические ключи проверки подлинности типа носителя, которые не требуется обновлять. Если требуется только проверка подлинности в развернутой веб-службе, не нужно настраивать проверку подлинности участника-службы, как показано выше.

Веб-службы, развернутые в службе Azure Kubernetes, по умолчанию *включены* в проверку подлинности на основе ключей. Развернутые службы экземпляров контейнеров Azure по умолчанию *отключили* проверку подлинности на основе ключей, но ее можно включить, установив `auth_enabled=True`при создании веб-службы ACI. Ниже приведен пример создания конфигурации развертывания ACI с включенной проверкой подлинности на основе ключей.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Затем можно использовать настраиваемую конфигурацию ACI в развертывании с помощью класса `Model`.

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

Чтобы получить ключи проверки подлинности, используйте `aci_service.get_keys()`. Чтобы повторно создать ключ, используйте функцию `regen_key()` и передайте **первичную** или **вторичную реплику**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Веб-службы также поддерживают проверку подлинности на основе маркеров, но только для развертываний службы Kubernetes Azure. Дополнительные сведения о проверке подлинности см [. в статье Использование веб](how-to-consume-web-service.md) -служб.

### <a name="token-based-web-service-authentication"></a>Проверка подлинности веб-службы на основе токенов

При включении проверки подлинности с помощью маркеров для веб-службы пользователи должны предоставить веб-службе Машинное обучение Azure JSON Web Token для доступа к ней. Срок действия маркера истекает через заданный промежуток времени, и его необходимо обновить, чтобы продолжить выполнение вызовов.

* При развертывании в службе Kubernetes Azure проверка подлинности по маркеру **отключена по умолчанию** .
* Проверка подлинности с помощью маркеров **не поддерживается** при развертывании в службе "экземпляры контейнеров Azure".

Чтобы управлять проверкой подлинности на маркере, используйте параметр `token_auth_enabled` при создании или обновлении развертывания.

Если включена проверка подлинности на маркере, можно использовать метод `get_token` для получения JSON Web Token (JWT) и срока действия маркера:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> После `refresh_by` времени маркера необходимо запросить новый токен. Если необходимо обновить маркеры за пределами пакета SDK для Python, можно использовать REST API с проверкой подлинности субъекта-службы, чтобы периодически выполнять вызов `service.get_token()`, как обсуждалось ранее.
>
> Настоятельно рекомендуется создать рабочую область Машинное обучение Azure в том же регионе, что и кластер службы Azure Kubernetes. 
>
> Для проверки подлинности с помощью маркера веб-служба выполнит вызов региона, в котором создана Рабочая область Машинное обучение Azure. Если регион рабочей области недоступен, вы не сможете получить маркер для веб-службы, даже если ваш кластер находится в другом регионе из рабочей области. В результате аутентификация Azure AD недоступна, пока регион рабочей области не будет снова доступен. 
>
> Кроме того, чем больше расстояние между регионом кластера и регионом рабочей области, тем дольше будет получена лексема.

## <a name="next-steps"></a>Следующие шаги

* [Обучение и развертывание модели классификации изображений](tutorial-train-models-with-aml.md).
* [Использование модели машинное обучение Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md).
