---
title: Preview — создать контейнер Windows Server в кластере Azure Kubernetes Service (AKS)
description: Узнайте, как быстро создать кластер Kubernetes, развертывать приложения в контейнер Windows Server в Azure Kubernetes Service (AKS) с помощью Azure CLI.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: zarhoads
ms.openlocfilehash: 328fb9707c7151b8696cfb55e13567db90e45b7f
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991141"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Preview — Создание контейнера Windows Server в кластер Azure Kubernetes Service (AKS), с помощью Azure CLI

Служба Azure Kubernetes (AKS) — это управляемая служба Kubernetes, которая позволяет быстро развертывать кластеры и управлять ими. В этой статье вы развернете кластер AKS с помощью Azure CLI. Вы также развернуть пример приложения ASP.NET в контейнер Windows Server в кластер.

Эта функция в настоящее время находится на стадии предварительной версии.

![Изображение перехода к примеру приложения ASP.NET](media/windows-container/asp-net-sample-app.png)

В этой статье требуется понимание основных концепций Kubernetes. Дополнительные сведения см. в статье [Ключевые концепции Kubernetes для службы Azure Kubernetes (AKS)][kubernetes-concepts].

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, здесь предполагается, что вы используете Azure CLI версии 2.0.61 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="before-you-begin"></a>Перед началом работы

После создания кластера, можно запускать контейнеры Windows Server, необходимо добавить пул дополнительный узел. Добавление пула дополнительный узел рассматривается позже, но сначала необходимо включить несколько функций предварительной версии.

> [!IMPORTANT]
> Компоненты предварительной версии AKS, самообслуживания и согласиться. Предварительные версии предоставляются для сбора отзывов и ошибки нашего сообщества. Тем не менее они не поддерживаются в службе технической поддержки Azure. Если создать кластер, или добавить эти компоненты в имеющиеся кластеры, этого кластера не поддерживается, пока эта функция больше не находится в предварительной версии и этапах общедоступная (GA).
>
> При возникновении проблем с помощью функции предварительной версии, [сообщите о них в репозитории AKS GitHub] [ aks-github] именем функции предварительной версии в заголовке ошибки.

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview
    
Команды CLI для создания и управления несколькими пулы узлов доступны в *предварительной версии aks* расширение интерфейса командной строки. Установка *предварительной версии aks* расширение Azure CLI с помощью [добавить расширения az] [ az-extension-add] команды, как показано в следующем примере:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Если вы ранее установили *предварительной версии aks* расширения, устанавливать доступные обновления с помощью `az extension update --name aks-preview` команды.

### <a name="register-windows-preview-feature"></a>Зарегистрируйте функцию предварительной версии Windows

Чтобы создать кластер AKS, можно использовать несколько пулов узлов и запускать контейнеры Windows Server, сначала включите функцию *WindowsPreview* флаги по вашей подписке компонентов. *WindowsPreview* функция также использует пул с несколькими узлами кластеров и масштабируемый набор для управления развертыванием и конфигурацию узлов Kubernetes виртуальных машин. Зарегистрировать *WindowsPreview* функции с помощью флага [az функция register] [ az-feature-register] команды, как показано в следующем примере:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Создается после успешной регистрации кластера AKS *WindowsPreview* этой предварительной версии кластера использовать флаг компонента. Чтобы продолжить создавать кластеры обычный, полностью поддерживаются, не включите функции предварительной версии на рабочие подписки. Используйте отдельный тестовый или разработка подписки Azure для тестирования функции предварительной версии.

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Состояние регистрации можно проверить с помощью команды [az feature list][az-feature-list].

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов *Microsoft.ContainerService* с помощью команды [az provider register][az-provider-register].

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Ограничения

При создании и управление кластерами AKS, которые поддерживают несколько пулов узлов, применяются следующие ограничения:

* Несколько пулов узлов доступны для кластеров, созданных после успешной регистрации *WindowsPreview*. Несколько пулов узла также доступны при регистрации *MultiAgentpoolPreview* и *VMSSPreview* компоненты для вашей подписки. Невозможно добавить или Управление пулами узел в существующий кластер AKS создан, прежде чем эти функции были успешно зарегистрирован.
* Невозможно удалить пул первый узел.

Пока эта функция находится на этапе предварительной версии, применяются следующие дополнительные ограничения.

* Кластер AKS может иметь не более восьми пулов узлов.
* Кластер AKS может иметь не более 400 узлов в пулах этих восьми узлов.
* Имя пула узла Windows Server имеет ограничение на число 6 символов.
* Пулы узлов Windows Server недоступны в регионах Канада в данный момент.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure — это логическая группа, в которой выполняется развертывание и администрирование ресурсов Azure. Во время создания группы ресурсов вам будет предложено указать расположение. В этом расположении сохраняются метаданные группы ресурсов, а также выполняется их работа в Azure, если во время создания ресурса вы не указали другой регион. Создайте группу ресурсов с помощью команды [az group create][az-group-create].

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

В следующем примере выходных данных показано, что группа ресурсов успешно создана:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>Создание кластера AKS
Чтобы запустить кластер AKS, который поддерживает пулы узлов для контейнеров Windows Server, необходимо использовать политику сети, которая использует кластер [Azure CNI] [ azure-cni-about] подключаемого модуля (Дополнительно) сети. Более подробные сведения, которые помогут в планировании диапазоны необходимые подсети и рекомендации по сети, см. в разделе [Настройка сети Azure CNI][use-advanced-networking]. Используйте [создать az aks] [ az-aks-create] команду, чтобы создать кластер службы контейнеров AZURE с именем *myAKSCluster*. Эта команда создаст необходимые сетевые ресурсы, если они не существуют.
  * Кластер настроен с одним узлом
  * *Windows-admin-password* и *имя пользователя администратора windows* параметры задать учетные данные администратора для любого контейнера Windows Server, создания в кластере.

