---
title: Создание образа реестра контейнеров Azure из приложения
description: Используйте команду AZ Dockerfile Pack Build, чтобы создать образ контейнера из приложения и отправить его в реестр контейнеров Azure без использования.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 10/10/2019
ms.author: danlep
ms.openlocfilehash: b544820a0c496e0814de44790ea9c28878031a7d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293902"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Сборка и отправка образа из приложения с помощью собственного облачного Буилдпакк

Команда Azure CLI `az acr pack build` использует средство CLI [`pack`](https://github.com/buildpack/pack) , из [буилдпаккс](https://buildpacks.io/)для создания приложения и отправки его образа в реестр контейнеров Azure. Эта функция предоставляет возможность быстро создать образ контейнера из исходного кода приложения на Node. js, Java и других языках без определения Dockerfile.

Для выполнения примеров, описанных в этой статье, можно использовать Azure Cloud Shell или локальную установку Azure CLI. Если вы хотите использовать его локально, требуется версия 2.0.70 или более поздняя. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="use-the-build-command"></a>Использование команды Build

Чтобы создать и отправить образ контейнера с помощью машинного кода Буилдпаккс, выполните команду [AZ запись пакета сборки][az-acr-pack-build] . В то время как команда [AZ запись контроля][az-acr-build] доступа создает и отправляет изображение из источника Dockerfile и связанного кода, с `az acr pack build` вы указываете дерево источника приложения напрямую.

Как минимум, при запуске `az acr pack build` необходимо указать следующее:

* Реестр контейнеров Azure, в котором выполняется команда
* Имя и тег изображения для полученного изображения
* Одно из [поддерживаемых контекстных расположений](container-registry-tasks-overview.md#context-locations) для задач записи контроля доступа, таких как локальный каталог, репозиторий GitHub или удаленный tarball.
* Имя изображения построителя Буилдпакк, например `cloudfoundry/cnb:0.0.12-bionic`.  

`az acr pack build` поддерживает другие функции команд задач контроля доступа, включая [переменные запуска](container-registry-tasks-reference-yaml.md#run-variables) и [журналы выполнения задач](container-registry-tasks-overview.md#view-task-logs) , которые передаются в потоке и сохраняются для последующего извлечения.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Пример: Создание образа Node. js с помощью построителя Cloud Foundry

В следующем примере создается образ контейнера из приложения Node. js в репозитории [Azure-Samples/NodeJS-документация-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) с помощью построителя `cloudfoundry/cnb:0.0.12-bionic`:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --pull --builder cloudfoundry/cnb:0.0.12-bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

В этом примере создается образ `node-app` с тегом `1.0` и отправляется в реестр контейнеров *myregistry* . Здесь имя целевого реестра явно добавляется в начало имени образа. Если этот параметр не указан, URL-адрес реестра автоматически добавляется в начало имени образа.

Параметр `--pull` указывает, что команда извлекает Последнее изображение построителя.

Выходные данные команды показывают ход создания и отправки образа. 

После успешной сборки образа его можно запустить с помощью DOCKER, если он установлен. Сначала войдите в реестр:

```azurecli
az acr login --name myregistry
```

Запустите образ:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Перейдите к `localhost:1337` в любимом браузере, чтобы просмотреть пример веб-приложения. Нажмите `[Ctrl]+[C]`, чтобы прерывать контейнер.

## <a name="example-build-java-image-with-heroku-builder"></a>Пример: Создание образа Java с помощью построителя Heroku

В следующем примере создается образ контейнера из приложения Java в репозитории [буилдпакк/Sample-Java-App](https://github.com/buildpack/sample-java-app) , используя построитель `heroku/buildpacks:18`:

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

В этом примере создается образ `java-app` с тегом ID запуска команды и отправляется в реестр контейнеров *myregistry* .

Параметр `--pull` указывает, что команда извлекает Последнее изображение построителя.

Выходные данные команды показывают ход создания и отправки образа. 

После успешной сборки образа его можно запустить с помощью DOCKER, если он установлен. Сначала войдите в реестр:

```azurecli
az acr login --name myregistry
```

Запустите образ, подставив тег Image для *RunID*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Перейдите к `localhost:8080` в любимом браузере, чтобы просмотреть пример веб-приложения. Нажмите `[Ctrl]+[C]`, чтобы прерывать контейнер.


## <a name="next-steps"></a>Следующие шаги

После сборки и отправки образа контейнера с `az acr pack build` можно развернуть его как любое изображение в выбранном целевом объекте. Варианты развертывания Azure включают в себя запуск в [службе приложений](../app-service/containers/tutorial-custom-docker-image.md) или [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md), а также другие.

Дополнительные сведения о функциях задач записи контроля доступа см. [в статье Автоматизация создания образов контейнеров и обслуживание с помощью задач контроля учетных](container-registry-tasks-overview.md)записей.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
