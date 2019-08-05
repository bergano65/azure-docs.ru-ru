---
title: включение файла
description: включение файла
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67185843"
---
## <a name="push-image-to-registry"></a>Отправка образа в реестр

Чтобы отправить образ в реестр контейнеров Azure, сначала нужно получить этот образ. Если у вас еще нет образов локального контейнера, используйте следующую команду [docker pull][docker-pull], чтобы извлечь существующий образ из Docker Hub. Для этого примера извлеките образ `hello-world`.

```
docker pull hello-world
```

Прежде чем отправить образ в реестр, нужно добавить в него тег с полным именем сервера входа для ACR. Имя сервера входа имеет формат *\<имя_реестра\>.azurecr.io* (в нижнем регистре), например *mycontainerregistry007.azurecr.io*.

Присвойте образу тег с помощью команды [docker tag][docker-tag]. Замените значение `<acrLoginServer>` именем сервера входа для экземпляра ACR.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Наконец, воспользуйтесь командой [docker push][docker-push] для отправки образа в экземпляр ACR. Замените значение `<acrLoginServer>` именем сервера входа для экземпляра ACR. В этом примере создается репозиторий **hello-world** с образом `hello-world:v1`.

```
docker push <acrLoginServer>/hello-world:v1
```

После передачи образа в реестр контейнеров удалите образ `hello-world:v1` из локальной среды Docker. (Обратите внимание, что команда [docker rmi][docker-rmi] не приводит к удалению образа из репозитория **hello-world** в реестре контейнеров Azure.)

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

