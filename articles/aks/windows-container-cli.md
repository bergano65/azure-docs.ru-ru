---
title: Запуск контейнера Windows Server в кластере службы Azure Kubernetes
description: Узнайте, как быстро создать кластер Kubernetes, разверните приложение в контейнере Windows Server в службе Azure Kubernetes (AKS) с помощью Azure CLI.
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 8d2a91f63815e7ba4bcbe4084b80a06fa7779099
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886727"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Предварительный просмотр - Создание контейнера Windows Server в кластере службы Azure Kubernetes (AKS) с помощью Azure CLI

Служба Azure Kubernetes (AKS) — это управляемая служба Kubernetes, которая позволяет быстро развертывать кластеры и управлять ими. В этой статье развертывается кластер AKS с помощью Azure CLI. Кроме того, в кластер еразвернутся ASP.NET пример приложения в контейнере Windows Server.

Эта функция в настоящее время находится на стадии предварительной версии.

![Изображение просмотра для ASP.NET пример приложения](media/windows-container/asp-net-sample-app.png)

Эта статья предполагает базовое понимание концепций Кубертетеса. Дополнительные сведения см. в статье [Ключевые концепции Kubernetes для службы Azure Kubernetes (AKS)][kubernetes-concepts].

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, эта статья требует, чтобы вы запускали версию Azure CLI 2.0.61 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="before-you-begin"></a>Подготовка к работе

После создания кластера, который может запускать контейнеры Windows Server, необходимо добавить дополнительный пул узлов. Добавление дополнительного пула узлов покрыто более поздним шагом, но сначала необходимо включить несколько функций предварительного просмотра.

