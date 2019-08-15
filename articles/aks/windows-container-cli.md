---
title: Предварительный просмотр. Создание контейнера Windows Server в кластере Azure Kubernetes Service (AKS)
description: Узнайте, как быстро создать кластер Kubernetes, развернуть приложение в контейнере Windows Server в службе Kubernetes Azure (AKS) с помощью Azure CLI.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: mlearned
ms.openlocfilehash: 6c4d143bdaee0818d32b846a38a63eb48f69f717
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034034"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Предварительный просмотр. Создание контейнера Windows Server в кластере Azure Kubernetes Service (AKS) с помощью Azure CLI

Служба Azure Kubernetes (AKS) — это управляемая служба Kubernetes, которая позволяет быстро развертывать кластеры и управлять ими. В этой статье вы развернете кластер AKS с помощью Azure CLI. Вы также развертываете пример приложения ASP.NET в контейнере Windows Server в кластере.

Эта функция в настоящее время находится на стадии предварительной версии.

![Изображение с примером приложения ASP.NET](media/windows-container/asp-net-sample-app.png)

В этой статье предполагается базовое понимание концепций Kubernetes. Дополнительные сведения см. в статье [Ключевые концепции Kubernetes для службы Azure Kubernetes (AKS)][kubernetes-concepts].

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для работы с этой статьей требуется Azure CLI версии 2.0.61 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="before-you-begin"></a>Перед началом работы

После создания кластера, который может запускать контейнеры Windows Server, необходимо добавить дополнительный пул узлов. Добавление дополнительного пула узлов рассматривается на более позднем этапе, но сначала необходимо включить несколько предварительных версий функций.

