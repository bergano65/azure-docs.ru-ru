---
title: Вход через SSH в узлы кластера Службы Azure Kubernetes (AKS)
description: Узнайте, как создать SSL-подключение к узлам кластера Службы Azure Kubernetes (AKS) для задач устранения неполадок и обслуживания.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/31/2019
ms.author: mlearned
ms.openlocfilehash: 748abc08c432518be4ce8698713b1df95077c3c1
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722464"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Подключение по протоколу SSH к узлам кластера Службы Azure Kubernetes (AKS) для обслуживания или устранения неполадок

На протяжении жизненного цикла кластера Службы Azure Kubernetes (AKS) вам может потребоваться доступ к узлу AKS. Этот доступ используется для обслуживания, сбора журналов или других операций по устранению неполадок. Доступ к узлам AKS можно получить с помощью SSH, включая узлы Windows Server (в настоящее время это предварительная версия в AKS). Вы также можете [подключаться к узлам Windows Server с помощью подключений по протоколу удаленного рабочего стола (RDP)][aks-windows-rdp]. В целях безопасности узлы AKS не доступны в Интернете. Чтобы подключиться к узлам AKS по протоколу SSH, используйте частный IP-адрес.

В этой статье показано, как создать SSH-подключение к узлу AKS с использованием его частного IP-адреса.

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, ознакомьтесь с кратким руководством по AKS, [используя Azure CLI][aks-quickstart-cli] или [с помощью портал Azure][aks-quickstart-portal].

По умолчанию ключи SSH получаются или создаются, а затем добавляются в узлы при создании кластера AKS. В этой статье показано, как указать разные ключи SSH, отличные от ключей SSH, используемых при создании кластера AKS. В этой статье также показано, как определить частный IP-адрес узла и подключиться к нему с помощью SSH. Если не нужно указывать другой ключ SSH, вы можете пропустить шаг добавления открытого ключа SSH к узлу.

В этой статье также предполагается, что у вас есть ключ SSH. Ключ SSH можно создать с помощью [macOS или Linux][ssh-nix] или [Windows][ssh-windows]. Если для создания пары ключей используется выводимое значение Gen, сохраните пару ключей в формате OpenSSH, а не в формате закрытого ключа по умолчанию (PPK-файл).

Также требуется Azure CLI версии 2.0.64 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Настройка кластеров AKS на основе масштабируемых наборов виртуальных машин для доступа по протоколу SSH

Чтобы настроить на основе масштабируемого набора виртуальных машин для доступа по протоколу SSH, найдите имя масштабируемого набора виртуальных машин кластера и добавьте открытый ключ SSH в этот масштабируемый набор.

Используйте команду [AZ AKS показывать][az-aks-show] , чтобы получить имя группы ресурсов кластера AKS, а затем команду [AZ vmss List][az-vmss-list] , чтобы получить имя масштабируемого набора.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

В приведенном выше примере имя группы ресурсов кластера для *myAKSCluster* в *MyResourceGroup* присваивается *CLUSTER_RESOURCE_GROUP*. Затем в примере используется *CLUSTER_RESOURCE_GROUP* для перечисления имени масштабируемого набора и его присвоения *SCALE_SET_NAME*.  

> [!NOTE]
> В настоящее время ключи SSH можно добавлять только на узлы Linux с помощью Azure CLI. Если вы хотите подключиться к узлу Windows Server с помощью SSH, используйте ключи SSH, предоставленные при создании кластера AKS, и пропустите следующий набор команд для добавления открытого ключа SSH. Вам по-прежнему потребуется IP-адрес узла, для которого необходимо устранить неполадки, который показан в последней команде этого раздела. Кроме того, вы можете [подключиться к узлам Windows Server, используя подключения по протоколу RDP][aks-windows-rdp] , а не использовать SSH.

Чтобы добавить ключи SSH к узлам в масштабируемом наборе виртуальных машин, используйте команды [AZ vmss Extension Set][az-vmss-extension-set] и [AZ vmss Update-Instances][az-vmss-update-instances] .

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

В приведенном выше примере используются переменные *CLUSTER_RESOURCE_GROUP* и *SCALE_SET_NAME* из предыдущих команд. В приведенном выше примере в качестве расположения для открытого ключа SSH также используется значение *~/.ssh/id_rsa.pub* .

> [!NOTE]
> По умолчанию имя пользователя для узлов AKS — *azureuser*.

После добавления открытого ключа SSH в масштабируемый набор можно выполнить подключение SSH к виртуальной машине узла в этом масштабируемом наборе, используя его IP-адрес. Просмотрите частные IP-адреса узлов кластера AKS с помощью [команды kubectl Get][kubectl-get].

