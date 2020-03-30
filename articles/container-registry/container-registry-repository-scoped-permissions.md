---
title: Разрешения на репозитории в реестре контейнеров Azure
description: Создание маркера с разрешениями, прикрытыми для определенных репозиториев в реестре, для вытягивания или нажатия изображений или выполнения других действий
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444325"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Создание маркера с разрешениями, разбившимися с репозиторием

В этой статье описывается, как создавать токены и карты областей для управления разрешениями, разбившимися репозиторием, в реестре контейнеров. Создавая токены, владелец реестра может предоставить пользователям или службам ограниченный по времени доступ к репозиториям для вытягивания или нажатия изображений или выполнения других действий. Токен предоставляет больше мелкозернистых разрешений, чем другие [варианты проверки подлинности](container-registry-authentication.md)реестра, которые размножения разрешений для всего реестра. 

Сценарии создания токена включают:

* Разрешить устройствам IoT с отдельными токенами вытащить изображение из репозитория
* Предоставить внешнюю организацию с разрешениями в конкретный репозиторий 
* Ограничьте доступ к репозиторию для различных групп пользователей в вашей организации. Например, обеспечить доступ к записи и чтению для разработчиков, которые создают изображения, ориентированные на конкретные репозитории, и считывает доступ к группам, развертываемым из этих репозиториев.

