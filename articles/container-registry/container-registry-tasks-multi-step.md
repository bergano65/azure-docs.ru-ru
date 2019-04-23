---
title: Автоматизация сборки, тестирования и исправления образов с помощью многошаговых задач Реестра контейнеров Azure
description: Общие сведения о многошаговых задачах — функции в составе службы "Задачи ACR" в Реестре контейнеров Azure, которая предоставляет рабочие процессы на основе задач для создания, тестирования и исправления образов контейнеров в облаке.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: ac0e4e9019a35d3fdb35c0b7af9cb1289f4bceeb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792490"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Выполнение многошаговых задач сборки, тестирования и исправления в службе "Задачи ACR"

Многошаговые задачи расширяют функциональные возможности сборки и отправки одного образа в службе "Задачи ACR" с помощью многошаговых рабочих процессов с несколькими контейнерами. Используйте многошаговые задачи для создания и отправки нескольких образов (последовательно или параллельно). Затем запустите эти образы в качестве команд в рамках выполнения одной задачи. Каждый шаг определяет операцию сборки или отправки образа контейнера, а также может определять выполнение контейнера. Каждый шаг в многошаговой задаче использует контейнер в качестве среды выполнения.

> [!IMPORTANT]
> Если вы ранее создавали задачи в предварительной версии, используя команду `az acr build-task`, эти задачи необходимо повторно создать, используя команду [az acr task][az-acr-task].

Например, можно запустить многошаговую задачу, позволяющую автоматически выполнять действия в следующей последовательности:

1. Сборка образа веб-приложения.
1. Выполнение контейнера веб-приложения.
1. Сборка тестового образа веб-приложения.
1. Выполнение контейнера для тестирования веб-приложения, который выполняет тестирование работающего контейнера приложения.
1. Если тесты проходят успешно, создайте пакет архива диаграмм Helm.
1. Выполнение команды `helm upgrade` с использованием нового пакета архива диаграмм Helm.

Все действия выполняются в рамках Azure, а значит вычисления переносятся на вычислительные ресурсы Azure, а вы в это время можете управлять инфраструктурой. Помимо Реестра контейнеров Azure вы платите только за используемые ресурсы. Сведения о ценах см. в разделе **Создание контейнера** страницы [цен на Реестр контейнеров Azure][pricing].


## <a name="common-task-scenarios"></a>Распространенные сценарии задач

Многошаговые задачи реализуют следующие сценарии:

* Сборка, маркировка и отправка одного или нескольких образов контейнеров (последовательно или параллельно).
* Выполнение модульного теста и запись его результатов и объема протестированного кода.
* Выполнение функциональных тестов и запись их результатов. Служба "Задачи ACR" поддерживает запуск нескольких контейнеров, выполняя ряд запросов между ними.
* Выполнение на основе задач, включая шаги обработки до сборки образа контейнера и после нее.
* Развертывание одного или нескольких контейнеров в целевую среду с использованием избранного модуля развертывания.

## <a name="multi-step-task-definition"></a>Определение многошаговой задачи

Многошаговая задача в службе "Задачи ACR" определяется как серия шагов в файле YAML. Каждый шаг может указывать зависимости от успешного выполнения одного или нескольких предыдущих шагов. Доступны следующие типы шагов задач:

* [`build`](container-registry-tasks-reference-yaml.md#build). Создание образов контейнеров, с помощью знакомых `docker build` синтаксис, ряда или параллельно.
* [`push`](container-registry-tasks-reference-yaml.md#push). Отправка готовые образы в реестр контейнеров. Поддерживаются такие закрытые реестры, как Реестр контейнеров Azure, а также общедоступный Центр Docker.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd). Чтобы запустите контейнер, таким образом, что он может работать как функцию в контексте выполняемой задачи. Вы можете передавать параметры в `[ENTRYPOINT]` контейнера и указать такие свойства, как env, detach, и другие знакомые параметры `docker run`. Тип шага `cmd` включает модульное и функциональное тестирование, а также параллельное выполнение контейнеров.

Следующие фрагменты кода демонстрируют, как объединить эти типы шагов задач. Многошаговая задача может быть такой же простой, как создание единого образа из файла Docker и отправка его в реестр с помощью файла YAML, аналогично этому примеру.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Или она может быть сложной, как это вымышленное многошаговое определение, включающее шаги по сборке и тестированию пакета Helm и его развертыванию (реестр контейнеров и конфигурация репозитория Helm не показаны).

```yml
version: v1.0.0
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

Файлы YAML для полного выполнения многошаговых задач и файлы Docker для нескольких сценариев см. в [примерах задач][task-examples].

## <a name="run-a-sample-task"></a>Выполнение простой задачи

Задачи поддерживают и выполнение вручную, называемое быстрым выполнением, и автоматическое выполнение при фиксации в Git или обновлении основного образа.

Чтобы выполнить задачу, сначала определите шаги задачи в файле YAML, а затем выполните команду Azure CLI [az acr run][az-acr-run].

Ниже приведен пример команды Azure CLI, которая запускает задачу, используя пример YAML-файла задачи. Ее шаги выполняют сборку, а затем отправку образа. Перед выполнением команды обновите переменную `\<acrName\>`, указав имя собственного реестра контейнеров Azure.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

При выполнении задачи выходные данные должны отображать выполнение каждого шага, определенного в файле YAML. В следующих выходных данных шаги отображаются как `acb_step_0` и `acb_step_1`.

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Дополнительные сведения об автоматизированных сборках при фиксации в Git или обновлении основного образа см. в статях [об автоматизации сборок образов](container-registry-tutorial-build-task.md) и [сборке с обновлением основного образа](container-registry-tutorial-base-image-update.md).

## <a name="next-steps"></a>Дальнейшие действия

Справочные сведения и примеры многошаговых задач можно найти здесь:

* [Справочник по задачам](container-registry-tasks-reference-yaml.md). Типы шагов задач, их свойства и использование.
* [Примеры задач][task-examples]. Примеры файлов `task.yaml` для нескольких сценариев любой сложности.
* [Репозиторий команд](https://github.com/AzureCR/cmd). Набор контейнеров и команд для Задач ACR.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task