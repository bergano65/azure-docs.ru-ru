---
title: Вход через SSH в узлы кластера Службы Azure Kubernetes (AKS)
description: Узнайте, как создать SSL-подключение к узлам кластера Службы Azure Kubernetes (AKS) для задач устранения неполадок и обслуживания.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: dfdcda40a24142f85bbeb360aacf0971d72d181f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593637"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Подключение по протоколу SSH к узлам кластера Службы Azure Kubernetes (AKS) для обслуживания или устранения неполадок

На протяжении жизненного цикла кластера Службы Azure Kubernetes (AKS) вам может потребоваться доступ к узлу AKS. Этот доступ используется для обслуживания, сбора журналов или других операций по устранению неполадок. Вы можете получить доступ к узлам AKS с помощью SSH, включая узлы Windows Server (в настоящее время в предварительном просмотре в AKS). Вы также можете [подключиться к узлам Windows Server с помощью удаленного соединения протокола рабочего стола (RDP).][aks-windows-rdp] В целях безопасности узлы AKS не подвергаются воздействию Интернета. Чтобы подключиться к узлам AKS по протоколу SSH, используйте частный IP-адрес.

В этой статье показано, как создать SSH-подключение к узлу AKS с использованием его частного IP-адреса.

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

По умолчанию sSH-ключи получаются или генерируются, а затем добавляются в узлы при создании кластера AKS. В этой статье показано, как указать различные клавиши SSH, чем клавиши SSH, используемые при создании кластера AKS. В статье также показано, как определить частный IP-адрес вашего узла и подключиться к нему с помощью SSH. Если вам не нужно указывать другой ключ SSH, то вы можете пропустить шаг для добавления общедоступного ключа SSH в узла.

Эта статья также предполагает, что у вас есть ключ SSH. Вы можете создать ключ SSH с помощью [macOS или Linux][ssh-nix] или [Windows.][ssh-windows] Если вы используете PuTTY Gen для создания пары ключей, сохраните пару ключей в формате OpenSSH, а не формат частного ключа PuTTy (файл.ppk).

Вам также нужна версия Azure CLI 2.0.64 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Настройка кластеров AKS на основе виртуального масштаба машины для доступа к SSH

Чтобы настроить набор для SSH-доступа виртуальной шкалы машин, найдите имя виртуального набора масштабов машин вашего кластера и добавьте ключ SSH к этому набору масштабов.

Используйте команду [шоу az aks,][az-aks-show] чтобы получить название группы ресурсов вашего кластера AKS, а затем команду [списка az vmss,][az-vmss-list] чтобы получить имя набора масштабов.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

Вышеприведенный пример присваивает название группы кластерных ресурсов *для myAKSCluster* в *myResourceGroup,* чтобы *CLUSTER_RESOURCE_GROUP*. Пример затем использует *CLUSTER_RESOURCE_GROUP,* чтобы перечислить имя набора шкалы и назначить его *SCALE_SET_NAME.*

> [!IMPORTANT]
> В настоящее время следует обновлять sSH-клавиши только для кластеров AKS на основе виртуального масштаба машины с помощью Azure CLI.
> 
> Для узлов Linux sSH в настоящее время можно добавлять только с помощью Azure CLI. Если вы хотите подключиться к узлю Windows Server с помощью SSH, используйте клавиши SSH, предоставленные при создании кластера AKS, и пропустите следующий набор команд для добавления общедоступного ключа SSH. Вам по-прежнему понадобится IP-адрес узла, который вы хотите устранить, который отображается в окончательной команде этого раздела. Кроме того, можно [подключиться к узлам Windows Server с помощью удаленного соединения протокола рабочего стола (RDP)][aks-windows-rdp] вместо sSH.

Чтобы добавить клавиши SSH в узлы в виртуальном наборе масштабов машины, используйте [набор расширений az vmss][az-vmss-extension-set] и команды обновления [vmss.][az-vmss-update-instances]

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

В приведенном выше примере используются *CLUSTER_RESOURCE_GROUP* и *SCALE_SET_NAME* переменные предыдущих команд. Вышеупомянутый пример также использует *q/.ssh/id_rsa.pub* как положение для вашего публичного ключа SSH.

> [!NOTE]
> По умолчанию имя пользователя для узлов AKS — *azureuser*.

