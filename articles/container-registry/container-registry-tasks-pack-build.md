---
title: Создание образа реестра контейнеров Azure из приложения
description: Используйте команду AZ Dockerfile Pack Build, чтобы создать образ контейнера из приложения и отправить его в реестр контейнеров Azure без использования.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/06/2019
ms.author: danlep
ms.openlocfilehash: 4e41bcaff8faef2c4eaec9ae852955d4b7ce354b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839907"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Сборка и отправка образа из приложения с помощью собственного облачного Буилдпакк

Команда `az acr pack build` Azure CLI [`pack`](https://github.com/buildpack/pack) использует средство CLI из [буилдпаккс](https://buildpacks.io/), чтобы создать приложение и отправить его образ в реестр контейнеров Azure. Эта функция предоставляет возможность быстро создать образ контейнера из исходного кода приложения на Node. js, Java и других языках без определения Dockerfile.

Для выполнения примеров, описанных в этой статье, можно использовать Azure Cloud Shell или локальную установку Azure CLI. Если вы хотите использовать его локально, требуется версия 2.0.70 или более поздняя. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="use-the-build-command"></a>Использование команды Build

Чтобы создать и отправить образ контейнера с помощью машинного кода Буилдпаккс, выполните команду [AZ запись пакета сборки][az-acr-pack-build] . В то время как команда [AZ запись контроля][az-acr-build] доступа создает и отправляет изображение из источника Dockerfile и связанного с ним кода, `az acr pack build` при этом непосредственно указывается дерево источника приложения.

Как минимум, при запуске `az acr pack build`необходимо указать следующее:

* Реестр контейнеров Azure, в котором выполняется команда
* Имя и тег изображения для полученного изображения
* Одно из [поддерживаемых контекстных расположений](container-registry-tasks-overview.md#quick-task) для задач записи контроля доступа, таких как локальный каталог, репозиторий GitHub или удаленный tarball.
* Имя изображения построителя Буилдпакк, например `cloudfoundry/cnb:bionic`.  

`az acr pack build`поддерживает другие функции команд задач контроля доступа, включая [переменные запуска](container-registry-tasks-reference-yaml.md#run-variables) и [журналы выполнения задач](container-registry-tasks-overview.md#view-task-logs) , которые передаются в потоке, а также сохраняются для последующего извлечения.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Пример Создание образа Node. js с помощью построителя Cloud Foundry

В следующем примере создается образ контейнера из приложения Node. js в репозитории [Azure-Samples/NodeJS-документация-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) с помощью `cloudfoundry/cnb:bionic` конструктора:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --pull --builder cloudfoundry/cnb:bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

В этом примере `node-app` образ создается `1.0` с помощью тега и отправляется в реестр контейнеров *myregistry* . Здесь имя целевого реестра явно добавляется в начало имени образа. Если этот параметр не указан, URL-адрес реестра автоматически добавляется в начало имени образа.

`--pull` Параметр указывает, что команда извлекает Последнее изображение построителя.

Выходные данные команды показывают ход создания и отправки образа. 

После успешной сборки образа его можно запустить с помощью DOCKER, если он установлен. Сначала войдите в реестр:

```azurecli
az acr login --name myregistry
```

Запустите образ:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

`localhost:1337` Перейдите в свой любимый браузер, чтобы просмотреть пример веб-приложения. Нажмите `[Ctrl]+[C]` , чтобы прерывать контейнер.

## <a name="example-build-java-image-with-heroku-builder"></a>Пример Создание образа Java с помощью построителя Heroku

В следующем примере создается образ контейнера из приложения Java в репозитории [буилдпакк/Sample-Java-App](https://github.com/buildpack/sample-java-app) с помощью `heroku/buildpacks:18` конструктора:

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

В этом примере выполняется `java-app` сборка образа, помеченного идентификатором запуска команды, и его отправка в реестр контейнеров *myregistry* .

`--pull` Параметр указывает, что команда извлекает Последнее изображение построителя.

Выходные данные команды показывают ход создания и отправки образа. 

После успешной сборки образа его можно запустить с помощью DOCKER, если он установлен. Сначала войдите в реестр:

```azurecli
az acr login --name myregistry
```

Запустите образ, подставив тег Image для *RunID*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

`localhost:8080` Перейдите в свой любимый браузер, чтобы просмотреть пример веб-приложения. Нажмите `[Ctrl]+[C]` , чтобы прерывать контейнер.


## <a name="next-steps"></a>Следующие шаги

После сборки и отправки образа контейнера с помощью `az acr pack build`его можно развернуть как любой образ в выбранном целевом объекте. Варианты развертывания Azure включают в себя запуск в [службе приложений](../app-service/containers/tutorial-custom-docker-image.md) или [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md), а также другие.

Дополнительные сведения о функциях задач записи контроля доступа см. [в статье Автоматизация создания образов контейнеров и обслуживание с помощью задач контроля учетных](container-registry-tasks-overview.md)записей.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
