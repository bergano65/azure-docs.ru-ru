---
title: Просмотр журналов выполнения задач - Задачи
description: Как просматривать и управлять журналами выполнения, генерируемыми ACR Tasks.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: f7098f470a3f8a0cdac019f4bf8eb8fe14330337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246972"
---
# <a name="view-and-manage-task-run-logs"></a>Просмотр и управление журналами выполнения задач

Каждая задача, выполненная в [задачах реестра контейнеров Azure,](container-registry-tasks-overview.md) генерирует выход журнала, который можно проверить, чтобы определить, успешно ли выполняются шаги задачи. 

В этой статье объясняется, как просматривать и управлять журналами выполнения задач.

## <a name="view-streamed-logs"></a>Просмотр потоковых журналов

При запуске задачи вручную выход журнала передается непосредственно на консоль. Например, когда вы запускаете задачу вручную, используя [сборку az acr,](/cli/azure/acr#az-acr-build) [az acr run](/cli/azure/acr#az-acr-run)или [az acr task run](/cli/azure/acr/task#az-acr-task-run) command, вы видите выход журнала, перетекающий на консоль. 

Следующий образец [команды az acr run](/cli/azure/acr#az-acr-run) вручную запускает задачу, выполняемую контейнером, вытягиваемым из того же реестра:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Потоковые журналы:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Просмотр сохраненных журналов 

Магазины реестра контейнеров Azure заражают журналы для всех задач. На портале Azure можно просмотреть сохраненные журналы выполнения. Или используйте команду [журналов задач az acr](/cli/azure/acr/task#az-acr-task-logs) для просмотра выбранного журнала. По умолчанию журналы сохраняются в течение 30 дней.

Если задача автоматически срабатывает, например обновлением исходного кода, доступ к сохраненным журналам является *единственным* способом просмотра журналов выполнения. Автоматические триггеры задач включают коммиты исходного кода или запросы на вытягивание, обновления базового изображения и триггеры таймеров.

Для просмотра журналов выполнения на портале:

1. Перейдите к реестру контейнеров.
1. В **службах**выберите **задачи.** > **Runs**
1. Выберите **идентификатор run** Id для просмотра состояния выполнения и журналов. При сгенерированном журнале та же информация, что и потоковый журнал.

![Просмотр портала входа в систему задач](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Для просмотра журнала с помощью Azure CLI запустите [журналы задач az acr](/cli/azure/acr/task#az-acr-task-logs) и укажите идентификатор выполнения, имя задачи или определенное изображение, создаваемое задачей сборки. Если имя задачи указано, команда отображает журнал для последнего созданного запуска.

Следующий пример выводит журнал для запуска с ID *cf4:*

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Альтернативное хранилище журналов

Возможно, вы захотите сохранить журналы выполнения задач в локальной файловой системе или использовать альтернативное решение архивирования, такое как Хранилище Azure.

Например, создайте локальный каталог *задач* и перенаправьте вывод [журналов задач az acr](/cli/azure/acr/task#az-acr-task-logs) в локальный файл:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Можно также сохранить локальные файлы журнала в Хранилище Azure. Например, для загрузки файлов в учетную запись хранилища используйте [портал Azure CLI,](../storage/blobs/storage-quickstart-blobs-cli.md) [портал Azure](../storage/blobs/storage-quickstart-blobs-portal.md)или другие методы.


## <a name="next-steps"></a>Дальнейшие действия

* Подробнее о [задачах реестра контейнеров Azure](container-registry-tasks-overview.md)

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
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
