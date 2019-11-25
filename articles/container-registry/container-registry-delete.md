---
title: Delete image resources
description: Details on how to effectively manage registry size by deleting container image data using Azure CLI commands.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 8d20bf2be1d472855c3e67dd79ea1725c152e3d2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455271"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Delete container images in Azure Container Registry using the Azure CLI

Чтобы управлять размером Реестра контейнеров Azure, необходимо периодически удалять данные устаревших образов. Хотя некоторые образы контейнеров, развернутые в рабочей среде, могут потребовать долговременного хранения, другие, как правило, могут быть удалены быстрее. Например, в сценарии автоматизированной сборки и тестирования реестр может быть быстро заполнен изображениями, которые никогда не придется развертывать, и после того, как завершится этап построения и тестирования, они незамедлительно могут быть удалены.

Так как данные образов можно удалить несколькими способами, важно понимать, как каждая из операций удаления влияет на использование хранилища. This article covers several methods for deleting image data:

* Удаление [репозитория](#delete-repository). Это действие удаляет из репозитория все образы и все уникальные слои.
* Удаление [тегов](#delete-by-tag). Это действие удаляет образ, тег и все уникальные слои, связанные с образом, и все другие теги, связанные с образом.
* Удаление [дайджеста манифеста](#delete-by-manifest-digest). Удаление образа, все уникальных слоев в образе и всех тегов, связанных с образом.

Sample scripts are provided to help automate delete operations.

For an introduction to these concepts, see [About registries, repositories, and images](container-registry-concepts.md).

## <a name="delete-repository"></a>Удаление репозитория

При удалении репозитория удаляются все образы хранилища, включая все теги, уникальные слои и манифесты. When you delete a repository, you recover the storage space used by the images that reference unique layers in that repository.

Следующая команда Azure CLI удаляет репозиторий acr-helloworld и все теги и манифесты репозитория. If layers referenced by the deleted manifests are not referenced by any other images in the registry, their layer data is also deleted, recovering the storage space.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Удаление по тегу

С помощью операции удаления можно удалить отдельные образы из репозитория по указанным именам репозиториев и тегов. При удалении по тегу восстанавливается дисковое пространство, используемое любыми уникальными слоями в образе (слои, которые не используются совместно с другими образами в реестре).

To delete by tag, use [az acr repository delete][az-acr-repository-delete] and specify the image name in the `--image` parameter. Удаляются все уникальные слои образа и все другие теги, связанные с удаляемым образом.

Пример удаления образа acr-helloworld:latest из реестра myregistry.

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> Удаление *по тегу* не следует путать с удалением тега (обестегивание). You can delete a tag with the Azure CLI command [az acr repository untag][az-acr-repository-untag]. No space is freed when you untag an image because its [manifest](container-registry-concepts.md#manifest) and layer data remain in the registry. Удаляется только ссылка на тег.

## <a name="delete-by-manifest-digest"></a>Удаление с помощью дайджеста манифеста

[Дайджест манифеста](container-registry-concepts.md#manifest-digest) может быть связан с одним, несколькими тегами или не иметь связей с тегами вообще. При удалении с помощью дайджеста все теги, связанные манифестом, удаляются, как и данные слоя для любых слоев, уникальных для образа. Данные общего слоя не удаляются.

Для удаления с помощью дайджеста сначала перечислите дайджесты манифеста для репозитория, содержащего изображения, которые необходимо удалить. Пример.

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
  }
]
```

Next, specify the digest you wish to delete in the [az acr repository delete][az-acr-repository-delete] command. Команда имеет следующий формат.

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Пример удаления последнего манифеста из списка выходных данных (с тегом "v2").

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

The `acr-helloworld:v2` image is deleted from the registry, as is any layer data unique to that image. Если манифест связан с несколькими тегами, все связанные теги также удаляются.

## <a name="delete-digests-by-timestamp"></a>Delete digests by timestamp

To maintain the size of a repository or registry, you might need to periodically delete manifest digests older than a certain date.

The following Azure CLI command lists all manifest digest in a repository older than a specified timestamp, in ascending order. Замените `<acrName>` и `<repositoryName>` значениями, уместными для вашей среды. The timestamp could be a full date-time expression or a date, as in this example.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

After identifying stale manifest digests, you can run the following Bash script to delete manifest digests older than a specified timestamp. Для работы этого сценария требуется Azure CLI и **xargs**. Сценарий не выполняет удаление по умолчанию. Чтобы включить удаление образа, измените значение `ENABLE_DELETE` на `true`.

> [!WARNING]
> Use the following sample script with caution--deleted image data is UNRECOVERABLE. If you have systems that pull images by manifest digest (as opposed to image name), you should not run these scripts. Deleting the manifest digests will prevent those systems from pulling the images from your registry. Instead of pulling by manifest, consider adopting a *unique tagging* scheme, a [recommended best practice](container-registry-image-tag-version.md). 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Удаление образов без тегов

Как было упомянуто в разделе [Дайджест манифеста](container-registry-concepts.md#manifest-digest), в результате отправки измененных образов с помощью существующего тега **untags** ранее отправленный образ стал потерянным (или "несвязанным"). Манифест образа, который был отправлен ранее, и его данные слоя остаются в реестре. Рассмотрим следующую последовательность событий.

1. Отправка образа *acr-helloworld* с тегом **latest**. `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Проверка манифеста для репозитория *acr-helloworld*.

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Изменение Dockerfile репозитория *acr-helloworld*.
1. Отправка образа *acr-helloworld* с тегом **latest**. `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Проверка манифеста для репозитория *acr-helloworld*.

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

As you can see in the output of the last step in the sequence, there is now an orphaned manifest whose `"tags"` property is an empty list. Этот манифест существует в реестре вместе с уникальными данными слоя, на которые он ссылается. **Чтобы удалить потерянные образы и их данные слоя, необходимо удалить дайджест манифеста**.

## <a name="delete-all-untagged-images"></a>Удаление всех образов без тегов

С помощью следующей команды Azure CLI можно вывести список всех образов репозитория без тегов. Замените `<acrName>` и `<repositoryName>` значениями, уместными для вашей среды.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Using this command in a script, you can delete all untagged images in a repository.

> [!WARNING]
> С осторожностью используйте следующие примеры сценариев — данные удаленных изображений восстановлению не подлежат. If you have systems that pull images by manifest digest (as opposed to image name), you should not run these scripts. Удаление образов без тегов не позволит этим системам получать образы из реестра. Instead of pulling by manifest, consider adopting a *unique tagging* scheme, a [recommended best practice](container-registry-image-tag-version.md).

**Azure CLI в Bash**

Следующий сценарий Bash удаляет из репозитория все образы, которые не были помечены тегом. Для работы этого сценария требуется Azure CLI и **xargs**. Сценарий не выполняет удаление по умолчанию. Чтобы включить удаление образа, измените значение `ENABLE_DELETE` на `true`.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Azure CLI в PowerShell**

Следующий сценарий PowerShell удаляет из репозитория все образы, которые не были помечены тегом. Для него требуется PowerShell и Azure CLI. Сценарий не выполняет удаление по умолчанию. Чтобы включить удаление образа, измените значение `$enableDelete` на `$TRUE`.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Автоматическая очистка тегов и манифестов (предварительная версия)

As an alternative to scripting Azure CLI commands, run an on-demand or scheduled ACR task to delete all tags that are older than a certain duration or match a specified name filter. For more information, see [Automatically purge images from an Azure container registry](container-registry-auto-purge.md).

Optionally set a [retention policy](container-registry-retention-policy.md) for each registry, to manage untagged manifests. When you enable a retention policy, image manifests in the registry that don't have any associated tags, and the underlying layer data, are automatically deleted after a set period.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об образе хранилища в реестре контейнеров Azure см. в статье [Хранение образа контейнера в реестре контейнеров Azure](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
