---
title: Объем файлов Маунт-Лазурные для контейнерной группы
description: Узнайте, как подключить том файлов Azure для сохранения состояния с помощью Экземпляров контейнеров Azure
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f66890c503de8de9160f11fb28795012ae57daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561343"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Подключение общего файлового ресурса Azure с помощью службы "Экземпляры контейнеров Azure"

По умолчанию в Экземплярах контейнеров Azure не отслеживается состояние. Если происходит сбой в контейнере или он завершает работу, все сведения о состоянии будут утеряны. Чтобы сохранить состояние после истечения времени существования контейнера, необходимо подключить том из внешнего хранилища. Как показано в этой статье, экземпляры контейнеров Azure могут смонтировать совместное изображение файлов Azure, созданное с помощью [файлов Azure.](../storage/files/storage-files-introduction.md) Azure Files предлагает полностью управляемые файлные акции, размещенные в Azure Storage, которые доступны через стандартный блок сообщений сервера (SMB). Использование файлового ресурса Azure с Экземплярами контейнеров Azure предоставляет функции обмена файлами, похожие на использование файлового ресурса Azure совместно с виртуальными машинами Azure.

> [!NOTE]
> Подключение общего ресурса службы файлов Azure сейчас поддерживается только для контейнеров Linux. Найдите текущие различия платформы в [обзоре.](container-instances-overview.md#linux-and-windows-containers)
>
> Установка доли файлов Azure в экземпляр контейнера похожа на [крепление](https://docs.docker.com/storage/bind-mounts/)Docker. Имейте в виду, что если вы устанавливаете долю в каталог контейнеров, в котором существуют файлы или каталоги, эти файлы или каталоги скрыты креплением и недоступны во время работы контейнера.
>

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
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Получение учетных данных хранилища

Для подключения файлового ресурса Azure в качестве тома в Экземплярах контейнеров Azure требуется три значения: имя учетной записи хранения, имя общего ресурса и ключ доступа к хранилищу.

* **Имя учетной записи хранилища** - Если вы использовали предыдущий скрипт, имя учетной записи хранилища было сохранено в переменной. `$ACI_PERS_STORAGE_ACCOUNT_NAME` Для отображения имени учетной записи введите:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Имя общего обмена** - Это значение `acishare` уже известно (определяется как в предыдущем скрипте)

* **Ключ учетной записи хранения** - Это значение можно найти с помощью следующей команды:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Развертывание контейнера и объем крепления - CLI

Чтобы установить долю файла Azure в качестве объема в контейнере с помощью Azure CLI, укажите точку крепления доли и громкости при создании контейнера с [помощью контейнера az.][az-container-create] Если вы выполнили предыдущие шаги, можно смонтировать созданную ранее акцию, используя следующую команду для создания контейнера:

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

Значение `--dns-name-label` должно быть уникальным в регионе Azure, где создается экземпляр контейнера. Если при выполнении команды появится сообщение об ошибке **Метка DNS-имени**, обновите значение в предыдущей команде.

## <a name="manage-files-in-mounted-volume"></a>Управление файлами в подключенном томе

После запуска контейнера можно использовать простое веб-приложение, развернутое через изображение [Aci-hellofiles][aci-hellofiles] Майкрософт, для создания небольших текстовых файлов в файле Azure на указанном вами пути крепления. Получите полное доменное имя (FQDN) веб-приложения с помощью команды [az container show][az-container-show].

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

После сохранения текста с помощью приложения можно использовать [портал Azure][portal] или такой инструмент, как [Microsoft Azure Storage Explorer,][storage-explorer] для извлечения и проверки файла или файлов, написанных на общий файл.

## <a name="deploy-container-and-mount-volume---yaml"></a>Развертывание контейнера и объем крепления - YAML

Можно также развернуть группу контейнеров и смонтировать громкость в контейнере с Azure CLI и [шаблоном YAML.](container-instances-multi-container-yaml.md) Развертывание шаблоном YAML является предпочтительным методом при развертывании контейнерных групп, состоящих из нескольких контейнеров.

Следующий шаблон YAML определяет группу контейнеров `aci-hellofiles` с одним контейнером, созданным с изображением. Контейнер устанавливает *ацишер* обмена файлами Azure, созданный ранее в виде объема. Если указано, введите имя и ключ хранения для учетной записи хранилища, в которой размещаются общие файлы. 

Как и в примере `dnsNameLabel` CLI, значение должно быть уникальным в регионе Azure, где создается экземпляр контейнера. При необходимости обновите значение в файле YAML.

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

Для развертывания с помощью шаблона YAML сохраните предыдущий YAML в файл с именем `deploy-aci.yaml`, а затем выполните команду [az container create][az-container-create] с параметром `--file`:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Развертывание контейнера и объема крепления - Менеджер ресурсов

В дополнение к развертыванию CLI и YAML можно развернуть группу контейнеров и смонтировать объем в контейнере с помощью [шаблона Управления ресурсами](/azure/templates/microsoft.containerinstance/containergroups)Azure.

Сначала заполните массив `volumes` в разделе `properties` группы контейнеров шаблона. 

Затем для каждого контейнера, в котором вы хотите смонтировать объем, заселите `volumeMounts` массив в `properties` разделе определения контейнера.

Следующий шаблон менеджера ресурсов определяет группу контейнеров `aci-hellofiles` с одним контейнером, созданным с изображением. Контейнер устанавливает *ацишер* обмена файлами Azure, созданный ранее в виде объема. Если указано, введите имя и ключ хранения для учетной записи хранилища, в которой размещаются общие файлы. 

Как и в предыдущих примерах, `dnsNameLabel` значение должно быть уникальным в регионе Azure, где создается экземпляр контейнера. При необходимости обновите значение в шаблоне.

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

Для развертывания с помощью шаблона Resource Manager сохраните предыдущий JSON в файл с именем `deploy-aci.json`, а затем выполните команду [az group deployment create][az-group-deployment-create] с параметром `--template-file`:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Подключение нескольких томов

Чтобы установить несколько томов в экземпляре контейнера, необходимо развернуть с помощью [шаблона менеджера ресурсов Azure,](/azure/templates/microsoft.containerinstance/containergroups)файла YAML или другого программного метода. Чтобы использовать шаблон или файл YAML, предоставьте сведения о общих `volumes` сведениях `properties` и определите объемы, заселив массив в разделе файла. 

Например, если вы создали две акции Azure Files, названные *share1* и *share2* в учетной записи хранения *myStorageAccount,* `volumes` массив в шаблоне Resource Manager будет отображаться на следующем:

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

## <a name="next-steps"></a>Дальнейшие действия

Сведения о подключении других типов томов в службе "Экземпляры контейнеров Azure" см. в следующих статьях:

* [Подключение тома emptyDir в службе "Экземпляры контейнеров Azure"](container-instances-volume-emptydir.md)
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