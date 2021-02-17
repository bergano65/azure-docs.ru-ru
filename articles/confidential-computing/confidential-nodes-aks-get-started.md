---
title: Краткое руководство. Развертывание кластера Службы Azure Kubernetes (AKS) с узлами конфиденциальных вычислений с помощью Azure CLI
description: Сведения о том, как создать кластер AKS с конфиденциальными узлами и развернуть приложение Hello World с помощью Azure CLI.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/5/2020
ms.author: amgowda
ms.openlocfilehash: b6fe8f4fe34799a71d59b7487d96217b4ac6a429
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833209"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli-preview"></a>Краткое руководство. Развертывание кластера Службы Azure Kubernetes (AKS) с узлами конфиденциальных вычислений (DCsv2) с помощью Azure CLI (предварительная версия)

Это краткое руководство предназначено для разработчиков и операторов кластера, которые хотят быстро создать кластер AKS и развернуть приложение для мониторинга приложений на основе управляемой службы Kubernetes в Azure.

## <a name="overview"></a>Обзор

В этом кратком руководстве описано, как развернуть кластер Службы Azure Kubernetes (AKS) с узлами конфиденциальных вычислений, используя Azure CLI, и запустить в анклаве приложение Hello World. Служба Azure Kubernetes (AKS) — это управляемая служба Kubernetes, которая позволяет быстро развертывать кластеры и управлять ими. Дополнительные сведения об AKS см. [здесь](../aks/intro-kubernetes.md).

> [!NOTE]
> Виртуальные машины DCsv2 для конфиденциальных вычислений работают на основе специализированного оборудования, которое стоит дороже обычного и доступно не во всех регионах. Дополнительные сведения см. на странице, посвященной [доступным ценовым категориям и поддерживаемым регионам](virtual-machine-solutions.md) для виртуальных машин.

> DCsv2 использует виртуальные машины 2-го поколения в Azure. Эта виртуальная машина 2-го поколения является функцией предварительной версии с поддержкой AKS. 

### <a name="deployment-pre-requisites"></a>Предварительные требования для развертывания
В этих инструкциях по развертыванию предполагается следующее.

