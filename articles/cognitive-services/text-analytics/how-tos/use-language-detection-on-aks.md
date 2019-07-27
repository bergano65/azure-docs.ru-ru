---
title: Запуск службы Kubernetes Анализ текста
titleSuffix: Azure Cognitive Services
description: Развертывание контейнера распознавания языка с работающим примером в Службе Azure Kubernetes и его тестирование в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 7b6621273b04d019791f604ca0db26a73c289f2b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562615"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>Развертывание контейнера обнаружения языка в службе Kubernetes Azure

Узнайте, как развертывать контейнер распознавания языка. В этой процедуре показано, как создать локальные контейнеры DOCKER, отправить контейнеры в собственный закрытый реестр контейнеров, запустить контейнер в кластере Kubernetes и проверить его в веб-браузере. С помощью контейнеров можно не только управлять инфраструктурой, но и сосредоточиться на разработке приложений.

## <a name="prerequisites"></a>предварительные требования

Для выполнения этой процедуры необходимо установить и запустить несколько средств локально. Не используйте Azure Cloud Shell. Кроме этого, вам потребуются:

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
* [Git](https://git-scm.com/downloads) для вашей операционной системы, чтобы можно было клонировать [образец](https://github.com/Azure-Samples/cognitive-services-containers-samples) , используемый в этой процедуре.
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Подсистема DOCKER](https://www.docker.com/products/docker-engine)и проверка того, что интерфейс командной строки DOCKER работает в окне консоли.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Ресурс Azure с правильной ценовой категорией. Не все ценовые категории поддерживают этот контейнер.
    * **Анализ текста ресурс Azure** только с ценовыми категориями F0 или Standard.
    * Ресурс **Azure Cognitive Services** с ценовой категорией S0.

## <a name="run-the-sample"></a>Запуск примера

Эта процедура используется для загрузки и запуска примера контейнера Cognitive Services для определения языка. Пример содержит два контейнера: один для клиентского приложения и второй для контейнера Cognitive Services. Эти образы необходимо отправить в собственный экземпляр реестра контейнеров Azure. После того, как они находятся в собственном реестре, создайте экземпляр службы Azure Kubernetes Service (AKS) для доступа к этим образам и запуска контейнеров. Когда контейнеры выполняются, используйте интерфейс командной строки **kubectl** для наблюдения за производительностью контейнеров. Создайте HTTP-запрос к клиентскому приложению и проверьте результаты его выполнения.

![Концепция запуска примеров контейнеров](../media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Примеры контейнеров

Образец содержит два образа контейнеров. Один предназначен для интерфейсного веб-сайта. Вторым изображением является контейнер обнаружения языка, возвращающий обнаруженный язык (язык и региональные параметры) текста. После завершения работы оба контейнера доступны из внешнего IP-адреса.

### <a name="the-language-front-end-container"></a>Внешний контейнер языка

Этот веб-сайт выполняет те же функции, что и клиентское приложение, то есть направляет запросы к конечной точки распознавания языка. Когда процедура будет завершена, вы сможете определить язык для строки символов, обращаясь к контейнеру веб-сайта через браузер по адресу `http://<external-IP>/<text-to-analyze>`. Пример URL-адреса: `http://132.12.23.255/helloworld!`. В браузере отображается результат `English`.

### <a name="the-language-container"></a>Контейнер распознавания языка

В этой конкретной процедуре контейнер обнаружения языка доступен для любого внешнего запроса. Контейнер не меняется каким-либо образом, поэтому доступен стандартный интерфейс API обнаружения на основе контейнера Cognitive Services.

Для этого используется запрос POST для распознавания языка. Как и для других контейнеров Cognitive Services, вы можете получить дополнительные сведения, используя размещенную информацию Swagger: `http://<external-IP>:5000/swagger/index.html`.

Порт 5000 используется по умолчанию со всеми контейнерами Cognitive Services.

## <a name="create-an-azure-container-registry-service"></a>Создание службы реестра контейнеров Azure

Чтобы развернуть контейнер в службе Azure Kubernetes, образы контейнеров должны быть доступны. Создайте собственную службу Реестра контейнеров Azure для размещения образов.

1. Войдите в Azure CLI.

    ```azurecli
    az login
    ```

1. Создайте группу ресурсов с именем `cogserv-container-rg`, в которой будут размещены все созданные в этой процедуре ресурсы.

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. Создайте собственный экземпляр реестра контейнеров Azure с форматом имени, за которым следует `registry`. Например, `pattyregistry`. Не используйте дефисы или подчеркивание символов в имени.

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Сохраните результаты, чтобы получить значение свойства **loginServer**. Это свойство является частью адреса размещенного контейнера, который используется позже в `language.yml` файле.

    ```console
    > az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Войдите в реестр контейнеров Для отправки образов в реестр необходимо войти в систему.

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-the-website-docker-image"></a>Получение образа DOCKER веб-сайта

1. Пример кода, используемый в этой процедуре, размещен в репозитории с примерами контейнеров Cognitive Services. Клонируйте этот репозиторий, чтобы получить локальную копию примера.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    После того как репозиторий находится на локальном компьютере, найдите веб-сайт в каталоге [\дотнет\лангуаже\фронтендсервице](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) . Этот веб-сайт выступает в качестве клиентского приложения, вызывающего API обнаружения языка, размещенного в контейнере обнаружения языка. 

1. Создайте образ Docker для этого веб-сайта. Убедитесь, что консоль находится в каталоге [\фронтендсервице](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) , где находится файл DOCKER при выполнении следующей команды:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Чтобы отслеживать версию реестра контейнеров, добавьте тег в формате версии, например `v1`. 

1. Отправьте образ в реестр контейнеров. Этот шаг может занять несколько минут. 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    При возникновении `az acr login --name <your-container-registry-name>` ошибки выполните вход с помощью команды. `unauthorized: authentication required` 

    По завершении процесса результаты будут выглядеть следующим образом:

    ```console
    > docker push pattyregistry.azurecr.io/language-frontend:v1
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-the-language-detection-docker-image"></a>Получение образа DOCKER для определения языка 

1. Вытяните последнюю версию образа Docker на локальный компьютер. Этот шаг может занять несколько минут. Если имеется более новая версия этого контейнера, измените значение с `1.1.006770001-amd64-preview` на более новую версию. 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Пометьте образ тегом реестра контейнеров. Найдите последнюю версию и замените ее версией `1.1.006770001-amd64-preview` , если у вас более новая версия. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Отправьте образ в реестр контейнеров. Этот шаг может занять несколько минут. 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Получение учетных данных Реестра контейнеров

Чтобы получить необходимые сведения для подключения реестра контейнеров к экземпляру службы Azure Kubernetes, созданному далее в этой процедуре, необходимы следующие шаги.

1. Создание субъекта-службы.

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    Сохраните из результата значение `appId`, которое вы присвоите параметру `<appId>` (уполномоченный объект) на шаге 3. Сохраните пароль для параметра `<client-secret>`Client-Secret следующего раздела.

    ```console
    > az ad sp create-for-rbac --skip-assignment
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Получите идентификатор реестра контейнеров.

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Сохраните результат, чтобы присвоить его параметру `<acrId>` (область действия) на следующем шаге. Он выглядит следующим образом:

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Сохраните полное значение для шага 3 в этом разделе. 

1. Чтобы правильно предоставить кластеру AKS права доступа для использования образов, хранящихся в реестре контейнеров, создайте назначение роли. Замените `<appId>` и `<acrId>` значениями, полученными на двух предыдущих шагах.

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-the-azure-kubernetes-service-cluster"></a>Создание кластера службы Kubernetes Azure

1. Создание кластера Kubernetes. Все значения параметров, кроме имени взяты из предыдущих разделов. Выберите имя, которое описывает понять, кто и зачем его создал, например `patty-kube`. 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Этот шаг может занять несколько минут. Результат: 

    ```console
    > az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    Служба будет создана, но контейнер веб-сайта или контейнер определения языка пока не существует. 

1. Получите учетные данные кластера Kubernetes. 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Загрузка определения оркестрации в службу Kubernetes

В этом разделе для взаимодействия с экземпляром службы Kubernetes Azure используется интерфейс командной строки **kubectl** . 

1. Перед загрузкой определения оркестрации убедитесь, что **kubectl** имеет доступ к узлам.

    ```console
    kubectl get nodes
    ```

    Ответ выглядит следующим образом:

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Скопируйте следующий файл и присвойте ему `language.yml`имя. Этот файл имеет раздел `service` и раздел `deployment` для контейнеров соответствующих типов: контейнер веб-сайта `language-frontend` и контейнер распознавания `language`. 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Измените строки `language.yml` языкового развертывания на основе следующей таблицы, чтобы добавить собственные имена образов реестра контейнеров, секрет клиента и параметры анализ текста.

    Параметры внешнего развертывания интерфейса пользователя|Цель|
    |--|--|
    |Строка 32<br> Свойство `image`|Расположение образа внешнего интерфейса в реестре контейнеров<br>`<container-registry-name>.azurecr.io/language-frontend:v1`.|
    |Строка 44<br> Свойство `name`|Секрет реестра контейнеров для образа, который называется `<client-secret>` в предыдущем разделе.|

1. Измените строки `language.yml` языкового развертывания в соответствии со следующей таблицей, чтобы добавить собственные имена образов реестра контейнеров, секрет клиента и параметры анализ текста.

    |Параметры развертывания распознавания языка|Цель|
    |--|--|
    |Строка 78<br> Свойство `image`|Расположение образа для языкового образа в реестре контейнеров<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`.|
    |Строка 95<br> Свойство `name`|Секрет реестра контейнеров для образа, который называется `<client-secret>` в предыдущем разделе.|
    |Строка 91<br> Свойство `apiKey`|Ключ ресурса Анализ текста.|
    |Строка 92<br> Свойство `billing`|Конечная точка выставления счетов для ресурса Анализ текста.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`.|

    Так как свойства **конечной точки** **apiKey** и выставления счетов устанавливаются как часть определения оркестрации Kubernetes, контейнеру веб-сайта не нужно знать о них или передавать их как часть запроса. Контейнер веб-сайта обращается к контейнеру распознавания языка по имени оркестратора `language`. 

1. Загрузите файл определения оркестрации для этого примера из той папки, в которой вы создали и сохранили `language.yml`. 

    ```console
    kubectl apply -f language.yml
    ```

    Ответ:

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Получение внешних IP-адресов контейнеров

Для двух контейнеров убедитесь, что `language-frontend` службы и `language` работают и получают внешний IP-адрес. 

```console
kubectl get all
```

```console
> kubectl get all
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Если для `EXTERNAL-IP` службы отображается значение ожидание, выполните команду повторно, пока IP-адрес не будет показан перед переходом к следующему шагу. 

## <a name="test-the-language-detection-container"></a>Тестирование контейнера распознавания языка

Откройте браузер и перейдите к внешнему IP-адресу `language` контейнера из предыдущего раздела:. `http://<external-ip>:5000/swagger/index.html` `Try it` Используйте функцию API для проверки конечной точки определения языка. 

![Просмотр документации по Swagger контейнера](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Тестирование контейнера с клиентским приложением

Измените URL-адрес в браузере до внешнего IP- `language-frontend` адреса контейнера, используя следующий формат:. `http://<external-ip>/helloworld` Для текста `helloworld` распознается английский язык: `English`.

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения работы с кластером удалите группу ресурсов Azure. 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Связанные сведения

* [kubectl для пользователей DOCKER](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Следующие шаги 

* Используйте больше [контейнеров Cognitive Services](../../cognitive-services-container-support.md).
* Используйте [Подключенные службы анализ текста](../vs-text-connected-service.md).


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->