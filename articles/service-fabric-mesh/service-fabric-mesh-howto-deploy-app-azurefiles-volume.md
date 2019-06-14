---
title: Использование тома службы файлов Azure в приложении "Сетка Service Fabric" | Документация Майкрософт
description: Узнайте, как сохранить состояние в приложении "Сетка Azure Service Fabric" путем подключения тома службы файлов Azure в службе с помощью Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: fa078f17768d4885403f2f3e3d6b91251f0aaced
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60419379"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Подключение тома службы файлов Azure в приложении "Сетка Service Fabric" 

В этой статье описано подключение тома службы файлов Azure в службе приложения "Сетка Service Fabric".  Драйвер тома службы файлов Azure — это драйвер тома Docker, используемый для подключения общей папки службы файлов Azure в контейнер, используемый для сохранения состояния службы. Тома предоставляют хранилище файлов общего назначения файлов и дают возможность чтения и записи файлов с помощью стандартных файловых API-интерфейсов дискового ввода-вывода.  Дополнительные сведения о томах и параметрах для хранения данных приложения см. в статье о [состоянии хранения](service-fabric-mesh-storing-state.md).

Чтобы подключить том в службе, создайте ресурс тома в приложении "Сетка Service Fabric" и затем укажите этот том в вашей службе.  Объявить ресурс тома и указать на него в ресурсе службы можно в [файлах ресурсов на основе YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) или [шаблоне развертывания на основе JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Прежде чем подключить тома, сначала необходимо создать учетную запись хранения и [файловый ресурс в службе файлов Azure](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Технические условия

Для выполнения задач этой статьи можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. 

Чтобы использовать Azure CLI локально в рамках работы с этой статьей, убедитесь, что `az --version` возвращает по крайней мере версию `azure-cli (2.0.43)`.  Установите (или обновите) модуль расширения интерфейса командной строки службы "Сетка Azure Service Fabric", выполнив эти [инструкции](service-fabric-mesh-howto-setup-cli.md).

Вход в Azure и настройка подписки:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Создание общего файлового ресурса и учетной записи (необязательно)
Для подключения тома службы файлов Azure требуется учетная запись хранения и общий файловый ресурс.  Вы можете использовать имеющуюся учетную запись хранения и файловый ресурс Azure или создать ресурсы:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Получение имени учетной записи хранения, ключа и имени файлового ресурса
В следующих разделах имя учетной записи хранения, ключ учетной записи хранения и имя файлового ресурса обозначены как `<storageAccountName>`, `<storageAccountKey>` и `<fileShareName>` соответственно. 

Выведите список учетных записей хранения и получите имя учетной записи хранения с файловым ресурсом, которые вы хотите использовать:
```azurecli-interactive
az storage account list
```

Получите имя файлового ресурса:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Получите ключ учетной записи хранения ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Эти значения также можно найти на [портале Azure](https://portal.azure.com):
* `<storageAccountName>` (в разделе **Учетные записи хранения**) — это имя учетной записи хранения, используемой для создания файлового ресурса.
* `<storageAccountKey>` Выберите учетную запись хранения в разделе **Учетные записи хранения**, а затем выберите **Ключи доступа** и используйте значение в разделе **key1**.
* `<fileShareName>` Выберите учетную запись хранения в разделе **Учетные записи хранения**, а затем выберите **Файлы**. Нужно использовать имя созданного файлового ресурса.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Объявление ресурса тома и обновление ресурса службы (JSON)

Добавьте параметры для значений `<fileShareName>`, `<storageAccountName>` и `<storageAccountKey>`, обнаруженных на предыдущем шаге. 

Создайте ресурс тома как узел ресурса приложения. Укажите имя и поставщика (SFAzureFile для использования тома службы файлов Azure). В `azureFileParameters` укажите параметры для значений `<fileShareName>`, `<storageAccountName>` и `<storageAccountKey>`, обнаруженных на предыдущем шаге.

Чтобы подключить том в службе, добавьте `volumeRefs` в элемент `codePackages` службы.  `name` — идентификатор ресурса для тома (или параметр шаблона развертывания для создаваемого ресурса тома) и имя тома, объявленного в файле ресурсов volume.yaml.  `destinationPath` — это локальный каталог, который будет подключен к тому.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Объявление ресурса тома и обновление ресурса службы (YAML)

Добавьте новый файл *volume.yaml* в каталог *ресурсов приложения* для приложения.  Укажите имя и поставщика (SFAzureFile для использования тома службы файлов Azure). `<fileShareName>`, `<storageAccountName>`, и `<storageAccountKey>` — это значения, обнаруженные на предыдущем шаге.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Обновите файл *service.yaml* в каталоге *ресурсов службы* для подключения тома в службе.  Добавьте элемент `volumeRefs` в элемент `codePackages`.  `name` — идентификатор ресурса для тома (или параметр шаблона развертывания для создаваемого ресурса тома) и имя тома, объявленного в файле ресурсов volume.yaml.  `destinationPath` — это локальный каталог, который будет подключен к тому.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с примером приложения, использующего том службы файлов Azure, на сайте [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Узнайте больше о модели ресурсов Service Fabric из раздела [Introduction to Service Fabric Resource Model](service-fabric-mesh-service-fabric-resources.md) (Общие сведения о модели ресурсов Service Fabric).
- Узнайте больше о службе "Сетка Service Fabric" из раздела [Что такое Сетка Service Fabric?](service-fabric-mesh-overview.md)
