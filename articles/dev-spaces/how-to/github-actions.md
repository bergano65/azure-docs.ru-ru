---
title: Действия GitHub & службы Azure Kubernetes
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Изучите и протестируйте изменения из запроса на вытягивание непосредственно в службе Azure Kubernetes, используя действия GitHub и Azure Dev Spaces.
keywords: DOCKER, Kubernetes, Azure, AKS, служба Kubernetes Azure, контейнеры, действия GitHub, Helm, сеть службы, маршрутизация в сети службы, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 2638fe2cd12407e43d3b3b698cdfca5231362db4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065942"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Действия GitHub & службы Azure Kubernetes (Предварительная версия)

Azure Dev Spaces предоставляет рабочий процесс с помощью действий GitHub, позволяющих проверять изменения из запроса на вытягивание непосредственно в AKS перед слиянием запроса на включение внесенных изменений в основную ветвь репозитория. Наличие работающего приложения для просмотра изменений запроса на вытягивание может повысить уверенность как разработчика, так и членов группы. Это работающее приложение также может помочь членам группы, таким как, менеджеры по продуктам и проектировщикам, стать частью процесса проверки на ранних стадиях разработки.

В этом руководстве вы узнаете как:

- настройка Azure Dev Spaces в управляемом кластере Kubernetes в Azure;
- развертывание большого приложения с несколькими микрослужбами в пространстве разработки;
- Настройка CI/CD с помощью действий GitHub.
- тестирование отдельных микрослужб в изолированном пространстве разработки в контексте всего приложения.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="prerequisites"></a>Технические условия

- Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
- [Установленный Azure CLI][azure-cli-installed].
- [Установленный Helm 2.13 или более поздней версии][helm-installed].
- Учетная запись GitHub с [включенными действиями GitHub][github-actions-beta-signup].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Вам нужно создать кластер AKS в [поддерживаемом регионе][supported-regions]. Следующие команды создают группу ресурсов *MyResourceGroup* и кластер AKS *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Включение Azure Dev Spaces в кластере AKS

С помощью команды `use-dev-spaces` включите Dev Spaces в кластере AKS и следуйте инструкциям на экране. Следующая команда включает Dev Spaces для кластера *MyAKS* в группе *MyResourceGroup* и создает пространство разработки с именем *dev*.

> [!NOTE]
> Команда `use-dev-spaces` также установит интерфейс командной строки Azure Dev Spaces, если он еще не установлен. Интерфейс командной строки Azure Dev Spaces невозможно установить в Azure Cloud Shell.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure

Создайте реестр контейнеров Azure (запись контроля доступа):

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Имя записи контроля доступа должно быть уникальным в пределах Azure и содержать 5-50 буквенно-цифровых символов. Все используемые буквы должны быть в нижнем регистре.

Сохраните значение *loginServer* из выходных данных, так как оно используется на более позднем шаге.

## <a name="create-a-service-principal-for-authentication"></a>Создание субъекта-службы для проверки подлинности

Для создания субъекта-службы используйте команду [AZ AD SP Create-для – RBAC][az-ad-sp-create-for-rbac] . Пример.

```cmd
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Сохраните выходные данные JSON, так как они используются на более позднем шаге.


Чтобы отобразить *идентификатор* кластера AKS, используйте команду [AZ AKS Показать][az-aks-show] .

```cmd
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Для отображения *идентификатора* записи контроля доступа используйте команду [AZ запись контроля][az-acr-show] доступа.

```cmd
az acr show --name <acrName> --query id
```

Используйте команду [AZ Role назначение][az-role-assignment-create] , чтобы предоставить *участнику* доступ к кластеру AKS и *акрпуш* доступ к записи контроля доступа.

```cmd
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Чтобы предоставить субъекту-службе доступ к этим ресурсам, необходимо быть владельцем кластера AKS и записи контроля доступа.

## <a name="get-sample-application-code"></a>Получение примера кода приложения

В этой статье вы используете [пример приложения для общего доступа к велосипеду Azure dev Spaces][bike-sharing-gh] , чтобы продемонстрировать использование Azure dev Spaces с действиями GitHub.

Разветвление Azure Dev Spaces пример репозитория, а затем перейдите к разветвленному репозиторию. Перейдите на вкладку *действия* и выберите, чтобы включить действия для этого репозитория.

Клонирование разветвленного репозитория и переход к его каталогу:

```cmd
git clone https://github.com/USERNAME/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Получение HostSuffix для *dev*

Используйте команду `azds show-context`, чтобы показать HostSuffix для *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Внесение данных о HostSuffix в чарт Helm

Откройте файл [charts/values.yaml][bike-sharing-values-yaml] и замените все вхождения `<REPLACE_ME_WITH_HOST_SUFFIX>` полученным ранее значением HostSuffix. Сохраните изменения и закройте файл.

## <a name="run-the-sample-application-in-kubernetes"></a>Запуск примера приложения в Kubernetes

