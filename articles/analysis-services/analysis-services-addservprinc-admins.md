---
title: Добавление основного обслуживания к роли админ-сервиса анализа Azure (ru) Документы Майкрософт
description: Узнайте, как добавить главное службы автоматизации в роль администратора сервера анализа Azure
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 1370f65405963ebf825e986e6801607a0d96156e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298094"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Добавление субъекта-службы к роли администратора сервера 

 Чтобы автоматизировать задачи PowerShell, выполняемые без участия пользователя, субъекту-службе должны быть предоставлены разрешения **администратора сервера** на управляемом сервере Analysis Services. В этой статье описывается, как добавить субъект-службу к роли администратора сервера на сервере Azure AS. Это можно сделать с помощью студии управления серверами S'L или шаблона менеджера ресурсов.

## <a name="before-you-begin"></a>Перед началом
Прежде чем приступить к этой задаче, нужно зарегистрировать субъект-службу в Azure Active Directory.

[Создание принципала сервиса - портал Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Создание субъекта-службы с помощью PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Использование среды SQL Server Management Studio

Вы можете настроить администраторов серверов с помощью студии управления серверами S'L (SSMS). Для выполнения этой задачи вам потребуются разрешения [администратора сервера](analysis-services-server-admins.md) на сервере Azure Analysis Services. 

1. В среде SSMS подключитесь к серверу Azure Analysis Services.
2. В > **безопасности свойств** **сервера**, нажмите **Добавить**.
3. В окне **Выберите пользователя или группу** выполните поиск своего зарегистрированного приложения по имени, выберите его и нажмите кнопку **Добавить**.

    ![Поиск учетной записи субъекта-службы](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Проверьте идентификатор учетной записи субъекта-службы и нажмите кнопку **ОК**.
    
    ![Поиск учетной записи субъекта-службы](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Использование шаблона менеджера ресурсов

Вы также можете настроить администраторов серверов, развернув сервер аналитических служб с помощью шаблона Azure Resource Manager. Ит.кификатор, работающий при развертывании, должен принадлежать роли **contributor** для ресурса в [Azure Role-Based Control (RBAC).](../role-based-access-control/overview.md)

> [!IMPORTANT]
> Основной сервис должен быть `app:{service-principal-client-id}@{azure-ad-tenant-id}`добавлен с помощью формата.

Следующий шаблон менеджера ресурсов развертывает сервер аналитических служб с указанным принципом службы, добавленным к роли администратора аналитических служб:

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

## <a name="using-managed-identities"></a>Использование управляемых идентификационных данных

Управляемый итог также может быть добавлен в список аналитиков. Например, у вас может быть [Logic App с системой, назначенной управляемой личностью,](../logic-apps/create-managed-service-identity.md)и вы хотите предоставить ему возможность администрировать сервер аналитических служб.

В большинстве частей портала Azure и AIS управляемые идентификаторы идентифицируются с помощью идентификатора основного объекта службы. Тем не менее, аналитические службы требуют, чтобы они были идентифицированы с помощью идентификатора клиента. Для получения идентификатора клиента для основного обслуживания можно использовать Azure CLI:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Кроме того, вы можете использовать PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Затем можно использовать этот идентификатор клиента в сочетании с идентификатором клиента, чтобы добавить управляемое удостоверение в список аналитиков, как описано выше.

## <a name="related-information"></a>Дополнительные сведения

* [Скачайте модуль PowerShell для SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Скачивание SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


