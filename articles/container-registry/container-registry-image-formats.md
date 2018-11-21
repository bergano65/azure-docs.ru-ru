---
title: Форматы содержимого Реестра контейнеров Azure
description: Сведения о поддерживаемых форматах содержимого в Реестре контейнеров Azure.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2018
ms.author: danlep
ms.openlocfilehash: e7155604339bc634078fd022e05ede5f902bc0d8
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634848"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Форматы содержимого, поддерживаемые в Реестре контейнеров Azure

Используйте частный репозиторий в Реестре контейнеров Azure для управления одним из следующих форматов содержимого. 

## <a name="docker-compatible-container-images"></a>Образы контейнеров, совместимые с Docker

* [Манифест версии 2 образа Docker, схема 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Манифест версии 2 образа Docker, схема 2](https://docs.docker.com/registry/spec/manifest-v2-2/) — включает в себя списки манифестов, которые позволяют реестрам хранить многоплатформенные образы в одной ссылке "image: tag"

* [Спецификация формата образа Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md) 


## <a name="helm-charts"></a>Чарты Helm

Реестр контейнеров Azure также может размещать репозитории для [чартов Helm](https://helm.sh/), формата упаковки, используемого для быстрого управления приложениями для Kubernetes и их развертывания. [Клиент Helm](https://docs.helm.sh/using_helm/#installing-helm) версии 2.11.0 или более поздней поддерживается.

## <a name="next-steps"></a>Дополнительная информация

* Узнайте, как [принудительно отправлять и извлекать](container-registry-get-started-docker-cli.md) образы с помощью Реестра контейнеров Azure.

* Используйте [задачи Реестра контейнеров Azure](container-registry-tasks-overview.md) для сборки и тестирования образов контейнера. 

* Используйте платформу [BuildKit Moby](https://github.com/moby/buildkit) для сборки и упаковки контейнеров в формате OCI.

* Настройте [репозиторий Helm](container-registry-helm-repos.md), размещенный в Реестре контейнеров Azure. 


