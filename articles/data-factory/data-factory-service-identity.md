---
title: Управляемое удостоверение для Фабрики данных
description: Узнайте об управляемой идентификации для фабрики данных Azure.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: d47450f3252074d3bae8df97766bf8858fca5972
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416584"
---
# <a name="managed-identity-for-data-factory"></a>Управляемое удостоверение для Фабрики данных

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Эта статья поможет вам понять, что является управляемым удостоверением личности для Data Factory (ранее известной как Managed Service Identity/MSI) и как она работает.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Обзор

При создании фабрики данных можно создавать управляемую идентификацию наряду с созданием фабрики. Управляемое удостоверение — это управляемое приложение, зарегистрированное в Azure Active Directory, и представляет эту конкретную фабрику данных.

Управляемая идентификация для Data Factory приносит пользу следующим функциям:

- [Храните учетные данные в Azure Key Vault](store-credentials-in-key-vault.md), в этом случае управляемые итог фабрики данных используются для проверки подлинности Azure Key Vault.
- Соединители, включая [хранилище BLOB-объектов Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage 1-го поколения](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md), [Базу данных SQL Azure](connector-azure-sql-database.md) и [Хранилище данных SQL Azure](connector-azure-sql-data-warehouse.md).
- [Веб-активность](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Создание управляемой идентификации

Управляемая идентификация для Data Factory генерируется следующим образом:

- При создании фабрики данных через **портал Azure или PowerShell**управляемый итог всегда будет создаваться автоматически.
- При создании фабрики данных через **SDK**управляемый итог будет создан только в том случае, если вы укажете "Identity - new FactoryIdentity()" в заводском объекте для создания. Пример см. в инструкциях по [созданию фабрики данных из краткого руководства по .NET](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- При создании фабрики данных через **REST API**управляемое удостоверение будет создаваться только в том случае, если в органе запроса указан раздел "идентификация". Пример см. в инструкциях по [созданию фабрики данных из краткого руководства по REST](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Если вы обнаружите, что фабрика данных не имеет управляемой идентификации, связанной после [получения управляемой](#retrieve-managed-identity) инструкции идентификации, вы можете явно создать ее, обновив фабрику данных с помощью инициатора идентификации программно:

- [Создание управляемой идентификации с помощью PowerShell](#generate-managed-identity-using-powershell)
- [Создание управляемого удостоверения с помощью REST API](#generate-managed-identity-using-rest-api)
- [Создание управляемого удостоверения с помощью шаблона управления ресурсами Azure](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Создание управляемого удостоверения с помощью SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Управляемая идентификация не может быть изменена. Обновление фабрики данных, которая уже имеет управляемую идентификацию, не будет иметь никакого влияния, управляемая идентификация остается неизменной.
>- Если вы обновите фабрику данных, у которой уже есть управляемое удостоверение, не указывая параметр "идентификация" на объекте завода или не указывая раздел "идентификация" в органе запроса REST, вы получите ошибку.
>- При удалении фабрики данных связанная управляемая идентификация будет удалена вместе.

### <a name="generate-managed-identity-using-powershell"></a>Создание управляемой идентификации с помощью PowerShell

Вызов **set-AzDataFactoryV2** снова, то вы видите "Идентификация" поля вновь генерируется:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Создание управляемого удостоверения с помощью REST API

Вызовите API с разделом identity в тексте запроса, как в примере ниже.

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Текст запроса**: add "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Ответ:** управляемый идентификатор создается автоматически, и раздел "идентификация" заполняется соответствующим образом.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Создание управляемого удостоверения с помощью шаблона управления ресурсами Azure

**Шаблон:** add "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Создание управляемого удостоверения с помощью SDK

Вызовите функцию create_or_update фабрики данных с Identity=new FactoryIdentity(). Пример кода с использованием .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Retrieve управляемой идентификации

Вы можете получить управляемую идентификацию с портала Azure или программно. В разделах ниже приведено несколько примеров.

>[!TIP]
> Если вы не видите управляемую идентификацию, [создайте управляемую идентификацию,](#generate-managed-identity) обновив фабрику.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Извлечение управляемого удостоверения с помощью портала Azure

Управенные данные идентификационных данных можно найти на портале Azure - > фабрике данных - > Свойства.

- Идентификатор управляемого объекта идентификации
- Управляемый арендатор идентификации
- Идентификатор управляемого удостоверения личности

Управляемая идентификационная информация также будет отображаться при создании связанной службы, которая поддерживает управляемую проверку подлинности идентификации, например Azure Blob, Хранилище озер данных Azure, Убежище ключей Azure и т.д.

При предоставлении разрешения используйте идентификатор объекта или имя фабрики данных (в качестве управляемого имени идентификации) для поиска этой идентификации.

### <a name="retrieve-managed-identity-using-powershell"></a>Извлечение управляемой идентификации с помощью PowerShell

Идентификатор управляемого идентификатора идентификации и идентификатор клиента будут возвращены, когда вы получите определенную фабрику данных следующим образом. Используйте **PrincipalId** для предоставления доступа:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Идентификатор приложения можно получить, копируя выше основного идентификатора, а затем запустив ниже команды Active Directory Azure с основным идентификатором в качестве параметра.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими темами, которые представляют, когда и как использовать управляемые удостоверения данных фабрики данных:

- [Храните учетные данные в Хранилище ключей Azure](store-credentials-in-key-vault.md)
- [Копирование данных в Azure Data Lake Storage Gen1 и из него с помощью фабрики данных Azure](connector-azure-data-lake-store.md)

Просматривайте [управляемые идентификаторы для обзора ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview) для получения дополнительной информации об управляемых идентификаторах для ресурсов Azure, на которых основана управляемая идентификаторами фабрики данных. 
