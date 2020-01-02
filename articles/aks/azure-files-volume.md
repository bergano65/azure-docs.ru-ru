---
title: Создание тома "Статический" для нескольких групп контейнеров в Службе Azure Kubernetes (AKS)
description: Сведения о том, как вручную создавать том с файлами Azure для использования с несколькими параллельными pod в Службе Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 7b5f7c25cd1627475d8e37a539956f01ae6151ab
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74914018"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Создание вручную и совместное использование тома с файловым ресурсом Azure в службе Azure Kubernetes (AKS)

Контейнерные приложения часто требуются для обращения к данным и их хранения во внешнем томе данных. Если нескольким модулям Pod требуется одновременный доступ к тому же тому хранилища, можно использовать службу файлов Azure для подключения с помощью [протокола SMB][smb-overview]. В этой статье показано, как вручную создать файловый ресурс Azure и присоединить его к контейнеру в AKS.

Дополнительные сведения о томах Kubernetes см. [в статье параметры хранения для приложений в AKS][concepts-storage].

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, ознакомьтесь с кратким руководством по AKS, [используя Azure CLI][aks-quickstart-cli] или [с помощью портал Azure][aks-quickstart-portal].

Также требуется Azure CLI версии 2.0.59 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

## <a name="create-an-azure-file-share"></a>создать файловый ресурс Azure;

Перед использованием файлов Azure в качестве тома Kubernetes необходимо создать учетную запись хранения Azure и файловый ресурс. Следующие команды создают группу ресурсов с именем *мяксшаре*, учетную запись хранения и общую папку с именем *аксшаре*:

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Запишите имя учетной записи хранилища и ключ, указанные в конце выходных данных сценария. Эти значения нужны для создания тома Kubernetes в одном из следующих действий.

## <a name="create-a-kubernetes-secret"></a>Создание секрета Kubernetes

Kubernetes требуются учетные данные для доступа к файловому ресурсу, созданные на предыдущем шаге. Эти учетные данные хранятся в [Kubernetes секрете][kubernetes-secret], на который указывает ссылка при создании Kubernetes Pod.

Создайте секрет с помощью команды `kubectl create secret`. Следующий пример создает ресурс с именем *azure-secret* и заполняет параметры *azurestorageaccountname* и *azurestorageaccountkey* данными, созданными на предыдущем шаге. Чтобы использовать существующую учетную запись Azure, укажите имя и ключ учетной записи.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Подключение файлового ресурса в качестве тома

Чтобы подключить общую папку службы файлов Azure к модулю, настройте том в спецификации контейнера. Создайте новый файл с именем `azure-files-pod.yaml` со следующим содержимым. Если вы изменили имя файлового ресурса или секрета, обновите *shareName* и *secretName*. При необходимости обновите `mountPath` — путь, в котором файловый ресурс установлен в pod. Для контейнеров Windows Server (в настоящее время на этапе предварительной версии в AKS) укажите *mountPath* с помощью соглашения о пути Windows, например *"d:"* .

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

```console
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
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
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

## <a name="mount-options"></a>Параметры подключения

Значение по умолчанию для *fileMode* и *дирмоде* — *0755* для Kubernetes версии 1.9.1 и выше. При использовании кластера с Куберетес версии 1.8.5 или более поздней и статическим созданием объекта постоянного тома необходимо указать параметры подключения для объекта *персистентволуме* . В следующем примере задается значение *0777*.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

При использовании кластера версии 1.8.0–1.8.4 контекст безопасности можно указать, задав для *runAsUser* значение *0*. Дополнительные сведения о контексте безопасности Pod см. в разделе [Настройка контекста безопасности][kubernetes-security-context].

Чтобы обновить параметры подключения, создайте файл *азурефиле-Mount-Options-PV. YAML* с *персистентволуме*. Пример.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Создайте файл *азурефиле-Mount-Options-PVC. YAML* с *Персистентволумеклаим* , использующим *персистентволуме*. Пример.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Используйте команды `kubectl`, чтобы создать *персистентволуме* и *персистентволумеклаим*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Убедитесь, что *персистентволумеклаим* создан и привязан к *персистентволуме*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Обновите спецификацию контейнера, чтобы она ссылалась на *персистентволумеклаим* и обновить свой модуль. Пример.

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Дальнейшие действия

Соответствующие рекомендации см. в разделе рекомендации [по хранению и резервному копированию в AKS][operator-best-practices-storage].

Дополнительные сведения о взаимодействии кластеров AKS с файлами Azure см. в разделе [подключаемый модуль Kubernetes для файлов Azure][kubernetes-files].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
