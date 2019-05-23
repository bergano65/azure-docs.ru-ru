---
title: Создание и публикация управляемого приложения каталога услуг Azure | Документация Майкрософт
description: Показано, как создать управляемое приложение Azure, предназначенное для членов вашей организации.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: dc86943924cd0c47c465e9d3bac4ca91b73a3ff5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171556"
---
# <a name="create-and-publish-a-managed-application-definition"></a>Создание и публикация определения управляемого приложения

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Вы можете создавать и публиковать [управляемые приложения Azure](overview.md), предназначенные для членов вашей организации. Например, отдел ИТ может публиковать управляемые приложения, которые соответствуют стандартам организации. Эти управляемые приложения доступны в каталоге служб, а не в Azure Marketplace.

Чтобы опубликовать управляемое приложение для каталога услуг, необходимо выполнить следующее.

* Создайте шаблон, который определяет ресурсы, развертываемые с управляемым приложением.
* Определите элементы пользовательского интерфейса для портала при развертывании управляемого приложения.
* Создайте ZIP-файл пакета, содержащий необходимые файлы шаблонов.
* Определите, каким пользователям, группам или приложениям нужен доступ к группе ресурсов в подписке пользователя.
* Создайте определение управляемого приложения, которое указывает на ZIP-файл пакета и запрашивает доступ для получения удостоверения.

Используемое в этой статье управляемое приложение имеет только учетную запись хранения. В этом руководстве мы покажем, как опубликовать управляемое приложение. Полные примеры приведены в статье [Примеры проектов для управляемых приложений Azure](sample-projects.md).

Для работы примеров PowerShell в этой статье требуется Azure PowerShell 6.2 или более поздняя версия. [Обновите свою версию](/powershell/azure/install-Az-ps), если необходимо.

## <a name="create-the-resource-template"></a>Создание шаблона ресурсов

В каждом определении управляемого приложения указан файл с именем **mainTemplate.json**. В нем определяются ресурсы Azure, которые необходимо развернуть. Этот шаблон ничем не отличается от обычного шаблона Resource Manager.

Создайте файл с именем **mainTemplate.json**. В имени следует учитывать регистр.

Добавьте в файл приведенный ниже код JSON. В нем определяются параметры для создания учетной записи хранения и задаются свойства для нее.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Сохраните файл mainTemplate.json.

## <a name="create-the-user-interface-definition"></a>Создание определения пользовательского интерфейса

На основе файла **createUiDefinition.json** портал Azure создает пользовательский интерфейс для пользователей, создающих управляемое приложение. Можно определить способ ввода каждого параметра пользователями. например раскрывающийся список, текстовое поле, поле ввода пароля или другие средства ввода. Чтобы узнать, как создать файл определения пользовательского интерфейса для управляемого приложения, изучите статью [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).

Создайте файл с именем **createUiDefinition.json**. В имени следует учитывать регистр.

Добавьте в файл следующий код JSON.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Сохраните файл createUiDefinition.json.

## <a name="package-the-files"></a>Упаковка файлов

Добавьте два файла в ZIP-файл с именем app.zip. Приведенные выше два файла должны быть расположены на корневом уровне ZIP-файла. Если поместить их в папку, то при создании определения управляемого приложения появится сообщение об ошибке из-за отсутствия необходимых файлов. 

Передайте пакет в доступное расположение, из которого его можно будет использовать. 

```powershell
New-AzResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Создание определения управляемого приложения

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Создание группы пользователей или приложения Azure Active Directory

Следующий шаг — выбор группы пользователей или приложения для управления ресурсами от имени клиента. Этой группе ресурсов или приложению предоставляются разрешения в управляемой группе ресурсов в соответствии с назначенной ролью. Это может быть любая встроенная роль управления доступом на основе ролей (RBAC), например "Владелец" или "Участник". Можно также предоставить разрешение на управление ресурсами отдельному пользователю, но обычно это разрешение назначается группе пользователей. Чтобы создать группу пользователей Active Directory, ознакомьтесь с разделом [Создание группы и добавление в нее пользователей в Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Требуется идентификатор объекта группы пользователей для управления ресурсами. 

```powershell
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Получение идентификатора определения роли

Далее необходимо получить идентификатор определения встроенной роли RBAC, к которой вы хотите предоставить доступ пользователю, группе пользователей или приложению. Обычно это роль "Владелец", "Участник" или "Читатель". Следующая команда позволяет получить идентификатор определения роли "Владелец".

```powershell
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Создание определения управляемого приложения

Если у вас еще нет группы ресурсов для хранения определения управляемого приложения, создайте ее.

```powershell
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Теперь создайте ресурс определения управляемого приложения.

```powershell
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

### <a name="make-sure-users-can-see-your-definition"></a>Убедитесь, что пользователи могут видеть ваше определение.

У вас есть доступ к определению управляемого приложения, но вы хотите, чтобы другие пользователи в вашей организации могли получить к нему доступ. Предоставьте им роль читателя в определении. Они могут унаследовать этот уровень доступа из группы подписки или ресурса. Чтобы узнать, как проверить, кто имеет доступ к определению, и добавить пользователей или группы, см. статью [Использование управления доступом на основе ролей для доступа к ресурсам подписки Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Дополнительная информация

* Сведения о том, как опубликовать управляемое приложение в Azure Marketplace, см. в статье [Управляемые приложения Azure в Marketplace](publish-marketplace-app.md).
* Сведения о том, как развернуть экземпляр управляемого приложения, см. в статье [Deploy service catalog app through Azure portal](deploy-service-catalog-quickstart.md) (Развертывание приложения из каталога служб с помощью портала Azure).