1. Активная подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
1. Установленное и настроенное на компьютере развертывания решение Azure CLI версии 2.0.64 или более поздней (выполните команду `az --version`, чтобы узнать версию). Если вам необходимо выполнить установку или обновление, ознакомьтесь со статьей [Установка Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
1. [Расширение aks-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) с версией не ниже 0.4.62 
1. Доступность квот на ядра виртуальных машин. Не менее шести доступных ядер **DC<x>s-v2** в вашей подписке. По умолчанию на каждую подписку Azure выделяется квота в 8 ядер виртуальных машин для конфиденциальных вычислений. Если вы планируете подготовить кластер, в котором будет более 8-ми ядер, выполните [эти инструкции](../azure-portal/supportability/per-vm-quota-requests.md), чтобы создать запрос на увеличение квоты.

### <a name="confidential-computing-node-features-dcxs-v2"></a>Возможности узлов конфиденциальных вычислений (DC<x>s-v2)

1. Рабочие узлы Linux с поддержкой только контейнеров Linux.
1. Виртуальная машина 2-го поколения с узлами виртуальных машин на основе Ubuntu 18.04
1. ЦП на основе Intel SGX с поддержкой EPC (кэш в памяти для зашифрованных страниц). Дополнительные сведения см. [здесь](./faq.md).
1. Поддержка Kubernetes версии 1.16+
1. Драйвер Intel SGX DCAP предварительно установлен на узлах AKS. Дополнительные сведения см. [здесь](./faq.md).
1. Поддержка интерфейса командной строки, развернутых во время действия предварительной версии, с помощью подготовки на портале после начала фазы общедоступности.


## <a name="installing-the-cli-pre-requisites"></a>Установка необходимых компонентов для CLI

Чтобы установить расширение aks-preview 0.4.62 или более поздней версии, выполните следующие команды Azure CLI:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
Чтобы обновить расширение aks-preview, выполните следующие команды Azure CLI:

```azurecli-interactive
az extension update --name aks-preview
```
### <a name="generation-2-vms-feature-registration-on-azure"></a>Регистрация компонентов виртуальной машины 2-го поколения в Azure
Регистрация Gen2VMPreview в подписке Azure. Эта функция позволяет подготавливать виртуальные машины 2-го поколения в качестве пулов узлов AKS:

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
Состояние Registered (Зарегистрировано) может появиться через несколько минут. Состояние регистрации можно проверить с помощью команды az feature list: Такая регистрация компонента выполняется только один раз для каждой подписки. Если он был зарегистрирован ранее, можно пропустить описанный выше шаг.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
Если отображается правильный статус, обновите регистрацию поставщика ресурсов Microsoft.ContainerService с помощью команды az provider register:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="azure-confidential-computing-feature-registration-on-azure-optional-but-recommended"></a>Регистрация функций конфиденциальных вычислений Azure в среде Azure (необязательно, но рекомендуется)
Регистрация AKS-ConfidentialComputingAddon в подписке Azure. Эта функция добавит две управляющих программы daemonset, как подробно описано [здесь](./confidential-nodes-aks-overview.md#aks-provided-daemon-sets-addon):
1. Подключаемый модуль драйвера устройства SGX
2. Вспомогательное приложение для подтверждения квоты на аттестацию SGX

```azurecli-interactive
az feature register --name AKS-ConfidentialComputingAddon --namespace Microsoft.ContainerService
```
Состояние Registered (Зарегистрировано) может появиться через несколько минут. Состояние регистрации можно проверить с помощью команды az feature list: Такая регистрация компонента выполняется только один раз для каждой подписки. Если он был зарегистрирован ранее, можно пропустить описанный выше шаг.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputingAddon')].{Name:name,State:properties.state}"
```
Если отображается правильный статус, обновите регистрацию поставщика ресурсов Microsoft.ContainerService с помощью команды az provider register:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="creating-an-aks-cluster"></a>Создание кластера AKS

Если у вас уже есть кластер AKS, который соответствует указанным выше требованиям, [перейдите к разделу для существующего кластера](#existing-cluster), чтобы добавить новый пул с узлами конфиденциальных вычислений.

Сначала создайте группу ресурсов для кластера, выполнив команду az group create. В следующем примере создается группа ресурсов *myResourceGroup* в регионе *westus2*:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Теперь выполните команду az aks create, чтобы создать кластер AKS.

```azurecli-interactive
# Create a new AKS cluster with  system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
В описанном выше примере создается кластер AKS с пулом узлов системы. Теперь добавляйте пользовательский узел типа Confidential Computing Nodepool в AKS (DCsv2)

В приведенном ниже примере добавляется пользовательский пул узлов с 3 узлами размером `Standard_DC2s_v2`. Вы можете выбрать другой поддерживаемый список номеров SKU и регионов DCsv2 [здесь](../virtual-machines/dcv2-series.md):

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2 --aks-custom-headers usegen2vm=true
```
Приведенная выше команда должна добавить новый пул узлов с двумя автоматически запускаемыми управляющими приложениями daemonset **DC<x>s-v2** в этом пуле узлов ([подключаемый модуль устройства SGX](confidential-nodes-aks-overview.md#sgx-plugin) & [вспомогательный модуль квотирования SGX](confidential-nodes-aks-overview.md#sgx-quote)).

Получите учетные данные для кластера AKS с помощью команды az aks get-credentials.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Убедитесь, что узлы успешно созданы, а наборы управляющих программ, имеющие отношение к SGX, выполняются в пулах узлов **DC<x>s-v2**, с помощью команд kubectl get pods и kubectl get nodes, как показано ниже:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Если выходные данные совпадают с примером выше, значит кластер AKS готов к выполнению конфиденциальных приложений.

Перейдите к разделу о [развертывании приложения Hello World из анклава](#hello-world), чтобы протестировать работу приложения в анклаве. Или выполните приведенные ниже инструкции, чтобы добавить в AKS дополнительные пулы узлов (AKS поддерживает сочетание пулов узлов с SGX и без SGX).

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Добавление пула узлов конфиденциальных вычислений в существующий кластер AKS<a id="existing-cluster"></a>

В этом разделе предполагается, что у вас уже есть работающий кластер AKS, который соответствует критериям из списка в разделе предварительных требований.

Сначала нужно добавить эту функцию в подписку Azure.

```azurecli-interactive
az feature register --name AKS-ConfidentialComputingAddon --namespace Microsoft.ContainerService
```
Состояние Registered (Зарегистрировано) может появиться через несколько минут. Состояние регистрации можно проверить с помощью команды az feature list: Такая регистрация компонента выполняется только один раз для каждой подписки. Если он был зарегистрирован ранее, можно пропустить описанный выше шаг.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputingAddon')].{Name:name,State:properties.state}"
```
Если отображается правильный статус, обновите регистрацию поставщика ресурсов Microsoft.ContainerService с помощью команды az provider register:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Далее включите в существующем кластере надстройки, связанные с конфиденциальными вычислениями:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
Теперь добавьте в кластер пул пользовательских узлов **DC<x>s-v2**.
    
> [!NOTE]
> Чтобы использовать возможность конфиденциальных вычислений, ваш существующий кластер AKS должен иметь по меньшей мере один пул узлов на основе SKU виртуальной машины **DC<x>s-v2**. Дополнительные сведения о номерах SKU виртуальных машин DCsv2 для конфиденциальных вычислений см. в [этой статье](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
Выходные данные должны подтвердить добавление confcompool1 в кластер AKS.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Если выходные данные совпадают с примером выше, значит кластер AKS готов к выполнению конфиденциальных приложений.

## <a name="hello-world-from-isolated-enclave-application"></a>Выполнение приложения Hello World из изолированного анклава <a id="hello-world"></a>
Создайте файл с именем *hello-world-enclave.yaml* и вставьте в него приведенный ниже манифест YAML. Этот код примера приложения на основе Open Enclave можно найти в [проекте Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). В приведенном ниже развертывании предполагается, что вы развернули дополнительный компонент confcom.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Теперь выполните команду kubectl apply, чтобы создать пример задания для выполнения в защищенном анклаве, как показано в следующем примере выходных данных:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Вы можете убедиться, что рабочая нагрузка успешно создала доверенную среду выполнения (анклав), выполнив следующие команды:

```console
$ kubectl get jobs -l app=sgx-test
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить кластер AKS или связанные с ним пулы узлов, выполните следующие команды:

Удаление кластера AKS
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Дальнейшие действия

Запустите приложения на Python, Node и т. п. конфиденциальным образом в конфиденциальных контейнерах, используя [эти примеры](https://github.com/Azure-Samples/confidential-container-samples).

Запустите приложения, поддерживающие анклавы, используя [примеры контейнеров Azure с поддержкой анклава](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).
