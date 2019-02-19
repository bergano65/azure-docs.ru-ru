---
title: Руководство. Автоматизация сборок образов контейнера с помощью набора функций "Задачи Реестра контейнеров Azure"
description: Из этого руководства вы узнаете, как настроить задачу Реестра контейнеров Azure, чтобы автоматически активировать сборки образов контейнера в облаке при фиксации исходного кода в репозитории Git.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 763ff0d5f619d2808fb06c05d5b266160b3a7069
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868571"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>Руководство. Автоматизация создания образов контейнеров в облаке при фиксации исходного кода

В дополнение к функции [быстрой задачи](container-registry-tutorial-quick-task.md) решение "Задачи ACR" поддерживает автоматические сборки образов контейнера Docker с помощью *задачи сборки*. Из этого руководства вы узнаете, как с помощью Azure CLI создать задание, которое автоматически активирует сборки образов в облаке при фиксации исходного кода в репозитории Git.

В этом руководстве, второй части серии:

> [!div class="checklist"]
> * Создание задачи
> * Тестирование задачи
> * Представление состояния задачи
> * Активация задачи с помощью фиксации кода

В этом руководстве предполагается, что вы уже выполнили шаги в [предыдущем руководстве](container-registry-tutorial-quick-task.md). Если вы еще этого не сделали, выполните действия, описанные в разделе [Предварительные требования](container-registry-tutorial-quick-task.md#prerequisites) в предыдущем руководстве, прежде чем перед продолжить.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы хотите использовать Azure CLI локально, установите Azure CLI версии **2.0.46** или более поздней и выполните вход с помощью [az login][az-login]. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо установить или обновить CLI, см. статью [Установка Azure CLI][azure-cli].

## <a name="prerequisites"></a>Предварительные требования

### <a name="get-sample-code"></a>Получение примера кода

В этом руководстве предполагается, что вы уже выполнили шаги в [предыдущем руководстве](container-registry-tutorial-quick-task.md), создали вилку и клонировали пример репозитория. Если вы еще этого не сделали, выполните действия, описанные в разделе [Предварительные требования](container-registry-tutorial-quick-task.md#prerequisites) в предыдущем руководстве, прежде чем перед продолжить.

### <a name="container-registry"></a>Реестр контейнеров

Для выполнения заданий данного руководства необходимо иметь реестр контейнеров Azure в подписке Azure. Если у вас его нет, воспользуйтесь инструкциями в [предыдущем руководстве](container-registry-tutorial-quick-task.md) или [кратком руководстве по созданию реестра контейнеров с помощью Azure CLI](container-registry-get-started-azure-cli.md).

## <a name="overview-of-acr-tasks"></a>Общие сведения о решении "Задачи ACR"

Задача определяет свойства автоматической сборки, включая расположение исходного образа контейнера и событие, которое запускает сборку. Когда происходит событие, определенное в задаче, например фиксация в репозитории Git, решение "Задачи ACR" инициирует сборку образа контейнера в облаке. Затем оно по умолчанию помещает успешно созданный образ в реестр контейнеров Azure, указанный в задаче.

Решение "Задачи ACR" в настоящее время поддерживает следующие триггеры:

* фиксация в репозитории Git;
* обновление базового образа.

В этом руководстве в Реестре контейнеров Azure создается и отправляется один контейнер изображения, указанный в Dockerfile. С помощью решения "Задачи ACR" можно также запускать [многошаговые задачи](container-registry-tasks-multi-step.md) (сейчас в предварительной версии), используя файл YAML для определения действий по созданию, отправке и тестированию при необходимости нескольких контейнеров.

## <a name="create-a-build-task"></a>Создание задачи сборки

В этом разделе вы сначала создаете личный маркер доступа GitHub (PAT) для использования с решением "Задачи ACR". Затем вы создаете задачу, которая запускает сборку при фиксации кода в вилке репозитория.

### <a name="create-a-github-personal-access-token"></a>Создание личного маркера доступа GitHub

Чтобы инициировать сборку при фиксации кода в репозитории Git, решению "Задачи ACR" требуется личный маркер доступа для доступа к репозиторию. Выполните следующие действия для создания PAT в GitHub:

1. Перейдите на страницу создания PAT на GitHub по адресу https://github.com/settings/tokens/new.
1. Введите краткое **описание** маркера, например "ACR Task Demo".
1. Под флажком **repo** установите флажки **repo:status** и **public_repo**.

   ![Снимок экрана со страницей создания личного маркера доступа GitHub][build-task-01-new-token]

1. Нажмите кнопку **Generate token** (Создать маркер) (может появиться запрос на подтверждение пароля).
1. Скопируйте и сохраните созданный маркер в **защищенном расположении** (вы используете этот маркер при определении задачи в следующем разделе).

   ![Снимок экрана созданного личного маркера доступа на GitHub][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Создание задачи сборки

Теперь, когда вы выполнили шаги, необходимые для включения в решении "Задачи ACR" чтения состояния фиксации и создания веб-перехватчиков в репозитории, вы можете создать задачу, которая запускает сборку образа контейнера при фиксации кода в репозитории.

Сначала заполните эти переменные среды оболочки значениями, подходящими для вашей среды. Этот шаг не является обязательным, но он упрощает выполнение многолинейных команд Azure CLI в этом руководстве. Если вы не заполните эти переменные среды, нужно будет вручную заменять каждое значение, где бы оно не появлялось в примерах команд.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Теперь создайте задачу, выполнив следующую команду [az acr task create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Если вы ранее создавали задачи в предварительной версии, используя команду `az acr build-task`, эти задачи необходимо повторно создать, используя команду [az acr task][az-acr-task].

Эта задача указывает, что каждый раз при фиксации кода в *главной* ветви репозитория, указанной в `--context`, решение "Задачи ACR" создает образ контейнера из кода в этой ветви. Для сборки образа используется Dockerfile, определяемый с помощью `--file` из корня репозитория. Аргумент `--image` указывает параметризованное значение `{{.Run.ID}}` для части с версией в теге образа, гарантируя, что образ коррелирует с конкретной сборкой и имеет уникальный тег.

Выходные данные успешно выполненной команды [az acr task create][az-acr-task-create] имеют следующий вид:

```console
$ az acr task create \
>     --registry $ACR_NAME \
>     --name taskhelloworld \
>     --image helloworld:{{.Run.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
>     --branch master \
>     --file Dockerfile \
>     --git-access-token $GIT_PAT
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2018-09-14T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-build-task"></a>Тестирование задачи сборки

Теперь у вас есть задача, которая определяет сборку. Чтобы протестировать конвейер сборки, запустите сборку вручную, выполнив команду [​​az acr task run][az-acr-task-run]:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

По умолчанию при выполнении команда `az acr task run` передает выходные данные журнала в консоль.

```console
$ az acr task run --registry $ACR_NAME --name taskhelloworld

2018/09/17 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2018/09/17 22:51:00 Setting up Docker configuration...
2018/09/17 22:51:02 Successfully set up Docker configuration
2018/09/17 22:51:02 Logging in to registry: myregistry.azurecr.io
2018/09/17 22:51:03 Successfully logged in
2018/09/17 22:51:03 Executing step: build
2018/09/17 22:51:03 Obtaining source code and scanning for dependencies...
2018/09/17 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 5f574fcf5816
Step 3/5 : RUN cd /src && npm install
 ---> Running in b1bca3b5f4fc
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.078s
Removing intermediate container b1bca3b5f4fc
 ---> 44457db20dac
Step 4/5 : EXPOSE 80
 ---> Running in 9e6f63ec612f
Removing intermediate container 9e6f63ec612f
 ---> 74c3e8ea0d98
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:11 Executing step: push
2018/09/17 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2018/09/17 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2018/09/17 22:51:21 Populating digests for step id: build...
2018/09/17 22:51:22 Successfully populated digests for step id: build
2018/09/17 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: da2 was successful after 27s
```

## <a name="trigger-a-build-with-a-commit"></a>Активация сборки с помощью фиксации

Теперь, когда вы протестировали задачу, выполнив ее вручную, запустите ее автоматически, изменив исходный код.

Сначала перейдите в каталог, содержащий локальный клон вашего [репозитория][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Затем выполните следующие команды для создания, фиксации и передачи нового файла в вилку вашего репозитория GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Может потребоваться предоставить ваши учетные данные GitHub при выполнении команды `git push`. Введите свое имя пользователя GitHub и укажите созданный ранее личный маркер доступа в качестве пароля.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

После того как вы передали фиксацию в ваш репозиторий, веб-перехватчик, созданный решением "Задачи ACR", запускается и выполняет сборку в службе "Реестр контейнеров Azure". Отобразите журналы текущей задачи, чтобы проверить ее и отслеживать ход ее выполнения:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Выходные данные выглядят следующим образом. Они показывают выполняемую (или последнюю выполненную) задачу:

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: da4

[...]

Run ID: da4 was successful after 38s
```

## <a name="list-builds"></a>Вывод списка сборок

Чтобы просмотреть список выполняемых задач, которые решение "Задачи ACR" выполнило в реестре, запустите команду [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Результат этой команды должен выглядеть примерно следующим образом: Отобразятся сборки, созданные решением "Задачи ACR", а также команда "Git Commit" в столбце TRIGGER для самой последней задачи:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как использовать задачу, чтобы автоматически активировать сборки образов контейнера в Azure при фиксации исходного кода в репозитории Git. Перейдите к следующему руководству, чтобы узнать, как создавать задачи, которые активируют сборку при обновлении базового образа контейнера.

> [!div class="nextstepaction"]
> [Руководство по автоматизации сборки образа при обновлении базового образа с помощью сборки реестра контейнеров Azure](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr
[az-acr-task-create]: /cli/azure/acr
[az-acr-task-run]: /cli/azure/acr
[az-acr-task-list-runs]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