```console
kubectl get nodes -o wide
```

В выходных данных примера ниже показаны внутренние IP-адреса всех узлов в кластере, включая узел Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Запишите внутренний IP-адрес узла, который вы хотите устранить.

Чтобы получить доступ к узлу по протоколу SSH, выполните действия, описанные в разделе [Создание SSH-подключения](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Настройка кластеров AKS на основе наборов доступности виртуальных машин для доступа по протоколу SSH

Чтобы настроить кластер AKS на основе набора доступности виртуальных машин для доступа по протоколу SSH, найдите имя узла Linux кластера и добавьте открытый ключ SSH в этот узел.

Используйте команду [AZ AKS показывать][az-aks-show] , чтобы получить имя группы ресурсов кластера AKS, а затем команду [AZ VM List][az-vm-list] , чтобы вывести имя виртуальной машины узла Linux в кластере.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

В приведенном выше примере имя группы ресурсов кластера для *myAKSCluster* в *MyResourceGroup* присваивается *CLUSTER_RESOURCE_GROUP*. Затем в примере используется *CLUSTER_RESOURCE_GROUP* для перечисления имени виртуальной машины. В примере выходных данных показано имя виртуальной машины: 

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Чтобы добавить ключи SSH на узел, используйте команду [AZ VM User Update][az-vm-user-update] .

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

В приведенном выше примере используется переменная *CLUSTER_RESOURCE_GROUP* и имя виртуальной машины узла из предыдущих команд. В приведенном выше примере в качестве расположения для открытого ключа SSH также используется значение *~/.ssh/id_rsa.pub* . Вместо указания пути можно также использовать содержимое открытого ключа SSH.

> [!NOTE]
> По умолчанию имя пользователя для узлов AKS — *azureuser*.

После добавления открытого ключа SSH к виртуальной машине узла можно выполнить подключение SSH к виртуальной машине по ее IP-адресу. Просмотрите частный IP-адрес узла кластера AKS с помощью команды [AZ VM List-IP-Addresses][az-vm-list-ip-addresses] .

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

В приведенном выше примере используется переменная *CLUSTER_RESOURCE_GROUP* , заданная в предыдущих командах. В следующем примере выходных данных показаны частные IP-адреса узлов AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Создание SSH-подключения

Для создания SSH-подключения к узлу AKS запустите вспомогательный модуль pod в кластере AKS. Этот модуль предоставляет доступ по протоколу SSH к кластеру, а затем дополнительный доступ к узлам SSH. Чтобы создать и использовать этот вспомогательный модуль pod, выполните следующие действия:

1. Запустите образ контейнера `debian` и свяжите с ним сеанс терминала. Этот контейнер можно использовать для создания сеанса SSH с любым узлом в кластере AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Если вы используете узлы Windows Server (в настоящее время в предварительной версии в AKS), добавьте селектор узла в команду, чтобы запланировать контейнер Debian на узле Linux:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. После подключения терминального сеанса к контейнеру установите SSH-клиент с помощью `apt-get`:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Откройте новое окно терминала, не подключенное к контейнеру, и выведите список модулей Pod в кластере AKS с помощью команды [kubectl Get][kubectl-get] Pod. Модуль pod, созданный на предыдущем шаге, начинается с имени *aks-ssh*, как показано в следующем примере:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. На предыдущем шаге вы добавили открытый ключ SSH к узлу AKS, который вы хотите устранить. Теперь скопируйте закрытый ключ SSH в модуль поддержки. Этот закрытый ключ используется для создания SSH в узле AKS.

    Предоставьте имя своего модуля pod *aks-ssh*, полученное на предыдущем шаге. При необходимости измените *~/.ssh/id_rsa* на расположение закрытого ключа SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Вернитесь в сеанс терминала к контейнеру, обновите разрешения для скопированного `id_rsa` закрытого ключа SSH так, чтобы он был доступен только для чтения.

    ```console
    chmod 0600 id_rsa
    ```

1. Создайте SSH-подключение к узлу AKS. И снова по умолчанию имя пользователя для узлов AKS — *azureuser*. Примите приглашение продолжить подключение, так как ключ SSH является изначально доверенным. Затем предоставляется строка bash вашего узла AKS:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Отключение доступа по протоколу SSH

Закончив, `exit` из сеанса SSH, а затем `exit` из интерактивного сеанса контейнера. Когда сеанса с этим контейнером закроется, модуль pod, используемый для доступа по протоколу SSH, будет удален из кластера AKS.

## <a name="next-steps"></a>Следующие шаги

Если требуются дополнительные данные по устранению неполадок, можно [Просмотреть журналы kubelet][view-kubelet-logs] или [Просмотреть журналы главного узла Kubernetes][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
