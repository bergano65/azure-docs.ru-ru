---
title: Заблокировать образа в реестр контейнеров Azure
description: Задание атрибутов для образа контейнера или репозитория, поэтому не может быть удален или перезаписан в реестр контейнеров Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: ebbfaba158e7ddb669111f097eb1adde2373aa6c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361291"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Заблокировать образ контейнера в реестр контейнеров Azure

В реестр контейнеров Azure можно заблокировать версию образа или репозитория, чтобы удалить или обновить. Чтобы заблокировать изображения или репозитория, обновить его атрибутов, используя команду Azure CLI [обновление репозитория az acr][az-acr-repository-update]. 

В этой статье требуется запустить Azure CLI в Azure Cloud Shell или локально (версии 2.0.55 или более поздней версии рекомендуется). Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

## <a name="scenarios"></a>Сценарии

По умолчанию изображение с тегами в реестре контейнеров Azure — *изменяемый*, поэтому с соответствующими разрешениями могут несколько раз обновить и отправка образа с тем же тегом в реестр. Образы контейнеров также может быть [удалены](container-registry-delete.md) при необходимости. Это полезно при разработке изображений и должен поддерживать размер для реестра.

Тем не менее, если образ контейнера развертывается в рабочей среде, вам может потребоваться *неизменяемый* образ контейнера. Неизменяемый образ — это приложения, не может случайно удалить или перезаписать. Используйте [обновление репозитория az acr] [ az-acr-repository-update] команду, чтобы задать атрибуты репозитория, поэтому вы можете:

* Заблокировать версию образа или весь репозиторий

* Защитить от удаления версия образа или репозитория, но разрешить обновления

* Запретить операции чтения (по запросу) на версию образа или весь репозиторий

В следующих разделах примеры.

## <a name="lock-an-image-or-repository"></a>Блокировать, изображения или репозитория 

### <a name="show-the-current-repository-attributes"></a>Показать атрибуты текущего репозитория
Чтобы просмотреть атрибуты текущего репозитория, выполните следующую команду [az acr репозитория show] [ az-acr-repository-show] команды:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Показать атрибуты текущего изображения
Чтобы просмотреть текущие атрибуты тега, выполните следующую команду [az acr репозитория show] [ az-acr-repository-show] команды:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Заблокировать изображение по тегу

Для блокировки *myrepo / myimage:tag* образ в *myregistry*, выполните следующую команду, [обновление репозитория az acr] [ az-acr-repository-update] команды:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Закрепить изображение манифеста хэш-кода

Для блокировки *myrepo/myimage* изображения, определенного параметром манифеста хэш-кода (хэш SHA-256, представленное в виде `sha256:...`), выполните следующую команду. (Чтобы найти манифеста дайджест, связанный с один или несколько тегов изображений, выполните [acr репозитория az show манифесты] [ az-acr-repository-show-manifests] команды.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Блокировать хранилище

Для блокировки *myrepo/myimage* репозиторий и все образы, выполните следующую команду:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Защита от удаления изображения или репозитория

### <a name="protect-an-image-from-deletion"></a>Защитить изображения от удаления

Чтобы разрешить *myrepo / myimage:tag* образ обновлен, но не удалена, выполните следующую команду:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Защитить репозиторием от удаления

Следующая команда задает *myrepo/myimage* репозиторий, поэтому его нельзя удалить. По-прежнему можно обновить или удалить отдельные изображения.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Запретить операции чтения изображения или репозитория

Во избежание операций чтения (по запросу) на *myrepo / myimage:tag* образ, выполните следующую команду:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Во избежание операций чтения на все образы в *myrepo/myimage* репозитория, выполните следующую команду:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Разблокировать изображения или репозитория

Чтобы восстановить поведение по умолчанию *myrepo / myimage:tag* изображение, чтобы его можно удалить и обновить, выполните следующую команду:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Чтобы восстановить поведение по умолчанию *myrepo/myimage* репозиторий и все образы, чтобы их можно удалить и обновить, выполните следующую команду:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали об использовании [обновление репозитория az acr] [ az-acr-repository-update] команду, чтобы предотвратить удаление или обновление версий образа в репозитории. Чтобы задать дополнительные атрибуты, см. в разделе [обновление репозитория az acr] [ az-acr-repository-update] описанием команды.

Для просмотра атрибутов, установленных для версии образа или репозитория, используйте [az acr репозитория show] [ az-acr-repository-show] команды.

Подробные сведения об операции удаления, см. в разделе [удалить образы контейнеров в реестре контейнеров Azure][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

