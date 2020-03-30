---
title: Внешняя аутентификация от задачи ACR
description: Нанастройка задачи реестра контейнеров Azure (ACR Task) для чтения учетных данных Docker Hub, хранящихся в хранилище ключей Azure, с помощью управляемого итисходительности для ресурсов Azure.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47d3d643ee1287ef4f444095a2c6cfe6dcab294b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842526"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Внешняя аутентификация в задаче ACR с использованием иственных данных, управляемых Azure 

В [задаче ACR](container-registry-tasks-overview.md)можно [включить управляемое удостоверение для ресурсов Azure.](container-registry-tasks-authentication-managed-identity.md) Задача может использовать итог для доступа к другим ресурсам Azure без необходимости предоставлять или управлять учетными данными. 

В этой статье вы узнаете, как включить управляемое удостоверение в задаче, которая получает доступ к секретам, хранящимся в хранилище ключей Azure. 

Для создания ресурсов Azure в этой статье требуется запустить версию Azure CLI 2.0.68 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][azure-cli]

## <a name="scenario-overview"></a>Обзор сценария

В примере задачи считывается учетные данные Docker Hub, хранящиеся в хранилище ключей Azure. Учетные данные для учетной записи Docker Hub с разрешениями на запись (толчок) в частный репозиторий Docker Hub. Чтобы прочитать учетные данные, вы настраиваете задачу с управляемым идентификатором и назначаете ей соответствующие разрешения. Задача, связанная с идентификацией, создает изображение и подписывается в Docker Hub, чтобы подтолкнуть изображение к частному репо. 

В этом примере показаны шаги с использованием либо назначенного пользователем, либо системного управляемого удостоверения. Ваш выбор удостоверения личности зависит от потребностей вашей организации.

В реальном сценарии компания может публиковать изображения в частном репо в Docker Hub в рамках процесса сборки. 

## <a name="prerequisites"></a>Предварительные требования

Вам нужен реестр контейнеров Azure, в котором вы запустите задачу. В этой статье этот реестр называется *myregistry*. Замените свое собственное имя реестра в более поздних шагах.

Если у вас еще нет реестра контейнеров [Quickstart: Create a private container registry using the Azure CLI](container-registry-get-started-azure-cli.md)Azure, см. Вам не нужно толкать изображения в реестр еще.

Также необходимо получить частный репозиторий в Docker Hub и учетную запись Docker Hub с разрешениями на запись в репо. В этом примере это репо называется *hubuser/hubrepo.* 

## <a name="create-a-key-vault-and-store-secrets"></a>Создание хранилища ключей и хранение секретов

Во-первых, если вам нужно, создайте группу ресурсов под названием *myResourceGroup* в *восточном* месте со следующей [группой az создать][az-group-create] команду:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Используйте команду [создания клавиатуры az][az-keyvault-create] для создания хранилища ключей. Обязательно укажите уникальное имя хранилища ключей. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Храните необходимые учетные данные Docker Hub в хранилище ключей, используя [команду секретного набора ключей az keyvault.][az-keyvault-secret-set] В этих командах значения передаются в переменных среды:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

В реальном сценарии секреты, скорее всего, будут установлены и сохранены в отдельном процессе.

## <a name="define-task-steps-in-yaml-file"></a>Определение шагов задач в файле YAML

Шаги для этой примерной задачи определены в [файле YAML.](container-registry-tasks-reference-yaml.md) Создайте файл, названный `dockerhubtask.yaml` в локальном рабочем каталоге, и вставьте следующее содержимое. Не забудьте заменить имя хранилища ключа в файле с именем вашего свода ключа.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

Шаги задачи делают следующее:

* Управление секретными учетными данными для проверки подлинности с помощью Docker Hub.
* Authenticate с Docker концентратор, передавая секреты `docker login` команде.
* Создайте изображение с помощью образца Dockerfile в репо [Azure-Samples/acr-tasks.](https://github.com/Azure-Samples/acr-tasks.git)
* Нажмите изображение в частное репозиторий Docker Hub.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Вариант 1: Создание задачи с помощью удостоверения, назначенного пользователем

Шаги в этом разделе создают задачу и позволяют установить идентификатор, назначенный пользователем. Если вместо этого требуется включить установленное системой удостоверение, [см.](#option-2-create-task-with-system-assigned-identity) 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Создание задачи

Создайте задачу *dockerhubtask,* выполняя следующую [задачу az acr создать][az-acr-task-create] команду. Задача выполняется без контекста исходного кода, `dockerhubtask.yaml` и команда ссылается на файл в рабочем каталоге. Параметр `--assign-identity` передает идентификатор ресурса назначенного пользователя удостоверения. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Вариант 2: Создание задачи с системой назначенных итог

Шаги в этом разделе создают задачу и позволяют установить идентификатор, назначенный системой. Если вместо этого требуется включить удостоверение, назначенное пользователем, [см.](#option-1-create-task-with-user-assigned-identity) 

### <a name="create-task"></a>Создание задачи

Создайте задачу *dockerhubtask,* выполняя следующую [задачу az acr создать][az-acr-task-create] команду. Задача выполняется без контекста исходного кода, `dockerhubtask.yaml` и команда ссылается на файл в рабочем каталоге. Параметр `--assign-identity` без значения позволяет системе назначенных идентификационных задач.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>Предоставление идентификационных данных к хранилищу ключей

Запустите следующую команду [набора ключов azvault,][az-keyvault-set-policy] чтобы установить политику доступа в хранилище ключей. Следующий пример позволяет идентификационной информации читать секреты из хранилища ключей. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Ручной выполнения задачи

Чтобы убедиться, что задача, в которой вы включили управляемый итог, успешно выполняется, вручную запустите задачу с помощью команды [выполнения задачи az acr.][az-acr-task-run] Параметр `--set` используется для передачи имени частного репо задаче. В этом примере имя заполнителя репо *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Когда задача выполняется успешно, выход показывает успешную аутентификацию Docker Hub, и изображение успешно построено и перемещено в частное репо:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Чтобы подтвердить нажатие изображения, проверьте`cf24` тег (в данном примере) в частном репо Docker Hub.

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
[az-identity-create]: /cli/azure/identity#az-identity-create
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
