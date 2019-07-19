---
title: Использование управляемого удостоверения с задачами реестра контейнеров Azure
description: Предоставьте доступ задаче реестра контейнеров Azure к ресурсам Azure, включая другие закрытые реестры контейнеров, назначив управляемое удостоверение для ресурсов Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 46351af375ab4c6e59a3ddfba3c05c1e517fab0d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311537"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Использование управляемого удостоверения Azure в задачах контроля доступа 

Используйте [управляемое удостоверение для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) , чтобы выполнять проверку подлинности из задач контроля доступа в реестре контейнеров Azure или других ресурсах Azure без необходимости предоставлять учетные данные и управлять ими в коде. Например, можно использовать управляемое удостоверение для извлечения или отправки образов контейнеров в другой реестр в качестве шага в задаче.

В этой статье вы узнаете больше об управляемых удостоверениях и о том, как:

> [!div class="checklist"]
> * Включение назначенного системой удостоверения или назначенного пользователю удостоверения для задачи записи контроля доступа
> * Предоставление удостоверениям доступа к ресурсам Azure, таким как другие реестры контейнеров Azure
> * Использование управляемого удостоверения для доступа к ресурсам из задачи 

Для создания ресурсов Azure в этой статье необходимо запустить Azure CLI версии 2.0.66 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

## <a name="why-use-a-managed-identity"></a>Для чего нужны управляемые удостоверения?

Функция управляемого удостоверения для ресурсов Azure предоставляет службам Azure автоматически управляемое удостоверение в Azure Active Directory (Azure AD). Вы можете настроить [определенные ресурсы Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), включая задачи записи контроля доступа, с управляемым удостоверением. Затем с использованием удостоверения вы можете получить доступ к другим ресурсам Azure, не предоставляя учетные данные в коде или сценариях.

Управляемые удостоверения бывают двух типов:

* *Назначаемые пользователем удостоверения*, которые можно назначить нескольким ресурсам и хранить столько, сколько необходимо. Назначаемые пользователем удостоверения сейчас доступны в предварительной версии.

* *Управляемое системой удостоверение*, которое является уникальным для конкретного ресурса, например задачи записи контроля доступа и продолжается в течение времени существования этого ресурса.

После настройки ресурса Azure с управляемым удостоверением предоставьте удостоверение доступ к другому ресурсу, как и любой субъект безопасности. Например, назначьте управляемому удостоверению роль с запросом Pull, Push и Pull или другими разрешениями для закрытого реестра контейнеров в Azure. (Полный список ролей реестра см. в статье [Роли и разрешения реестра контейнеров Azure](container-registry-roles.md).) Удостоверению можно предоставить доступ к одному или нескольким ресурсам.

## <a name="create-container-registries"></a>Создание реестров контейнеров

В этом руководстве вам понадобятся три реестра контейнеров:

* Первый реестр используется для создания и выполнения задач контроля доступа. В этой статье этот исходный реестр называется *myregistry*. 
* Вторым и третьим реестрами являются целевые реестры для первого примера задачи для отправки образа, который он создает. В этой статье целевые реестры с именами *customregistry1* и *customregistry2*.

Замените собственными именами реестра на последующих шагах.

Если у вас еще нет необходимых реестров контейнеров Azure, см. раздел [краткое руководство. по созданию закрытого реестра контейнеров с помощью Azure CLI](container-registry-get-started-azure-cli.md). Вам не нужно отправлять образы в реестр.

## <a name="example-task-with-a-system-assigned-identity"></a>Пример: Задача с удостоверением, назначенным системой

В этом примере показано, как создать многошаговую [задачу](container-registry-tasks-multi-step.md) с назначенным системой удостоверением. Задача создает образ, а затем использует удостоверение для проверки подлинности с помощью двух целевых реестров для отправки образа.

