---
title: Проверка подлинности перекрестного реестра с задачи ACR
description: Нанастройка задачи реестра контейнеров Azure (ACR Task) для доступа к другому частному реестру контейнеров Azure с помощью управляемого итога для ресурсов Azure
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47b2a50784cf56b089fea0981e5a06d581b8ba3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842505"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Проверка подлинности кросс-реестра в задаче ACR с использованием удостоверения, управляемого Azure 

В [задаче ACR](container-registry-tasks-overview.md)можно [включить управляемое удостоверение для ресурсов Azure.](container-registry-tasks-authentication-managed-identity.md) Задача может использовать итог для доступа к другим ресурсам Azure без необходимости предоставлять или управлять учетными данными. 

В этой статье вы узнаете, как включить управляемое удостоверение в задаче, чтобы вытащить изображение из реестра, отличающийся от того, который используется для выполнения задачи.

Для создания ресурсов Azure в этой статье требуется запустить версию Azure CLI 2.0.68 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][azure-cli]

## <a name="scenario-overview"></a>Обзор сценария

Пример задачи вытягивает базовое изображение из другого реестра контейнеров Azure для создания и нажатия изображения приложения. Чтобы вытащить базовое изображение, вы настраиваете задачу с управляемым идентификатором и назначаете ему соответствующие разрешения. 

В этом примере показаны шаги с использованием либо назначенного пользователем, либо системного управляемого удостоверения. Ваш выбор удостоверения личности зависит от потребностей вашей организации.

В реальном сценарии организация может поддерживать набор базовых изображений, используемых всеми командами разработчиков для создания своих приложений. Эти базовые изображения хранятся в корпоративном реестре, при этом каждая команда разработчиков имеет только права на вытягивание. 

## <a name="prerequisites"></a>Предварительные требования

Для этой статьи вам нужны два реестра контейнеров Azure:

* Первый реестр используется для создания и выполнения задач ACR. В этой статье этот реестр называется *myregistry*. 
* Во втором реестре размещает базовое изображение, используемое для создания изображения. В этой статье, второй реестр называется *mybaseregistry*. 

Заменить с вашими собственными именами реестра в более поздних шагах.

Если у вас еще нет необходимых реестров контейнеров Azure, [см.](container-registry-get-started-azure-cli.md) Вам не нужно толкать изображения в реестр еще.

## <a name="prepare-base-registry"></a>Подготовка базового реестра

Сначала создайте рабочий каталог, а затем создайте файл под названием Dockerfile со следующим содержанием. Этот простой пример создает базовое изображение Node.js из общедоступного изображения в Docker Hub.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
В текущем каталоге запустите команду [сборки az acr][az-acr-build] для создания и отодвинете базовое изображение в базовый реестр. На практике другая группа или процесс в организации может вести базовый реестр.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Определение шагов задач в файле YAML

Шаги для этого примера [многоступенчатой задачи](container-registry-tasks-multi-step.md) определены в [файле YAML.](container-registry-tasks-reference-yaml.md) Создайте файл, названный `helloworldtask.yaml` в локальном рабочем каталоге, и вставьте следующее содержимое. Обновление значения `REGISTRY_NAME` в шаге сборки с именем сервера вашего базового реестра.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

Шаг сборки `Dockerfile-app` использует файл в репо [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) для создания изображения. Ссылки `--build-arg` на базовый реестр, чтобы вытащить базовое изображение. При успешном построении изображение перемещается в реестр, используемый для выполнения задачи.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Вариант 1: Создание задачи с помощью удостоверения, назначенного пользователем

Шаги в этом разделе создают задачу и позволяют установить идентификатор, назначенный пользователем. Если вместо этого требуется включить установленное системой удостоверение, [см.](#option-2-create-task-with-system-assigned-identity) 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Создание задачи

Создайте задачу *helloworldtask,* выполняя следующую [задачу az acr создать][az-acr-task-create] команду. Задача выполняется без контекста исходного кода, `helloworldtask.yaml` и команда ссылается на файл в рабочем каталоге. Параметр `--assign-identity` передает идентификатор ресурса назначенного пользователя удостоверения. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Вариант 2: Создание задачи с системой назначенных итог

Шаги в этом разделе создают задачу и позволяют установить идентификатор, назначенный системой. Если вместо этого требуется включить удостоверение, назначенное пользователем, [см.](#option-1-create-task-with-user-assigned-identity) 

### <a name="create-task"></a>Создание задачи

Создайте задачу *helloworldtask,* выполняя следующую [задачу az acr создать][az-acr-task-create] команду. Задача выполняется без контекста исходного кода, `helloworldtask.yaml` и команда ссылается на файл в рабочем каталоге. Параметр `--assign-identity` без значения позволяет системе назначенных идентификационных задач. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>Предоставление разрешений на вытягивание удостоверения в базовом реестре

В этом разделе, дать управляемые разрешения личности, чтобы вытащить из базового реестра, *mybaseregistry*.

Используйте команду [шоу az acr,][az-acr-show] чтобы получить идентификатор ресурсов базового реестра и хранить его в переменной:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Используйте [команду создания назначения роли аз][az-role-assignment-create] `acrpull` для присвоения роли базовому реестру. Эта роль имеет разрешения только на извлечение изображений из реестра.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Добавление учетных данных целевого реестра в задачу

Теперь используйте [команду добавления задач az acr,][az-acr-task-credential-add] чтобы позволить задаче аутентифицироваться с базовым реестром, используя учетные данные интилита. Выполнить команду, соответствующую типу управляемого идентификатора, включенного в задаче. Если вы включили удостоверение `--use-identity` личности, назначенное пользователем, передайте идентификатор клиента идентификатору. Если вы включили установленную системой идентификацию, передайте `--use-identity [system]`.

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

## <a name="manually-run-the-task"></a>Ручной выполнения задачи

Чтобы убедиться, что задача, в которой вы включили управляемый итог, успешно выполняется, вручную запустите задачу с помощью команды [выполнения задачи az acr.][az-acr-task-run] 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Если задача выполняется успешно, выход аналогичен:

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

Выполнить [az acr репозиторий шоу-теги][az-acr-repository-show-tags] команды, чтобы убедиться, что изображение построено и успешно был перемещен в *myregistry*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Выходные данные примера:

```console
cf10
```

## <a name="next-steps"></a>Дальнейшие действия

* Подробнее о [включении управляемого удостоверения в задачу ACR.](container-registry-tasks-authentication-managed-identity.md)
* Посмотреть [ссылку ACR Tasks YAML](container-registry-tasks-reference-yaml.md)

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
