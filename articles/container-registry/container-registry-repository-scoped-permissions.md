---
title: Разрешения для репозиториев в реестре контейнеров Azure
description: Создание маркера с разрешениями, ограниченными конкретными репозиториями в реестре для извлечения или отправки изображений или выполнения других действий.
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 9004c45401833d3070266055dd7eb99a2bb43bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618830"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Создание маркера с разрешениями в области репозитория

В этой статье описывается создание маркеров и сопоставлений областей для управления разрешениями уровня репозитория в реестре контейнеров. Создавая маркеры, владелец реестра может предоставлять пользователям или службам доступ к репозиториям с ограниченным временем по времени, чтобы получать или отправлять изображения или выполнять другие действия. Маркер предоставляет более детализированные разрешения, чем другие [Параметры проверки подлинности](container-registry-authentication.md)реестра, определяющие разрешения для всего реестра. 

Ниже приведены сценарии создания маркера.

* Разрешить устройствам IoT с отдельными маркерами получать изображение из репозитория
* Предоставление внешней организации с разрешениями для определенного репозитория 
* Ограничьте доступ к репозиторию для разных групп пользователей в Организации. Например, предоставьте доступ на запись и чтение для разработчиков, создающих образы, предназначенные для конкретных репозиториев, и доступ для чтения к командам, которые развертываются из этих репозиториев.

