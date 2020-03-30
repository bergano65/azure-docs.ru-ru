---
title: Форматы поддерживаемого контента
description: Узнайте о форматах содержимого, поддерживаемых реестром контейнеров Azure, включая изображения контейнеров, совместимых с Docker, диаграммы руля, изображения OCI и артефакты OCI.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247011"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Форматы содержимого, поддерживаемые в Реестре контейнеров Azure

Используйте частный репозиторий в Реестре контейнеров Azure для управления одним из следующих форматов содержимого. 

## <a name="docker-compatible-container-images"></a>Образы контейнеров, совместимые с Docker

Поддерживаются следующие форматы изображения контейнеров Docker:

* [Манифест версии 2 образа Docker, схема 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Манифест версии 2 образа Docker, схема 2](https://docs.docker.com/registry/spec/manifest-v2-2/) — включает в себя списки манифестов, которые позволяют реестрам хранить многоплатформенные образы в одной ссылке "image: tag"

## <a name="oci-images"></a>Изображения OCI

Реестр контейнеров Azure поддерживает изображения, которые соответствуют [спецификации формата формата изображений Open Container Initiative (OCI).](https://github.com/opencontainers/image-spec/blob/master/spec.md) Форматы упаковки включают [формат сингулярности изображения (SIF).](https://github.com/sylabs/sif)

## <a name="oci-artifacts"></a>Артефакты OCI

Реестр контейнеров Azure поддерживает [спецификацию распределения OCI,](https://github.com/opencontainers/distribution-spec)нейтральную для поставщика, облачно-агностику спецификацию для хранения, обмена, защиты и развертывания изображений контейнеров и других типов содержимого (артефактов). Спецификация позволяет реестру хранить широкий спектр артефактов в дополнение к изображениям контейнеров. Вы используете инструментарий, соответствующий артефакту, чтобы толкать и тянуть артефакты. Например, [см. Push и вытяните артефакт OCI с помощью реестра контейнеров Azure.](container-registry-oci-artifacts.md)

Чтобы узнать больше об артефактах OCI, см. реестр OCI как РЕПО [хранения (ORAS)](https://github.com/deislabs/oras) и РЕПО [OCI Artifacts](https://github.com/opencontainers/artifacts) на GitHub.

## <a name="helm-charts"></a>Чарты Helm

Реестр контейнеров Azure может размещать репозитории для [диаграмм Helm,](https://helm.sh/)формат упаковки, используемый для быстрого управления и развертывания приложений для Kubernetes. [Поддерживается версия клиента Helm](https://docs.helm.sh/using_helm/#installing-helm) 2 (2.11.0 или позже).

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [принудительно отправлять и извлекать](container-registry-get-started-docker-cli.md) образы с помощью Реестра контейнеров Azure.

* Используйте [задачи Реестра контейнеров Azure](container-registry-tasks-overview.md) для сборки и тестирования образов контейнера. 

* Используйте платформу [BuildKit Moby](https://github.com/moby/buildkit) для сборки и упаковки контейнеров в формате OCI.

* Настройте [репозиторий Helm](container-registry-helm-repos.md), размещенный в Реестре контейнеров Azure. 


