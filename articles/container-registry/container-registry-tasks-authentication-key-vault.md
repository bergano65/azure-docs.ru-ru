---
title: Внешняя проверка подлинности из задачи контроля доступа
description: Включите управляемое удостоверение для ресурсов Azure в задаче реестра контейнеров Azure (запись контроля доступа), чтобы разрешить задачам считывать учетные данные DOCKER Hub, хранящиеся в хранилище ключей Azure.
ms.topic: article
ms.date: 07/12/2019
ms.openlocfilehash: a7086050a4aef380f11298c819817692396216b2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456218"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Внешняя проверка подлинности в задаче контроля доступа с помощью удостоверения, управляемого Azure 

В [задаче контроля](container-registry-tasks-overview.md)доступа можно [Включить управляемое удостоверение для ресурсов Azure](container-registry-tasks-authentication-managed-identity.md). Эта задача может использовать удостоверение для доступа к другим ресурсам Azure без необходимости предоставлять учетные данные или управлять ими. 

Из этой статьи вы узнаете, как включить управляемое удостоверение в задаче, которая обращается к секретам, хранящимся в хранилище ключей Azure. 

Для создания ресурсов Azure в этой статье необходимо запустить Azure CLI версии 2.0.68 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

## <a name="scenario-overview"></a>Обзор сценария

В примере задачи считываются учетные данные концентратора DOCKER, хранящиеся в хранилище ключей Azure. Учетные данные предназначены для учетной записи центра DOCKER с разрешениями на запись (Push) в частном репозитории в центре DOCKER. Чтобы считать учетные данные, необходимо настроить задачу с помощью управляемого удостоверения и назначить ей соответствующие разрешения. Задача, связанная с удостоверением, создает образ и выполняет вход в центр DOCKER для отправки образа в частный репозиторий. 

В этом примере показаны действия, использующие назначенное пользователем или управляемое системой удостоверение. Выбор удостоверения зависит от потребностей Организации.

В реальном сценарии компания может публиковать образы в частном репозитории в центре DOCKER в рамках процесса сборки. 

## <a name="prerequisites"></a>предварительным требованиям

Вам понадобится реестр контейнеров Azure, в котором выполняется задача. В этой статье этот реестр называется *myregistry*. Замените на собственное имя реестра в последующих шагах.

Если у вас еще нет реестра контейнеров Azure, см. раздел [Краткое руководство. Создание частного реестра контейнеров с помощью Azure CLI](container-registry-get-started-azure-cli.md). Вам не нужно отправлять образы в реестр.

Вам также понадобится частный репозиторий в центре DOCKER и учетная запись центра DOCKER с разрешениями на запись в репозиторий. В этом примере этот репозиторий называется *хубусер/хубрепо*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Создание хранилища ключей и сохранение секретов

Во-первых, при необходимости создайте группу ресурсов с именем *myResourceGroup* в расположении *eastus* с помощью следующей команды [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Чтобы создать хранилище ключей, используйте команду [AZ keyvault Create][az-keyvault-create] . Не забудьте указать уникальное имя хранилища ключей. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Сохраните необходимые учетные данные DOCKER Hub в хранилище ключей с помощью команды [AZ keyvault Secret Set][az-keyvault-secret-set] . В этих командах значения передаются в переменные среды:

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

В реальном сценарии секреты, скорее всего, будут устанавливаться и поддерживаться в отдельном процессе.

## <a name="define-task-steps-in-yaml-file"></a>Определение шагов задачи в файле YAML

Шаги для этого примера задачи определяются в [файле YAML](container-registry-tasks-reference-yaml.md). Создайте файл с именем `dockerhubtask.yaml` в локальном рабочем каталоге и вставьте следующее содержимое. Не забудьте заменить имя хранилища ключей в файле именем хранилища ключей.

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: docker login --username '{{.Secrets.username}}' --password '{{.Secrets.password}}'
# Build image
  - build: -t {{.Values.PrivateRepo}}:{{.Run.ID}} https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:{{.Run.ID}}
```

Шаги задачи выполняют следующие действия.

* Управление секретными учетными данными для проверки подлинности с помощью DOCKER Hub.
* Проверяйте подлинность с помощью DOCKER Hub, передав секреты команде `docker login`.
* Создайте образ, используя пример Dockerfile в репозитории [Azure-Samples/контроля доступа к задачам](https://github.com/Azure-Samples/acr-tasks.git) .
* Отправьте образ в частный репозиторий концентратора DOCKER.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Вариант 1. Создание задачи с назначенным пользователем удостоверением

В этом разделе описывается создание задачи и включение назначенного пользователю удостоверения. Если вместо этого требуется включить назначенное системой удостоверение, см. раздел [вариант 2. Создание задачи с назначенным системой удостоверением](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Создание задачи

Создайте задачу *доккерхубтаск* , выполнив следующую команду [AZ контроля доступа Task Create][az-acr-task-create] . Задача выполняется без контекста исходного кода, а команда ссылается на файл `dockerhubtask.yaml` в рабочем каталоге. Параметр `--assign-identity` передает идентификатор ресурса назначенного пользователю удостоверения. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Вариант 2. Создание задачи с удостоверением, назначенным системой

В этом разделе описано, как создать задачу и включить назначенное системой удостоверение. Если вместо этого вы хотите включить пользовательское удостоверение, см. раздел [вариант 1. Создание задачи с назначенным пользователем удостоверением](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Создание задачи

Создайте задачу *доккерхубтаск* , выполнив следующую команду [AZ контроля доступа Task Create][az-acr-task-create] . Задача выполняется без контекста исходного кода, а команда ссылается на файл `dockerhubtask.yaml` в рабочем каталоге. Параметр `--assign-identity` без значения включает в задаче назначенное системой удостоверение.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>Предоставление удостоверений доступ к хранилищу ключей

Выполните следующую команду [AZ keyvault Set-Policy][az-keyvault-set-policy] , чтобы задать политику доступа для хранилища ключей. В следующем примере удостоверение позволяет считывать секреты из хранилища ключей. 

```azurecli
az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

## <a name="manually-run-the-task"></a>Запуск задачи вручную

Чтобы убедиться, что задача, в которой вы включили управляемое удостоверение, запущена успешно, вручную активируйте задачу с помощью команды [AZ контроля][az-acr-task-run] доступа для запуска. Параметр `--set` используется для передачи имени частного репозитория задаче. В этом примере имя репозитория-заполнителя — *хубусер/хубрепо*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo 
```

При успешном выполнении задачи выходные данные показывают успешную проверку подлинности в DOCKER Hub, и образ успешно создается и передается в частный репозиторий:

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

Чтобы убедиться, что образ отправлен, проверьте наличие тега (`cf24` в этом примере) в частном репозитории DOCKER Hub.

## <a name="next-steps"></a>Дополнительная информация

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
