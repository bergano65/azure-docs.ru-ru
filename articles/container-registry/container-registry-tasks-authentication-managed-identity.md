---
title: Использование управляемого удостоверения с реестром контейнеров Azure
description: Укажите реестр контейнеров Azure задачи доступа к ресурсам Azure, включая другие закрытым реестрам контейнеров, назначив управляемое удостоверение для ресурсов Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148027"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Используйте Azure управляемого удостоверения в задачах ACR 

Используйте [управляемое удостоверение для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) для проверки подлинности из ACR задачи в реестре контейнеров Azure или других ресурсов Azure без необходимости предоставления или управление учетными данными в коде. Например используйте управляемое удостоверение для вытягивания или отправить другой реестр образов контейнеров в качестве шага в задаче.

В этой статье вы узнаете больше об управляемых удостоверениях и как:

> [!div class="checklist"]
> * Включить присвоенным системой идентификатором или назначаемого пользователем удостоверения на задачу ACR
> * Предоставить удостоверению доступ к ресурсам Azure, таким как других реестров контейнеров Azure
> * Использование управляемого удостоверения для доступа к ресурсам из задачи 

Чтобы создать ресурсы Azure, в этой статье необходимо запустить Azure CLI версии 2.0.66 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

## <a name="why-use-a-managed-identity"></a>Для чего нужны управляемые удостоверения?

Функция управляемого удостоверения для ресурсов Azure предоставляет службам Azure автоматически управляемое удостоверение в Azure Active Directory (Azure AD). Можно настроить [определенных ресурсов Azure,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), включая задачи ACR с помощью управляемого удостоверения. Затем с использованием удостоверения вы можете получить доступ к другим ресурсам Azure, не предоставляя учетные данные в коде или сценариях.

Управляемые удостоверения бывают двух типов:

* *Назначаемые пользователем удостоверения*, которые можно назначить нескольким ресурсам и хранить столько, сколько необходимо. Назначаемые пользователем удостоверения сейчас доступны в предварительной версии.

* Объект *удостоверения, управляемые системой*, который является уникальным для определенного ресурса, например задачу ACR и продолжается в течение времени существования этого ресурса.

После настройки ресурса Azure с помощью управляемого удостоверения, предоставьте удостоверению доступ к другому ресурсу, как и любому субъекту безопасности. Например назначьте управляемое удостоверение пользователю роль по запросу, Push-уведомлений и по запросу или другие разрешения для закрытого реестра контейнеров в Azure. (Полный список ролей реестра см. в статье [Роли и разрешения реестра контейнеров Azure](container-registry-roles.md).) Удостоверению можно предоставить доступ к одному или нескольким ресурсам.

## <a name="create-container-registries"></a>Создавать реестры контейнеров

В этом руководстве вам потребуется три реестры контейнеров:

* Для создания и выполнения задач ACR используется первый реестра. В этой статье называется этого реестра источника *myregistry*. 
* Второй и третий реестры являются реестры целевой объект для выполнения первой задачи пример отправить образ, который создается. В этой статье называются реестры целевой *customregistry1* и *customregistry2*.

Замените именами своего реестра в последующих шагах.

Если у вас еще нет реестров необходимые контейнеров, см. в разделе [краткое руководство: по созданию закрытого реестра контейнеров с помощью Azure CLI](container-registry-get-started-azure-cli.md). Отправка образов в реестр еще не нужно.

## <a name="example-task-with-a-system-assigned-identity"></a>Пример: Задача с присвоенным системой идентификатором

В этом примере показано, как создать [многошаговых задач](container-registry-tasks-multi-step.md) с присвоенным системой идентификатором. Задача формирует образ, а затем использует удостоверение для проверки подлинности с помощью два реестра целевой объект для отправки образа.

