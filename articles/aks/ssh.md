---
title: Вход через SSH в узлы кластера Службы Azure Kubernetes (AKS)
description: Узнайте, как создать SSL-подключение к узлам кластера Службы Azure Kubernetes (AKS) для задач устранения неполадок и обслуживания.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/05/2019
ms.author: iainfou
ms.openlocfilehash: 680e087e80d3e9891e201e7cb474ccfcf7fcc70b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072634"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Подключение по протоколу SSH к узлам кластера Службы Azure Kubernetes (AKS) для обслуживания или устранения неполадок

На протяжении жизненного цикла кластера Службы Azure Kubernetes (AKS) вам может потребоваться доступ к узлу AKS. Этот доступ используется для обслуживания, сбора журналов или других операций по устранению неполадок. Узлы AKS — это виртуальные машины Linux, поэтому к ним можно получить доступ с помощью протокола SSH. В целях безопасности узлы Службы Azure Kubernetes (AKS) недоступны через Интернет.

В этой статье показано, как создать SSH-подключение к узлу AKS с использованием его частного IP-адреса.

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

Вам также понадобится Azure CLI версии 2.0.59 или более поздней версии установлен и настроен. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Добавление открытого ключа SSH

По умолчанию ключи SSH формируются при создании кластера AKS. Если вы не указали ключи SSH при создании кластера AKS, добавьте открытие ключи SSH в узлы AKS.

Чтобы добавить ключ SSH в узел AKS, выполните следующие действия:

1. Получите имя группы ресурсов для ресурсов кластера AKS с помощью команды [az aks show][az-aks-show]. Укажите собственную основную группу ресурсов и имя кластера AKS:

    ```azurecli-interactive
    az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
    ```

1. Перечислите виртуальные машины в группе ресурсов кластера AKS с помощью команды [az vm list][az-vm-list]. Эти виртуальные машины и являются узлами AKS:

    ```azurecli-interactive
    az vm list --resource-group MC_myResourceGroup_myAKSCluster_eastus -o table
    ```

    В следующем примере выходных данных показаны узлы AKS:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Чтобы добавить ключи SSH к узлу, используйте команду [az vm user update][az-vm-user-update]. Укажите имя группы ресурсов, а затем один из узлов AKS, полученных на предыдущем шаге. По умолчанию имя пользователя для узлов AKS — *azureuser*. Укажите расположение собственного открытого ключа SSH, например *~/.ssh/id_rsa.pub*, или вставьте содержимое открытого ключа SSH:

    ```azurecli-interactive
    az vm user update \
      --resource-group MC_myResourceGroup_myAKSCluster_eastus \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="get-the-aks-node-address"></a>Получение адреса узла AKS

Узлы AKS не являются общедоступными через Интернет. Чтобы подключиться к узлам AKS по протоколу SSH, используйте частный IP-адрес. На следующем шаге создается вспомогательный pod в кластере AKS, которая позволяет SSH это частный IP-адрес узла.

Просмотреть частный IP-адрес узла кластера AKS можно с помощью команды [az vm list-ip-addresses][ az-vm-list-ip-addresses]. Укажите имя своей группы ресурсов кластера AKS, полученное на предыдущем шаге [az-aks-show][az-aks-show]:

```azurecli-interactive
az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table
```

В следующем примере выходных данных показаны частные IP-адреса узлов AKS:

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

1. Базовый образ Debian не включает компоненты SSH. После подключения сеанса терминала к контейнеру установите SSH-клиент, используя `apt-get` следующим образом:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. В новом окне терминала, не подключенном к контейнеру, перечислите модули pod в кластере AKS, используя команду [kubectl get pods][kubectl-get]. Модуль pod, созданный на предыдущем шаге, начинается с имени *aks-ssh*, как показано в следующем примере:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. На первом шаге этой статьи вы добавили открытый ключ SSH в узел AKS. Теперь скопируйте закрытый ключ SSH в модуль pod. Этот закрытый ключ используется для установки подключения SSH к узлам AKS.

    Предоставьте имя своего модуля pod *aks-ssh*, полученное на предыдущем шаге. При необходимости измените *~/.ssh/id_rsa* на расположение закрытого ключа SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Вернитесь в сеанс терминала с контейнером, обновите разрешения для скопированного закрытого ключа SSH `id_rsa`, чтобы он был доступен только для чтения:

    ```console
    chmod 0600 id_rsa
    ```

1. Теперь создайте SSH-подключение к вашему узлу AKS. И снова по умолчанию имя пользователя для узлов AKS — *azureuser*. Примите приглашение продолжить подключение, так как ключ SSH является изначально доверенным. Затем предоставляется строка bash вашего узла AKS:

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
