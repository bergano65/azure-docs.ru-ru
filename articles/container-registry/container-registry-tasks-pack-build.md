---
title: Сборка изображения с помощью пакета Cloud Native Buildpack
description: Используйте команду сборки пакета az acr для создания изображения контейнера из приложения и нажатия на реестр контейнеров Azure без использования Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087085"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Создание и нажатие изображения из приложения с помощью пакета Cloud Native Buildpack

Команда `az acr pack build` Azure CLI [`pack`](https://github.com/buildpack/pack) использует инструмент CLI от [Buildpacks](https://buildpacks.io/)для создания приложения и вхотливки его изображения в реестр контейнеров Azure. Эта функция предоставляет возможность быстро гонимась в исходном коде приложения из исходного кода приложения на языках Node.js, Java и на других языках без определения Dockerfile.

Для запуска примеров в этой статье можно использовать оболочку Azure Cloud Shell или локальную установку Azure CLI. Если вы хотите использовать его локально, требуется версия 2.0.70 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][azure-cli-install]

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="use-the-build-command"></a>Используйте команду сборки

Чтобы создать и нажать изображение контейнера с помощью Cloud Native Buildpacks, запустите команду [сборки пакета az acr.][az-acr-pack-build] В то время как команда [az acr строит][az-acr-build] и выталкивает изображение из `az acr pack build` источника Dockerfile и связанного с ним кода, при этом вы указываете дерево источника приложения напрямую.

Как минимум, укажите следующее `az acr pack build`при запуске:

* Реестр контейнеров Azure, где вы управляете командой
* Имя изображения и тег для полученного изображения
* Одно из [поддерживаемых местоположений контекста](container-registry-tasks-overview.md#context-locations) для задач ACR, таких как локальный каталог, репо GitHub или удаленный тарбол
* Имя изображения buildpack builder подходит для вашего приложения. Реестр контейнеров Azure кэширует, создавая изображения, например `cloudfoundry/cnb:0.0.34-cflinuxfs3` для более быстрых сборок.  

`az acr pack build`поддерживает другие функции команд ACR Tasks, включая [переменные выполнения](container-registry-tasks-reference-yaml.md#run-variables) и [журналы выполнения задач,](container-registry-tasks-logs.md) которые передаются в потоковое и также сохраняются для последующего поиска.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Пример: Сборка изображения Node.js с помощью строителя Cloud Foundry

Следующий пример создает изображение контейнера из приложения Node.js в [azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) repo с помощью `cloudfoundry/cnb:0.0.34-cflinuxfs3` репо-строителя. Этот строитель кэшируется в реестре контейнеров Azure, поэтому `--pull` параметр не требуется:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Этот пример создает `node-app` изображение `1.0` с тегом и толкает его в реестр контейнеров *myregistry.* В этом примере целевое имя реестра явно готовится к имени изображения. Если не указано, имя сервера входа в реестр автоматически готовится к имени изображения.

Вывод командования показывает ход создания и нажатия изображения. 

После того, как изображение успешно построено, вы можете запустить его с Docker, если у вас есть он установлен. Первый знак в вашем реестре:

```azurecli
az acr login --name myregistry
```

Выполнить изображение:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Просмотрите `localhost:1337` в вашем любимом браузере, чтобы увидеть образец веб-приложения. Нажмите, `[Ctrl]+[C]` чтобы остановить контейнер.

## <a name="example-build-java-image-with-heroku-builder"></a>Пример: Создание изображения Java с помощью builder Heroku

Следующий пример создает изображение контейнера из приложения Java в репо [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) с помощью `heroku/buildpacks:18` builder. Параметр `--pull` указывает, что команда должна вытащить последнее изображение builder. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Этот пример создает `java-app` изображение, помеченное идентификатором команды запуска, и передвигает его в реестр контейнеров *myregistry.*

Вывод командования показывает ход создания и нажатия изображения. 

После того, как изображение успешно построено, вы можете запустить его с Docker, если у вас есть он установлен. Первый знак в вашем реестре:

```azurecli
az acr login --name myregistry
```

Выполнить изображение, заменив тег изображения для *runid:*

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Просмотрите `localhost:8080` в вашем любимом браузере, чтобы увидеть образец веб-приложения. Нажмите, `[Ctrl]+[C]` чтобы остановить контейнер.


## <a name="next-steps"></a>Дальнейшие действия

После создания и нажатия `az acr pack build`изображения контейнера с, вы можете развернуть его, как любое изображение к цели по вашему выбору. Варианты развертывания Azure включают в себя запуск службы [приложений](../app-service/containers/tutorial-custom-docker-image.md) или [службы Azure Kubernetes.](../aks/tutorial-kubernetes-deploy-cluster.md)

Для получения дополнительной информации о [Automate container image builds and maintenance with ACR Tasks](container-registry-tasks-overview.md)функциях ACR Tasks см.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
