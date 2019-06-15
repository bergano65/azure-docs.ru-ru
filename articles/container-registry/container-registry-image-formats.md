---
title: Форматы содержимого Реестра контейнеров Azure
description: Сведения о поддерживаемых форматах содержимого в Реестре контейнеров Azure.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: fe129847e685c7151a9b7ad7ea65abbd38530733
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60827465"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Форматы содержимого, поддерживаемые в Реестре контейнеров Azure

Используйте частный репозиторий в Реестре контейнеров Azure для управления одним из следующих форматов содержимого. 

## <a name="docker-compatible-container-images"></a>Образы контейнеров, совместимые с Docker

Поддерживаются следующие форматы изображений контейнера Docker:

* [Манифест версии 2 образа Docker, схема 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Манифест версии 2 образа Docker, схема 2](https://docs.docker.com/registry/spec/manifest-v2-2/) — включает в себя списки манифестов, которые позволяют реестрам хранить многоплатформенные образы в одной ссылке "image: tag"

## <a name="oci-images"></a>OCI образов

Реестр контейнеров Azure также поддерживает образы, которые соответствуют [спецификации формата изображения откройте контейнер инициативе Initiative](https://github.com/opencontainers/image-spec/blob/master/spec.md). Упаковка форматы включают [формат изображения Сингулярность (SIF)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Чарты Helm

Реестр контейнеров Azure можно разместить репозиториев для [Helm диаграммы](https://helm.sh/), формат упаковки, позволяет быстро приступить к развертыванию приложений для Kubernetes. [Клиент Helm](https://docs.helm.sh/using_helm/#installing-helm) версии 2.11.0 или более поздней поддерживается.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [принудительно отправлять и извлекать](container-registry-get-started-docker-cli.md) образы с помощью Реестра контейнеров Azure.

* Используйте [задачи Реестра контейнеров Azure](container-registry-tasks-overview.md) для сборки и тестирования образов контейнера. 

* Используйте платформу [BuildKit Moby](https://github.com/moby/buildkit) для сборки и упаковки контейнеров в формате OCI.

* Настройте [репозиторий Helm](container-registry-helm-repos.md), размещенный в Реестре контейнеров Azure. 


