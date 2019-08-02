---
title: Проверка подлинности между реестром в задаче реестра контейнеров Azure
description: Включите управляемое удостоверение для ресурсов Azure в задаче реестра контейнеров Azure (запись контроля доступа), чтобы разрешить задаче доступ к другому частному реестру контейнеров.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: 8fac70e7e5125ae86b2b5ce13041bbf1fd067bbe
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642076"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Проверка подлинности между реестром в задаче контроля доступа с помощью удостоверения, управляемого Azure 

В [задаче контроля](container-registry-tasks-overview.md)доступа можно [Включить управляемое удостоверение для ресурсов Azure](container-registry-tasks-authentication-managed-identity.md). Эта задача может использовать удостоверение для доступа к другим ресурсам Azure без необходимости предоставлять учетные данные или управлять ими. 

Из этой статьи вы узнаете, как включить управляемое удостоверение в задаче, которая извлекает образ из реестра, отличающегося от используемого для выполнения задачи.

Для создания ресурсов Azure в этой статье необходимо запустить Azure CLI версии 2.0.68 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

## <a name="scenario-overview"></a>Обзор сценария

В примере задачи извлекается базовый образ из другого реестра контейнеров Azure для создания и отправки образа приложения. Чтобы извлечь базовый образ, необходимо настроить задачу с помощью управляемого удостоверения и назначить ей соответствующие разрешения. 

В этом примере показаны действия, использующие назначенное пользователем или управляемое системой удостоверение. Выбор удостоверения зависит от потребностей Организации.

В реальной ситуации Организация может поддерживать набор базовых образов, используемых всеми группами разработчиков для создания приложений. Эти базовые образы хранятся в корпоративном реестре, и каждая группа разработчиков имеет только права на вытягивание. 

## <a name="prerequisites"></a>предварительные требования

В этой статье вам понадобятся два реестра контейнеров Azure:

* Первый реестр используется для создания и выполнения задач контроля доступа. В этой статье этот реестр называется *myregistry*. 
* Во втором реестре размещается базовый образ, используемый задачей для создания образа. В этой статье второй реестр называется *мибасерегистри*. 

Замените собственными именами реестра на последующих шагах.

Если у вас еще нет необходимых реестров контейнеров Azure, см. раздел [краткое руководство. по созданию закрытого реестра контейнеров с помощью Azure CLI](container-registry-get-started-azure-cli.md). Вам не нужно отправлять образы в реестр.

## <a name="prepare-base-registry"></a>Подготовка базового реестра

Сначала создайте рабочий каталог, а затем создайте файл с именем Dockerfile со следующим содержимым. В этом простом примере создается базовый образ Node. js из общедоступного образа в центре DOCKER.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
В текущем каталоге выполните команду AZ запись [контроля][az-acr-build] доступа, чтобы создать и отправить базовый образ в базовый реестр. На практике другая команда или процесс в Организации может поддерживать базовый реестр.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Определение шагов задачи в файле YAML

Шаги для этого примера многошаговой [задачи](container-registry-tasks-multi-step.md) определяются в [файле YAML](container-registry-tasks-reference-yaml.md). Создайте файл с именем `helloworldtask.yaml` в локальном рабочем каталоге и вставьте в него следующее содержимое. Обновите значение `REGISTRY_NAME` на шаге сборки, указав имя сервера в базовом реестре.

```yml
version: v1.0.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Для создания образа на этапе `Dockerfile-app` сборки используется файл в репозитории [Azure-Samples/контроля учетных записей-сборка-HelloWorld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) . Объект `--build-arg` ссылается на базовый реестр для извлечения базового образа. После успешной сборки образ помещается в реестр, используемый для выполнения задачи.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Вариант 1. Создание задачи с удостоверением, назначенным пользователем

В этом разделе описывается создание задачи и включение назначенного пользователю удостоверения. Если вместо этого вы хотите включить назначенное системой удостоверение, см [. вариант 2. Создать задачу с назначенным системой удостоверением](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Создание задачи

Создайте задачу *хелловорлдтаск* , выполнив следующую команду [AZ контроля доступа Task Create][az-acr-task-create] . Контекст задачи является локальной системой, а команда ссылается на файл `helloworldtask.yaml` в рабочем каталоге. `--assign-identity` Параметр передает идентификатор ресурса назначенного пользователю удостоверения. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Вариант 2. Создание задачи с удостоверением, назначенным системой

В этом разделе описано, как создать задачу и включить назначенное системой удостоверение. Если вместо этого вы хотите включить пользовательское удостоверение, см [. вариант 1. Создать задачу с назначенным пользователем удостоверением](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Создание задачи

Создайте задачу *хелловорлдтаск* , выполнив следующую команду [AZ контроля доступа Task Create][az-acr-task-create] . Контекст задачи является локальной системой, а команда ссылается на файл `helloworldtask.yaml` в рабочем каталоге. `--assign-identity` Параметр без значения активирует для задачи назначенное системой удостоверение. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>Предоставление удостоверений для получения разрешений на базовый реестр

В этом разделе предоставьте управляемому удостоверению разрешения на извлечение из базового реестра *мибасерегистри*.

Используйте команду [AZ запись контроля][az-acr-show] доступа, чтобы получить идентификатор ресурса для базового реестра и сохранить его в переменной:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Используйте команду [AZ Role назначение Create][az-role-assignment-create] , чтобы назначить удостоверение `acrpull` роли базовому реестру. Эта роль имеет разрешения только на извлечение изображений из реестра.

```azurecli
az role assignment create --assignee $principalID --scope $baseregID --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Добавление учетных данных целевого реестра в задачу

Теперь используйте команду [AZ запись контроля учетных данных][az-acr-task-credential-add] для добавления учетных данных удостоверения в задачу, чтобы она могла проходить проверку подлинности в основном реестре. Выполните команду, соответствующую типу управляемого удостоверения, включенного в задаче. Если вы включили назначенное пользователем удостоверение, `--use-identity` передайте идентификатор клиента удостоверения. Если вы включили назначенное системой удостоверение, `--use-identity [system]`передайте.

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>Запуск задачи вручную

Чтобы убедиться, что задача, в которой вы включили управляемое удостоверение, запущена успешно, вручную активируйте задачу с помощью команды [AZ контроля][az-acr-task-run] доступа для запуска. 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Если задача выполняется успешно, выходные данные выглядят примерно так:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Выполните команду [AZ myregistry Repository][az-acr-repository-show-tags] , чтобы убедиться, что образ создан и успешно передан в:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Выходные данные примера:

```console
cf10
```

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о [включении управляемого удостоверения в задаче контроля](container-registry-tasks-authentication-managed-identity.md)доступа.
* См. раздел [YAML Tasks Reference](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
