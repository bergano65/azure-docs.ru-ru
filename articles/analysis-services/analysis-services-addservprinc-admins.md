---
title: Добавление субъекта-службы к роли администратора Azure Analysis Services | Документация Майкрософт
description: Узнайте, как добавить субъект-службу для автоматизации к роли администратора сервера Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: bc3e17ce5becf039703585167e1ef3aa825cf2ab
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697536"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Добавление субъекта-службы к роли администратора сервера 

 Чтобы автоматизировать задачи PowerShell, выполняемые без участия пользователя, субъекту-службе должны быть предоставлены разрешения **администратора сервера** на управляемом сервере Analysis Services. В этой статье описывается, как добавить субъект-службу к роли администратора сервера на сервере Azure AS. Это можно выполнить с помощью SQL Server Management Studio или шаблона Resource Manager.

## <a name="before-you-begin"></a>Перед началом
Прежде чем приступить к этой задаче, нужно зарегистрировать субъект-службу в Azure Active Directory.

[Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../active-directory/develop/howto-create-service-principal-portal.md)   
[Создание субъекта-службы с помощью PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Использование среды SQL Server Management Studio

Вы можете настроить администраторов сервера с помощью SQL Server Management Studio (SSMS). Для выполнения этой задачи вам потребуются разрешения [администратора сервера](analysis-services-server-admins.md) на сервере Azure Analysis Services. 

1. В среде SSMS подключитесь к серверу Azure Analysis Services.
2. Выберите **Свойства сервера** > **Безопасность** и нажмите кнопку **Добавить**.
3. В окне **Выберите пользователя или группу** выполните поиск своего зарегистрированного приложения по имени, выберите его и нажмите кнопку **Добавить**.

    ![Поиск учетной записи субъекта-службы](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Проверьте идентификатор учетной записи субъекта-службы и нажмите кнопку **ОК**.
    
    ![Поиск учетной записи субъекта-службы](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Использование шаблона Resource Manager

Кроме того, администраторов сервера можно настроить, развернув сервер Analysis Services из шаблона Azure Resource Manager. В разделе [Управление доступом на основе ролей](../role-based-access-control/overview.md) в Azure удостоверению, которое используется для развертывания, должна быть присвоена роль **участника** для доступа к ресурсу.

> [!IMPORTANT]
> Субъект-служба добавляется в формате `app:{service-principal-client-id}@{azure-ad-tenant-id}`.

Следующий шаблон Resource Manager развертывает сервер Analysis Services с указанным субъект-службой, который добавлен в роль администратора Analysis Services.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Использование управляемых удостоверений

Управляемое удостоверение также можно добавить в список администраторов Analysis Services. Например, если у вас есть [приложение логики с назначаемым системой управляемым удостоверением](../logic-apps/create-managed-service-identity.md), вы можете предоставить ему возможность администрировать сервер Analysis Services.

В большинстве разделов портала Azure и во многих API управляемые удостоверения определяются по идентификатору объекта для соответствующего субъекта-службы. Однако Analysis Services требует идентифицировать их по идентификатору клиента. Чтобы узнать идентификатор клиента для субъекта-службы, вы можете применить Azure CLI.

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Вы также можете использовать PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Затем вы можете использовать этот идентификатор клиента с идентификатором арендатора, чтобы добавить управляемое удостоверение в список администраторов Analysis Services, как описано выше.

## <a name="related-information"></a>Дополнительные сведения

* [Скачивание модуля PowerShell SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Скачивание SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


