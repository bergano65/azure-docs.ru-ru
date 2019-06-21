---
title: Удаление ресурсов образов в Реестре контейнеров Azure
description: Дополнительные сведения об эффективном управлении размером реестра путем удаления данных образа контейнера.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/17/2019
ms.author: danlep
ms.openlocfilehash: c544c8ed6fbfcb859ff1ff01e7bedf46cfb21418
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295135"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Удаление образов контейнеров в службе "Реестр контейнеров Azure"

Чтобы управлять размером Реестра контейнеров Azure, необходимо периодически удалять данные устаревших образов. Хотя некоторые образы контейнеров, развернутые в рабочей среде, могут потребовать долговременного хранения, другие, как правило, могут быть удалены быстрее. Например, в сценарии автоматизированной сборки и тестирования реестр может быть быстро заполнен изображениями, которые никогда не придется развертывать, и после того, как завершится этап построения и тестирования, они незамедлительно могут быть удалены.

Так как данные образов можно удалить несколькими способами, важно понимать, как каждая из операций удаления влияет на использование хранилища. Сначала в этой статье представлены компоненты реестра Docker и образов контейнера, а затем несколько методов удаления образа контейнера. Примеры сценариев предоставляются для автоматизации операций удаления.

## <a name="registry"></a>Реестр

*Реестр* контейнера — служба, которая хранит и распределяет образы контейнеров. Центр Docker является публичным реестром контейнеров Docker в то время, как Реестр контейнеров Azure предоставляет частный реестр контейнеров Docker в Azure.

## <a name="repository"></a>Репозиторий

