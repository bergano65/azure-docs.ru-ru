---
title: Разработка на Сервисе Azure Kubernetes (AKS) с проектом
description: Используйте Draft с AKS и реестром контейнеров Azure для упаковки и запуска контейнеров приложений в кластере.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: 2d69676121fcb26f7d2f796e6de42435447084cd
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392756"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Быстрый запуск: Разработка на Azure Kubernetes службы (AKS) с проектом

Проект — это инструмент с открытым исходным кодом, который помогает упаковывать и запускать контейнеры приложений в кластере Kubernetes. С помощью Draft можно быстро перераспределить приложение в Kubernetes по мере изменения кода без необходимости вносить изменения в управление версиями. Для получения дополнительной информации о проекте, [см.][draft-documentation]

В этой статье показано, как использовать проект для упаковки и запуска приложения на AKS.


## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет подписки НаAz, можно создать [бесплатную учетную запись.](https://azure.microsoft.com/free)
* [Установленный Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Докер установлен и настроен. Docker предоставляет пакеты, которые позволяют настроить Docker в системе [Mac][docker-for-mac], [Windows][docker-for-windows] или [Linux][docker-for-linux].
* [Шлем v2 установлен][helm-install].
* [Проект установлен][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Создайте кластер AKS. Следующие команды создают группу ресурсов MyResourceGroup и кластер AKS MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure
Для использования проекта для запуска приложения в кластере AKS необходим реестр контейнеров Azure для хранения изображений контейнеров. Ниже приведен пример использует [az acr создать][az-acr-create] для создания ACR имени *MyDraftACR* в группе ресурсов *MyResourceGroup* с *Basic* SKU. Вы должны предоставить свое уникальное имя реестра. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. SKU *Базовый* — это оптимизированная по стоимости точка входа для целей разработки, обеспечивающая баланс ресурсов хранения и пропускной способности.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

Результат будет похож на следующий пример. Обратите внимание на значение *входа в* сервер для вашего ACR, так как он будет использоваться на более позднем этапе. В приведенном ниже примере *mydraftacr.azurecr.io* является *логинСервер* для *MyDraftACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
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


Для использования экземпляра ACR для использования проекта необходимо сначала войти в систему. Используйте команду [входа в систему az acr][az-acr-login] для входа. Ниже приведен пример будет войти в ACR имени *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Команда возвращает сообщение *Логин Успешно* после завершения.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Создание отношения доверия между кластером AKS и ACR

Кластер AKS также нуждается в доступе к ACR для вытягивания изображений контейнеров и их запуска. Вы разрешаете доступ к ACR от AKS, устанавливая траст. Чтобы установить отношения доверия между кластером AKS и реестром ACR, предоставьте субъекту-службе Azure Active Directory, используемому кластером AKS, разрешение на доступ к реестру ACR. Следующие команды предоставляют разрешения директору службы кластера *MyAKS* в *MyResourceGroup* *MyDraftACR* ACR в *MyResourceGroup.*

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Подключение к кластеру AKS

Для подключения к кластеру Kubernetes с локального компьютера используйте средство [kubectl][kubectl] (клиент командной строки Kubernetes).

Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Его также можно установить локально с помощью команды [az aks install-cli][]:

```azurecli
az aks install-cli
```

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][]. Следующий пример получает учетные данные для кластера AKS под названием *MyAKS* в *MyResourceGroup:*

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Создание учетной записи службы для Helm

Перед развертыванием Helm в кластере AKS с поддержкой RBAC необходимо создать учетную запись службы и привязку роли для службы Tiller. Дополнительные сведения о защите Helm и Tiller в кластере с поддержкой RBAC см. в руководстве по [использованию Tiller, пространств имен и RBAC][tiller-rbac]. Если кластер AKS не включен RBAC, пропустите этот шаг.

Создайте файл `helm-rbac.yaml` и скопируйте в него следующий код YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Создайте учетную запись службы и привязку роли с помощью команды `kubectl apply`:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Настройка Helm
Чтобы развернуть базовую службу Tiller в кластере AKS, используйте команду [helm init][helm-init]. Если кластер не включен RBAC, `--service-account` удалите аргумент и значение.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Настройка Draft

Если вы еще не настроили проект `draft init`на локальной машине, запустите:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Также необходимо настроить проект, чтобы использовать *loginServer* вашего ACR. Следующая команда `draft config set` используется `mydraftacr.azurecr.io` в качестве реестра.

```console
draft config set registry mydraftacr.azurecr.io
```

Вы настроили проект для использования ACR, а Draft может передвигать изображения контейнеров в ACR. Когда Draft запускает ваше приложение в вашем кластере AKS, никакие пароли или секреты не требуются для нажатия или вытягивания из реестра ACR. Поскольку между кластером AKS и ACR был создан траст, аутентификация происходит на уровне менеджера ресурсов Azure с использованием active Directory Azure.

## <a name="download-the-sample-application"></a>Загрузка примера приложения

Этот быстрый запуск использует [пример Java-приложения из репозитория Draft GitHub.][example-java] Клонировать приложение от GitHub и `draft/examples/example-java/` перейти к каталогу.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Выполнить пример приложения с проектом

Используйте `draft create` команду для подготовки приложения.

```console
draft create
```

Эта команда создает артефакты, которые используются для запуска приложения в кластере Kubernetes. Эти элементы включают файл Dockerfile, диаграмму Helm и файл *draft.toml*, который является файлом конфигурации Draft.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Чтобы запустить пример приложения в кластере AKS, используйте команду `draft up`.

```console
draft up
```

Эта команда создает Dockerfile для создания изображения контейнера, толкает изображение к вашему ACR и устанавливает диаграмму Helm для запуска приложения в AKS. Первый раз, когда вы запустите эту команду, нажатие и потянув изображение контейнера может занять некоторое время. После кэширования базовых уровней время, необходимое для развертывания приложения, значительно сократится.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Подключитесь к запущенной примеру приложения из локальной машины

Чтобы проверить приложение, используйте команду `draft connect`.

```console
draft connect
```

Эта команда устанавливает безопасное прокси-подключение к модулю pod Kubernetes. По завершении доступ к приложению можно получить через предоставленный URL-адрес.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Перейдите к приложению в `localhost` браузере, используя URL, чтобы увидеть образец приложения. В приведенном выше примере URL-адрес . `http://localhost:49804` Остановите подключение `Ctrl+c`с помощью .

## <a name="access-the-application-on-the-internet"></a>Доступ к приложению через Интернет

На предыдущем шаге было создано прокси-подключение к модулю pod приложения в кластере AKS. При разработке и тестировании приложения можно предоставить доступ к приложению через Интернет. Чтобы разоблачить приложение в Интернете, вы можете создать сервис Kubernetes с типом [LoadBalancer.][kubernetes-service-loadbalancer]

Обновление `charts/example-java/values.yaml` для создания службы *LoadBalancer.* Измените стоимость *service.type* с *ClusterIP* на *LoadBalancer.*

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Сохраните изменения, закройте файл `draft up` и запустите для повторного запуска приложения.

```console
draft up
```

Возвращение общедоступного IP-адреса службы займет несколько минут. Чтобы отследить ход выполнения, используйте команду `kubectl get service` с параметром *watch*:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Перейдите к балансу нагрузочно-балансаторв вашего приложения в браузере с помощью *EXTERNAL-IP,* чтобы увидеть образец приложения. В приведенном выше примере ИС . `52.175.224.118`

## <a name="iterate-on-the-application"></a>Выполнение итерации приложения

Вы можете итерировать приложение, внося `draft up`изменения локально и повторяя.

Обновление сообщения, [возвращенного на линии 7 src/main/java/helloworld/Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Выполните команду `draft up` для повторного развертывания приложения:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Чтобы увидеть обновленное приложение, перейдите на IP-адрес балансиста нагрузки снова и проверьте ваши изменения.

## <a name="delete-the-cluster"></a>Удаление кластера

Когда кластер больше не нужен, используйте команду [удаления группы аз для][az-group-delete] удаления группы ресурсов, кластера AKS, реестра контейнеров, сохраненных там изображений контейнеров и всех связанных ресурсов.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Когда вы удаляете кластер, субъект-служба Azure Active Directory, используемый в кластере AKS, не удаляется. Инструкции по удалению субъекта-службы см. в разделе [Дополнительные замечания][sp-delete]. Если вы использовали управляемую идентификацию, идентификация управляется платформой и не требует удаления.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Draft см. в документации Draft на GitHub.

> [!div class="nextstepaction"]
> [Документация по Draft][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
