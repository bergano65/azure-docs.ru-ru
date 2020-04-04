---
title: Действия GitHub & службе Azure Kubernetes (предварительный просмотр)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Просмотр и тестирование запроса на вытягивание непосредственно в службе Azure Kubernetes с помощью действий GitHub и пространства Azure Dev
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, контейнеры, действия GitHub, шлем, сервисная сетка, разгром сервисной сетки, кубектль, k8s
manager: gwallace
ms.openlocfilehash: a83da0ef3958748831eb0eeda1aa5e91efa7ef2e
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637944"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Действия GitHub & службе Azure Kubernetes (предварительный просмотр)

Пространства Azure Dev обеспечивают рабочий процесс с помощью действий GitHub, который позволяет тестировать изменения от запроса притяжения непосредственно в AKS до того, как запрос на вытягивание будет объединен в основную ветвь репозитория. Наличие запущенного приложения для рассмотрения изменений запроса на вытягивание может повысить доверие как разработчика, так и членов команды. Это запущенное приложение также может помочь членам группы, таким как, менеджеры по продуктам и дизайнеры, стать частью процесса проверки на ранних стадиях разработки.

Из этого руководства вы узнаете, как выполнить следующие задачи:

* настройка Azure Dev Spaces в управляемом кластере Kubernetes в Azure;
* развертывание большого приложения с несколькими микрослужбами в пространстве разработки;
* Настройка CI/CD с помощью действий GitHub.
* тестирование отдельных микрослужб в изолированном пространстве разработки в контексте всего приложения.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
* [Установленный Azure CLI][azure-cli-installed].
* [Установленная версия Helm 3][helm-installed].
* Аккаунт GitHub с [включенными действиями GitHub.][github-actions-beta-signup]
* [Пример приложения Azure Dev Spaces Bike Sharing,](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) работаемщее на кластере AKS.

## <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure

Создание экземпляра службы "Реестр контейнеров Azure" (ACR).

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Имя ACR должно быть уникальным в Azure и содержать 5-50 буквенно-цифровых символов. Любые буквы, которые вы используете, должны быть ниже.

Сохраните значение *loginServer* от вывода, поскольку оно используется на более позднем этапе.

## <a name="create-a-service-principal-for-authentication"></a>Создание принципа службы для проверки подлинности

Используйте [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] для создания основного сервиса. Пример:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Сохранить выход JSON, поскольку он используется на более позднем этапе.

Используйте [акс-шоу][az-aks-show] для отображения *идентификатора* кластера AKS:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Используйте [acr-шоу az acr][az-acr-show] для отображения *идентификатора* ACR:

```azurecli
az acr show --name <acrName> --query id
```

Используйте [создание назначения роли аз,][az-role-assignment-create] чтобы предоставить *доступ к* кластеру AKS и *доступу AcrPush* к вашему ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Вы должны быть владельцем кластера AKS и ACR, чтобы предоставить вашему сервису основной доступ к этим ресурсам.

## <a name="configure-your-github-action"></a>Настройте действие GitHub

> [!IMPORTANT]
> Для репозитория необходимо включили действия GitHub. Чтобы включить действия GitHub для вашего репозитория, перейдите к репозиторию на GitHub, нажмите на вкладку «Действия» и выберите включение действий для этого репозитория.

Перейдите к раздвентивному репозиторию и *нажмите «Настройки».* Нажмите на *Секреты* в левой боковой панели. Нажмите *Добавить новый секрет,* чтобы добавить каждый новый секрет ниже:

1. *AZURE_CREDENTIALS*: весь выход из основного творения службы.
1. *RESOURCE_GROUP*: группа ресурсов для кластера AKS, которая в данном примере является *MyResourceGroup.*
1. *CLUSTER_NAME*: название кластера AKS, который в этом примере *MyAKS*.
1. *CONTAINER_REGISTRY*: *loginServer* для ACR.
1. *HOST*: хост для вашего Dev Space, который принимает форму *<MASTER_SPACE>,<APP_NAME>,<HOST_SUFFIX>*, который в этом примере является *dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET*: название секрета вы хотите использовать, например, *демо-секрет*.
1. *MASTER_SPACE*: имя вашего родителя Dev пространства, который в этом примере *является dev*.
1. *REGISTRY_USERNAME*: *clientId* от выхода JSON от создания обслуживания главным образом.
1. *REGISTRY_PASSWORD*: *clientSecret* от выхода JSON от творения обслуживания главным образом.

> [!NOTE]
> Все эти секреты используются в действии GitHub и настроены в [.github/workflows/bikes.yml.][github-action-yaml]

Дополнительно, если вы хотите обновить мастер-пространство после того, как ваш PR сливается, добавить *GATEWAY_HOST* секрет, который принимает форму *<MASTER_SPACE>.gateway.<HOST_SUFFIX>*, который в этом примере является *dev.gateway.fedcab0987.eus.azds.io*. Как только вы объедините изменения в основной ветке в вилке, другое действие будет запущено для восстановления и запуска всего приложения в пространстве мастер-разработчиков. В этом примере мастер-пространство является *dev.* Это действие настроено в [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

Кроме того, если вы хотите, чтобы изменения в вашем PR для запуска в grandchild пространстве, обновить *MASTER_SPACE* и *HOST* секреты. Например, если приложение работает в *dev* с ребенком пространства *dev/azureuser1*, чтобы иметь PR работать в детском пространстве *dev/azureuser1*:

* Обновление *MASTER_SPACE* в детское пространство, необходимое для того, чтобы быть родительским, в этом примере *azureuser1.*
* Обновление *HOST* до *<GRANDPARENT_SPACE>.<APP_NAME>,<HOST_SUFFIX>*, в этом примере *dev.bikesharingweb.fedcab0987.eus.azds.io*.

## <a name="create-a-new-branch-for-code-changes"></a>Создание новой ветки для изменения кода

Перейдите `BikeSharingApp/` к и создать новую ветку под названием *велосипед-изображения.*

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Edit [Bikes/server.js][bikes-server-js] для удаления строк 232 и 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Раздел должен теперь выглядеть следующим образом:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Сохранить файл затем `git add` `git commit` использовать и инсценировать изменения.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Нажмите ваши изменения

Используйте `git push` для нажатия новой ветви к раздвентивному репозиторию:

```cmd
git push origin bike-images
```

После завершения нажатия перейдите к раздвоенную репозиторию на GitHub, чтобы создать запрос на вытягивание с *основной* ветвью в раздвоенной репозитории в качестве базовой ветви по сравнению с ветвью *изображений велосипедов.*

После того, как ваш запрос на вытягивание будет открыт, *Bikes* перейдите на вкладку *«Действия».*

## <a name="view-the-child-space-with-your-changes"></a>Просмотр детского пространства с вашими изменениями

После завершения действия вы увидите комментарий с URL-адресом для нового детского пространства, основанный на изменениях в запросе на вытягивание.

> [!div class="mx-imgBorder"]
> ![GitHub действий Url](../media/github-actions/github-action-url.png)

Перейдите к сервису *bikesharingweb,* открыв URL из комментария. Выберите *Аурелия Бриггс (клиент) в* качестве пользователя, а затем выберите велосипед в аренду. Убедитесь, что вы больше не видите изображение заполнителя для велосипеда.

При слиянии изменений в *основной* ветке в вилке будет запущено другое действие для восстановления и запуска всего приложения в пространстве родительского разработчика. В этом примере родительское пространство — *это dev.* Это действие настроено в [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Очистка ресурсов Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
