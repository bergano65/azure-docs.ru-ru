---
title: Настройка проверки подлинности
titleSuffix: Azure Machine Learning
description: Узнайте, как настроить и настроить аутентификацию для различных ресурсов и рабочих процессов в Azure Machine Learning. Существует несколько способов настройки и использования аутентификации в службе, начиная от простого auth на основе uI для целей разработки или тестирования и концентрируясь подлинности службы полной функциональности Active Directory.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283541"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Настройка проверки подлинности для ресурсов и рабочих процессов машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как настроить и настроить аутентификацию для различных ресурсов и рабочих процессов в Azure Machine Learning. Существует несколько способов проверки подлинности службы, начиная от простого auth на основе uI для целей разработки или тестирования и концентрирующей подлинностью службы активного каталога Azure Active Directory. В этой статье также разъясняютразличия в том, как работает аутентификация веб-службы, а также как аутентифицировать aPI Azure Machine Learning REST.

Это способ, как вы показывает вам, как сделать следующие задачи:

* Используйте интерактивную аутентификацию uI для тестирования/разработки
* Настройка основной аутентификации службы
* Проверка подлинности рабочего пространства
* Получите токены типа OAuth2.0 для API Azure Machine Learning REST
* Понимание аутентификации веб-службы

Ознакомиться с [концепцией статьи](concept-enterprise-security.md) можно для общего обзора безопасности и аутентификации в Azure Machine Learning.

## <a name="prerequisites"></a>Предварительные требования