После добавления общедоступного ключа SSH в набор масштабов можно SSH в виртуальную машину в этом масштабе, используя свой IP-адрес. Просмотр частных IP-адресов кластерных узлов AKS с помощью [команды kubectl.][kubectl-get]

```console
kubectl get nodes -o wide
```

В следующем выводе примера показаны внутренние IP-адреса всех узлов кластера, включая узлы Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Запишите внутренний IP-адрес узла, который вы хотите устранить.

Чтобы получить доступ к узлам с помощью SSH, выполните последующие действия в [«Создайте соединение SSH».](#create-the-ssh-connection)

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Настройка кластеров AKS на основе виртуальной доступности машин для доступа SSH

Чтобы настроить кластер AKS, основанный на доступности виртуальной машины, для доступа к SSH, найдите имя linux-узла вашего кластера и добавьте ключ SSH к этому узлю.

Используйте команду [шоу az aks,][az-aks-show] чтобы получить название группы ресурсов вашего кластера AKS, а затем команду [списка az vm,][az-vm-list] чтобы перечислить виртуальное имя машины вашего кластера Linux узла.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Вышеприведенный пример присваивает название группы кластерных ресурсов *для myAKSCluster* в *myResourceGroup,* чтобы *CLUSTER_RESOURCE_GROUP*. Пример затем использует *CLUSTER_RESOURCE_GROUP,* чтобы перечислить имя виртуальной машины. На примере вывода отображается название виртуальной машины:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Чтобы добавить ключи SSH к узлу, используйте команду [az vm user update][az-vm-user-update].

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

В приведенном выше примере используется *переменная CLUSTER_RESOURCE_GROUP* и название виртуальной машины узла из предыдущих команд. Вышеупомянутый пример также использует *q/.ssh/id_rsa.pub* как положение для вашего публичного ключа SSH. Вы также можете использовать содержимое вашего общедоступного ключа SSH вместо указания пути.

> [!NOTE]
> По умолчанию имя пользователя для узлов AKS — *azureuser*.

После добавления sSH-общедоступного ключа в виртуальную машину узла можно SSH в эту виртуальную машину, используя ее IP-адрес. Просмотреть частный IP-адрес узла кластера AKS можно с помощью команды [az vm list-ip-addresses][az-vm-list-ip-addresses].

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

В приведенном выше примере используется *CLUSTER_RESOURCE_GROUP* переменная, установленная в предыдущих командах. В следующем примере выходных данных показаны частные IP-адреса узлов AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Создание SSH-подключения

Для создания SSH-подключения к узлу AKS запустите вспомогательный модуль pod в кластере AKS. Этот модуль предоставляет доступ по протоколу SSH к кластеру, а затем дополнительный доступ к узлам SSH. Чтобы создать и использовать этот вспомогательный модуль pod, выполните следующие действия:

1. Запустите образ контейнера `debian` и свяжите с ним сеанс терминала. Этот контейнер можно использовать для создания сеанса SSH с любым узлом в кластере AKS:

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Если вы используете узлы Windows Server (в настоящее время в предварительном просмотре в AKS), добавьте селектор узла к команде, чтобы запланировать контейнер Debian на узлу Linux:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Как только сеанс терминала подключен к контейнеру, `apt-get`установите клиент SSH с помощью:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Откройте новое окно терминала, не подключенное к контейнеру, скопируйте свой личный ключ SSH в котел-помощник. Этот закрытый ключ используется для создания SSH в узла AKS. 

   При необходимости измените *~/.ssh/id_rsa* на расположение закрытого ключа SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Вернитесь к сеансу терминала в контейнер, обновите разрешения на скопированный `id_rsa` частный ключ SSH, чтобы он был только для чтения:

    ```console
    chmod 0600 id_rsa
    ```

1. Создайте соединение SSH с вашим узлам AKS. И снова по умолчанию имя пользователя для узлов AKS — *azureuser*. Примите приглашение продолжить подключение, так как ключ SSH является изначально доверенным. Затем предоставляется строка bash вашего узла AKS:

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

## <a name="next-steps"></a>Дальнейшие действия

Если вам нужны дополнительные сведения об устранении неполадок, вы можете [просмотреть журналы kubelet][view-kubelet-logs] или [журналы главного узла Kubernetes][view-master-logs].

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
