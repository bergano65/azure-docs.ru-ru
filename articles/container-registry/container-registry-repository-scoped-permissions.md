---
title: Разрешения для репозиториев
description: Создание маркера с разрешениями, ограниченными конкретными репозиториями в реестре для извлечения или отправки изображений
ms.topic: article
ms.date: 10/31/2019
ms.openlocfilehash: cf36a49ffd6c04897e6f44b844f0c813d0992b18
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454911"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Разрешения уровня репозитория в реестре контейнеров Azure 

Реестр контейнеров Azure поддерживает несколько [вариантов проверки подлинности](container-registry-authentication.md) с помощью удостоверений, имеющих [доступ на основе ролей](container-registry-roles.md) ко всему реестру. Однако в некоторых сценариях может потребоваться предоставить доступ только к определенным *репозиториям* в реестре. 

В этой статье показано, как создать и использовать маркер доступа, имеющий разрешения на выполнение действий только с конкретными репозиториями в реестре. С помощью маркера доступа можно предоставить пользователям или службам доступ к репозиториям с ограниченным временем по времени, чтобы получать или отправлять изображения или выполнять другие действия. 

Дополнительные сведения об основных понятиях и сценариях маркеров см. в разделе Общие [сведения о разрешениях в области репозитория](#about-repository-scoped-permissions)далее в этой статье.

> [!IMPORTANT]
> Сейчас эта функция доступна в предварительной версии, и применяются [некоторые ограничения](#preview-limitations). Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="preview-limitations"></a>Ограничения предварительной версии

* Эта функция доступна только в реестре контейнеров уровня " **премиум** ". Сведения об уровнях и ограничениях служб реестра см. в статье [номера SKU реестра контейнеров Azure](container-registry-skus.md).
* В настоящее время невозможно назначить разрешения уровня репозитория для объекта Azure Active Directory, такого как субъект-служба или управляемое удостоверение.

## <a name="prerequisites"></a>предварительным требованиям

* **Azure CLI** . для работы с этой статьей требуется локальная установка Azure CLI (версия 2.0.76 или более поздняя). Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).
* **DOCKER** — для проверки подлинности в реестре также необходима локальная установка DOCKER. На сайте Docker предоставляются инструкции по установке для систем [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Реестр контейнеров с репозиториями** . Если у вас его нет, создайте реестр контейнеров в подписке Azure. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI](container-registry-get-started-azure-cli.md). 

  В целях тестирования [отправьте](container-registry-get-started-docker-cli.md) или [импортируйте](container-registry-import-images.md) один или несколько образцов изображений в реестр. Примеры в этой статье относятся к следующим изображениям в двух репозиториях: `samples/hello-world:v1` и `samples/nginx:v1`. 

## <a name="create-an-access-token"></a>Создание маркера доступа

Создайте маркер с помощью команды [AZ контроля доступа токена Create][az-acr-token-create] . При создании маркера укажите один или несколько репозиториев и связанных действий в каждом репозитории или укажите существующую карту области с этими параметрами.

### <a name="create-access-token-and-specify-repositories"></a>Создание маркера доступа и указание репозиториев

В следующем примере создается маркер доступа с разрешениями на выполнение действий `content/write` и `content/read` в репозитории `samples/hello-world`, а также действие `content/read` в репозитории `samples/nginx`. По умолчанию команда создает два пароля. 

В этом примере для маркера задается состояние `enabled` (значение по умолчанию), но токен можно обновить в любое время и установить для состояния значение `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

В выходных данных отображаются сведения о маркере, включая созданные пароли и карту области. Рекомендуется сохранять пароли в надежном месте для последующего использования с `docker login`. Невозможно снова получить пароли, но можно создать новые.

Выходные данные также показывают, что автоматически создается таблица областей с именем `MyToken-scope-map`. Карту области можно использовать для применения одних и тех же действий репозитория к другим токенам. Или обновите карту области позже, чтобы изменить разрешения маркера.

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
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

### <a name="create-a-scope-map-and-associated-token"></a>Создание сопоставления области и связанного токена

Кроме того, можно указать карту области с репозиториями и связанными действиями при создании маркера. Чтобы создать карту области, используйте команду [AZ контроля доступа Scope-Map Create][az-acr-scope-map-create] .

В следующем примере команда создает карту области с теми же разрешениями, которые используются в предыдущем примере. Это позволяет выполнять действия `content/write` и `content/read` в репозитории `samples/hello-world`, а также действие `content/read` в репозитории `samples/nginx`.

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

Результат аналогичен приведенному ниже:

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

Выполните команду [AZ контроля доступа токена Create][az-acr-token-create] , чтобы создать маркер, связанный с картой области *мископемап* . По умолчанию команда создает два пароля. В этом примере для маркера задается состояние `enabled` (значение по умолчанию), но токен можно обновить в любое время и установить для состояния значение `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

В выходных данных отображаются сведения о маркере, включая созданные пароли и применяемую карту области. Рекомендуется сохранять пароли в надежном месте для последующего использования с `docker login`. Невозможно снова получить пароли, но можно создать новые.

## <a name="generate-passwords-for-token"></a>Создание паролей для токена

Если при создании маркера были созданы пароли, перейдите к [разделу Проверка подлинности с помощью реестра](#authenticate-using-token).

Если у вас нет пароля маркера или вы хотите создать новые пароли, выполните команду [AZ контроля учетных данных маркера][az-acr-token-credential-generate] .

В следующем примере создается новый пароль для созданного маркера с периодом действия 30 дней. Пароль сохраняется в переменной среды TOKEN_PWD. Этот пример отформатирован для оболочки bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>Проверка подлинности с помощью токена

Запустите `docker login` для проверки подлинности в реестре с помощью учетных данных маркера. Введите имя маркера в качестве имени пользователя и укажите один из его паролей. Следующий пример форматируется для оболочки bash и предоставляет значения с помощью переменных среды.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Выходные данные должны показывать успешную проверку подлинности:

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>Проверка доступа с областью действия

Вы можете убедиться, что маркер предоставляет доступ к репозиториям в реестре с заданной областью. В этом примере следующие `docker pull` команды успешно выполняются для извлечения образов, доступных в репозиториях `samples/hello-world` и `samples/nginx`:

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

Так как пример маркера разрешает действие `content/write` только в репозитории `samples/hello-world`, `docker push` завершается успешно в этом репозитории, но завершается сбоем для `samples/nginx`:

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>Обновить карту области и токен

Чтобы обновить разрешения маркера, обновите разрешения в соответствующей карте области с помощью команды [AZ запись контроля доступа — обновление схемы][az-acr-scope-map-update]. Например, чтобы обновить *мископемап* для удаления действия `content/write` в репозитории `samples/hello-world`:

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

Если схема области связана с несколькими маркерами, команда обновляет разрешение всех связанных маркеров.

Если вы хотите обновить маркер с другой картой области, выполните команду AZ запись [контроля обновления токена][az-acr-token-update]. Например,

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

После обновления маркера или сопоставления области, связанного с маркером, изменения разрешений вступают в силу при следующей `docker login` или другой проверке подлинности с помощью маркера.

После обновления маркера может потребоваться создать новые пароли для доступа к реестру. Выполните команду [AZ запись контроля учетных данных маркера][az-acr-token-credential-generate]. Например,

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>О разрешениях с областью действия репозитория

### <a name="concepts"></a>Основные понятия

Чтобы настроить разрешения в пределах репозитория, создайте *маркер доступа* и связанную с ним *карту области* с помощью команд в Azure CLI.

* **Маркер доступа** — это учетные данные, используемые с паролем для проверки подлинности в реестре. Для каждого маркера разрешены *действия* , ограниченные одним или несколькими репозиториями. Для каждого токена можно задать срок действия. 

* **Действия** с каждым из указанных репозиториев включают в себя один или несколько следующих элементов.

  |Действие  |ОПИСАНИЕ  |
  |---------|---------|
  |`content/read`     |  Чтение данных из репозитория. Например, извлекать артефакт.  |
  |`metadata/read`    | Чтение метаданных из репозитория. Например, перечислите теги или отобразите метаданные манифеста.   |
  |`content/write`     |  Запись данных в репозиторий. Используйте with `content/read` для отправки артефакта.    |
  |`metadata/write`     |  Запись метаданных в репозиторий. Например, обновите атрибуты манифеста.  |
  |`content/delete`    | Удалите данные из репозитория. Например, удалите репозиторий или манифест. |

* **Map области** — это объект реестра, который группирует разрешения репозитория, применяемые к маркеру, или может повторно применяться к другим токенам. Если вы не примените карту области при создании маркера, автоматически создается таблица области, чтобы сохранить параметры разрешений. 

  Схема области позволяет настроить несколько пользователей с одинаковым доступом к набору репозиториев. Реестр контейнеров Azure также предоставляет определенные системой карты областей, которые можно применять при создании маркеров доступа.

На следующем рисунке показана сводка связей между токенами и картами области. 

![Карты и маркеры области реестра](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>Сценарии

Ниже перечислены сценарии использования маркера доступа.

* Предоставление устройств Интернета вещей отдельным маркерам для извлечения образа из репозитория
* Предоставление внешней организации с разрешениями для определенного репозитория 
* Ограничьте доступ к репозиторию для конкретных групп пользователей в вашей организации. Например, предоставьте доступ на запись и чтение для разработчиков, создающих образы, предназначенные для конкретных репозиториев, и доступ для чтения к командам, которые развертываются из этих репозиториев.

### <a name="authentication-using-token"></a>Проверка подлинности с помощью токена

Используйте имя маркера в качестве имени пользователя и одного из его связанных паролей для проверки подлинности в целевом реестре. Метод проверки подлинности зависит от настроенных действий.

### <a name="contentread-or-contentwrite"></a>содержимое, чтение, содержимое или запись

Если маркер допускает только `content/read` или `content/write` действия, укажите учетные данные маркера в одном из следующих потоков проверки подлинности:

* Проверка подлинности с помощью DOCKER с использованием `docker login`
* Проверяйте подлинность с помощью реестра, выполнив команду [AZ контроля учетных записей][az-acr-login] в Azure CLI

После проверки подлинности маркер разрешает настроенные действия в репозитории или репозиториях с заданной областью. Например, если маркер разрешает `content/read` действие в репозитории, `docker pull` операции разрешены для образов в этом репозитории.

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>метаданные, чтение, метаданные, запись, содержимое или удаление

Если маркер разрешает `metadata/read`, `metadata/write`или `content/delete` действиям в репозитории, учетные данные маркера должны быть предоставлены в качестве параметров с помощью связанных команд [AZ контроля][az-acr-repository] доступа в Azure CLI.

Например, если в репозитории разрешены `metadata/read` действия, передайте учетные данные маркера при выполнении команды AZ запись в [репозитории для просмотра][az-acr-repository-show-tags] тегов.

## <a name="next-steps"></a>Дополнительная информация

* Чтобы управлять картами области и маркерами доступа, используйте дополнительные команды в группах команд AZ запись в [области видимости записей: Map][az-acr-scope-map] и [AZ][az-acr-token] .
* В разделе [Общие сведения о проверке подлинности](container-registry-authentication.md) приведены сценарии для проверки подлинности в реестре контейнеров Azure с помощью учетной записи администратора или удостоверения Azure Active Directory.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
