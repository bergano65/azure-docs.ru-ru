---
title: Создание тома "Статический" для нескольких групп контейнеров в Службе Azure Kubernetes (AKS)
description: Сведения о том, как вручную создавать том с файлами Azure для использования с несколькими параллельными pod в Службе Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 1a8609dbf5fa1c1e7d5f4e35b081ecaa09994eb6
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068083"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Создание вручную и совместное использование тома с файловым ресурсом Azure в службе Azure Kubernetes (AKS)

Контейнерные приложения часто требуются для обращения к данным и их хранения во внешнем томе данных. Если несколько модулей pod требуют одновременный доступ в одно и то же хранилище, используйте службу файлов Azure для подключения с помощью [протокола Server Message Block (SMB)][smb-overview]. В этой статье показано, как вручную создать файловый ресурс Azure и присоединить его к контейнеру в AKS.

Дополнительные сведения о томах Kubernetes см. в разделе [Volumes][kubernetes-volumes] (Тома).

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

Кроме того, нужно установить и настроить Azure CLI 2.0.46 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="create-an-azure-file-share"></a>создать файловый ресурс Azure;

Перед использованием файлов Azure в качестве тома Kubernetes необходимо создать учетную запись хранения Azure и файловый ресурс. Следующий скрипт создает группу ресурсов с именем *myAKSShare*, учетную запись хранения и файловый ресурс с именем *aksshare*:

```azurecli
#!/bin/bash

# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Запишите имя учетной записи хранилища и ключ, указанные в конце выходных данных сценария. Эти значения нужны для создания тома Kubernetes в одном из следующих действий.

## <a name="create-a-kubernetes-secret"></a>Создание секрета Kubernetes

Kubernetes требуются учетные данные для доступа к файловому ресурсу, созданные на предыдущем шаге. Эти учетные данные хранятся в [секрете Kubernetes][kubernetes-secret], который указывается при создании pod Kubernetes.

Создайте секрет с помощью команды `kubectl create secret`. Следующий пример создает ресурс с именем *azure-secret* и заполняет параметры *azurestorageaccountname* и *azurestorageaccountkey* данными, созданными на предыдущем шаге. Чтобы использовать существующую учетную запись Azure, укажите имя и ключ учетной записи.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Подключение файлового ресурса в качестве тома

Чтобы подключить файловый ресурс Azure к pod, настройте том в спецификации контейнера. Создайте файл `azure-files-pod.yaml` со следующим содержимым. Если вы изменили имя файлового ресурса или секрета, обновите *shareName* и *secretName*. При необходимости обновите `mountPath` — путь, в котором файловый ресурс установлен в pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Создайте pod с помощью команды `kubectl`.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

Теперь у вас есть запущенная группа pod с подключенным файловым ресурсом Azure в папке */mnt/azure*. Можно использовать `kubectl describe pod mypod` для проверки успешного подключения ресурсов. Ниже приведен краткий пример выходных данных, где показано, что том подключен к контейнеру:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Mon, 08 Oct 2018 19:28:34 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о взаимодействии кластеров AKS с файлами Azure, см. в разделе [Подключаемый модуль Kubernetes для службы файлов Azure][kubernetes-files].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
