---
title: Краткое руководство. Развертывание кластера Службы Azure Kubernetes
description: Узнайте, как быстро создать кластер Kubernetes, развернуть приложение и отслеживать производительность в Службе Azure Kubernetes (AKS) с помощью PowerShell.
services: container-service
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 74a71bdc4c9aef9a6964f0c9120a902262a50526
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85207147"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Краткое руководство. Развертывание кластера Службы Azure Kubernetes с помощью PowerShell

Выполнив инструкции из этого краткого руководства, вы развернете кластер Службы Azure Kubernetes (AKS) с помощью PowerShell. Служба Azure Kubernetes (AKS) — это управляемая служба Kubernetes, которая позволяет быстро развертывать кластеры и управлять ими. Затем в кластере будет запущено многоконтейнерное приложение, которое включает в себя веб-интерфейс и экземпляр Redis. Затем вы узнаете, как отслеживать работоспособность кластера и модулей pod, на которых выполняется ваше приложение.

Дополнительные сведения о создании пула узлов Windows Server см. в разделе [Создание кластера AKS, поддерживающего контейнеры Windows Server][windows-container-powershell].

![Приложение для голосования, развернутое в Службе Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

В этом руководстве предполагается, что у вас есть некоторое представление о функциях Kubernetes. Дополнительные сведения см. в статье [Ключевые концепции Kubernetes для службы Azure Kubernetes (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

Если вы решили использовать PowerShell локально, для работы с этой статьей установите модуль PowerShell Az и подключитесь к учетной записи Azure с помощью командлета [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). См. сведения об [установке модуля Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Если вы используете несколько подписок Azure, выберите ту, за ресурсы в которой будут выставляться счета. Выберите идентификатор требуемой подписки с помощью командлета [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

[Группа ресурсов Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) — это логическая группа, в которой развертываются и управляются ресурсы Azure. Во время создания группы ресурсов вам будет предложено указать расположение. В этом расположении сохраняются метаданные группы ресурсов, а также выполняется их работа в Azure, если во время создания ресурса вы не указали другой регион. Создайте группу ресурсов с помощью командлета [New-AzResourceGroup][new-azresourcegroup].

В следующем примере создается группа ресурсов с именем **myResourceGroup** в регионе **eastus**.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

В следующем примере выходных данных показано, что группа ресурсов успешно создана:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Создание кластера AKS

Создайте пару ключей SSH с помощью служебной программы командной строки `ssh-keygen`. Дополнительные сведения см. в статье [Краткая инструкция. Создание и использование пары из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

Для создания кластера AKS используйте командлет [New-AzAks][new-azaks]. В следующем примере создается кластер **myAKSCluster** с одним узлом. Служба Azure Monitor для контейнеров также включена по умолчанию. Выполнение занимает несколько минут.

> [!NOTE]
> При создании кластера AKS для хранения ресурсов AKS автоматически создается вторая группа ресурсов. Дополнительные сведения см. в разделе [Почему с AKS создаются две группы ресурсов?](https://docs.microsoft.com/azure/aks/faq#why-are-two-resource-groups-created-with-aks)

```azurepowershell-interactive
New-AzAks -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
```

Через несколько минут выполнение команды завершается и отображаются сведения о кластере.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

Управлять кластером Kubernetes можно c помощью [kubectl][kubectl], клиента командной строки Kubernetes. Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Чтобы установить `kubectl` локально, используйте командлет `Install-AzAksKubectl`.

```azurepowershell
Install-AzAksKubectl
```

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, используйте командлет [Import-AzAksCredential][import-azakscredential]. В следующем примере выполняется скачивание учетных данных и настройка интерфейса командной строки Kubernetes для их использования.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Чтобы проверить подключение к кластеру, используйте команду [kubectl get][kubectl-get] для получения списка узлов кластера.

```azurepowershell-interactive
.\kubectl get nodes
```

В следующем примере показан единый узел, созданный на предыдущих шагах. Убедитесь, что узел находится в состоянии **Ready** (Готово):

```plaintext
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
```

## <a name="run-the-application"></a>Выполнение приложения

Файл манифеста Kubernetes определяет требуемое состояние для кластера, включая образы контейнеров, которые нужно запустить. В этом кратком руководстве манифест используется для создания всех объектов, необходимых для запуска приложения Azure для голосования. Этот манифест включает в себя два [развертывания Kubernetes][kubernetes-deployment]. Одно используется для примера приложений Azure для голосования на Python, а другое — для экземпляра Redis. Создаются две службы Kubernetes, внутренняя и внешняя. Внутренняя служба используется для экземпляра Redis, а внешняя — для доступа к приложению Azure для голосования через Интернет.

> [!TIP]
> В этом кратком руководстве вы вручную создадите и развернете манифесты приложений в кластере AKS.
> В сценариях, более приближенных к реальному использованию, можно использовать [Azure Dev Spaces][azure-dev-spaces] для быстрого итерационного выполнения кода и его отладки непосредственно в кластере AKS. Служба Dev Spaces позволяет использовать различные платформы ОС и среды разработки и работать совместно с другими членами команды.

Создайте файл `azure-vote.yaml` и скопируйте в него следующее определение YAML. Если вы используете Azure Cloud Shell, этот файл можно создать с помощью `vi` или `nano`, как при работе в виртуальной или физической системе:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Разверните приложение с помощью команды [kubectl apply][kubectl-apply] и укажите имя манифеста YAML:

```azurepowershell-interactive
.\kubectl apply -f azure-vote.yaml
```

В следующем примере выходных данных показано, что развертывания и службы успешно созданы.

```plaintext
deployment.apps/azure-vote-back created
service/azure-vote-back created
deployment.apps/azure-vote-front created
service/azure-vote-front created
```

## <a name="test-the-application"></a>Тестирование приложения

При запуске приложения Служба Kubernetes предоставляет внешний интерфейс приложения в Интернете.
Процесс создания может занять несколько минут.

Чтобы отслеживать ход выполнения, используйте команду [kubectl get service][kubectl-get] с аргументом `--watch`.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

Изначально для параметра **EXTERNAL-IP** (Внешний IP-адрес) службы **azure-vote-front** отображается состояние **pending** (ожидание).

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Когда значение **EXTERNAL-IP** изменится с состояния **pending** на фактический общедоступный IP-адрес, используйте команду `CTRL-C`, чтобы остановить процесс отслеживания `kubectl`. В следующем примере выходных данных показан общедоступный IP-адрес, присвоенный службе.

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Чтобы увидеть приложение для голосования Azure в действии, откройте в веб-браузере внешний IP-адрес вашей службы.

![Приложение для голосования, развернутое в Службе Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

После создания кластера AKS для сбора метрик работоспособности узлов кластера и модулей pod включается служба [Azure Monitor для контейнеров](../azure-monitor/insights/container-insights-overview.md). Эти метрики работоспособности доступны на портале Azure.

## <a name="delete-the-cluster"></a>Удаление кластера

Чтобы избежать расходов за использование Azure, необходимо удалить ненужные ресурсы. Чтобы удалить ненужные кластер, группу ресурсов, службу контейнеров и все связанные с ней ресурсы, используйте командлет [Remove-AzResourceGroup][remove-azresourcegroup].

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Когда вы удаляете кластер, субъект-служба Azure Active Directory, используемый в кластере AKS, не удаляется. Инструкции по удалению субъекта-службы см. в разделе с [дополнительными замечаниями][sp-delete]. Управляемые удостоверения администрируются платформой, и их не нужно удалять.

## <a name="get-the-code"></a>Получение кода

В этом кратком руководстве для создания развертывания Kubernetes используются предварительно созданные образы контейнеров. Связанный с приложением код, файл Dockerfile и файл манифеста Kubernetes доступны на сайте GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Дальнейшие действия

С помощью этого краткого руководства мы развернули кластер Kubernetes, а затем развернули в нем многоконтейнерное приложение. Кроме того, вы можете [получить доступ к веб-панели мониторинга Kubernetes][kubernetes-dashboard] для своего кластера AKS.

Дополнительные сведения о AKS и инструкции по созданию полного кода для примера развертывания см. в руководстве по кластерам Kubernetes.

> [!div class="nextstepaction"]
> [Руководство по AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
