---
title: Блокировка образов
description: Установите атрибуты для изображения контейнера или репозитория, чтобы его нельзя было удалить или перезаписать в реестре контейнеров Azure.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659702"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Блокировка изображения контейнера в реестре контейнеров Azure

В реестре контейнеров Azure можно заблокировать версию изображения или репозиторий, чтобы ее нельзя было удалить или обновить. Чтобы заблокировать изображение или репозиторий, обновите его атрибуты с помощью [обновления репозитория][az-acr-repository-update]Azure CLI. 

В этой статье требуется запустить CLI Azure в оболочке Azure Cloud Shell или локально (версия 2.0.55 или позже рекомендуемая). Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][azure-cli]

> [!IMPORTANT]
> Эта статья не распространяется на блокировку всего реестра, например, с использованием **«Настройки > замки»** на портале Azure или `az lock` команд в Azure CLI. Блокировка ресурса реестра не мешает создавать, обновлять или удалять данные в репозиториях. Блокировка реестра влияет только на операции управления, такие как добавление или удаление репликаций или удаление самого реестра. Дополнительная информация в [ресурсах Lock для предотвращения неожиданных изменений.](../azure-resource-manager/management/lock-resources.md)

## <a name="scenarios"></a>Сценарии

По умолчанию помеченное изображение в реестре контейнеров Azure *является изменяемым,* поэтому при соответствующих разрешениях вы можете повторно обновлять и перекачить изображение с тем же тегом в реестр. Изображения контейнеров также могут быть удалены по мере [необходимости.](container-registry-delete.md) Такое поведение полезно при разработке изображений и необходимости поддержания размера для вашего реестра.

Однако при развертывании изображения контейнера в производство может потребоваться *неизменяемое изображение контейнера.* Непреложное изображение — это изображение, которое нельзя случайно удалить или перезаписать.

Смотрите [Рекомендации для пометки и версии изображений контейнеров](container-registry-image-tag-version.md) для стратегий пометки и версии изображений в вашем реестре.

Используйте команду [обновления репозитория az acr][az-acr-repository-update] для установки атрибутов репозитория, чтобы можно было:

* Блокировка версии изображения или всего репозитория

* Защитите версию изображения или репозиторий от удаления, но разрешить обновления

* Предотвращение считывательных (вытягиваний) операций на версии изображения или в целом репозитории

Ниже приведены разделы для примеров. 

## <a name="lock-an-image-or-repository"></a>Блокировка изображения или репозитория 

### <a name="show-the-current-repository-attributes"></a>Отображить текущие атрибуты репозитория
Чтобы увидеть текущие атрибуты репозитория, запустите следующую команду [отображаемого хранилища az acr:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Отображение текущих атрибутов изображения
Чтобы увидеть текущие атрибуты тега, запустите следующую команду [репозитория az acr:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Блокировка изображения по тегу

Чтобы заблокировать *myrepo/myimage:tag* изображение в *myregistry,* запустите следующую команду [обновления репозитория az acr:][az-acr-repository-update]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Блокировка изображения явным дайджестом

Чтобы заблокировать изображение *myrepo/myimage,* идентифицированное явным дайджестом (SHA-256 хэш, представленный как), `sha256:...`запустите следующую команду. (Чтобы найти манифест дайджест, связанный с одним или более тегами изображений, запустите команду [шоу-манифестов az acr.)][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Блокировка репозитория

Чтобы заблокировать репозиторий *myrepo/myimage* и все изображения в нем, запустите следующую команду:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Защита изображения или репозитория от удаления

### <a name="protect-an-image-from-deletion"></a>Защита изображения от удаления

Чтобы позволить обновлению, но не удалению изображения *myrepo/myimage:tag:*

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Защита репозитория от удаления

Следующая команда устанавливает репозиторий *myrepo/myimage,* поэтому он не может быть удален. Отдельные изображения по-прежнему могут быть обновлены или удалены.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Предотвращение считывательных операций на изображении или репозитории

Чтобы предотвратить чтение (тянуть) операции на *myrepo /myimage:тег* изображения, запустите следующую команду:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Чтобы предотвратить чтение операций на всех изображениях в репозитории *myrepo/myimage,* запустите следующую команду:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Разблокировать изображение или репозиторий

Чтобы восстановить поведение по умолчанию изображения *myrepo/myimage:tag,* чтобы его можно было удалить и обновить, запустите следующую команду:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Чтобы восстановить поведение по умолчанию репозитория *myrepo/myimage* и всех изображений, чтобы они могли быть удалены и обновлены, запустите следующую команду:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали об использовании команды [обновления репозитория az acr][az-acr-repository-update] для предотвращения удаления или обновления версий изображений в репозитории. Чтобы установить дополнительные [az acr repository update][az-acr-repository-update] атрибуты, см.

Чтобы увидеть атрибуты, установленные для версии изображения или репозитория, используйте команду [шоу-репозитория az acr.][az-acr-repository-show]

Подробную информацию об операциях по удалению можно узнать [в реестре контейнеров Azure.][container-registry-delete]

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

