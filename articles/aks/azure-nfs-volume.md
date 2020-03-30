---
title: Создание сервера NFS (Network File System) Ubuntu Server для использования стручками службы Azure Kubernetes (AKS)
description: Узнайте, как вручную создать объем NFS Ubuntu Linux Server для использования с помощью стручков в службе Azure Kubernetes (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: e5676710bc47557318f3e2adcf36ec0ed13d47de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596629"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Вручную создавайте и используйте объем Linux Server NFS (Network File System) с помощью сервиса Azure Kubernetes (AKS)
Обмен данными между контейнерами часто является необходимым компонентом контейнерных служб и приложений. Обычно у вас есть различные стручки, которые нуждаются в доступе к одной и той же информации на внешнем постоянном томе.    
Хотя файлы Azure являются опцией, создание Сервера NFS на VM Azure является еще одной формой постоянного общего хранения. 

В этой статье будет показан способ создания NFS Server на виртуальной машине Ubuntu. А также предоставить вашим контейнерам AKS доступ к этой общей файловой системе.

## <a name="before-you-begin"></a>Перед началом
Эта статья предполагает, что у вас есть существующий кластер AKS. Если вам нужен кластер AKS, см. быстрый запуск AKS [с помощью Azure CLI][aks-quickstart-cli] или [с помощью портала Azure.][aks-quickstart-portal]

Кластер AKS должен жить в тех же или заглянунных виртуальных сетях, что и NFS Server. Кластер должен быть создан в существующем VNET, который может быть таким же VNET, как ваш VM.

Шаги для настройки с существующим VNET описаны в документации: [создание кластера AKS в существующих VNET][aks-virtual-network] и [подключение виртуальных сетей с пирингом VNET][peer-virtual-networks]

Он также предполагает, что вы создали Ubuntu Linux Виртуальная машина (например, 18,04 LTS). Настройки и размер могут быть по своему вкусу и могут быть развернуты через Azure. Для быстрого запуска Linux, см [Linux VM управления][linux-create].

Если сначала развернуть кластер AKS, Azure автоматически заполнит виртуальное сетевое поле при развертывании машины Ubuntu, заставляя их жить в пределах одного и того же VNET. Но если вы хотите работать с заглянул сетей вместо этого, проконсультируйтесь с документацией выше.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Развертывание сервера NFS на виртуальную машину
Вот скрипт для настройки NFS Server в вашей виртуальной машине Ubuntu:
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
Сервер будет перезапустить (из-за скрипта), и вы можете смонтировать NFS Server на AKS.

>[!IMPORTANT]  
>Убедитесь в том, чтобы заменить **AKS_SUBNET** с правильным из вашего кластера, иначе "Я" откроет ваш NFS Server для всех портов и соединений.

После создания VM скопируйте приведенный выше сценарий в файл. Затем вы можете переместить его из локальной машины, или где бы ни был скрипт, в VM с помощью: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
После того, как ваш скрипт находится в вашем VM, вы можете ssh в VM и выполнить его через команду:
```console
sudo ./nfs-server-setup.sh
```
Если его выполнение завершается неудачей из-за ошибки, которую было отказано в разрешении, установите разрешение на выполнение через команду:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Подключение кластера AKS к серверу NFS
Мы можем подключить NFS Server к нашему кластеру, подготавлив стойкий объем и постоянный объем претензии, которые определяют, как получить доступ к объему.

Необходимо подключение двух служб в одни и те же или заглянунные виртуальные сети. Инструкции по настройке кластера в том же VNET здесь: [Создание кластера AKS в существующих VNET][aks-virtual-network]

Как только они находятся в той же виртуальной сети (или заглянул), необходимо обеспечить постоянный объем и постоянный объем претензии в вашем кластере AKS. Контейнеры могут затем смонтировать диск NFS в их местный каталог.

Вот пример kubernetes определение для постоянного объема (Это определение предполагает, что ваш кластер и VM находятся в том же VNET):

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
Замените **информацию NFS_INTERNAL_IP,** **NFS_NAME** и **NFS_EXPORT_FILE_PATH** информацией о сервере NFS.

Вам также понадобится постоянный файл претензий объема. Вот пример того, что включить:

>[!IMPORTANT]  
>**"хранениеClassName"** должно оставаться пустой строкой или претензия не будет работать.

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
Если вы не можете подключиться к серверу из кластера, проблема может быть экспортируемого каталога, или его родитель, не имеет достаточных разрешений для доступа к серверу.

Убедитесь, что в каталоге экспорта и в его материнском каталоге имеется 777 разрешений.

Вы можете проверить разрешения, запустив команду ниже, а в каталогах должны быть разрешения *'drwxwxrwx':*
```console
ls -l
```

## <a name="more-information"></a>Дополнительные сведения
Чтобы получить полное пошаговый шаг или помочь вам отладить настройки NFS Server, вот углубленный учебник:
  - [Учебник NFS][nfs-tutorial]

## <a name="next-steps"></a>Дальнейшие действия

Для связанных с [Best practices for storage and backups in AKS][operator-best-practices-storage]этим рекомендаций см.

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
