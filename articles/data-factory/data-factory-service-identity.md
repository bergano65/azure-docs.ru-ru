---
title: Управляемое удостоверение для фабрики данных | Документация Майкрософт
description: Дополнительные сведения о управляемое удостоверение для фабрики данных Azure.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 3c1bb38eb12ce77d172257706cd458cebda4bd8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66153417"
---
# <a name="managed-identity-for-data-factory"></a>Управляемое удостоверение для Фабрики данных

Эта статья поможет вам понять, что такое управляемое удостоверение для фабрики данных (ранее известный как управляемого удостоверения службы или MSI) и принципах ее работы.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Обзор

При создании фабрики данных, наряду с созданием фабрики можно создать управляемое удостоверение. Управляемое удостоверение — это управляемое приложение, зарегистрированное в Azure Activity Directory и представляет этой фабрики данных.

Управляемое удостоверение для фабрики данных преимущества следующие функции:

- [Store учетных данных в хранилище ключей Azure](store-credentials-in-key-vault.md), в этом случае удостоверение управляемый фабрики данных используется для проверки подлинности Azure Key Vault.
- Соединители, включая [хранилище BLOB-объектов Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage 1-го поколения](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md), [Базу данных SQL Azure](connector-azure-sql-database.md) и [Хранилище данных SQL Azure](connector-azure-sql-data-warehouse.md).
- [Веб-действия](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Создание управляемого удостоверения

Управляемое удостоверение для фабрики данных формируется следующим образом:

- При создании фабрики данных с помощью **портала Azure или PowerShell**управляемого удостоверения всегда будет создаваться автоматически.
- При создании фабрики данных с помощью **SDK**управляемого удостоверение будет создаваться только в том случае, если указать «Identity = new FactoryIdentity()» в объекте фабрики для создания. Пример см. в инструкциях по [созданию фабрики данных из краткого руководства по .NET](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- При создании фабрики данных с помощью **REST API**управляемого удостоверение будет создаваться только в том случае, если указать «identity» раздел в тексте запроса. Пример см. в инструкциях по [созданию фабрики данных из краткого руководства по REST](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Если обнаружится фабрикой данных не имеет управляемое удостоверение связанного следующие [получить управляемое удостоверение](#retrieve-managed-identity) инструкции, вы можете явным образом создать его, обновив фабрику данных с помощью инициатора удостоверений программными средствами:

- [Создание управляемого удостоверения, с помощью PowerShell](#generate-managed-identity-using-powershell)
- [Создание управляемого удостоверения, с помощью REST API](#generate-managed-identity-using-rest-api)
- [Создание управляемого удостоверения, с помощью шаблона Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Создание управляемого удостоверения, с помощью пакета SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Управляемое удостоверение не может изменяться. Обновление фабрики данных, в которой уже есть управляемое удостоверение не оказывает никакого влияния, управляемым удостоверением останется таким же.
>- Если обновить фабрику данных, в которой уже есть управляемое удостоверение без указания параметра «identity» в объекте фабрики либо не указав раздел «identity» в тексте запроса REST, вы получите ошибку.
>- При удалении фабрики данных, связанного управляемого удостоверения будут удалены вместе.

### <a name="generate-managed-identity-using-powershell"></a>Создание управляемого удостоверения, с помощью PowerShell

Вызовите **AzDataFactoryV2 набора** команду еще раз, вы увидите, что созданы поля «Identity»:

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

### <a name="generate-managed-identity-using-rest-api"></a>Создание управляемого удостоверения, с помощью REST API

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

**Ответ**: управляемое удостоверение создается автоматически, и раздел «identity» заполняется соответствующим образом.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Создание управляемого удостоверения, с помощью шаблона Azure Resource Manager

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

### <a name="generate-managed-identity-using-sdk"></a>Создание управляемого удостоверения, с помощью пакета SDK

Вызовите функцию create_or_update фабрики данных с Identity=new FactoryIdentity(). Пример кода с использованием .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Получение управляемого удостоверения

Вы можете получить управляемое удостоверение с портала Azure или программным способом. В разделах ниже приведено несколько примеров.

>[!TIP]
> Если вы не видите управляемого удостоверения, [создать управляемое удостоверение](#generate-managed-identity) , обновив фабрику.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Получение управляемого удостоверения, с помощью портала Azure

Вы найдете сведения управляемого удостоверения на портале Azure -> фабрики данных "->" Свойства:

- Идентификатор объекта управляемого удостоверения
- Клиент управляемого удостоверения
- **Управляемый идентификатор приложения удостоверения** > скопируйте это значение

![Получение управляемого удостоверения](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>Получение управляемого удостоверения, с помощью PowerShell

Управляемое удостоверение, идентификатор субъекта и идентификатор клиента возвращаются при получении определенной фабрики данных следующим образом:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Скопируйте идентификатор субъекта, а затем выполните указанную ниже команду Azure Active Directory с идентификатором субъекта в качестве параметра, чтобы получить **идентификатор приложения**, используемый для предоставления доступа:

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Дальнейшие действия
См. следующие разделы, которые вводят, когда и как использовать фабрику данных управляемого удостоверения:

- [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md)
- [Копирование данных в Azure Data Lake Storage Gen1 и из него с помощью фабрики данных Azure](connector-azure-data-lake-store.md)

См. в разделе [удостоверений, управляемых Azure ресурсы Обзор](/azure/active-directory/managed-identities-azure-resources/overview) для основан на дополнительные сведения об управляемых удостоверений для ресурсов Azure, какая фабрика данных управляемого удостоверения. 