Укажите собственные безопасные *PASSWORD_WIN*.

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

Через несколько минут выполнение команды завершается и отображаются сведения о кластере в формате JSON.

## <a name="add-a-windows-server-node-pool"></a>Добавьте пул узлов Windows Server

По умолчанию кластер службы контейнеров AZURE создается с пулом узел, который можно запускать контейнеры Linux. Используйте `az aks nodepool add` команду, чтобы добавить пул дополнительный узел, можно запускать контейнеры Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0
```

Приведенная выше команда создает новый пул узел с именем *npwin* и добавляет ее к *myAKSCluster*. При создании пула узел для запуска контейнеров Windows Server, значение по умолчанию для *размер для виртуальной машины узла* — *Standard_D2s_v3*. Если вы решили установить *размер для виртуальной машины узла* параметр, проверьте список [ограниченные размеры виртуальных Машин][restricted-vm-sizes]. Минимальный рекомендуемый размер — *Standard_D2s_v3*. Приведенная выше команда также использует подсеть по умолчанию в виртуальной сети по умолчанию, созданные при работе `az aks create`.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

Управлять кластером Kubernetes можно при помощи [kubectl][kubectl], клиента командной строки Kubernetes. Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Чтобы установить `kubectl` локально, используйте команду [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Чтобы настроить подключение `kubectl` к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Проверьте подключение к кластеру, выполнив команду [kubectl get][kubectl-get], чтобы просмотреть список узлов кластера.

```azurecli-interactive
kubectl get nodes
```

В следующем примере показан единый узел, созданный на предыдущих шагах. Убедитесь, что узел находится в состоянии *Ready* (Готово):

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.0
aksnpwin987654                      Ready    agent   108s   v1.14.0
```

## <a name="run-the-application"></a>Выполнение приложения

Файл манифеста Kubernetes определяет требуемое состояние для кластера, включая образы контейнеров, которые нужно запустить. В этой статье манифест используется для создания всех объектов, необходимых для запуска примера приложения ASP.NET в контейнер Windows Server. Этот манифест содержит [развертывания Kubernetes] [ kubernetes-deployment] для примера приложения ASP.NET и внешним [службы Kubernetes] [ kubernetes-service] для доступ к приложению из Интернета.

Пример приложения ASP.NET предоставляется как часть [примеры .NET Framework] [ dotnet-samples] и выполняется в контейнере Windows Server. AKS требуется контейнеры Windows Server, должен быть основан на образы *Windows Server 2019* или более поздней версии. Также необходимо определить файл манифеста Kubernetes [селектор узла] [ node-selector] сообщить кластера AKS для запуска примера приложения ASP.NET pod в узле, который можно запускать контейнеры Windows Server.

Создайте файл `sample.yaml` и скопируйте в него следующее определение YAML. Если вы используете Azure Cloud Shell, этот файл можно создать с помощью `vi` или `nano`, как при работе в виртуальной или физической системе:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800m
          requests:
            cpu: .1
            memory: 300m
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Разверните приложение с помощью команды [kubectl apply][kubectl-apply] и укажите имя манифеста YAML:

```azurecli-interactive
kubectl apply -f sample.yaml
```

В следующем примере выходных данных показано развертывание и служба успешно создана:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Тестирование приложения

При запуске приложения Служба Kubernetes предоставляет внешний интерфейс приложения в Интернете. Процесс создания может занять несколько минут.

Чтобы отслеживать ход выполнения, используйте команду [kubectl get service][kubectl-get] с аргументом `--watch`.

```azurecli-interactive
kubectl get service sample --watch
```

Изначально *EXTERNAL-IP* для *пример* службы отображается как *ожидающие*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Когда значение *EXTERNAL-IP* изменится с состояния *pending* на фактический общедоступный IP-адрес, используйте команду `CTRL-C`, чтобы остановить процесс отслеживания `kubectl`. В следующем примере выходных данных показан общедоступный IP-адрес, присвоенный службе.

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Чтобы просмотреть пример приложения в действии, откройте веб-браузере внешний IP-адрес службы.

![Изображение перехода к примеру приложения ASP.NET](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Удалить кластер

Чтобы удалить ненужные кластер, группу ресурсов, службу контейнеров и все связанные с ней ресурсы, выполните команду [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Когда вы удаляете кластер, субъект-служба Azure Active Directory, используемый в кластере AKS, не удаляется. Инструкции по удалению субъекта-службы см. в разделе [Дополнительные замечания][sp-delete].

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы развернули кластер Kubernetes и развернуть пример приложения ASP.NET в контейнер Windows Server в нем. Чтобы выполнить доступ к панели мониторинга кластера Kubernetes, который вы создали, см. статью [Подключение веб-панели мониторинга Kubernetes в Службе Azure Kubernetes (AKS)][kubernetes-dashboard].

Дополнительные сведения о AKS и инструкции по созданию полного кода для примера развертывания см. в руководстве по кластерам Kubernetes.

> [!div class="nextstepaction"]
> [Руководство по AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[aks-github]: https://github.com/azure/aks/issues
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