> [!IMPORTANT]
> Сейчас эта функция доступна в предварительной версии, и применяются [некоторые ограничения](#preview-limitations). Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="preview-limitations"></a>Ограничения предварительной версии

* Эта функция доступна только в реестре **контейнеров Premium.** Для получения информации о уровнях [Azure Container Registry SKUs](container-registry-skus.md)и ограничениях службы реестра см.
* В настоящее время нельзя присваивать разрешения с размахом репозитория идентификатору Active Directory, например основной службы или управляемые удостоверения.

## <a name="concepts"></a>Основные понятия

Для настройки разрешений, связанных с репозиторием, создается *маркер* с помощью связанной *карты области.* 

* **Токен** вместе с генерируемым паролем позволяет пользователю проверить подлинность в реестре. Вы можете установить дату истечения срока действия пароля маркеров или отключить токен в любое время.  

  После проверки подлинности с помощью маркера пользователь или служба могут выполнять одно или несколько *действий,* применяемых к одному или несколько репозиториям.

  |Действие  |Описание  | Пример |
  |---------|---------|--------|
  |`content/delete`    | Удаление данных из репозитория  | Удалить репозиторий или манифест |
  |`content/read`     |  Чтение данных из репозитория |  Вытяните артефакт |
  |`content/write`     |  Запись данных в репозиторий     | Используйте `content/read` с для нажатия артефакта |
  |`metadata/read`    | Чтение метаданных из репозитория   | Список тегов или манифестов |
  |`metadata/write`     |  Запись метаданных в репозиторий  | Включить или отключить считывание, запись или удаление операций |

* **Карта области** группирует разрешения репозитория, которые вы применяете к токену, и может повторно применяться к другим токерам. Каждый маркер связан с одной картой области. 

   С картой прицела:

    * Настройка нескольких токенов с идентичными разрешениями на набор репозиториев
    * Обновление разрешений маркеров при добавлении или удалении действий репозитория на карте области или применение другой карты области 

  Реестр контейнеров Azure также предоставляет несколько системоопределенных карт сферы, которые можно применить, с фиксированными разрешениями во всех репозиториях.

Следующее изображение показывает связь между токенами и картами области. 

![Токены реестра и карты сферы](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Предварительные требования

* **Команды Azure CLI** - Azure CLI для создания и управления токенами доступны в версии Azure CLI 2.0.76 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.](/cli/azure/install-azure-cli)
* **Docker** - Для проверки подлинности с реестром, чтобы вытащить или нажать изображения, вам нужна локальная установка Docker. На сайте Docker предоставляются инструкции по установке для систем [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Реестр контейнеров** - Если у вас его нет, создайте реестр контейнеров Premium в подписке Azure или обновите существующий реестр. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Создание токена - CLI

### <a name="create-token-and-specify-repositories"></a>Создание токенов и уточнение репозиториев

Создайте токен, используя [команду создания маркера az acr.][az-acr-token-create] При создании маркера можно указать одно или несколько репозиторий и связанных с ним действий в каждом репозитории. Репозитории еще не должны быть в реестре. Чтобы создать маркер, указав существующую карту области, см.

Следующий пример создает токен в *реестре myregistry* со `samples/hello-world` следующими `content/write` `content/read`разрешениями на репо: и . По умолчанию команда устанавливает статус маркера по умолчанию, `enabled` `disabled` но вы можете обновить статус в любое время.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

На выходе отображается подробная информация о токене, включая два генерируемых пароля. Рекомендуется сохранять пароли в безопасном месте для последующего использования для проверки подлинности. Пароли не могут быть восстановлены снова, но новые могут быть сгенерированы.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

Выход включает сведения о созданной командой карте области. Карту области можно использовать здесь, `MyToken-scope-map`чтобы применить те же действия репозитория к другим токерам. Или обновите карту области позже, чтобы изменить разрешения связанных токенов.

### <a name="create-token-and-specify-scope-map"></a>Создание токенов и уточнение карты области

Альтернативным способом создания маркера является определение существующей карты области. Если у вас еще нет карты области, сначала создайте ее, указав репозитории и связанные с ней действия. Затем укажите карту области при создании маркера. 

Чтобы создать карту области, используйте команду az [acr scope-map.][az-acr-scope-map-create] Следующая команда создает карту области с теми же разрешениями в `samples/hello-world` репозитории, использовавававававом ранее. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Выполнить [маркер az acr][az-acr-token-create] для создания маркера, указав карту области *MyScopeMap.* Как и в предыдущем примере, команда устанавливает `enabled`статус маркера по умолчанию.

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

На выходе отображается подробная информация о токене, включая два генерируемых пароля. Рекомендуется сохранять пароли в безопасном месте для последующего использования для проверки подлинности. Пароли не могут быть восстановлены снова, но новые могут быть сгенерированы.

## <a name="create-token---portal"></a>Создание токена - портал

Для создания токенов и карт областей можно использовать портал Azure. Как и `az acr token create` в случае с командой CLI, можно применить существующую карту области или создать карту области при создании маркера, указав одно или несколько репозиторий и связанных с ними действий. Репозитории еще не должны быть в реестре. 

Следующий пример создает маркер и создает карту области со следующими разрешениями `content/write` в `content/read` `samples/hello-world` репозитории: и :

1. На портале перейдите в реестр контейнеров.
1. В **рамках служб**выберите **токены (Предварительный просмотр) > «Добавить**.
  ![Создание токена на портале](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Введите имя маркера.
1. Под **картой Scope**выберите **Создать новый**.
1. Налаживаем карту области:
    1. Введите имя и описание карты области. 
    1. Под **репозиториями,** введите, `samples/hello-world`и `content/write`под **Разрешения,** выберите `content/read` и . Затем выберите **«Добавить**.  
    ![Создание карты области на портале](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. После добавления репозиториев и разрешений выберите **добавить** карту области.
1. Примите **значение** маркера по умолчанию **включено,** а затем выберите **«Создать».**

После проверки и создания маркера на экране маркеров появляются детали **маркеров.**

### <a name="add-token-password"></a>Добавление пароля маркеров

Создайте пароль после создания маркера. Для проверки подлинности реестра маркер должен быть включен и иметь действительный пароль.

Вы можете создать один или два пароля и установить срок действия каждого из них. 

1. На портале перейдите в реестр контейнеров.
1. В **рамках служб**выберите **токены (Preview)** и выберите токен.
1. В маркерных деталях выберите **пароль1** или **пароль2**и выберите значок Generate.
1. На экране пароля дополнительно установить дату истечения срока действия пароля и выбрать **Generate.**
1. После создания пароля скопируйте его и сохраните в безопасном месте. Вы не можете получить генерируемый пароль после закрытия экрана, но вы можете создать новый.

    ![Создание пароля токенов на портале](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Authenticate с токеном

Когда пользователь или служба использует маркер для проверки подлинности в целевом реестре, он предоставляет имя маркера в качестве имени пользователя и один из созданных паролях. Метод проверки подлинности зависит от настроенного действия или действий, связанных с маркером.

|Действие  |Как проверить подлинность  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`в Azure CLI |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`в Azure CLI  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`в Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`в Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`в Azure CLI |

## <a name="examples-use-token"></a>Примеры: Используйте токен

Следующие примеры используют маркер, созданный ранее в этой статье, для выполнения общих операций в репозитории: нажатие и вытягивание изображений, удаление изображений и список тегов репозитория. Токен был настроен первоначально с разрешениями push (и`content/write` `content/read` действиями) в `samples/hello-world` репозитории.

### <a name="pull-and-tag-test-images"></a>Потяните и теги тестовых изображений

Для следующих примеров `hello-world` `alpine` вытяните изображения из Docker Hub и отметьте их для вашего реестра и репозитория.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Authenticate с помощью токена

Выполнить `docker login` для проверки подлинности с реестром, Уведить имя маркера в качестве имени пользователя, и предоставить один из его паролей. Токен должен иметь `Enabled` статус.

Следующий пример отформатирован для оболочки Bash и предоставляет значения с использованием переменных среды.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Выход должен показать успешную аутентификацию:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Отправка образов в реестр

После успешного входа, попытка нажать помеченные изображения в реестр. Поскольку маркер имеет разрешения на нажатие изображений в `samples/hello-world` репозиторий, следующий толчок удается:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Токен не имеет разрешений `samples/alpine` на репо, поэтому следующая попытка нажатия `requested access to the resource is denied`не удается с ошибкой, аналогичной:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Изменение разрешений push/pull

Чтобы обновить разрешения маркера, обновите разрешения на сопутствуенных картах области. Обновленная карта области немедленно применяется ко всем связанным токерам. 

Например, `MyToken-scope-map` обновить `content/write` `content/read` с и `samples/alpine` действия в репозитории, и удалить `content/write` действие на `samples/hello-world` репозиторий.  

Чтобы использовать Azure CLI, запустите [обновление области acr-карты][az-acr-scope-map-update] для обновления карты области:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

На портале Azure выполните следующие действия.

1. Перейдите к реестру контейнеров.
1. В **рамках служб**— выберите карты области **(Preview)** и выберите карту области для обновления.
1. Под **репозиториями,** введите, `samples/alpine`и `content/write`под **Разрешения,** выберите `content/read` и . Затем выберите **«Добавить**.
1. Под **репозиториями**, `samples/hello-world` выберите и под **Разрешения,** deselect `content/write`. Затем выберите **Сохранить**.

После обновления карты области, следующий толчок удается:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Поскольку карта области `content/read` имеет только `samples/hello-world` разрешение на репозиторий, попытка нажатия на `samples/hello-world` репо теперь завершается неудачей:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Вытягивать изображения от обоих repos `content/read` преуспевает, потому что карта сферы обеспечивает позволения на обоих репозиториях:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Удаление изображений

Обновите карту `content/delete` области, `alpine` добавив действие в репозиторий. Это действие позволяет удалить изображения в репозитории или вывести весь репозиторий.

Для краткости мы показываем только команду [обновления области области acr для][az-acr-scope-map-update] обновления карты прицела:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Чтобы обновить карту области с помощью портала, см.

Используйте следующую команду [удаления аз-акр,][az-acr-repository-delete] чтобы удалить `samples/alpine` репозиторий. Чтобы удалить изображения или репозитории, маркер не `docker login`проверяетподлинность через . Вместо этого передайте команде имя и пароль токена. В следующем примере используются переменные среды, созданные ранее в статье:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Показать теги репо 

Обновите карту `metadata/read` области, `hello-world` добавив действие в репозиторий. Это действие позволяет считывать данные и отмечать данные в репозитории.

Для краткости мы показываем только команду [обновления области области acr для][az-acr-scope-map-update] обновления карты прицела:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Чтобы обновить карту области с помощью портала, см.

To read metadata in the `samples/hello-world` repository, run the [az acr repository show-manifests][az-acr-repository-show-manifests] or [az acr repository show-tags][az-acr-repository-show-tags] command. 

Чтобы прочитать метаданные, маркер не проверяетподлинность через `docker login`. Вместо этого передайте имя и пароль маркера любой команде. В следующем примере используются переменные среды, созданные ранее в статье:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Образец вывода:

```console
[
  "v1"
]
```
## <a name="manage-tokens-and-scope-maps"></a>Управление токенами и картами области

### <a name="list-scope-maps"></a>Карты сферы действия списка

Используйте команду [списка список прицела-карты az acr][az-acr-scope-map-list] или экран **карты сферы (Preview)** на портале, чтобы перечислить все карты сферы, настроенные в реестре. Пример:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

На выходе отображается определенные карты сферы и несколько системоопределенных карт сферы, которые можно использовать для настройки токенов:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Показать детали токенов

Для просмотра деталей маркера, таких как статус и даты истечения срока действия пароля, запустите команду [показа маркера az acr][az-acr-token-show] или выберите маркер на экране **токенов (Preview)** на портале. Пример:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Используйте команду [списка маркеров az acr][az-acr-token-list] или экран **маркеров (Preview)** на портале, чтобы перечислить все маркеры, настроенные в реестре. Пример:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Создание паролей для маркера

Если у вас нет пароля маркеров или вы хотите создать новые пароли, запустите команду [создания учетных данных маркера az acr.][az-acr-token-credential-generate] 

Следующий пример создает новое значение для password1 для токена *MyToken* со сроком действия 30 дней. Он хранит пароль в `TOKEN_PWD`переменной среды. Этот пример отформатирован для оболочки Bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Чтобы использовать портал Azure для создания пароля маркеров, смотрите шаги в [токене Create - портале](#create-token---portal) ранее в этой статье.

### <a name="update-token-with-new-scope-map"></a>Обновление токена с помощью новой карты области

Если требуется обновить маркер с другой картой прицела, запустите [обновление маркера acr az acr][az-acr-token-update] и укажите новую карту области. Пример:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

На портале, на экране **токенов (предварительный просмотр)** выберите маркер и под **картой Scope**— выберите другую карту области.

> [!TIP]
> После обновления маркера с новой картой области может потребоваться создание новых паролей маркеров. Используйте учетную запись [маркера az acr, генерируем][az-acr-token-credential-generate] команду или регенерируем пароль маркера на портале Azure.

## <a name="disable-or-delete-token"></a>Отключить или удалить токен

Возможно, потребуется временно отключить использование учетных данных маркеров для пользователя или службы. 

С помощью Azure CLI запустите команду [обновления маркеров az acr,][az-acr-token-update] чтобы `status` настроить: `disabled`

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

На портале выберите токен на экране **токенов (Preview)** и выберите **Инвалида** по **статусу.**

Чтобы удалить токен, чтобы навсегда аннулировать доступ любого, кто использует его учетные данные, запустите команду [удаления маркера az acr.][az-acr-token-delete] 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

На портале выберите токен на экране **токенов (Preview)** и выберите **Discard**.

## <a name="next-steps"></a>Дальнейшие действия

* Для управления картами и токенами области используйте дополнительные команды в группах командных групп [az acr scope-map][az-acr-scope-map] и [acr acr.][az-acr-token]
* Ознакомьтесь с [обзором подлинности](container-registry-authentication.md) для других вариантов проверки подлинности с помощью реестра контейнеров Azure, включая использование интактификации Active Directory Azure, директора службы или учетной записи админ.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
