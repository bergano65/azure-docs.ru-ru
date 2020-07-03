---
title: Добавление субъекта-службы в Azure Analysis Services роль администратора | Документация Майкрософт
description: Узнайте, как добавить субъект-службу автоматизации к роли администратора Azure Analysis Services Server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 925fbbb51ac240b96486a2c0aa09c850a8d164bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80408646"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Добавление субъекта-службы к роли администратора сервера 

 Чтобы автоматизировать задачи PowerShell, выполняемые без участия пользователя, субъекту-службе должны быть предоставлены разрешения **администратора сервера** на управляемом сервере Analysis Services. В этой статье описывается, как добавить субъект-службу к роли администратора сервера на сервере Azure AS. Это можно сделать с помощью SQL Server Management Studio или шаблона диспетчер ресурсов.

## <a name="before-you-begin"></a>Подготовка к работе
Прежде чем приступить к этой задаче, нужно зарегистрировать субъект-службу в Azure Active Directory.

[Создание субъекта-службы — портал Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Создание субъекта-службы — PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Использование среды SQL Server Management Studio

Администраторы сервера можно настроить с помощью SQL Server Management Studio (SSMS). Для выполнения этой задачи вам потребуются разрешения [администратора сервера](analysis-services-server-admins.md) на сервере Azure Analysis Services. 

1. В среде SSMS подключитесь к серверу Azure Analysis Services.
2. В окне **Свойства** > **безопасности**сервера нажмите кнопку **Добавить**.
3. В окне **Выберите пользователя или группу** выполните поиск своего зарегистрированного приложения по имени, выберите его и нажмите кнопку **Добавить**.

    ![Поиск учетной записи субъекта-службы](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Проверьте идентификатор учетной записи субъекта-службы и нажмите кнопку **ОК**.
    
    ![Поиск учетной записи субъекта-службы](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Использование шаблона Resource Manager

Также можно настроить администраторов сервера, развернув сервер Analysis Services с помощью шаблона Azure Resource Manager. Удостоверение, выполняющее развертывание, должно принадлежать роли **участника** для ресурса в [контроле доступа на основе ролей (RBAC) Azure](../role-based-access-control/overview.md).

> [!IMPORTANT]
> Субъект-служба необходимо добавить с использованием формата `app:{service-principal-client-id}@{azure-ad-tenant-id}`.

Следующий шаблон диспетчер ресурсов развертывает сервер Analysis Services с указанным субъектом-службой, добавленным в роль администратора Analysis Services.

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

Управляемое удостоверение можно также добавить в список администраторов Analysis Services. Например, у вас может быть [приложение логики с управляемым удостоверением, назначенное системой](../logic-apps/create-managed-service-identity.md), и вы хотите предоставить ему возможность администрирования сервера Analysis Services.

В большинстве частей портал Azure и API управляемые удостоверения определяются с помощью идентификатора объекта субъекта-службы. Однако Analysis Services требует, чтобы они были идентифицированы с помощью идентификатора клиента. Чтобы получить идентификатор клиента для субъекта-службы, можно использовать Azure CLI:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Кроме того, можно использовать PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Затем этот идентификатор клиента можно использовать в сочетании с ИДЕНТИФИКАТОРом клиента, чтобы добавить управляемое удостоверение в список администраторов Analysis Services, как описано выше.

## <a name="related-information"></a>Дополнительные сведения

* [Скачайте модуль PowerShell для SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Скачать SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


