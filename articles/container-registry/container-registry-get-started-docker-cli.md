---
title: Отправка образа Docker в частный реестр контейнеров Azure
description: Отправка образов Docker в частный реестр контейнеров в Azure и их получение с помощью интерфейса командной строки Docker
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 2cb401dfd68075ff0867ae3f89eee3474000b5de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60828771"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Отправка первого образа в частный реестр контейнеров Docker с помощью интерфейса командной строки Docker

В реестре контейнеров Azure хранятся частные образы контейнеров [Docker](https://hub.docker.com), а также осуществляется управление ими (подобно тому, как в [Docker Hub](https://hub.docker.com/) хранятся общедоступные образы Docker). [Интерфейс командной строки Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) позволяет [входить](https://docs.docker.com/engine/reference/commandline/login/) в реестр контейнеров, а также выполнять [отправку](https://docs.docker.com/engine/reference/commandline/push/), [извлечение](https://docs.docker.com/engine/reference/commandline/pull/) и другие операции.

Выполняя следующие действия, вы скачаете официальный [образ Nginx](https://store.docker.com/images/nginx) из общедоступного реестра Docker Hub, поместите его в частный реестр контейнеров Azure, отправите его в свой реестр, а затем извлечете его от туда.

## <a name="prerequisites"></a>Технические условия

* **Реестр контейнеров Azure.** Создайте реестр контейнеров в своей подписке Azure. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI**. Также необходим локально установленный модуль Docker. Docker предоставляет пакеты, которые позволяют быстро настроить Docker в любой системе: [macOS][docker-mac], [Windows][docker-windows] или [Linux][docker-linux].

## <a name="log-in-to-a-registry"></a>Вход в раздел реестра

Существуют [несколько способов выполнить аутентификацию](container-registry-authentication.md) в частном реестре контейнеров. При работе в командной строке мы рекомендуем выполнить команду Azure CLI [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Например, чтобы войти в реестр с именем *myregistry*, выполните следующую команду:

```azurecli
az acr login --name myregistry
```

Вы также можете выполнить вход с помощью команды [docker login](https://docs.docker.com/engine/reference/commandline/login/). Например, [назначение субъекта-службы](container-registry-authentication.md#service-principal) для реестра позволяет автоматизировать некоторые сценарии. При запуске следующей команды, когда появляется запрос в интерактивном режиме, укажите идентификатор приложения (имя пользователя) и пароль субъекта-службы. Рекомендации по управлению учетными данными см. в справочнике по команде [docker login](https://docs.docker.com/engine/reference/commandline/login/).

```
docker login myregistry.azurecr.io
```

По завершению обе команды возвращают `Login Succeeded`.

> [!TIP]
> Всегда указывайте полное имя реестра (все знаки в нижнем реестре), когда используете `docker login` и когда отмечаете изображения тегами для отправки в свой реестр. В примерах этой статьи полное доменное имя выглядит так: *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Извлечение официального образа Nginx

Сначала общедоступный образ Nginx нужно отправить на локальный компьютер.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Локальный запуск контейнера

Выполните указанную ниже команду [docker run](https://docs.docker.com/engine/reference/run/), чтобы запустить локальный экземпляр контейнера Nginx в интерактивном режиме (`-it`) на порте 8080. Аргумент `--rm` указывает, что контейнер следует удалить, когда вы его остановите.

```
docker run -it --rm -p 8080:80 nginx
```

Перейдите к `http://localhost:8080` для просмотра веб-страницы по умолчанию, обслуживаемую Nginx в выполняющемся контейнере. Вы должны увидеть страницу, аналогичную показанной ниже:

![Nginx на локальном компьютере](./media/container-registry-get-started-docker-cli/nginx.png)

Так как вы запустили контейнер в интерактивном режиме с помощью `-it`, вы можете увидеть выходные данные сервера Nginx в командной строке после перемещения к нему в браузере.

Чтобы остановить и удалить контейнер, нажмите `Control`+`C`.

## <a name="create-an-alias-of-the-image"></a>Создание псевдонима образа

Выполните команду [docker tag](https://docs.docker.com/engine/reference/commandline/tag/), чтобы создать псевдоним образа с полным путем к вашему реестру. Чтобы избежать беспорядка в корне реестра, эта команда указывает пространство имен `samples`.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Дополнительные сведения о расстановке тегов с помощью пространства имен см. в разделе [Пространства имен репозитория](container-registry-best-practices.md#repository-namespaces) статьи [Рекомендации по использованию реестра контейнеров Azure](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Отправка образа в реестр

Теперь, когда вы отметили образ с абсолютным путем к частному реестру тегами, его можно отправить в реестр, выполнив команду [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Извлечение образа из реестра

Выполните команду [docker pull](https://docs.docker.com/engine/reference/commandline/pull/), чтобы извлечь образ из реестра:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Запуск контейнера Nginx

Выполните команду [docker run](https://docs.docker.com/engine/reference/run/), чтобы запустить образ, извлеченный из реестра:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Перейдите к `http://localhost:8080` Чтобы просмотреть запущенный контейнер.

Чтобы остановить и удалить контейнер, нажмите `Control`+`C`.

## <a name="remove-the-image-optional"></a>Удаление образа (необязательно)

Если вам больше не нужен образ Nginx, можно удалить его локально, выполнив команду [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Чтобы удалить образы из реестра контейнеров Azure, выполните команду Azure CLI [az acr repository delete](/cli/azure/acr/repository#az-acr-repository-delete). Например, эта команда удаляет манифест, на который ссылается тег `samples/nginx:latest`, все данные отдельного слоя и все другие теги, ссылающиеся на манифест.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы знаете основы, можно приступать к использованию реестра. Например, можно развернуть образы контейнера из реестра в следующие службы:

* [Служба Azure Kubernetes (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Экземпляры контейнеров Azure](../container-instances/container-instances-tutorial-prepare-app.md);
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

При необходимости установите [расширение Docker для Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) и расширение [учетной записи Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) для работы со своими реестрами контейнеров Azure. Извлекайте и отправляйте образы в реестр контейнеров Azure или запускайте Задачи ACR в Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