> [!IMPORTANT]
> Сейчас эта функция доступна в предварительной версии, и применяются [некоторые ограничения](#preview-limitations). Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="preview-limitations"></a>Ограничения предварительной версии

* Эта функция доступна только в реестре контейнеров уровня " **премиум** ". Сведения об уровнях и ограничениях служб реестра см. в статье [номера SKU реестра контейнеров Azure](container-registry-skus.md).
* В настоящее время невозможно назначить разрешения уровня репозитория для удостоверения Azure Active Directory, например субъекта-службы или управляемого удостоверения.
* Нельзя создать карту области в реестре, для которой разрешен [анонимный доступ по запросу](container-registry-faq.md#how-do-i-enable-anonymous-pull-access).

## <a name="concepts"></a>Основные понятия

Чтобы настроить разрешения в пределах репозитория, создайте *маркер* со связанной *картой области*. 

* **Маркер** вместе с созданным паролем позволяет пользователю пройти проверку подлинности в реестре. Вы можете задать дату истечения срока действия пароля маркера или отключить маркер в любое время.  

  После проверки подлинности с помощью маркера пользователь или служба могут выполнить одно или несколько *действий* с областью действия одного или нескольких репозиториев.

  |Действие  |Описание  | Пример |
  |---------|---------|--------|
  |`content/delete`    | Удаление данных из репозитория  | Удаление репозитория или манифеста |
  |`content/read`     |  Чтение данных из репозитория |  Извлечение артефакта |
  |`content/write`     |  Запись данных в репозиторий     | Использование с `content/read` для отправки артефакта |
  |`metadata/read`    | Чтение метаданных из репозитория   | Перечислить теги или манифесты |
  |`metadata/write`     |  Запись метаданных в репозиторий  | Включение или отключение операций чтения, записи или удаления |

* В **карте области** группируются разрешения репозитория, применяемые к маркеру, и они могут применяться к другим токенам. Каждый токен связан с одной картой области. 

   С картой области:

    * Настройка нескольких токенов с одинаковыми разрешениями для набора репозиториев
    * Обновление разрешений маркера при добавлении или удалении действий репозитория в карте области или применении другой схемы области 

  Реестр контейнеров Azure также предоставляет несколько определяемых системой сопоставлений областей с фиксированными разрешениями во всех репозиториях.

На следующем рисунке показана связь между токенами и картами области. 

![Маркеры реестра и карты области](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Предварительные условия

* Команды **Azure CLI** -Azure CLI для создания маркеров и управления ими доступны в Azure CLI версии 2.0.76 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).
* **DOCKER** — для проверки подлинности в реестре для извлечения или отправки образов требуется локальная установка DOCKER. На сайте Docker предоставляются инструкции по установке для систем [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Реестр контейнеров** . Если у вас ее нет, создайте реестр контейнеров уровня "Премиум" в подписке Azure или обновите существующий реестр. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Создание токена с интерфейсом командной строки

### <a name="create-token-and-specify-repositories"></a>Создание токена и указание репозиториев

Создайте маркер с помощью команды [AZ контроля доступа токена Create][az-acr-token-create] . При создании маркера можно указать один или несколько репозиториев и связанные действия в каждом репозитории. Репозитории пока не обязательно должны находиться в реестре. Сведения о создании токена путем указания существующей схемы области см. в следующем разделе.

В следующем примере создается маркер в реестре *myregistry* со следующими разрешениями в `samples/hello-world` репозитории: `content/write` и `content/read`. По умолчанию команда устанавливает для `enabled`токена по умолчанию значение, но вы можете обновить состояние `disabled` в любое время.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

В выходных данных отображаются сведения о токене, включая два созданных пароля. Рекомендуется сохранять пароли в надежном месте для последующего использования при проверке подлинности. Не удается снова получить пароли, но можно создать новые.

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

Выходные данные содержат сведения об области, созданной командой. Для применения одних и тех же действий репозитория `MyToken-scope-map`к другим токенам можно использовать карту области с именем. Или обновите карту области позже, чтобы изменить разрешения для связанных маркеров.

### <a name="create-token-and-specify-scope-map"></a>Создать токен и указать карту области

Альтернативный способ создания маркера — указать существующую карту области. Если у вас еще нет схемы области, сначала создайте ее, указав репозитории и связанные действия. Затем укажите карту области при создании маркера. 

Чтобы создать карту области, используйте команду [AZ контроля доступа Scope-Map Create][az-acr-scope-map-create] . Следующая команда создает карту области с теми же разрешениями в `samples/hello-world` репозитории, который использовался ранее. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Выполните команду [AZ контроля доступа токена Create][az-acr-token-create] , чтобы создать маркер, указав карту области *мископемап* . Как и в предыдущем примере, команда устанавливает для `enabled`токена по умолчанию состояние.

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

В выходных данных отображаются сведения о токене, включая два созданных пароля. Рекомендуется сохранять пароли в надежном месте для последующего использования при проверке подлинности. Не удается снова получить пароли, но можно создать новые.

## <a name="create-token---portal"></a>Создание токена — портал

Для создания маркеров и сопоставлений областей можно использовать портал Azure. Как и в `az acr token create` случае с командой CLI, можно применить существующую карту области или создать карту области при создании токена, указав один или несколько репозиториев и связанные действия. Репозитории пока не обязательно должны находиться в реестре. 

В следующем примере создается маркер и создается таблица областей со следующими разрешениями в `samples/hello-world` репозитории: `content/write` и. `content/read`

1. На портале перейдите к реестру контейнеров.
1. В **Services**разделе "службы **" выберите токены (Предварительная версия) > + добавить**.
  ![Создание токена на портале](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Введите имя токена.
1. В разделе **схема области**выберите **создать**.
1. Настройте карту области:
    1. Введите имя и описание для Map области. 
    1. В разделе **репозитории**введите `samples/hello-world`и в разделе **разрешения**выберите `content/read` и `content/write`. Затем выберите **+ Добавить**.  
    ![Создание схемы области на портале](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. После добавления репозиториев и разрешений нажмите кнопку **Добавить** , чтобы добавить карту области.
1. Примите значение параметра **состояние** токена по умолчанию **включено** и нажмите кнопку **создать**.

После проверки и создания маркера на экране **токены** появятся сведения о токене.

### <a name="add-token-password"></a>Добавление пароля токена

Создайте пароль после создания маркера. Для проверки подлинности в реестре маркер должен быть включен и иметь действительный пароль.

Можно создать один или два пароля и задать дату окончания срока действия для каждого из них. 

1. На портале перейдите к реестру контейнеров.
1. В разделе **службы**выберите **токены (Предварительная версия)** и выберите маркер.
1. В сведениях о токене выберите **password1** или **password2**и щелкните значок Создать.
1. На экране пароля при необходимости задайте дату окончания срока действия пароля и выберите **создать**.
1. После создания пароля скопируйте и сохраните его в надежном месте. После закрытия экрана невозможно получить созданный пароль, но можно создать новый.

    ![Создание пароля маркера на портале](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Проверка подлинности с помощью токена

Когда пользователь или служба использует маркер для проверки подлинности в целевом реестре, он предоставляет имя пользователя и один из его созданных паролей. Метод проверки подлинности зависит от настроенного действия или действий, связанных с маркером.

|Действие  |Проверка подлинности  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`в Azure CLI |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`в Azure CLI  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`в Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`в Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`в Azure CLI |

## <a name="examples-use-token"></a>Примеры: использование токена

В следующих примерах используется маркер, созданный ранее в этой статье, для выполнения общих операций с репозиторием: отправка и Извлечение изображений, удаление изображений и создание списка тегов репозитория. Первоначально маркер был изначально настроен с разрешениями на`content/write` Push `content/read` -уведомления (и `samples/hello-world` действиями) в репозитории.

### <a name="pull-and-tag-test-images"></a>Образы для тестирования по запросу и по тегам

В следующих примерах извлекаются `hello-world` образы `alpine` и из центра DOCKER и размечаются для реестра и репозитория.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Проверка подлинности с помощью токена

Запустите `docker login` для проверки подлинности в реестре, укажите имя маркера в качестве имени пользователя и укажите один из его паролей. Маркер должен иметь `Enabled` состояние.

Следующий пример форматируется для оболочки bash и предоставляет значения с помощью переменных среды.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Выходные данные должны показывать успешную проверку подлинности:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Отправка образов в реестр

После успешного входа попытайтесь отправить изображения с тегами в реестр. Поскольку у маркера есть разрешения на передачу изображений в `samples/hello-world` репозиторий, следующая операция push-уведомления будет выполнена успешно:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Маркер не имеет разрешений для `samples/alpine` репозитория, поэтому следующая попытка принудительной отправки завершается ошибкой, как показано `requested access to the resource is denied`ниже.

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Изменение разрешений Push/Pull

Чтобы обновить разрешения маркера, обновите разрешения в соответствующей карте области. Обновленная схема области применяется сразу ко всем связанным маркерам. 

Например, обновите `MyToken-scope-map` с `content/write` помощью `content/read` действий и для `samples/alpine` репозитория и удалите `content/write` действие в `samples/hello-world` репозитории.  

Чтобы использовать Azure CLI, выполните команду [AZ запись контроля доступа с обновлением Map][az-acr-scope-map-update] , чтобы обновить карту области:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

На портале Azure выполните следующие действия.

1. Перейдите к реестру контейнеров.
1. В разделе " **службы**" выберите " **карты области (Предварительная версия)**" и выберите карту области для обновления.
1. В разделе **репозитории**введите `samples/alpine`и в разделе **разрешения**выберите `content/read` и `content/write`. Затем выберите **+ Добавить**.
1. В разделе **репозитории**выберите и в разделе `samples/hello-world` **разрешения**снимите `content/write`флажок. Затем нажмите кнопку **Save** (Сохранить).

После обновления схемы области выполняется следующая Отправка:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Так как на карте области имеется только `content/read` разрешение на `samples/hello-world` репозиторий, попытка принудительной отправки `samples/hello-world` в репозиторий теперь завершается ошибкой:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Извлечение образов из обоих репозиториев будет проделано, так как схема `content/read` области предоставляет разрешения для обоих репозиториев:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Удаление изображений

Обновите карту области, добавив `content/delete` действие в `alpine` репозиторий. Это действие позволяет удалять образы в репозитории или удалять весь репозиторий.

Для краткости мы покажем только команду [AZ, область обновления Map][az-acr-scope-map-update] , чтобы обновить карту области:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Чтобы обновить карту области с помощью портала, см. предыдущий раздел.

Чтобы удалить `samples/alpine` репозиторий, используйте следующую команду AZ запись в [репозитории: Delete][az-acr-repository-delete] . Для удаления образов или репозиториев маркер не проходит проверку подлинности с помощью `docker login`. Вместо этого передайте имя и пароль маркера в команду. В следующем примере используются переменные среды, созданные ранее в статье:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Отображение тегов репозитория 

Обновите карту области, добавив `metadata/read` действие в `hello-world` репозиторий. Это действие позволяет считывать данные манифеста и тега в репозитории.

Для краткости мы покажем только команду [AZ, область обновления Map][az-acr-scope-map-update] , чтобы обновить карту области:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Чтобы обновить карту области с помощью портала, см. предыдущий раздел.

Чтобы прочитать метаданные в `samples/hello-world` репозитории, выполните команду AZ запись в [репозитории, просмотр-манифесты][az-acr-repository-show-manifests] или [AZ контроля репозиториев][az-acr-repository-show-tags] . 

Для чтения метаданных маркер не проходит проверку подлинности с помощью `docker login`. Вместо этого передайте имя и пароль маркера в любую команду. В следующем примере используются переменные среды, созданные ранее в статье:

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
## <a name="manage-tokens-and-scope-maps"></a>Управление маркерами и картами области

### <a name="list-scope-maps"></a>Список сопоставлений областей

Для вывода списка всех карт области, настроенных в реестре, используйте команду [AZ запись контроля доступа (область сопоставления][az-acr-scope-map-list] ) или экран **сопоставления области (Предварительная версия)** на портале. Пример:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

В выходных данных отображаются определенные карты области, а также несколько определенных системой карт области, которые можно использовать для настройки маркеров:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Отобразить сведения о маркере

Чтобы просмотреть сведения о маркере, такие как его состояние и даты истечения срока действия пароля, выполните команду [AZ запись маркера][az-acr-token-show] или выберите маркер на портале в окне **токены (Предварительная версия)** . Пример:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Чтобы получить список всех токенов, настроенных в реестре, используйте команду AZ запись в [списке токенов][az-acr-token-list] или окно **токены (Предварительная версия)** на портале. Пример:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Создание паролей для токена

Если у вас нет пароля маркера или вы хотите создать новые пароли, выполните команду [AZ контроля учетных данных маркера][az-acr-token-credential-generate] . 

В следующем примере создается новое значение для password1 для токена *митокен* с периодом действия 30 дней. Пароль сохраняется в переменной `TOKEN_PWD`среды. Этот пример отформатирован для оболочки bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Чтобы использовать портал Azure для создания пароля маркера, см. действия, описанные в разделе [создание токена на портале](#create-token---portal) выше в этой статье.

### <a name="update-token-with-new-scope-map"></a>Обновить маркер с новой картой области

Если вы хотите обновить маркер с другой картой области, выполните команду [AZ контроля доступа Update][az-acr-token-update] и укажите новую карту области. Пример:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

На портале на экране **токены (Предварительная версия)** выберите маркер и в разделе **Map Scope (отображение области**) выберите другую карту области.

> [!TIP]
> После обновления маркера с новой картой области может потребоваться создать новые пароли маркеров. Используйте команду [AZ контроля учетных данных токена безопасности Generate][az-acr-token-credential-generate] или повторно создайте пароль маркера в портал Azure.

## <a name="disable-or-delete-token"></a>Отключение или удаление токена

Может потребоваться временно отключить использование учетных данных маркера для пользователя или службы. 

С помощью Azure CLI выполните команду [AZ контроля доступа токена Update][az-acr-token-update] , чтобы установить `status` для `disabled`значение:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

На портале выберите маркер на экране **токены (Предварительная версия)** и в разделе **состояние**выберите **отключено** .

Чтобы удалить маркер для постоянного недействительного доступа любым пользователем с помощью его учетных данных, выполните команду [AZ запись маркера Delete][az-acr-token-delete] . 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

На портале выберите маркер на экране **токены (Предварительная версия)** и нажмите кнопку **отменить**.

## <a name="next-steps"></a>Дальнейшие шаги

* Чтобы управлять картами и маркерами области, используйте дополнительные команды в группах команд AZ запись в [области видимости][az-acr-scope-map] записей для Map и [AZ][az-acr-token] .
* Дополнительные параметры проверки подлинности в реестре контейнеров Azure см. в [обзоре аутентификации](container-registry-authentication.md) , включая использование удостоверения Azure Active Directory, субъекта-службы или учетной записи администратора.


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
