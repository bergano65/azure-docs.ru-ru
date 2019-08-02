---
title: Разработка в Azure Kubernetes Service (AKS) с черновиком
description: Использование Draft с AKS и реестром контейнеров Azure
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "67303542"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Краткое руководство. Разработка в Azure Kubernetes Service (AKS) с черновиком

Черновик — это средство с открытым исходным кодом, которое помогает упаковывать и запускать контейнеры приложений в кластере Kubernetes. С помощью черновика можно быстро выполнить повторное развертывание приложения в Kubernetes по мере внесения изменений в код без фиксации изменений в системе управления версиями. Дополнительные сведения о черновике см. в [документации по черновику на сайте GitHub][draft-documentation].

В этой статье показано, как использовать черновик пакета и запускать приложение в AKS.


## <a name="prerequisites"></a>предварительные требования

* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
* [Установленный Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
* DOCKER установлен и настроен. DOCKER предоставляет пакеты, которые настраивают DOCKER в системе [Mac][docker-for-mac], [Windows][docker-for-windows]или [Linux][docker-for-linux] .
* [Helm установлен](https://github.com/helm/helm/blob/master/docs/install.md).
* [Установленный черновик][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Создайте кластер AKS. Приведенные ниже команды создают группу ресурсов с именем MyResourceGroup и кластер AKS с именем Мякс.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure
Чтобы использовать черновик для запуска приложения в кластере AKS, вам понадобится реестр контейнеров Azure для хранения образов контейнеров. В приведенном ниже примере с помощью команды [AZ запись контроля][az-acr-create] доступа создается для создания записи контроля доступа с именем *мидрафтакр* в группе ресурсов *MyResourceGroup* с номером SKU *Basic* . Необходимо указать собственное уникальное имя реестра. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. SKU *Базовый* — это оптимизированная по стоимости точка входа для целей разработки, обеспечивающая баланс ресурсов хранения и пропускной способности.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

Результат будет похож на следующий пример. Запишите значение *loginServer* для записи контроля доступа, так как оно будет использоваться на более позднем этапе. В приведенном ниже примере *mydraftacr.azurecr.IO* является *loginServer* для *мидрафтакр*.

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


Чтобы использовать экземпляр записи контроля доступа в черновике, необходимо сначала войти в систему. Для входа используйте команду [AZ запись контроля][az-acr-login] доступа. В приведенном ниже примере выполняется вход в запись контроля доступа с именем *мидрафтакр*.

```azurecli
az acr login --name MyDraftACR
```

После выполнения эта команда возвращает сообщение *Login Succeeded* (Вход выполнен).

## <a name="create-trust-between-aks-cluster-and-acr"></a>Создание отношения доверия между кластером AKS и ACR

Кластеру AKS также требуется доступ к записи контроля доступа для извлечения образов контейнеров и их запуска. Вы разрешаете доступ к записи контроля доступа из AKS, установив доверие. Чтобы установить отношения доверия между кластером AKS и реестром ACR, предоставьте субъекту-службе Azure Active Directory, используемому кластером AKS, разрешение на доступ к реестру ACR. Следующие команды предоставляют разрешения субъекту-службе кластера *мякс* в *MyResourceGroup* записи контроля доступа *мидрафтакр* в *MyResourceGroup*.

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

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][]. Следующий пример получает учетные данные для кластера AKS с именем *мякс* в *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Создание учетной записи службы для Helm

Перед развертыванием Helm в кластере AKS с поддержкой RBAC необходимо создать учетную запись службы и привязку роли для службы Tiller. Дополнительные сведения о защите Helm и кассы в кластере с поддержкой RBAC см. в разделе [ящик, пространства имен и RBAC][tiller-rbac]. Если в кластере AKS не включен RBAC, пропустите этот шаг.

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
Чтобы развернуть базовый ящик в кластере AKS, используйте команду [Helm init][helm-init] . Если в кластере не включен RBAC, удалите `--service-account` аргумент и значение.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Настройка Draft

Если вы еще не настроили черновик на локальном компьютере `draft init`, выполните:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Кроме того, необходимо настроить черновик для использования *loginServer* записи контроля доступа. Следующая команда использует `draft config set` для использования `mydraftacr.azurecr.io` в качестве реестра.

```console
draft config set registry mydraftacr.azurecr.io
```

Вы настроили черновик для использования записи контроля доступа, и черновик может отправить образы контейнеров в запись контроля доступа. Когда черновик запускает приложение в кластере AKS, пароли или секреты не требуются для отправки в реестр записей контроля доступа или извлечения из него. Так как между кластером AKS и записью контроля доступа было создано отношение доверия, проверка подлинности выполняется на уровне Azure Resource Manager с помощью Azure Active Directory.

## <a name="download-the-sample-application"></a>Загрузка примера приложения

В этом кратком руководстве используется [пример приложения Java из черновика репозитория GitHub][example-java]. Клонирование приложения из GitHub и переход к `draft/examples/example-java/` каталогу.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Запуск примера приложения с черновиком

Для подготовки приложения используйте команду.`draft create`

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

Эта команда создает Dockerfile для создания образа контейнера, отправляет образ в запись контроля доступа и устанавливает диаграмму Helm для запуска приложения в AKS. При первом выполнении этой команды Отправка и извлечение образа контейнера может занять некоторое время. После кэширования базовых уровней время, необходимое для развертывания приложения, значительно сократится.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Подключение к выполняющемуся примеру приложения с локального компьютера

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

Перейдите к приложению в браузере `localhost` по URL-адресу, чтобы просмотреть пример приложения. В приведенном выше примере URL-адрес `http://localhost:49804`имеет значение. Останавливает подключение с помощью `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Доступ к приложению через Интернет

На предыдущем шаге было создано прокси-подключение к модулю pod приложения в кластере AKS. При разработке и тестировании приложения можно предоставить доступ к приложению через Интернет. Чтобы предоставить доступ к приложению через Интернет, можно создать службу Kubernetes с типом подсистемы балансировки [нагрузки][kubernetes-service-loadbalancer].

Обновите `charts/example-java/values.yaml` , чтобы создать службу *балансировки нагрузки* . Измените значение *Service. Type* с *кластера* на подсистему *балансировки нагрузки*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Сохраните изменения, закройте файл и выполните команду `draft up` , чтобы повторно запустить приложение.

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

Перейдите к подсистеме балансировки нагрузки приложения в браузере с помощью *внешнего IP-адреса* , чтобы просмотреть пример приложения. В приведенном выше примере это IP- `52.175.224.118`адрес.

## <a name="iterate-on-the-application"></a>Выполнение итерации приложения

Вы можете выполнить итерацию приложения, внеся изменения локально и `draft up`после выполнения.

Обновите сообщение, возвращенное в [строке 7 из src/Main/Java/HelloWorld/Hello. Java.][example-java-hello-l7]

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

Чтобы просмотреть обновленное приложение, перейдите к IP-адресу балансировщика нагрузки еще раз и убедитесь, что изменения отображаются.

## <a name="delete-the-cluster"></a>Удаление кластера

Если кластер больше не нужен, используйте команду [AZ Group Delete][az-group-delete] , чтобы удалить группу ресурсов, кластер AKS, реестр контейнеров, хранящиеся в нем образы контейнеров и все связанные с ним ресурсы.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Когда вы удаляете кластер, субъект-служба Azure Active Directory, используемый в кластере AKS, не удаляется. Инструкции по удалению субъекта-службы см. в разделе с [дополнительными замечаниями][sp-delete].

## <a name="next-steps"></a>Следующие шаги

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