> [!IMPORTANT]
> Функции предварительной версии AKS — это самостоятельная служба. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично покрываются службой поддержки клиентов на основе лучших усилий. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения об отсутствии см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Для использования контейнеров Windows Server требуется расширение CLI *AKS-Preview* версии 0.4.1 или более поздней. Установите расширение Azure CLI *AKS-Preview* с помощью команды [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Регистрация функции предварительной версии Windows

Чтобы создать кластер AKS, который может использовать несколько пулов узлов и запустить контейнеры Windows Server, сначала включите флаги функции *виндовспревиев* в подписке. Функция *виндовспревиев* также использует кластеры пулов с несколькими узлами и масштабируемый набор виртуальных машин для управления развертыванием и конфигурацией узлов Kubernetes. Зарегистрируйте флаг функции *виндовспревиев* с помощью команды [AZ Feature Register][az-feature-register] , как показано в следующем примере:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Любой кластер AKS, созданный после успешной регистрации флага функции *виндовспревиев* , используется для предварительной версии кластера. Чтобы продолжить создание обычных, полностью поддерживаемых кластеров, не включайте предварительные версии функций в производственных подписках. Используйте отдельную тестовую или новую подписку Azure для тестирования предварительных версий функций.

Для завершения регистрации потребуется несколько минут. Проверьте состояние регистрации с помощью команды [AZ Feature List][az-feature-list] .

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Когда состояние регистрации — `Registered`, нажмите клавиши CTRL + C, чтобы отключить наблюдение за состоянием.  Затем обновите регистрацию поставщика ресурсов *Microsoft. ContainerService* с помощью команды [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Ограничения

При создании кластеров AKS, поддерживающих несколько пулов узлов, и управлении ими действуют следующие ограничения.

* Для кластеров, созданных после успешной регистрации *виндовспревиев*, доступны несколько пулов узлов. Если вы регистрируете компоненты *мултиажентпулпревиев* и *вмсспревиев* для вашей подписки, можно также использовать несколько пулов узлов. Вы не можете добавлять пулы узлов или управлять ими с помощью существующего кластера AKS, созданного до успешной регистрации этих компонентов.
* Пул первого узла удалить нельзя.

Хотя эта функция находится на этапе предварительной версии, применяются следующие дополнительные ограничения.

* Кластер AKS может иметь не более восьми пулов узлов.
* Кластер AKS может содержать не более 400 узлов в восьми пулах узлов.
* Длина имени пула узлов Windows Server ограничена 6 символами.

## <a name="create-a-resource-group"></a>Создать группу ресурсов

Группа ресурсов Azure — это логическая группа, в которой выполняется развертывание и администрирование ресурсов Azure. Во время создания группы ресурсов вам будет предложено указать расположение. В этом расположении сохраняются метаданные группы ресурсов, а также выполняется их работа в Azure, если во время создания ресурса вы не указали другой регион. Создайте группу ресурсов с помощью команды [az group create][az-group-create].

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

> [!NOTE]
> В этой статье для команд в этом руководстве используется синтаксис bash.
> Если вы используете Azure Cloud Shell, убедитесь, что в раскрывающемся списке в левом верхнем углу окна Cloud Shell установлено значение **Bash**.

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

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

Чтобы запустить кластер AKS, который поддерживает пулы узлов для контейнеров Windows Server, кластер должен использовать сетевую политику, которая использует подключаемый модуль сети [Azure CNI][azure-cni-about] (дополнительно). Дополнительные сведения о планировании требуемых диапазонов подсетей и сетевых факторов см. в статье [Настройка сетей Azure CNI][use-advanced-networking]. Используйте команду [AZ AKS Create][az-aks-create] , чтобы создать кластер AKS с именем *myAKSCluster*. Эта команда создает необходимые сетевые ресурсы, если они не существуют.
  * В кластере настроен один узел
  * Параметры *Windows-Admin-Password* и *Windows-Admin-username* устанавливают учетные данные администратора для всех контейнеров Windows Server, созданных в кластере.

Предоставьте собственный безопасный *PASSWORD_WIN* (Помните, что команды в этой статье введены в оболочку Bash):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.5 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> Если вы получаете сообщение об ошибке проверки пароля, попробуйте создать группу ресурсов в другом регионе.
> Затем попытайтесь создать кластер с новой группой ресурсов.

Через несколько минут выполнение команды завершается и отображаются сведения о кластере в формате JSON.

## <a name="add-a-windows-server-node-pool"></a>Добавление пула узлов Windows Server

По умолчанию кластер AKS создается с пулом узлов, который может выполнять контейнеры Linux. Используйте `az aks nodepool add` команду, чтобы добавить дополнительный пул узлов, который может запускать контейнеры Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.5
```

Приведенная выше команда создает пул узлов с именем *нпвин* и добавляет его в *myAKSCluster*. При создании пула узлов для запуска контейнеров Windows Server по умолчанию для *node-VM-size* используется значение *Standard_D2s_v3*. Если вы решили задать параметр *node-VM-size* , просмотрите список [ограниченных размеров виртуальных машин][restricted-vm-sizes]. Минимальный рекомендуемый размер — *Standard_D2s_v3*. Приведенная выше команда также использует подсеть по умолчанию в виртуальной сети по `az aks create`умолчанию, создаваемой при запуске.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

Управлять кластером Kubernetes можно c помощью [kubectl][kubectl], клиента командной строки Kubernetes. Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Чтобы установить `kubectl` локально, используйте команду [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

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
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.5
aksnpwin987654                      Ready    agent   108s   v1.14.5
```

## <a name="run-the-application"></a>Выполнение приложения

Файл манифеста Kubernetes определяет требуемое состояние для кластера, включая образы контейнеров, которые нужно запустить. В этой статье манифест используется для создания всех объектов, необходимых для запуска примера приложения ASP.NET в контейнере Windows Server. Этот манифест включает [развертывание Kubernetes][kubernetes-deployment] для примера приложения ASP.NET и внешней [службы Kubernetes][kubernetes-service] для доступа к приложению из Интернета.

Пример приложения ASP.NET предоставляется как часть [примеров .NET Framework][dotnet-samples] и выполняется в контейнере Windows Server. AKS требует, чтобы контейнеры Windows Server были основаны на образах *Windows server 2019* или более поздней версии. Файл манифеста Kubernetes должен также определять [селектор узла][node-selector] , чтобы сообщить кластеру AKS о необходимости запуска модуля pod приложения ASP.NET на узле, который может запускать контейнеры Windows Server.

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
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
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

В следующем примере выходных данных показано, что развертывание и служба успешно созданы.

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

Изначально *внешний IP-адрес* для *примера* службы отображается как ожидающий.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Когда значение *EXTERNAL-IP* изменится с состояния *pending* на фактический общедоступный IP-адрес, используйте команду `CTRL-C`, чтобы остановить процесс отслеживания `kubectl`. В следующем примере выходных данных показан общедоступный IP-адрес, присвоенный службе.

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Чтобы увидеть пример приложения в действии, откройте в веб-браузере внешний IP-адрес службы.

![Изображение с примером приложения ASP.NET](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Удалить кластер

Чтобы удалить ненужные кластер, группу ресурсов, службу контейнеров и все связанные с ней ресурсы, выполните команду [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Когда вы удаляете кластер, субъект-служба Azure Active Directory, используемый в кластере AKS, не удаляется. Инструкции по удалению субъекта-службы см. в разделе с [дополнительными замечаниями][sp-delete].

## <a name="next-steps"></a>Следующие шаги

В этой статье вы развернули кластер Kubernetes и развернули пример приложения ASP.NET в контейнере Windows Server. [Доступ к веб-панели мониторинга Kubernetes][kubernetes-dashboard] для только что созданного кластера.

Дополнительные сведения о AKS и инструкции по созданию полного кода для примера развертывания см. в руководстве по кластерам Kubernetes.

> [!div class="nextstepaction"]
> [Руководство по AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