* Создайте [рабочее пространство для машинного обучения Azure.](how-to-manage-workspace.md)
* [Настройте среду разработки](how-to-configure-environment.md) для установки SDK для машинного обучения Azure или используйте [VM-ноутбук Azure Machine Learning Notebook VM](concept-azure-machine-learning-architecture.md#compute-instance) с уже установленным SDK.

## <a name="interactive-authentication"></a>Интерактивная аутентификация

Большинство примеров в документации для этой службы используют интерактивную аутентификацию в ноутбуках Jupyter как простой метод тестирования и демонстрации. Это легкий способ проверить, что вы строите. Есть два вызова функции, которые автоматически подскажут вам с помощью потока проверки подлинности на основе uI.

Вызов `from_config()` функции будет выдавать запрос.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Функция `from_config()` ищет файл JSON, содержащий информацию о подключении рабочего пространства. Вы также можете указать детали соединения `Workspace` явно с помощью конструктора, который также подскажет для интерактивной аутентификации. Оба вызова эквивалентны.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Если у вас есть доступ к нескольким арендаторам, возможно, потребуется импортировать класс и четко определить, на какого арендатора вы ориентируетесь. Вызов конструктора `InteractiveLoginAuthentication` для также подскажет вам войти в систему, похожую на вызовы выше.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

В то время как полезно для тестирования и обучения, интерактивная аутентификация не поможет вам в создании автоматизированных или обезглавливых рабочих процессов. Настройка основной аутентификации служб является наилучшим подходом для автоматизированных процессов, которые используют SDK.

## <a name="set-up-service-principal-authentication"></a>Настройка основной аутентификации службы

Этот процесс необходим для обеспечения аутентификации, которая отделяется от определенного входа пользователя, что позволяет аутентифицировать подлинность для SDK Azure Machine Learning Python в автоматизированных рабочих процессах. Основная аутентификация сервиса также позволит вам [проверить подлинность НА REST API.](#azure-machine-learning-rest-api-auth)

Чтобы настроить основную аутентификацию службы, сначала создайте регистрацию приложения в Active Directory Azure, а затем предоставляете приложению ролевой доступ к рабочему пространству ML. Самый простой способ завершить эту настройку — это спомощьей [оболочки облака Azure](https://azure.microsoft.com/features/cloud-shell/) на портале Azure. После входа на портал нажмите `>_` значок в правом верхнем виде страницы рядом с вашим именем, чтобы открыть оболочку.

Если ранее в учетной записи Azure вы не использовали облачную оболочку, необходимо создать ресурс учетной записи для хранения всех написанных файлов. В целом этот счет хранения будет нести незначительную ежемесячную стоимость. Кроме того, установите расширение машинного обучения, если вы не использовали его ранее со следующей командой.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Вы должны быть админ-амином по подписке для выполнения следующих шагов.

Затем запустите следующую команду для создания основного обслуживания. Дайте ему имя, в данном случае **мл-аут**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

Выход будет JSON похож на следующее. Обратите внимание `clientId`на `clientSecret`, `tenantId` и поля, как вы будете нуждаться в них для других шагов в этой статье.

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

Затем запустите следующую команду, чтобы получить сведения о только `clientId` что созданном вами сервисе, используя значение сверху в качестве ввода параметра. `--id`

```azurecli-interactive
az ad sp show --id your-client-id
```

Ниже приводится упрощенный пример вывода JSON из команды. Примите к `objectId` сведению поле, так как вам понадобится его значение для следующего шага.

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

Затем используйте следующую команду, чтобы назначить основной доступ к рабочему пространству машинного обучения. Вам понадобится имя рабочего пространства и его `-w` `-g` название группы ресурсов для и параметров, соответственно. Для `--user` параметра используйте `objectId` значение предыдущего шага. Параметр `--role` позволяет установить роль доступа для основного сервиса, и в целом вы будете использовать либо **владельца,** либо **участника.** Оба имеют доступ к существующим ресурсам, таким как вычислительные кластеры и хранилища данных, но только **владелец** может предоставить эти ресурсы. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Этот вызов не производит никаких выходных данных, но теперь для рабочего пространства настроена основная аутентификация службы.

## <a name="authenticate-to-your-workspace"></a>Authenticate к вашему рабочему пространству

Теперь, когда у вас есть основной auth службы включен, вы можете проверить подлинность в ашего рабочего пространства в SDK без физического входа в качестве пользователя. Используйте `ServicePrincipalAuthentication` конструктор класса и используйте значения, полученные из предыдущих шагов, в качестве параметров. Параметр `tenant_id` карты `tenantId` сверху, `service_principal_id` `clientId`карты, `service_principal_password` и `clientSecret`карты .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Теперь в переменной `sp` содержится объект проверки подлинности, который используется непосредственно в SDK. В целом, рекомендуется хранить идентификаторы/секреты, используемые выше, в переменных среды, как показано в следующем коде.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Для автоматизированных рабочих процессов, которые работают в Python и используют SDK в первую очередь, вы можете использовать этот объект как-это в большинстве случаев для проверки подлинности. Следующий код проверяет подлинность рабочего пространства с помощью только что созданного вами объекта auth.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API auth

Основной сервис, созданный на приведенных выше шагах, также может быть использован для проверки подлинности aPI Azure Machine Learning [REST.](https://docs.microsoft.com/rest/api/azureml/) Вы используете [поток предоставления учетных данных клиентов](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)Azure Active Directory, который позволяет звонить об обслуживании для безголовых аутентификаций в автоматизированных рабочих процессах. Примеры реализованы с [библиотекой ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) как в Python, так и в Node.js, но вы также можете использовать любую библиотеку с открытым исходным кодом, которая поддерживает OpenID Connect 1.0. 

> [!NOTE]
> MSAL.js — это более новая библиотека, чем ADAL, но вы не можете выполнять аутентификацию обслуживания с использованием учетных данных клиента с MSAL.js, так как это в первую очередь клиентская библиотека, предназначенная для интерактивной/пользовательской проверки подлинности, привязанной к конкретному пользователю. Мы рекомендуем использовать ADAL, как показано ниже, для создания автоматизированных рабочих процессов с помощью REST API.

### <a name="nodejs"></a>Node.js

Используйте следующие шаги для создания токена auth с помощью Node.js. В вашей `npm install adal-node`среде, запустить . Затем используйте `tenantId` `clientId`свой `clientSecret` принцип службы, созданный в приведенных выше шагах, в качестве значений для соответствующих переменных в следующем сценарии.

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

Переменная `tokenResponse` — это объект, включающий маркер и связанные с ним метаданные, такие как время истечения срока действия. Токены действительны в течение 1 часа и могут быть обновлены, снова запустив тот же вызов для получения нового маркера. Ниже приводится пример ответа.

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

Используйте `accessToken` свойство, чтобы получить токен auth. Ознакомьтесь с [документацией REST API](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) для примеров использования маркера для вызова API.

### <a name="python"></a>Python 

Используйте следующие шаги для создания токена auth с помощью Python. В вашей `pip install adal`среде, запустить . Затем используйте `tenantId` `clientId`свой `clientSecret` принцип службы, созданный в приведенных выше шагах, в качестве значений для соответствующих переменных в следующем сценарии.

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

Переменная `token_response` представляет собой словарь, который включает маркер и связанные с ними метаданные, такие как время истечения срока действия. Токены действительны в течение 1 часа и могут быть обновлены, снова запустив тот же вызов для получения нового маркера. Ниже приводится пример ответа.

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

Используйте `token_response["accessToken"]` для получения токена auth. Ознакомьтесь с [документацией REST API](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) для примеров использования маркера для вызова API.

## <a name="web-service-authentication"></a>Аутентификация веб-службы

Веб-службы в Azure Machine Learning используют другой шаблон проверки подлинности, чем описано выше. Самый простой способ проверки подлинности развернутых веб-сервисов — это использование **проверки подлинности на основе ключей,** которая генерирует стеритентические ключи проверки подлинности типа носителя, которые не нуждаются в обновлении. Если вам требуется только аутентификация развернутой веб-службы, вам не нужно настроить принцип проверки подлинности службы, как показано выше.

Веб-сервисы, развернутые в службе Azure Kubernetes, по умолчанию *включены* на основе ключей. Развертываемые службы Azure Container Instances имеют отключение ключей *по* умолчанию, но можно включить его, установив `auth_enabled=True`при создании веб-службы ACI. Ниже приводится пример создания конфигурации развертывания ACI с включенным ключом auth.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Затем можно использовать пользовательскую конфигурацию ACI при развертывании с помощью `Model` класса.

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

Для того чтобы принести `aci_service.get_keys()`ключи auth, используйте . Чтобы регенерировать ключ, `regen_key()` используйте функцию и пройти либо **Первоначального** или **вторичного**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Веб-сервисы также поддерживают проверку подлинности на основе токенов, но только для развертывания службы Azure Kubernetes. [Ознакомьтесь](how-to-consume-web-service.md) с информацией о проверке подлинности.

### <a name="token-based-web-service-authentication"></a>Аутентификация веб-службы на основе токенов

При вращении маркерной аутентификации для веб-службы пользователи должны представить веб-службе для доступа к веб-службе маркер Azure Machine Learning JSON Web Token. Срок действия токена истекает по указанному таймфрейму и нуждается в обновлении, чтобы продолжать совершать звонки.

* Аутентификация токенов **отключена по умолчанию** при развертывании в службе Azure Kubernetes.
* Аутентификация маркеров **не поддерживается** при развертывании в экземплярах контейнеров Azure.

Чтобы контролировать аутентификацию `token_auth_enabled` маркеров, используйте параметр при создании или обновлении развертывания.

Если аутентификация маркеров включена, можно использовать `get_token` метод для извлечения токена JSON Web Token (JWT) и срока действия этого маркера:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> После `refresh_by` времени токена необходимо запросить новый токен. Если вам нужно обновить токены за пределами Python SDK, одним из вариантов является использование REST `service.get_token()` API с обязательной аутентификацией службы для периодическиго вызова, как обсуждалось ранее.
>
> Настоятельно рекомендуем создать рабочее пространство Azure Machine Learning в том же регионе, что и кластер службы Azure Kubernetes. 
>
> Чтобы проверить подлинность с помощью маркера, веб-служба сделает вызов в область, в которой создается рабочее пространство Azure Machine Learning. Если область рабочего пространства недоступна, вы не сможете получить токен для веб-сервиса, даже если ваш кластер находится в другом регионе от рабочего пространства. В результате проверка подлинности Azure AD недоступна до тех пор, пока область рабочего пространства не будет снова доступна. 
>
> Кроме того, чем больше расстояние между областью кластера и областью рабочего пространства, тем больше времени потребуется для получения маркера.

## <a name="next-steps"></a>Дальнейшие действия

* [Поезд и развертывание модели классификации изображений.](tutorial-train-models-with-aml.md)
* [Потребляйте модель машинного обучения Azure, развернутую в качестве веб-службы.](how-to-consume-web-service.md)