Команды, используемые для запуска примера приложения в Kubernetes, являются частью существующего процесса и не зависят от средств Azure Dev Spaces. В нашем примере для запуска примера приложения используется Helm, но вы можете выбрать и другие средства для выполнения всего приложения в пространстве имен в кластере. Команды Helm используют пространство разработки с именем *dev*, которое вы создали ранее. Это пространство разработки одновременно является и пространством имен Kubernetes. Это означает, что любые средства могут использовать пространство разработки точно так же, как любые другие пространства имен.

Azure Dev Spaces можно использовать для разработки после запуска приложения в кластере независимо от средств, используемых для его развертывания.

Используйте команды `helm init` и `helm install` для настройки и установки примера приложения в кластере.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

> [!Note]
> **Если вы используете кластер с поддержкой RBAC**, обязательно настройте [учетную запись службы для Tiller][tiller-rbac]. В противном случае выполнение команды `helm` завершится ошибкой.

Выполнение команды `helm install` может занять несколько минут. Выходные данные команды отобразят состояние всех служб, которые она развернула в кластере:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

По завершении установки примера приложения в кластере, для которого включена служба Dev Spaces, вы можете с помощью команды `azds list-uris` отобразить URL-адреса примера приложения в пространстве разработки *dev*, которое выбрано в данный момент.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Перейдите к службе *bikesharingweb* по общедоступному URL-адресу, который вам предоставила команда `azds list-uris`. В примере выше для службы *bikesharingweb* используется общедоступный URL-адрес `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. В качестве пользователя выберите *Аурелиа Бриггс (клиент)* , а затем выберите велосипед для аренды. Убедитесь, что вы видите изображение заполнителя для велосипеда.

## <a name="configure-your-github-action"></a>Настройка действия GitHub

Перейдите к разветвленному репозиторию и щелкните *Параметры*. Щелкните *секреты* в левой боковой панели. Щелкните *Добавить новый секрет* , чтобы добавить каждый новый секрет, приведенный ниже.

1. *AZURE_CREDENTIALS*— все выходные данные создания субъекта-службы.
1. *RESOURCE_GROUP*: Группа ресурсов для кластера AKS, в данном примере — *MyResourceGroup*.
1. *CLUSTER_NAME*: имя кластера AKS, в данном примере — *мякс*.
1. *CONTAINER_REGISTRY*: *LOGINSERVER* для записи контроля доступа.
1. *Узел*. узел для пространства разработки, который принимает форму *< MASTER_SPACE >. < APP_NAME >. < HOST_SUFFIX >* , в этом примере — *dev.bikesharingweb.fedcab0987.EUS.azds.IO*.
1. *IMAGE_PULL_SECRET*: имя секрета, который вы хотите использовать, например " *демонстрационный секрет*".
1. *MASTER_SPACE*: имя родительского пространства разработки, которое в данном примере является *dev*.
1. *REGISTRY_USERNAME*: *ClientID* из выходных данных JSON для создания субъекта-службы.
1. *REGISTRY_PASSWORD*: *clientSecret* из выходных данных JSON при создании субъекта-службы.

> [!NOTE]
> Все эти секреты используются действием GitHub и настраиваются в [. GitHub/Workflows/Bikes. yml][github-action-yaml].

## <a name="create-a-new-branch-for-code-changes"></a>Создание новой ветви для изменения кода

Вернитесь к `BikeSharingApp/` и создайте новую ветвь под названием *Bike-Images*.

```cmd
cd ..
git checkout -b bike-images
```

Измените [велосипеды/Server. js][bikes-server-js] , чтобы удалить строки 232 и 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Теперь раздел должен выглядеть следующим образом:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Сохраните файл, а затем используйте `git add` и `git commit` для размещения изменений.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Отправка изменений

Используйте `git push` для отправки новой ветви в разветвленный репозиторий:

```cmd
git push origin bike-images
```

После завершения отправки перейдите к разветвленному репозиторию на сайте GitHub и создайте запрос на включение внесенных изменений с *главной* ветвью в разветвленном репозитории по сравнению с ветвью " *образы велосипедов* ".

После открытия запроса на вытягивание перейдите на вкладку *действия* . Убедитесь, что новое действие началось и создает службу *велосипедов* .

## <a name="view-the-child-space-with-your-changes"></a>Просмотр дочернего пространства с вашими изменениями

После завершения действия вы увидите комментарий с URL-адресом нового дочернего пространства на основе изменений в запросе на вытягивание.

> [!div class="mx-imgBorder"]
> URL-адрес действия ![GitHub](../media/github-actions/github-action-url.png)

Перейдите к службе *бикешарингвеб* , открыв URL-адрес из комментария. В качестве пользователя выберите *Аурелиа Бриггс (клиент)* , а затем выберите велосипед для аренды. Убедитесь, что вы больше не видите изображение заполнителя для велосипеда.

## <a name="clean-up-your-azure-resources"></a>Очистка ресурсов Azure

```cmd
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
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[tiller-rbac]: https://helm.sh/docs/using_helm/#role-based-access-control
[supported-regions]: ../about.md#supported-regions-and-configurations
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md