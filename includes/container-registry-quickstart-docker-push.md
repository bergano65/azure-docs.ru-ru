---
title: включить файл
description: включить файл
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 16c1dec9e52be98e5b4e0103a583bdc219bc2417
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031823"
---
## <a name="push-image-to-registry"></a>Отправка образа в реестр

Чтобы отправить образ в реестр контейнеров Azure, сначала нужно получить этот образ. Если у вас еще нет образов локального контейнера, используйте следующую команду [docker pull][docker-pull], чтобы извлечь существующий образ из Docker Hub. Для этого примера извлеките образ `hello-world`.

```
docker pull hello-world
```

Прежде чем отправить образ в реестр, нужно добавить в него тег с полным именем сервера входа для реестра. Имя сервера входа имеет формат *\<registry-name\>.azurecr.io* (в нижнем регистре), например *mycontainerregistry.azurecr.io*.

Присвойте образу тег с помощью команды [docker tag][docker-tag]. Замените значение `<login-server>` именем сервера входа для экземпляра ACR.

```
docker tag hello-world <login-server>/hello-world:v1
```

Пример

```
docker tag hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


Наконец, воспользуйтесь командой [docker push][docker-push] для принудительной отправки образа в экземпляр реестра. Замените значение `<login-server>` именем сервера входа для экземпляра реестра. В этом примере создается репозиторий **hello-world** с образом `hello-world:v1`.

```
docker push <login-server>/hello-world:v1
```

После передачи образа в реестр контейнеров удалите образ `hello-world:v1` из локальной среды Docker. (Обратите внимание, что команда [docker rmi][docker-rmi] не приводит к удалению образа из репозитория **hello-world** в реестре контейнеров Azure.)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

