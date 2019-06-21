---
title: Разработка в службе Azure Kubernetes (AKS) с помощью Draft
description: Использование Draft с AKS и реестром контейнеров Azure
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303542"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Краткое руководство. Разработка в службе Azure Kubernetes (AKS) с помощью Draft

Draft — это инструмент с открытым кодом, который помогает упаковывать и запускать приложения-контейнеры в кластере Kubernetes. С помощью Draft можно быстро повторно развернуть приложение в Kubernetes при изменении кода без необходимости зафиксируйте изменения в систему управления версиями. Дополнительные сведения о Draft см. в разделе [документации по Draft на GitHub][draft-documentation].

В этой статье показано, как использовать пакет Draft и запускать приложения в AKS.


## <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
* [Установленный Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker установлен и настроен. Docker предоставляет пакеты, которые настраивают Docker на [Mac][docker-for-mac], [Windows][docker-for-windows], или [Linux][docker для linux] системы.
* [Установить helm](https://github.com/helm/helm/blob/master/docs/install.md).
* [Черновик установлен][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Создание кластера AKS. Следующие команды создания группа ресурсов с именем MyResourceGroup и вызывается MyAKS кластера AKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure
Чтобы использовать черновик для запуска приложения в кластере AKS, необходимо в реестре контейнеров Azure для хранения образов контейнеров. Ниже примере использует [создать az acr][az-acr-create] создать запись контроля доступа с именем *MyDraftACR* в *MyResourceGroup* группу ресурсов с помощью *основные* НОМЕР SKU. Необходимо предоставить собственное имя уникальным реестра. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. SKU *Базовый* — это оптимизированная по стоимости точка входа для целей разработки, обеспечивающая баланс ресурсов хранения и пропускной способности.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

Результат будет похож на следующий пример. Запомните или запишите *loginServer* значение для записи контроля доступа, так как он будет использоваться в дальнейшем. В следующем примере *mydraftacr.azurecr.io* — *loginServer* для *MyDraftACR*.

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


Черновая версия для использования в экземпляр ACR необходимо сначала выполнить вход. Используйте [az acr login][az-acr-login] команду, чтобы войти. Пример ниже будут входить в записи контроля доступа с именем *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

После выполнения эта команда возвращает сообщение *Login Succeeded* (Вход выполнен).

## <a name="create-trust-between-aks-cluster-and-acr"></a>Создание отношения доверия между кластером AKS и ACR

Кластера AKS также требуется доступ к вашей записи контроля доступа для извлечения образов контейнеров и их выполнения. Можно разрешить доступ к записи управления Доступом с AKS, доверительные отношения. Чтобы установить отношения доверия между кластером AKS и реестром ACR, предоставьте субъекту-службе Azure Active Directory, используемому кластером AKS, разрешение на доступ к реестру ACR. Следующие команды предоставить разрешения для субъекта-службы *MyAKS* кластера в *MyResourceGroup* для *MyDraftACR* ACR в  *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Подключитесь к кластеру AKS

Чтобы подключиться к кластеру Kubernetes с локального компьютера, используйте [kubectl][kubectl], клиент командной строки Kubernetes.

Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Его также можно установить локально с помощью команды [az aks install-cli][]:

```azurecli
az aks install-cli
```

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][]. В следующем примере возвращаются учетные данные для кластера AKS с именем *MyAKS* в *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Создайте учетную запись службы для Helm

Перед развертыванием Helm в кластере AKS с поддержкой RBAC необходимо создать учетную запись службы и привязку роли для службы Tiller. Дополнительные сведения о защите Helm / кластером с поддержкой Tiller в RBAC, см. в разделе [Tiller, пространства имен и RBAC][tiller-rbac]. Если кластер AKS не включена RBAC, пропустите этот шаг.

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
Чтобы развернуть базовый Tiller в кластере AKS, используйте [helm init][helm-init] команды. Если кластер не включен RBAC, удалите `--service-account` аргумент и значение.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Настройка Draft

Если вы еще не настроили Draft на локальном компьютере, запустите `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Необходимо также настроить черновик, чтобы использовать *loginServer* из ACR. Приведенная ниже команда использует `draft config set` использовать `mydraftacr.azurecr.io` как реестра.

```console
draft config set registry mydraftacr.azurecr.io
```

Вы настроили черновик, чтобы использовать ACR, а черновик можно отправлять образы контейнера в ACR. При запуске приложения в кластере AKS черновика не пароли или секреты должны отправлять к и извлекать из реестра ACR. С момента создания отношения доверия между кластером AKS и ACR, аутентификация выполняется на уровне Azure Resource Manager, с помощью Azure Active Directory.

## <a name="download-the-sample-application"></a>Загрузка примера приложения

В этом кратком руководстве [пример приложения java из репозитория GitHub черновик][example-java]. Клонирование приложения из GitHub и перейдите к `draft/examples/example-java/` каталога.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Запустите пример приложения с помощью Draft

Используйте `draft create` команду, чтобы подготовить приложение.

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

Эта команда создает файл Docker, чтобы создать образ контейнера, отправляет образ в ACR и устанавливает диаграмму Helm для запуска приложения в AKS. При первом выполнении этой команды, отправка и получение образа контейнера может занять некоторое время. После кэширования базовых уровней время, необходимое для развертывания приложения, значительно сократится.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Подключение с запуском примера приложения на локальном компьютере

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

Перейдите к приложению в браузере с помощью `localhost` URL-адрес, чтобы увидеть пример приложения. В приведенном выше примере URL-адрес является `http://localhost:49804`. Остановка соединения с помощью `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Доступ к приложению через Интернет

На предыдущем шаге было создано прокси-подключение к модулю pod приложения в кластере AKS. При разработке и тестировании приложения можно предоставить доступ к приложению через Интернет. Чтобы предоставить доступ к приложению в Интернете, можно создать службу Kubernetes с типом [LoadBalancer][kubernetes-service-loadbalancer].

Обновление `charts/example-java/values.yaml` для создания *LoadBalancer* службы. Измените значение свойства *service.type* из *неожиданным* для *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Сохраните изменения и закройте файл запуска `draft up` Чтобы снова запустить приложение.

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

Перейдите к подсистеме балансировки нагрузки приложения в браузере с помощью *внешний IP-* для ознакомьтесь с примером приложения. В приведенном выше примере IP-адрес является `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Выполнение итерации приложения

Приложение может выполнять итерацию, внесение изменений локально и повторный запуск `draft up`.

Обновить сообщение, возвращенное на [строка 7 из src/main/java/helloworld/Hello.java][example-java-hello-l7]

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

Чтобы увидеть обновленное приложение, еще раз перейдите к IP-адрес своей подсистемы балансировки нагрузки и убедитесь, что изменения отображаются.

## <a name="delete-the-cluster"></a>Удаление кластера

Если кластер больше не нужен, используйте [удаление группы az][az-group-delete] команду, чтобы удалить группу ресурсов, кластер AKS, реестр контейнеров, образы контейнеров хранящихся на нем, и все связанные ресурсы.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Когда вы удаляете кластер, субъект-служба Azure Active Directory, используемый в кластере AKS, не удаляется. Действия по удалению субъекта-службы, см. в разделе [AKS службы субъекта рекомендации и удаления][sp-delete].

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
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