Реестры контейнеров управляют *репозиториями*, коллекциями образов контейнеров с одинаковыми именами, но разными тегами. Например, следующие три образа находятся в репозитории acr-helloworld.

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Имена репозиториев также могут включать [пространство имен](container-registry-best-practices.md#repository-namespaces). Пространства имен, чтобы разрешить для группирования образов с использованием имен прямой разделением косыми чертами репозитория, например:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="components-of-an-image"></a>Компоненты образа

Образ контейнера вместе с реестром связан с одним или несколькими тегами, обладает одним или несколькими слоями и определяется с помощью манифеста. Общие сведения об этих компонентах, связанных друг с другом, могут помочь определить лучшие методы для освобождения места в реестре.

### <a name="tag"></a>Тег

*Тег* образа указывает его версию. Один образ без репозитория может быть назначен одному или нескольким тегам, а также может оставаться "без тега". То есть можно удалить все теги из образа, пока данных изображения (его слоев) остаются в реестре.

Репозиторий (или репозиторий и пространство имен) вместе с тегом определяют имя образа. Образ можно отправлять и получать. Для этого в операции передачи или получения необходимо указать его имя.

В частном реестре, например в Реестре контейнеров Azure, имя образа также включает полное имя узла реестра. Сервер реестра для образов в ACR имеет формат *acrname.azurecr.io* (прописными буквами). Например будет полное имя первое изображение в пространство имен «marketing» в предыдущем разделе:

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

Дополнительные сведения по добавлению тегов к образам см. в записи блога MSDN [Docker Tagging: Советы и рекомендации для пометки и управление версиями образов docker][tagging-best-practices] записи блога на сайте MSDN.

### <a name="layer"></a>Слой

Образы создаются для одного или нескольких *слоев*, каждый из которых соответствует строке в Dockerfile, которая определяет образ. Образы в реестре используют слои совместно, что позволяет увеличить эффективность хранилища. Например, несколько образов, которые находятся в разных репозиториях, могут совместно использовать базовый слой Alpine Linux, но только одна копия из этого слоя может хранится в реестре.

Совместное использование слоя также оптимизирует распределение слоев для узлов с несколькими образами, совместно использующих общие слои. Например, если образ уже находится в узле, который включает в себя уровень Alpine Linux в качестве основы, последующее притяжение другого образа, ссылающегося на один и тот же слой, не переносит его слой на узел. Вместо этого он ссылается на слой, который уже существует на узле.

### <a name="manifest"></a>Manifest

Каждый образ контейнера, который отправляется в реестр контейнеров, связан с *манифестом*. Манифест создается реестром во время отправки образа, уникально идентифицирует образ и указывает его слой. Вы можете вывести список манифестов для репозитория с помощью команды Azure CLI [acr репозитория az show манифесты][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Пример перечисления дайджестов манифестов для репозитория acr-helloworld.

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

Манифесты идентифицируются с помощью уникального хэша SHA-256 или *дайджеста манифеста*. Каждый образ, к которому был или не был применен тег, идентифицируется по дайджесту. Значение дайджеста уникально, даже если данные слоя образа идентичны данным другого образа. Данный механизм позволяет многократно отправлять образы с идентичными тегами в реестр. Например, можно многократно отправлять `myimage:latest` в реестр, при этом не получая сообщений об ошибке. Это происходит из за того, что каждый образ идентифицируется по уникальному дайджесту.

Чтобы извлечь образ из реестра, необходимо указать его дайджест в операции передачи. Некоторые системы можно настроить на извлечения с помощью дайджеста, так как он гарантирует, что будет извлечена запрашиваемая версия образа. Операция будет выполнена, даже если образы, которые были идентично помечены, впоследствии будут перенесены в реестр.

Пример получения образа из репозитория acr-helloworld с помощью дайджеста манифеста.

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Если многократно отправлять измененные образы с идентичными тегами, можно создать потерянные образы, которые остались без тегов, но все еще потребляют пространство реестра. Образы без тегов не отображаются в Azure CLI или на портале Azure при просмотре списка или образов по тегу. Тем не менее их слои по-прежнему существуют и занимают место в реестре. В разделе [Удаление образов без тегов](#delete-untagged-images) этой статьи рассматривается освобождение пространства используемого образами без тегов.

## <a name="delete-image-data"></a>Удаление данных образа

Удалить данные образа из реестра контейнеров можно несколькими способами.

* Удаление [репозитория](#delete-repository). Это действие удаляет из репозитория все образы и все уникальные слои.
* Удаление [тегов](#delete-by-tag). Это действие удаляет образ, тег и все уникальные слои, связанные с образом, и все другие теги, связанные с образом.
* Удаление [дайджеста манифеста](#delete-by-manifest-digest). Удаление образа, всех уникальных слоев в образе и всех тегов, связанных с образом.

## <a name="delete-repository"></a>Удаление репозитория

При удалении репозитория удаляются все образы хранилища, включая все теги, уникальные слои и манифесты. При удалении репозитория, то восстановить дисковое пространство, используемое с образов, которые ссылаются на уникальный слои в нем.

Следующая команда Azure CLI удаляет репозиторий acr-helloworld и все теги и манифесты репозитория. Если слои, ссылается на удаленные манифесты не содержит ссылок на другие образы в реестре, также будут удалены их данные слоя, в результате восстановления дискового пространства.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Удаление по тегу

С помощью операции удаления можно удалить отдельные образы из репозитория по указанным именам репозиториев и тегов. При удалении по тегу восстанавливается дисковое пространство, используемое любыми уникальными слоями в образе (слои, которые не используются совместно с другими образами в реестре).

Чтобы удалить тег, используйте [az acr репозитория delete][az-acr-repository-delete] и укажите имя образа в `--image` параметра. Удаляются все уникальные слои образа и все другие теги, связанные с удаляемым образом.

Пример удаления образа acr-helloworld:latest из реестра myregistry.

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> Удаление *по тегу* не следует путать с удалением тега (обестегивание). Вы можете удалить тег с помощью команды Azure CLI [az acr репозитория удалить тег][az-acr-repository-untag]. Отсутствует пространство освобождается в том случае, когда тег изображения, так как его [манифеста](#manifest) и уровень данных остаются в реестре. Удаляется только ссылка на тег.

## <a name="delete-by-manifest-digest"></a>Удаление с помощью дайджеста манифеста

[Дайджест манифеста](#manifest-digest) может быть связан с одним, несколькими тегами или не иметь связей с тегами вообще. При удалении с помощью дайджеста все теги, связанные манифестом, удаляются, как и данные слоя для любых слоев, уникальных для образа. Данные общего слоя не удаляются.

Для удаления с помощью дайджеста сначала перечислите дайджесты манифеста для репозитория, содержащего изображения, которые необходимо удалить. Пример:

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

Затем укажите хэш-кода, необходимо удалить в [az acr репозитория delete][az-acr-repository-delete] команды. Команда имеет следующий формат.

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Пример удаления последнего манифеста из списка выходных данных (с тегом "v2").

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

`acr-helloworld:v2` Образ будет удален из реестра, так как все данные слоя, уникальные для этого образа. Если манифест связан с несколькими тегами, все связанные теги также удаляются.

## <a name="delete-digests-by-timestamp"></a>Удалить дайджестов по метке времени

Чтобы поддерживался размер репозитория или реестра, может потребоваться периодически удалять манифеста дайджестов старше определенной даты.

Следующая команда Azure CLI выводит все манифеста хэш-кода в репозитории, старше указанной отметке времени, в порядке возрастания. Замените `<acrName>` и `<repositoryName>` значениями, уместными для вашей среды. Метка времени может быть выражение с даты времени или даты, как в следующем примере.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

После определения устаревших манифеста дайджестов, можно запустить следующий сценарий Bash, чтобы удалить манифеста дайджестов старше указанной отметке времени. Для работы этого сценария требуется Azure CLI и **xargs**. Сценарий не выполняет удаление по умолчанию. Чтобы включить удаление образа, измените значение `ENABLE_DELETE` на `true`.

> [!WARNING]
> Используйте приведенный ниже пример сценария с осторожностью--данные удаленных изображения — неисправимая. Если у вас есть системы, которые извлекают образы с манифеста хэш-кода (в отличие от имени образа), не следует запускать эти скрипты. Если удалить манифеста дайджестов не этих систем извлекать образы из реестра. Вместо извлечения манифестом, попробуйте внедрить *уникального разметка* схема [рекомендация][tagging-best-practices]. 

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

Как было упомянуто в разделе [Дайджест манифеста](#manifest-digest), в результате отправки измененных образов с помощью существующего тега **untags** ранее отправленный образ стал потерянным (или "несвязанным"). Манифест образа, который был отправлен ранее, и его данные слоя остаются в реестре. Рассмотрим следующую последовательность событий.

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

Как показано в выходных данных последнего шага в последовательности, теперь установлена потерянной манифеста, `"tags"` свойство является пустым списком. Этот манифест существует в реестре вместе с уникальными данными слоя, на которые он ссылается. **Чтобы удалить потерянные образы и их данные слоя, необходимо удалить дайджест манифеста**.

## <a name="delete-all-untagged-images"></a>Удаление всех образов без тегов

С помощью следующей команды Azure CLI можно вывести список всех образов репозитория без тегов. Замените `<acrName>` и `<repositoryName>` значениями, уместными для вашей среды.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

С помощью этой команды в сценарии, можно удалить все образы без тегов в репозитории.

> [!WARNING]
> С осторожностью используйте следующие примеры сценариев — данные удаленных изображений восстановлению не подлежат. Если у вас есть системы, которые извлекают образы с манифеста хэш-кода (в отличие от имени образа), не следует запускать эти скрипты. Удаление образов без тегов не позволит этим системам получать образы из реестра. Вместо извлечения манифестом, попробуйте внедрить *уникального разметка* схема [рекомендация][tagging-best-practices].

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об образе хранилища в реестре контейнеров Azure см. в статье [Хранение образа контейнера в реестре контейнеров Azure](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
