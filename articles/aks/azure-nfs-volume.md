---
title: Создание сервера Ubuntu NFS (Network File System) для использования модулей Azure Kubernetes Service (AKS)
description: Узнайте, как вручную создать том NFS-сервер Ubuntu Linux для использования с POD, содержащихся в службе Azure Kubernetes (AKS)
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65468504"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Вручную создать и использовать на том сервере Linux NFS (Network File System) со службой Azure Kubernetes (AKS)
Обмен данными между контейнерами чаще необходимые компоненты контейнерных служб и приложений. У вас обычно есть различные модули, которым требуется доступ к той же информации на внешнего постоянного тома.    
Хотя служба файлов Azure — это вариант, создание NFS-сервер на виртуальной Машине Azure — это еще один постоянный общего хранилища. 

В этой статье показано, как создать NFS-сервер на виртуальной машине Ubuntu. А также предоставить доступ контейнеры AKS к этой общей файловой системы.

## <a name="before-you-begin"></a>Перед началом работы
В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, см. в этом кратком руководстве AKS [с помощью Azure CLI] [ aks-quickstart-cli] или [с помощью портала Azure][aks-quickstart-portal].

Кластера AKS должны находиться в том же или пиринговых виртуальных сетей как NFS-сервер. Кластер должны создаваться в существующей виртуальной сети, который может быть той же виртуальной сети виртуальной Машины.

В документации описаны действия по настройке с существующей виртуальной сети: [создание кластера AKS в имеющейся виртуальной сети] [ aks-virtual-network] и [для подключения виртуальных сетей с помощью пиринга виртуальной сети][peer-virtual-networks]

Также предполагается, что вы создали виртуальной машины Ubuntu Linux (например, 18.04 LTS). Параметры и размер может быть по выбору и можно развернуть с помощью Azure. Linux с помощью портала, см. в разделе [управление виртуальными Машинами linux][linux-create].

Если вы развертываете кластер AKS, Azure будут заполнены автоматически поле виртуальную сеть при развертывании вашей машины Ubuntu, что делает их реальном времени в пределах той же виртуальной сети. Но если вы хотите работать с одноранговыми сетями, вместо этого см. в документации выше.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Развертывание сервера NFS на виртуальную машину
Ниже приведен скрипт для настройки NFS-сервер в виртуальной машине Ubuntu.
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
Сервер будет перезагружен (из-за сценарий), и вы можете подключить NFS-сервер для AKS

>[!IMPORTANT]  
>Не забудьте заменить **AKS_SUBNET** с ее из кластера или else «*» будет открыт NFS-сервер для всех подключений и порты.

После создания виртуальной Машины, скопируйте выше сценария в файл. Затем его можно переместить с локального компьютера или везде, где он, в виртуальной Машине с помощью: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Когда сценарий на виртуальной Машине, можно ssh в виртуальную Машину и выполнить его с помощью команды:
```console
sudo ./nfs-server-setup.sh
```
Если его выполнение завершается сбоем из-за ошибки отказа в доступе, набор разрешений на выполнение с помощью команды:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Подключение кластера AKS для NFS-сервера
Мы подключения NFS-сервер наш кластер, подготовив постоянного тома и утверждение постоянного тома, которая определяет доступ к тому.  
Подключение двух служб, в том же или пиринговых виртуальных сетях не требуется. Инструкции по настройке кластера в той же виртуальной сети можно здесь: [создание кластера AKS в имеющейся виртуальной сети][aks-virtual-network]

Когда они находятся в той же виртуальной сети (или пиринговой связи), необходимо выделяйте утверждение постоянного тома и постоянного тома в кластере AKS. Контейнеры можно подключить диск NFS для их локального каталога.

Ниже приведен пример определения kubernetes, для постоянного тома (это определение предполагается, что кластер и виртуальной Машины в той же виртуальной сети).

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Замените **NFS_INTERNAL_IP**, **NFS_NAME** и **NFS_EXPORT_FILE_PATH** данными NFS-сервер.

Вам также потребуется файл утверждение постоянного тома. Ниже приведен пример того, что для включения:

>[!IMPORTANT]  
>**«storageClassName»** необходимо оставить пустой строку или утверждение не будет работать.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Устранение неполадок
Если не удается подключиться к серверу из кластера, проблема может быть экспортированного каталога или его родительского, не имеет достаточных прав для доступа к серверу.

Убедитесь, что как в каталоге экспорта, так и в родительский каталог предоставлены разрешения 777.

Вы можете проверить разрешения, выполнив следующую команду и каталоги должны иметь *«drwxrwxrwx»* разрешения:
```console
ls -l
```

## <a name="more-information"></a>Дополнительные сведения
Чтобы получить полное Пошаговое руководство или помочь в отладке настройки NFS-сервер, вот подробное руководство по:
  - [Учебник NFS][nfs-tutorial]

## <a name="next-steps"></a>Дальнейшие действия

Связанные практические рекомендации, см. в разделе [советы и рекомендации для хранилища и резервных копий в AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
