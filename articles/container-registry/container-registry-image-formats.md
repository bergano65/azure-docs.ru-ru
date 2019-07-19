---
title: Форматы содержимого Реестра контейнеров Azure
description: Сведения о поддерживаемых форматах содержимого в Реестре контейнеров Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: c804dd7b73ea4a51c02c8b342a4ac60d992ec7c5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310680"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Форматы содержимого, поддерживаемые в Реестре контейнеров Azure

Используйте частный репозиторий в Реестре контейнеров Azure для управления одним из следующих форматов содержимого. 

## <a name="docker-compatible-container-images"></a>Образы контейнеров, совместимые с Docker

Поддерживаются следующие форматы образов контейнеров docker:

* [Манифест версии 2 образа Docker, схема 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Манифест версии 2 образа Docker, схема 2](https://docs.docker.com/registry/spec/manifest-v2-2/) — включает в себя списки манифестов, которые позволяют реестрам хранить многоплатформенные образы в одной ссылке "image: tag"

## <a name="oci-images"></a>Изображения OCI

Реестр контейнеров Azure также поддерживает образы, соответствующие [спецификации формата образа для инициативы Open Container (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md). Форматы упаковки включают [Формат изображения в формате "только в единственном числе" (SIF)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Чарты Helm

Реестр контейнеров Azure может размещать репозитории для [диаграмм Helm](https://helm.sh/), формат упаковки, используемый для быстрого управления и развертывания приложений для Kubernetes. [Клиент Helm](https://docs.helm.sh/using_helm/#installing-helm) версии 2.11.0 или более поздней поддерживается.

## <a name="next-steps"></a>Следующие шаги

* Узнайте, как [принудительно отправлять и извлекать](container-registry-get-started-docker-cli.md) образы с помощью Реестра контейнеров Azure.

* Используйте [задачи Реестра контейнеров Azure](container-registry-tasks-overview.md) для сборки и тестирования образов контейнера. 

* Используйте платформу [BuildKit Moby](https://github.com/moby/buildkit) для сборки и упаковки контейнеров в формате OCI.

* Настройте [репозиторий Helm](container-registry-helm-repos.md), размещенный в Реестре контейнеров Azure. 