Действия для выполнения этой задачи примере определены в [yaml-файл](container-registry-tasks-reference-yaml.md) с именем `testtask.yaml`. Файл расположен в каталоге multipleRegistries [acr задачи](https://github.com/Azure-Samples/acr-tasks) примеры репозитория. Здесь приведен файл:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Создать задачу с присвоенным системой идентификатором

Создать задачу *несколько reg* , выполнив следующую [az acr задача создание] [ az-acr-task-create] команды. Контекст задачи — папка multipleRegistries примерами в репозитории, и команда ссылается на файл `testtask.yaml` в репозитории. `--assign-identity` Параметр без дополнительных значений создает назначенное системой удостоверение для задачи. Эта задача настройки, чтобы вам нужно активировать ее вручную, но можно настроить его для запуска при фиксаций в репозиторий или запросе на включение внесенных изменений. 

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

В результатах выполнения команды `identity` разделе показано, удостоверение типа `SystemAssigned` устанавливается в задаче. `principalId` — Это идентификатор субъекта-службы, удостоверения:

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

Используйте [az acr задач show] [ az-acr-task-show] команду, чтобы хранить `principalId` в переменной, для использования в последующих командах:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Предоставить разрешения удостоверений Push-уведомлений на два реестра целевой контейнер

В этом разделе описано, предоставить разрешения присвоенным системой идентификатором для принудительной отправки двух целевых реестры, с именем *customregistry1* и *customregistry2*.

Сначала с помощью [az acr show] [ az-acr-show] команду, чтобы получить идентификатор ресурса для каждого реестра и хранить идентификаторы в переменных:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Используйте [Создание назначений ролей az] [ az-role-assignment-create] команду, чтобы назначить удостоверение `acrpush` роли для каждого реестра. Эта роль имеет разрешения на получение и отправка образов в реестр контейнеров.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Добавление учетных данных реестра целевого объекта к задаче

Теперь с помощью [добавьте учетные данные задачи acr az] [ az-acr-task-credential-add] команду, чтобы добавить учетные данные удостоверения к задаче, таким образом, чтобы оно могло проверять подлинность в реестрах обоих целевой.

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

### <a name="manually-run-the-task"></a>Вручную запустить задачу

Используйте [выполнения задачи az acr] [ az-acr-task-run] команду, чтобы вручную запустить задачу. `--set` Параметр используется для передачи имен входа сервера реестров двух целевых как значения переменных задач `REGISTRY1` и `REGISTRY2`.

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

## <a name="example-task-with-a-user-assigned-identity"></a>Пример: Задачи с помощью назначаемого пользователем удостоверения

В этом примере создайте назначаемое пользователем удостоверение с разрешениями на чтение секретов из Azure key vault. Это удостоверение можно назначить многоэтапный задачу, которая считывает секрет, формирует образ и входит в Azure CLI для чтения тег изображения.

### <a name="create-a-key-vault-and-store-a-secret"></a>Создание хранилища ключей и хранить секрет

Во-первых, если вам нужно создать группу ресурсов с именем *myResourceGroup* в *eastus* расположение со следующими [Создание группы az] [ az-group-create]команды:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Используйте [создать az keyvault] [ az-keyvault-create] команду, чтобы создать хранилище ключей. Не забудьте указать имя уникальным хранилища ключей. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Store пример секрета в хранилище ключей с помощью [секретный набор az keyvault] [ az-keyvault-secret-set] команды:

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Например может потребоваться сохранить учетные данные для проверки подлинности с помощью частного реестра Docker, чтобы вы можете извлечь частного образа.

### <a name="create-an-identity"></a>Создание удостоверения

Создать удостоверение с именем *myACRTasksId* в своей подписке с помощью [создать удостоверение az] [ az-identity-create] команды. Можно использовать ту же группу ресурсов, который использовался ранее для создания реестра контейнеров или хранилище ключей или его.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Чтобы настроить удостоверение на следующих шагах, выполните команду [az identity show][az-identity-show] для сохранения идентификатора ресурса и идентификатора субъекта-службы удостоверения в переменных.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Предоставить удостоверению доступ к хранилищу ключей для считывания секрета

Выполните следующую команду, [az keyvault set-policy] [ az-keyvault-set-policy] команду, чтобы задать политику доступа хранилища ключей. Следующий пример позволяет назначаемое пользователем удостоверение для получения секретов из хранилища ключей. Такой доступ понадобятся в дальнейшем для успешного выполнения задачу из нескольких шагов.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Предоставить удостоверение доступ для чтения к группе ресурсов для реестра

Выполните следующую команду, [Создание назначений ролей az] [ az-role-assignment-create] команду, чтобы назначить удостоверение роль модуля чтения, в этом случае для группы ресурсов, содержащей источник реестра. Эта роль понадобятся в дальнейшем для успешного выполнения задачу из нескольких шагов.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Создать задачу с помощью назначаемого пользователем удостоверения

Теперь создайте [многошаговых задач](container-registry-tasks-multi-step.md) и назначьте его назначаемого пользователем удостоверения. Для этого примера создайте [yaml-файл](container-registry-tasks-reference-yaml.md) с именем `managed-identities.yaml` в локальный рабочий каталог и вставьте следующее содержимое. Не забудьте заменить имя хранилища ключей в файле с именем хранилища ключей

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

Эта задача выполняет следующие функции:

* Проверяет, что он имеет доступ к секрету в хранилище ключей. Этот шаг является для демонстрационных целей. В реальной ситуации может потребоваться шаг задач, чтобы получить учетные данные для доступа к частный репозиторий Docker Hub.
* Создает и отправляет `mywebsite` образ в реестр источника.
* Журналы в Azure CLI для списка `my-website` тегов в реестре источника изображения.

Создать задачу с именем *msitask* и передать его идентификатор ресурса назначаемого пользователем удостоверения, вы создали ранее. Эта задача примере создается на основе `managed-identities.yaml` файл, сохраненный в локальном рабочем каталоге, поэтому вам нужно вручную запустить.

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

В результатах выполнения команды `identity` разделе показано, удостоверение типа `UserAssigned` устанавливается в задаче. `principalId` — Это идентификатор субъекта-службы, удостоверения:

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

### <a name="manually-run-the-task"></a>Вручную запустить задачу

Используйте [выполнения задачи az acr] [ az-acr-task-run] команду, чтобы вручную запустить задачу. `--set` Параметр используется для передачи в реестре исходного кода в задаче:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

Выходные данные показывают, разрешается секрет, изображение успешно, который отправляется, а задача выполняет вход в Azure CLI с удостоверением для считывания тега изображения из источника реестра:

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


## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали об использовании управляемых удостоверений с реестром контейнеров Azure и как:

> [!div class="checklist"]
> * Включить присвоенным системой идентификатором или пользователю назначить задачу ACR
> * Предоставить удостоверению доступ к ресурсам Azure, таким как других реестров контейнеров Azure
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
