---
title: About repositories & images
description: Introduction to key concepts of Azure container registries, repositories, and container images.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 9de0c344b226a0b13e76c7f02977ba3c91ba2d2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455292"
---
# <a name="about-registries-repositories-and-images"></a>About registries, repositories, and images

This article introduces the key concepts of container registries, repositories, and container images and related artifacts. 

## <a name="registry"></a>Реестр

*Реестр* контейнера — служба, которая хранит и распределяет образы контейнеров. Docker Hub is a public container registry that supports the open source community and serves as a general catalog of images. Azure Container Registry provides users with direct control of their images, with integrated authentication, [geo-replication](container-registry-geo-replication.md) supporting global distribution and reliability for network-close deployments, [virtual network and firewall configuration](container-registry-vnet.md), [tag locking](container-registry-image-lock.md), and many other enhanced features. 

In addition to Docker container images, Azure Container Registry supports related [content artifacts](container-registry-image-formats.md) including Open Container Initiative (OCI) image formats.

## <a name="content-addressable-elements-of-an-artifact"></a>Content addressable elements of an artifact

The address of an artifact in an Azure container registry includes the following elements. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** - The fully qualified name of the registry host. The registry host in an Azure container registry is in the format *myregistry*.azurecr.io (all lowercase). You must specify the loginUrl when using Docker or other client tools to pull or push artifacts to an Azure container registry. 
* **namespace** - Slash-delimited logical grouping of related images or artifacts - for example, for a workgroup or app
* **artifact** - The name of a repository for a particular image or artifact
* **tag** - A specific version of an image or artifact stored in a repository


For example, the full name of an image in an Azure container registry might look like:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

See the following sections for details about these elements.

## <a name="repository-name"></a>Имя репозитория

Container registries manage *repositories*, collections of container images or other artifacts with the same name, but different tags. Например, следующие три образа находятся в репозитории acr-helloworld.

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Имена репозиториев также могут включать [пространство имен](container-registry-best-practices.md#repository-namespaces). Namespaces allow you to group images using forward slash-delimited repository names, for example:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Изображение

A container image or other artifact within a registry is associated with one or more tags, has one or more layers, and is identified by a manifest. Understanding how these components relate to each other can help you manage your registry effectively.

### <a name="tag"></a>Тег

The *tag* for an image or other artifact specifies its version. A single artifact within a repository can be assigned one or many tags, and may also be "untagged." That is, you can delete all tags from an image, while the image's data (its layers) remain in the registry.

Репозиторий (или репозиторий и пространство имен) вместе с тегом определяют имя образа. Образ можно отправлять и получать. Для этого в операции передачи или получения необходимо указать его имя.

How you tag container images is guided by your scenarios to develop or deploy them. For example, stable tags are recommended for maintaining your base images, and unique tags for deploying images. For more information, see [Recommendations for tagging and versioning container images](container-registry-image-tag-version.md).

### <a name="layer"></a>Слой

Container images are made up of one or more *layers*, each corresponding to a line in the Dockerfile that defines the image. Образы в реестре используют слои совместно, что позволяет увеличить эффективность хранилища. Например, несколько образов, которые находятся в разных репозиториях, могут совместно использовать базовый слой Alpine Linux, но только одна копия из этого слоя может хранится в реестре.

Совместное использование слоя также оптимизирует распределение слоев для узлов с несколькими образами, совместно использующих общие слои. Например, если образ уже находится в узле, который включает в себя уровень Alpine Linux в качестве основы, последующее притяжение другого образа, ссылающегося на один и тот же слой, не переносит его слой на узел. Вместо этого он ссылается на слой, который уже существует на узле.

To provide secure isolation and protection from potential layer manipulation, layers are not shared across registries.

### <a name="manifest"></a>Manifest

Each container image or artifact pushed to a container registry is associated with a *manifest*. Манифест создается реестром во время отправки образа, уникально идентифицирует образ и указывает его слой. You can list the manifests for a repository with the Azure CLI command [az acr repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

For example, list the manifests for the "acr-helloworld" repository:

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Дайджест манифеста

Манифесты идентифицируются с помощью уникального хэша SHA-256 или *дайджеста манифеста*. Each image or artifact--whether tagged or not--is identified by its digest. Значение дайджеста уникально, даже если данные слоя образа идентичны данным другого образа. Данный механизм позволяет многократно отправлять образы с идентичными тегами в реестр. Например, можно многократно отправлять `myimage:latest` в реестр, при этом не получая сообщений об ошибке. Это происходит из за того, что каждый образ идентифицируется по уникальному дайджесту.

Чтобы извлечь образ из реестра, необходимо указать его дайджест в операции передачи. Некоторые системы можно настроить на извлечения с помощью дайджеста, так как он гарантирует, что будет извлечена запрашиваемая версия образа. Операция будет выполнена, даже если образы, которые были идентично помечены, впоследствии будут перенесены в реестр.

For example, pull an image from the "acr-helloworld" repository by manifest digest:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Если многократно отправлять измененные образы с идентичными тегами, можно создать потерянные образы, которые остались без тегов, но все еще потребляют пространство реестра. Образы без тегов не отображаются в Azure CLI или на портале Azure при просмотре списка или образов по тегу. Тем не менее их слои по-прежнему существуют и занимают место в реестре. Deleting an untagged image frees registry space when the manifest is the only one, or the last one, pointing to a particular layer. For information about freeing space used by untagged images, see [Delete container images in Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Дальнейшие действия

Learn more about [image storage](container-registry-storage.md) and [supported content formats](container-registry-image-formats.md) in Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


