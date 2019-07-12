---
title: Подключение тома службы файлов Azure в службе "Экземпляры контейнеров Azure"
description: Узнайте, как подключить том файлов Azure для сохранения состояния с помощью Экземпляров контейнеров Azure
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bc09aa500743d608c0a3a7a379fe9584c9c55e9b
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657617"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Подключение общего файлового ресурса Azure с помощью службы "Экземпляры контейнеров Azure"

По умолчанию в Экземплярах контейнеров Azure не отслеживается состояние. Если происходит сбой в контейнере или он завершает работу, все сведения о состоянии будут утеряны. Чтобы сохранить состояние после истечения времени существования контейнера, необходимо подключить том из внешнего хранилища. В этой статье показано, как подключить файловый ресурс Azure, созданный с помощью [файлов Azure](../storage/files/storage-files-introduction.md), для использования с Экземплярами контейнеров Azure. Служба файлов Azure предоставляет полностью управляемые общие файловые ресурсы в облаке, доступ к которым можно получить с помощью стандартного отраслевого протокола SMB. Использование файлового ресурса Azure с Экземплярами контейнеров Azure предоставляет функции обмена файлами, похожие на использование файлового ресурса Azure совместно с виртуальными машинами Azure.

> [!NOTE]
> Подключение общего ресурса службы файлов Azure сейчас поддерживается только для контейнеров Linux. Пока мы работаем, чтобы обеспечить все функции для контейнеров Windows, можно найти текущей платформы есть отличия в [Обзор](container-instances-overview.md#linux-and-windows-containers).

## <a name="create-an-azure-file-share"></a>создать файловый ресурс Azure;

Перед использованием файлового ресурса Azure с Экземплярами контейнеров Azure необходимо создать его. Выполните приведенный ниже скрипт, чтобы создать учетную запись хранения для размещения файлового ресурса и общего ресурса. Имя учетной записи хранения должно быть глобально уникальным, поэтому скрипт добавляет случайное значение к базовой строке.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Получение учетных данных хранилища

Для подключения файлового ресурса Azure в качестве тома в Экземплярах контейнеров Azure требуется три значения: имя учетной записи хранения, имя общего ресурса и ключ доступа к хранилищу.

Если вы использовали сценарий выше, имя учетной записи хранения было сохранено в переменной ACI_PERS_STORAGE_ACCOUNT_NAME. Для отображения имени учетной записи введите:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Имя общего ресурса уже известно (определено как *acishare* в сценарии выше), так что остается только ключ учетной записи хранения, который можно найти с помощью следующей команды:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Развертывание контейнера и подключение тома - CLI

Чтобы подключить файловый ресурс Azure в качестве тома в контейнере с помощью Azure CLI, укажите общего ресурса и тома точку подключения при создании контейнера с [создать контейнер az][az-container-create]. Если вы выполнили предыдущие инструкции, можно подключить созданный ранее общий ресурс с помощью следующей команды для создания контейнера:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

`--dns-name-label` Значение должно быть уникальным в пределах региона Azure, где создается экземпляр контейнера. Если при выполнении команды появится сообщение об ошибке **Метка DNS-имени**, обновите значение в предыдущей команде.

## <a name="manage-files-in-mounted-volume"></a>Управление файлами в подключенном томе

После запуска контейнера, можно использовать простое веб-приложение, развернутое с помощью Microsoft [aci-hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show] команды:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

После сохранения текста, с помощью приложения, можно использовать [портала Azure][portal] or a tool like the [Microsoft Azure Storage Explorer][storage-explorer] для извлечения и проверить файл, записанный в файловый ресурс.

## <a name="deploy-container-and-mount-volume---yaml"></a>Развертывание контейнера и подключение тома - YAML

Также можно развернуть группу контейнеров и подключение тома в контейнере с помощью Azure CLI и [шаблона YAML](container-instances-multi-container-yaml.md). При развертывании группы контейнеров, состоящей из нескольких контейнеров, рекомендуется использовать шаблон YAML.

Следующий шаблон YAML определяет группу контейнеров с помощью одного контейнера, созданных с помощью `aci-hellofiles` изображения. Контейнер монтирует общую папку Azure *acishare* созданную ранее в качестве тома. Там, где указано, введите имя и ключ учетной записи хранения, на котором размещается файловый ресурс. 

Как показано в примере CLI `dnsNameLabel` значение должно быть уникальным в пределах региона Azure, где создается экземпляр контейнера. Обновите значение в файле YAML, при необходимости.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Для развертывания с помощью шаблона yaml-ФАЙЛ, сохраните выше yaml-ФАЙЛ в файл с именем `deploy-aci.yaml`, затем выполните [создать контейнер az][az-container-create] с `--file` параметр:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Развертывание контейнера и подключение тома — Resource Manager

Помимо развертывания интерфейс командной строки и YAML, вы можете развернуть группу контейнеров и подключить том в контейнер с помощью Azure [шаблона Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Сначала заполните массив `volumes` в разделе `properties` группы контейнеров шаблона. 

Затем для каждого контейнера, в котором вы хотите подключить, заполнить `volumeMounts` массива в `properties` разделе определения контейнера.

Следующий шаблон Resource Manager определяет группу контейнеров с помощью одного контейнера, созданных с помощью `aci-hellofiles` изображения. Контейнер монтирует общую папку Azure *acishare* созданную ранее в качестве тома. Там, где указано, введите имя и ключ учетной записи хранения, на котором размещается файловый ресурс. 

Как и в предыдущих примерах `dnsNameLabel` значение должно быть уникальным в пределах региона Azure, где создается экземпляр контейнера. Обновите значение в шаблоне, при необходимости.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Чтобы развернуть с помощью шаблона Resource Manager, сохраните вышеупомянутого JSON-файла в файл с именем `deploy-aci.json`, затем выполните [Создание развертывания группы az][az-group-deployment-create] с `--template-file` параметр:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Подключение нескольких томов

Чтобы подключить несколько томов в экземпляре контейнера, необходимо выполнить развертывание с помощью [шаблона Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) или yaml-файла. Чтобы использовать шаблон или файл YAML, предоставляют сведения о файловом ресурсе и определите тома, заполнив `volumes` массива в `properties` раздел шаблона. 

Например, если вы создали два файловых ресурсах Azure с именем *share1* и *share2* в учетной записи хранения *myStorageAccount*, `volumes` массива в Resource Manager шаблон будет выглядеть следующим образом:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Затем для каждого контейнера в группе контейнеров, в которой нужно подключить тома, заполните массив `volumeMounts` в разделе `properties` определения контейнера. В результате этого, подключатся, например, тома *myvolume1* и *myvolume2*, определенные ранее.

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Следующие шаги

Сведения о подключении других типов томов в службе "Экземпляры контейнеров Azure" см. в следующих статьях:

* [Mount an emptyDir volume in Azure Container Instances](container-instances-volume-emptydir.md) (Подключение тома emptyDir в службе "Экземпляры контейнеров Azure")
* [Подключение тома gitRepo в службе "Экземпляры контейнеров Azure"](container-instances-volume-gitrepo.md)
* [Подключение тома secret в службе "Экземпляры контейнеров Azure"](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create