Шаги для этого примера задачи определяются в [файле YAML](container-registry-tasks-reference-yaml.md) с именем `testtask.yaml`. Файл находится в каталоге Мултиплерегистриес репозитория с примерами записей [контроля доступа](https://github.com/Azure-Samples/acr-tasks) . Файл воспроизводится следующим образом:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Создание задачи с удостоверением, назначенным системой

Создайте задачу с *несколькими* адресами, выполнив следующую команду [AZ контроля доступа Task Create][az-acr-task-create] . Контекст задачи — это папка мултиплерегистриес репозитория Samples, а команда ссылается на файл `testtask.yaml` в репозитории. `--assign-identity` Параметр без дополнительного значения создает для задачи назначенное системой удостоверение. Эта задача настроена, поэтому ее необходимо активировать вручную, но ее можно настроить для запуска, когда фиксации отправляются в репозиторий или запрос на включение внесенных изменений. 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

В выходных данных команды в `identity` разделе показано, что в задаче задано удостоверение типа. `SystemAssigned` `principalId` — Идентификатор субъекта-службы для удостоверения:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

Используйте команду [AZ запись контроля][az-acr-task-show] доступа для сохранения `principalId` в переменной, которая будет использоваться в последующих командах:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Предоставление разрешений на принудительную отправку удостоверениям для двух целевых реестров контейнеров

В этом разделе предоставьте назначенные системе разрешения удостоверений для отправки в два целевых реестра с именами *customregistry1* и *customregistry2*.

Сначала используйте команду [AZ запись контроля][az-acr-show] доступа, чтобы получить идентификатор ресурса для каждого реестра и сохранить идентификаторы в переменных:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Используйте команду [AZ Role назначение Create][az-role-assignment-create] , чтобы назначить удостоверение `acrpush` роли для каждого реестра. Эта роль имеет разрешения на извлечение и передачу образов в реестр контейнеров.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Добавление учетных данных целевого реестра в задачу

Теперь используйте команду [AZ запись контроля учетных данных][az-acr-task-credential-add] для добавления учетных данных удостоверения в задачу, чтобы она могла проходить проверку подлинности в обоих целевых реестрах.

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>Запуск задачи вручную

Выполните команду [AZ запись контроля][az-acr-task-run] доступа, чтобы запустить задачу вручную. Параметр используется для передачи имен серверов входа двух целевых реестров в качестве значений переменных `REGISTRY1` задачи и `REGISTRY2`. `--set`

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

Она выводит выходные данные следующего вида:

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>Пример: Задача с удостоверением, назначенным пользователем

В этом примере вы создадите назначенное пользователем удостоверение с разрешениями на чтение секретов из хранилища ключей Azure. Это удостоверение назначается многошаговой задаче, которая считывает секрет, создает образ и подписывается в Azure CLI для чтения тега Image.

### <a name="create-a-key-vault-and-store-a-secret"></a>Создание хранилища ключей и сохранение секрета

Во-первых, при необходимости создайте группу ресурсов с именем *myResourceGroup* в расположении *eastus* с помощью следующей команды [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Чтобы создать хранилище ключей, используйте команду [AZ keyvault Create][az-keyvault-create] . Не забудьте указать уникальное имя хранилища ключей. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Сохраните пример секрета в хранилище ключей с помощью команды [AZ keyvault Secret Set][az-keyvault-secret-set] :

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Например, может потребоваться сохранить учетные данные для проверки подлинности в частном реестре DOCKER, чтобы можно было извлечь закрытый образ.

### <a name="create-an-identity"></a>Создание удостоверения

Создайте удостоверение с именем *мякртасксид* в подписке с помощью команды [AZ Identity Create][az-identity-create] . Вы можете использовать ту же группу ресурсов, которая использовалась ранее для создания реестра контейнеров или хранилища ключей или другого.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Чтобы настроить удостоверение в следующих шагах, используйте команду [AZ Identity][az-identity-show] -use, чтобы сохранить идентификатор ресурса удостоверения и идентификатор субъекта-службы в переменных.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Предоставление удостоверениям доступа к keyvault для чтения секрета

Выполните следующую команду [AZ keyvault Set-Policy][az-keyvault-set-policy] , чтобы задать политику доступа для хранилища ключей. В следующем примере можно назначить пользователю удостоверение для получения секретов из хранилища ключей. Этот доступ потребуется позже для успешного выполнения многоэтапной задачи.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Предоставление средству чтения удостоверений доступа к группе ресурсов для реестра

Выполните следующую команду [AZ Role Assign Create][az-role-assignment-create] , чтобы назначить удостоверение роли читателя в данном случае в группу ресурсов, содержащую исходный реестр. Для успешного выполнения многошаговой задачи эта роль потребуется позже.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Создание задачи с удостоверением, назначенным пользователем

Теперь создайте многошаговую [задачу](container-registry-tasks-multi-step.md) и назначьте ей удостоверение, назначенное пользователем. Для этого примера задачи создайте [файл YAML](container-registry-tasks-reference-yaml.md) с именем `managed-identities.yaml` в локальном рабочем каталоге и вставьте следующее содержимое. Не забудьте заменить имя хранилища ключей в файле именем хранилища ключей.

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

Эта задача выполняет следующие действия.

* Проверяет, может ли он получить доступ к секрету в хранилище ключей. Этот шаг предназначен для демонстрационных целей. В реальной ситуации может потребоваться шаг задачи для получения учетных данных для доступа к частному репозиторию DOCKER Hub.
* Создает и передает `mywebsite` образ в исходный реестр.
* Выполняет вход в Azure CLI для вывода списка `my-website` тегов изображений в исходном реестре.

Создайте задачу с именем *мситаск* и передайте ей идентификатор ресурса назначенного пользователем удостоверения, созданного ранее. Этот пример задачи создается на основе `managed-identities.yaml` файла, сохраненного в локальном рабочем каталоге, поэтому его необходимо активировать вручную.

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

В выходных данных команды в `identity` разделе показано, что в задаче задано удостоверение типа. `UserAssigned` `principalId` — Идентификатор субъекта-службы для удостоверения:

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>Запуск задачи вручную

Выполните команду [AZ запись контроля][az-acr-task-run] доступа, чтобы запустить задачу вручную. `--set` Параметр используется для передачи имени исходного реестра задаче.

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

Выходные данные показывают, что секрет разрешен, образ успешно создан и отправлен, а задача входит в Azure CLI с удостоверением для чтения тега образа из исходного реестра:

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>Следующие шаги

В этой статье вы узнали об использовании управляемых удостоверений с задачами реестра контейнеров Azure и о том, как:

> [!div class="checklist"]
> * Включение назначенного системой удостоверения или назначенного пользователю элемента в задаче записи контроля доступа
> * Предоставление удостоверениям доступа к ресурсам Azure, таким как другие реестры контейнеров Azure
> * Использование управляемого удостоверения для доступа к ресурсам из задачи  

* Дополнительные сведения см. в статье [Использование управляемых удостоверений в Службе приложений и Функциях Azure](/azure/active-directory/managed-identities-azure-resources/).

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