> [!IMPORTANT]
> Функции предварительного просмотра AKS являются отказом от самообслуживания. Предварительные просмотры предоставляются "как есть" и "по мере возможности" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Анонсы AKS частично покрываются поддержкой клиентов на основе наилучших усилий. Таким образом, эти функции не предназначены для использования в производстве. Для получения дополнительной информации, пожалуйста, ознакомьтесь со следующими статьями поддержки:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Для использования контейнеров Windows Server требуется версия расширения *CLI aks-preview* 0.4.12 или выше. Установите расширение *aks-preview* Azure CLI с помощью команды [расширения az,][az-extension-add] а затем проверьте все доступные обновления с помощью команды [обновления расширения аз::][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Регистрация функции предварительного просмотра Windows

Для создания кластера AKS, который может использовать несколько пулов узлов и запускать контейнеры Windows Server, сначала включите флаги функций *WindowsPreview* в подписке. Функция *WindowsPreview* также использует многоузловые пулы и набор виртуального масштаба машины для управления развертыванием и конфигурацией узлов Kubernetes. Зарегистрируйте флаг функции *WindowsPreview,* используя команду [регистра функций az,][az-feature-register] показанную в следующем примере:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Любой кластер AKS, который вы создаете после успешной регистрации флага функции *WindowsPreview,* использует этот кластер предварительного просмотра. Чтобы продолжать создавать регулярные, полностью поддерживаемые кластеры, не включайте функции предварительного просмотра в производственных подписках. Используйте отдельный тест или подписку Azure на разработку для тестирования функций предварительного просмотра.

Регистрация занимает несколько минут. Проверьте статус регистрации с помощью команды [списка функций az:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Когда состояние `Registered`регистрации, нажмите Ctrl-C, чтобы остановить мониторинг состояния.  Затем обновите регистрацию поставщика ресурсов *Microsoft.ContainerService* с помощью команды [регистра аз-провайдера:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Ограничения

При создании и управлении кластерами AKS, поддерживающих несколько пулов узлов, применяются следующие ограничения:

* Вы не можете удалить пул первого узла.

В то время как эта функция находится в предварительном просмотре, применяются следующие дополнительные ограничения:

* Кластер AKS может иметь максимум восемь пулов узлов.
* Кластер AKS может иметь максимум 400 узлов в этих восьми пулах узлов.
* Имя пула узла Windows Server имеет ограничение в 6 символов.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure — это логическая группа, в которой развертываются и управляются ресурсы Azure. Во время создания группы ресурсов вам будет предложено указать расположение. В этом расположении сохраняются метаданные группы ресурсов, а также выполняется их работа в Azure, если во время создания ресурса вы не указали другой регион. Создайте группу ресурсов с помощью команды [az group create][az-group-create].

Следующий пример создает группу ресурсов под названием *myResourceGroup* в *восточном* месте.

> [!NOTE]
> Эта статья использует Bash синтаксис для команд в этом учебнике.
> Если вы используете Azure Cloud Shell, убедитесь, что выпадение в верхнем левом окне оболочки облака настроено на **Bash.**

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

Для запуска кластера AKS, поддерживающего пулы узлов для контейнеров Windows Server, кластеру необходимо использовать сетевую политику, использующее плагин сети [Azure CNI][azure-cni-about] (продвинутый). Для получения более подробной информации, которая поможет спланировать требуемые диапазоны подсетей и соображения сети, [см.][use-advanced-networking] Используйте команду [az aks для][az-aks-create] создания кластера AKS под названием *myAKSCluster.* Эта команда создаст необходимые сетевые ресурсы, если их не существует.
  * Кластер настроен с двумя узлами
  * Параметры *Windows-admin-password* и *windows-admin-username* устанавливают учетные данные администратора для любых контейнеров Windows Server, созданных в кластере.

> [!NOTE]
> Чтобы обеспечить надежное функционирование кластера, необходимо запустить не менее 2 (двух) узлов в пуле узлов по умолчанию.

Предоставьте свой собственный защищенный *PASSWORD_WIN* (помните, что команды в этой статье вводятся в оболочку BASH):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.15.7 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --network-plugin azure
```

> [!Note]
> Если вы получили ошибку проверки пароля, попробуйте создать группу ресурсов в другом регионе.
> Затем попробуйте создать кластер с новой группой ресурсов.

> [!Note]
> Если вы не можете создать кластер AKS, потому что версия не поддерживается в этом регионе, то вы можете использовать команду «az aks get-version - location eastus», чтобы найти поддерживаемый список версий для этого региона.


Через несколько минут выполнение команды завершается и отображаются сведения о кластере в формате JSON. Иногда для обеспечения кластера может уйти больше нескольких минут. Разрешить до 10 минут в этих случаях. 

## <a name="add-a-windows-server-node-pool"></a>Добавление пула узлов Windows Server

По умолчанию кластер AKS создается с пулом узлов, который может запускать контейнеры Linux. Используйте `az aks nodepool add` команду, чтобы добавить дополнительный пул узлов, который может запускать контейнеры Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

Вышеупомянутая команда создает новый пул узлов под названием *npwin* и добавляет его в *myAKSCluster.* При создании пула узлов для запуска контейнеров Windows Server значение по умолчанию для *узла-vm-size* *составляет Standard_D2s_v3.* Если вы решили установить параметр *размера узла-Vm,* пожалуйста, проверьте список [ограниченных размеров VM.][restricted-vm-sizes] Минимальный рекомендуемый размер *составляет Standard_D2s_v3.* Вышеупомянутая команда также использует подсеть по умолчанию `az aks create`в внесети по умолчанию, созданной при запуске.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

Управлять кластером Kubernetes можно при помощи [kubectl][kubectl], клиента командной строки Kubernetes. Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Чтобы установить `kubectl` локально, используйте команду [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Чтобы проверить подключение к кластеру, используйте команду [kubectl get][kubectl-get] для получения списка узлов кластера.

```console
kubectl get nodes
```

Следующий вывод приведен в следующем примере показывает все узлы в кластере. Убедитесь, что состояние всех узлов *готово:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>Запуск приложения

Файл манифеста Kubernetes определяет требуемое состояние для кластера, включая образы контейнеров, которые нужно запустить. В этой статье используется манифест для создания всех объектов, необходимых для запуска ASP.NET примерприложения в контейнере Windows Server. Этот манифест включает в себя [развертывание Kubernetes][kubernetes-deployment] для ASP.NET примерприложения и внешнюю [службу Kubernetes][kubernetes-service] для доступа к приложению из Интернета.

ASP.NET пример приложения предоставляется как часть [рамочных образцов .NET][dotnet-samples] и работает в контейнере Windows Server. AKS требует, чтобы контейнеры Windows Server основывались на изображениях *Windows Server 2019* или больше. Файл манифеста Kubernetes должен также определить [селектор узлов,][node-selector] чтобы указывать кластеру AKS для запуска стручка ASP.NET образца приложения на узлах, которые могут запускать контейнеры Windows Server.

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

```console
kubectl apply -f sample.yaml
```

Следующий пример вывода показывает успешно созданные развертывание и обслуживание:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Тестирование приложения

При запуске приложения Служба Kubernetes предоставляет внешний интерфейс приложения в Интернете. Процесс создания может занять несколько минут. Иногда предоставление службы может занять больше нескольких минут. Разрешить до 10 минут в этих случаях.

Чтобы отслеживать ход выполнения, используйте команду [kubectl get service][kubectl-get] с аргументом `--watch`.

```console
kubectl get service sample --watch
```

Первоначально *EXTERNAL-IP* для *службы образца* отображается как *отложенный.*

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Когда значение *EXTERNAL-IP* изменится с состояния *pending* на фактический общедоступный IP-адрес, используйте команду `CTRL-C`, чтобы остановить процесс отслеживания `kubectl`. В следующем примере выходных данных показан общедоступный IP-адрес, присвоенный службе.

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Чтобы увидеть пример приложения в действии, откройте веб-браузер на внешний IP-адрес вашего сервиса.

![Изображение просмотра для ASP.NET пример приложения](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Если вы получаете тайм-аут соединения при попытке загрузить страницу, то вы должны проверить образец приложение готово со следующей командой "kubectl получить стручки - смотреть". Иногда контейнер windows не будет запущен к моменту появления внешнего IP-адреса.

## <a name="delete-cluster"></a>Удаление кластера

Чтобы удалить ненужные кластер, группу ресурсов, службу контейнеров и все связанные с ней ресурсы, выполните команду [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Когда вы удаляете кластер, субъект-служба Azure Active Directory, используемый в кластере AKS, не удаляется. Инструкции по удалению субъекта-службы см. в разделе [Дополнительные замечания][sp-delete].

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы развернули кластер Kubernetes и развернули ASP.NET пример приложения в контейнере Windows Server. Чтобы выполнить доступ к панели мониторинга кластера Kubernetes, который вы создали, см. статью [Подключение веб-панели мониторинга Kubernetes в Службе Azure Kubernetes (AKS)][kubernetes-dashboard].

Дополнительные сведения о AKS и инструкции по созданию полного кода для примера развертывания см. в руководстве по кластерам Kubernetes.

> [!div class="nextstepaction"]
> [Учебник AKS][aks-tutorial]

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
