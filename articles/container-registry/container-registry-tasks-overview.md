---
title: Обзор Задач ACR
description: An introduction to ACR Tasks, a suite of features in Azure Container Registry that provides secure, automated container image build, management, and patching in the cloud.
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: b4710591dfd78f0633d5071c78d80e300349f498
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456154"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automate container image builds and maintenance with ACR Tasks

Контейнеры предоставляют новые уровни виртуализации, изолируя зависимости приложения и разработчиков от требований в отношении инфраструктуры и эксплуатации. What remains, however, is the need to address how this application virtualization is managed and patched over the container lifecycle.

## <a name="what-is-acr-tasks"></a>Что такое "Задачи ACR"?

**Задачи ACR** — это набор компонентов в Реестре контейнеров Azure. It provides cloud-based container image building for [platforms](#image-platforms) including Linux, Windows, and ARM, and can automate [OS and framework patching](#automate-os-and-framework-patching) for your Docker containers. ACR Tasks not only extends your "inner-loop" development cycle to the cloud with on-demand container image builds, but also enables automated builds triggered by source code updates, updates to a container's base image, or timers. For example, with base image update triggers, you can automate your OS and application framework patching workflow, maintaining secure environments while adhering to the principles of immutable containers.

## <a name="task-scenarios"></a>Task scenarios

ACR Tasks supports several scenarios to build and maintain container images and other artifacts. See the following sections in this article for details.

* **[Quick task](#quick-task)** - Build and push a single container image to a container registry on-demand, in Azure, without needing a local Docker Engine installation. Выполняйте `docker build` и `docker push` в облаке.
* **Automatically triggered tasks** - Enable one or more *triggers* to build an image:
  * **[Trigger on source code update](#trigger-task-on-source-code-update)** 
  * **[Trigger on base image update](#automate-os-and-framework-patching)** 
  * **[Trigger on a schedule](#schedule-a-task)** 
* **[Multi-step task](#multi-step-tasks)** - Extend the single image build-and-push capability of ACR Tasks with multi-step, multi-container-based workflows. 

Each ACR Task has an associated [source code context](#context-locations) - the location of a set of source files used to build a container image or other artifact. Example contexts include a Git repository or a local filesystem.

Tasks can also take advantage of [run variables](container-registry-tasks-reference-yaml.md#run-variables), so you can reuse task definitions and standardize tags for images and artifacts.

## <a name="quick-task"></a>Быстрая задача

Цикл разработки "внутреннего цикла" — это итеративный процесс написания кода, сборки и тестирования приложения перед фиксацией в системе управления версиями. Это лишь начало управления жизненным циклом контейнера.

Прежде чем зафиксировать первую строку кода, функция [Быстрая задача](container-registry-tutorial-quick-task.md) службы "Задачи ACR" может обеспечить интегрированный опыт разработки, разгружая сборки образов контейнера в Azure. С помощью быстрых задач можно проверить автоматические определения сборки и перехватывать потенциальные проблемы перед фиксацией кода.

Using the familiar `docker build` format, the [az acr build][az-acr-build] command in the Azure CLI takes a [context](#context-locations) (the set of files to build), sends it ACR Tasks and, by default, pushes the built image to its registry upon completion.

For an introduction, see the quickstart to [build and run a container image](container-registry-quickstart-task-cli.md) in Azure Container Registry.  

Служба "Задачи ACR" разработана как примитив жизненного цикла контейнеров. К примеру, интегрируйте службу "Задачи ACR" в решение CI/CD. By executing [az login][az-login] with a [service principal][az-login-service-principal], your CI/CD solution could then issue [az acr build][az-acr-build] commands to kick off image builds.

Узнайте, как использовать быстрые задачи из первого руководства по Задачам ACR: [Руководство. Создание образов контейнера в облаке с помощью службы "Задачи Реестра контейнеров Azure"](container-registry-tutorial-quick-task.md).

> [!TIP]
> If you want to build and push an image directly from source code, without a Dockerfile, Azure Container Registry provides the [az acr pack build][az-acr-pack-build] command (preview). This tool builds and pushes an image from application source code using [Cloud Native Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Trigger task on source code update

Trigger a container image build or multi-step task when code is committed, or a pull request is made or updated, to a Git repository in GitHub or Azure DevOps. For example, configure a build task with the Azure CLI command [az acr task create][az-acr-task-create] by specifying a Git repository and optionally a branch and Dockerfile. When your team updates code in the repository, an ACR Tasks-created webhook triggers a build of the container image defined in the repo. 

ACR Tasks supports the following triggers when you set a Git repo as the task's context:

| Триггер | Enabled by default |
| ------- | ------------------ |
| Фиксация | ДА |
| Pull request | Нет |

To configure the trigger, you provide the task a personal access token (PAT) to set the webhook in the GitHub or Azure DevOps repo.

Узнайте, как активировать сборку при фиксации исходного кода из второго руководства по службе "Задачи ACR": [Руководство. Автоматизация сборок образов контейнера с помощью службы "Задачи Реестра контейнеров Azure"](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Автоматизация установки исправлений ОС и платформы

Возможность службы "Задачи ACR" значительно улучшать рабочий процесс сборки контейнера обуславливается ее способностью обнаруживать обновление базового образа. When the updated base image is pushed to your registry, or a base image is updated in a public repo such as in Docker Hub, ACR Tasks can automatically build any application images based on it.

Образы контейнеров можно классифицировать на *базовые* образы и образы *приложения*. К базовым образам, как правило, относятся операционная система и платформы приложений, на основе которых создано приложение, а также другие настройки. These base images are themselves typically based on public upstream images, for example: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], or [Node.js][base-node]. Несколько образов вашего приложения могут совместно использовать общий базовый образ.

Когда вышестоящая программа обслуживания обновляет образ ОС или платформу приложения, к примеру, путем внесения критического исправления системы безопасности ОС, необходимо также внести его и в базовые образы. Затем необходимо повторно выполнить сборку каждого образа приложения, чтобы внести эти вышестоящие исправления, внесенные в базовый образ.

Так как при создании образа контейнера служба "Задачи ACR" динамически находит зависимости базовых образов, она может определить время обновления базового образа приложения. С помощью одной предварительно настроенной [задачи сборки](container-registry-tutorial-base-image-update.md#create-a-task) служба "Задачи ACR" **автоматически повторно выполняет сборку каждого образа приложения**. Благодаря такому автоматическому обнаружению и повторному выполнению сборок служба "Задачи ACR" экономит время и усилия, необходимые для того, чтобы вручную отслеживать и обновлять каждый образ приложения, ссылающийся на обновленный базовый образ.

For image builds from a Dockerfile, an ACR task tracks a base image update when the base image is in one of the following locations:

* реестр контейнеров Azure, в котором выполняется задача;
* другой реестр контейнеров Azure в том же регионе; 
* общедоступный репозиторий в Docker Hub;
* общедоступный репозиторий в Реестре контейнеров Azure.

> [!NOTE]
> * The base image update trigger is enabled by default in an ACR task. 
> * Currently, ACR Tasks only tracks base image updates for application (*runtime*) images. ACR Tasks doesn't track base image updates for intermediate (*buildtime*) images used in multi-stage Dockerfiles. 

Learn more about OS and framework patching in the third ACR Tasks tutorial, [Automate image builds on base image update with Azure Container Registry Tasks](container-registry-tutorial-base-image-update.md).

## <a name="schedule-a-task"></a>Планирование задачи

Optionally schedule a task by setting up one or more *timer triggers* when you create or update the task. Scheduling a task is useful for running container workloads on a defined schedule, or running maintenance operations or tests on images pushed regularly to your registry. For details, see [Run an ACR task on a defined schedule](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Многошаговые задачи

Multi-step tasks provide step-based task definition and execution for building, testing, and patching container images in the cloud. Task steps defined in a [YAML file](container-registry-tasks-reference-yaml.md) specify individual build and push operations for container images or other artifacts. Они также могут определять выполнение одного или нескольких контейнеров, причем каждый шаг использует контейнер в качестве среды выполнения.

Например, можно создать многошаговую задачу, которая автоматизирует следующие операции:

1. Сборка образа веб-приложения.
1. Выполнение контейнера веб-приложения.
1. Сборка тестового образа веб-приложения.
1. Run the web application test container, which performs tests against the running application container
1. Если тесты проходят успешно, создайте пакет архива диаграмм Helm.
1. Выполнение команды `helm upgrade` с использованием нового пакета архива диаграмм Helm.

Многошаговые задачи позволяют разделить создание, выполнение и тестирование образов на большее количество составных шагов с поддержкой зависимостей между шагами. Благодаря многошаговым задачам в службе "Задачи ACR" у вас есть более детальный контроль над рабочими процессами создания и тестирования образа, а также исправления платформы и ОС.

Дополнительные сведения о многошаговых задачах см. в статье [Выполнение многошаговых задач сборки, тестирования и исправления в службе "Задачи ACR"](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Context locations

В следующей таблице приведено несколько примеров поддерживаемых расположений контекста для Задач ACR:

| Расположение контекста | Описание | Пример |
| ---------------- | ----------- | ------- |
| Локальная файловая система | Файлы в каталоге в локальной файловой системе. | `/home/user/projects/myapp` |
| Главная ветвь GitHub | Файлы в главной ветви (или другой ветви по умолчанию) репозитория GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Ветвь GitHub | Определенная ветвь репозитория GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Вложенная папка GitHub | Файлы во вложенной папке в репозитории GitHub. Example shows combination of a branch and subfolder specification. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Azure DevOps subfolder | Files within a subfolder in an Azure repo. Example shows combination of branch and subfolder specification. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Удаленный архив Tarball | Файлы в сжатом архиве на удаленном веб-сервере. | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>Image platforms

By default, ACR Tasks builds images for the Linux OS and the amd64 architecture. Specify the `--platform` tag to build Windows images or Linux images for other architectures. Specify the OS and optionally a supported architecture in OS/architecture format (for example, `--platform Linux/arm`). For ARM architectures, optionally specify a variant in OS/architecture/variant format (for example, `--platform Linux/arm64/v8`):

| ОС | Архитектура|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>View task logs

Each task run generates log output that you can inspect to determine whether the task steps ran successfully. If you use the [az acr build](/cli/azure/acr#az-acr-build), [az acr run](/cli/azure/acr#az-acr-run), or [az acr task run](/cli/azure/acr/task#az-acr-task-run) command to trigger the task, log output for the task run is streamed to the console and also stored for later retrieval. When a task is automatically triggered, for example by a source code commit or a base image update, task logs are only stored. View the logs for a task run in the Azure portal, or use the [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) command.

By default, data and logs for task runs in a registry are retained for 30 days and then automatically purged. If you want to archive the data for a task run, enable archiving using the [az acr task update-run](/cli/azure/acr/task#az-acr-task-update-run) command. The following example enables archiving for the task run *cf11* in registry *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Дальнейшие действия

When you're ready to automate container image builds and maintenance in the cloud, check out the [ACR Tasks tutorial series](container-registry-tutorial-quick-task.md).

При необходимости установите [расширение Docker для Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) и расширение [учетной записи Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) для работы со своими реестрами контейнеров Azure. Извлекайте и отправляйте образы в реестр контейнеров Azure или запускайте Задачи ACR в Visual Studio Code.

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
