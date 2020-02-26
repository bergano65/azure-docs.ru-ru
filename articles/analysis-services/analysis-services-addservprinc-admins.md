---
title: Добавление субъекта-службы в Azure Analysis Services роль администратора | Документация Майкрософт
description: Узнайте, как добавить субъект-службу автоматизации к роли администратора Azure Analysis Services Server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b75740e9bff714ad68c93bea7e387e60da2f1c59
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212496"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Добавление субъекта-службы к роли администратора сервера 

 Чтобы автоматизировать задачи PowerShell, выполняемые без участия пользователя, субъекту-службе должны быть предоставлены разрешения **администратора сервера** на управляемом сервере Analysis Services. В этой статье описывается, как добавить субъект-службу к роли администратора сервера на сервере Azure AS. Это можно сделать с помощью SQL Server Management Studio или шаблона диспетчер ресурсов.
 
> [!NOTE]
> Для операций сервера, использующих командлеты Azure PowerShell, субъект-служба также должен принадлежать к роли **владельца** для ресурса в [контроле доступа на основе ролей (RBAC) Azure](../role-based-access-control/overview.md). 

## <a name="before-you-begin"></a>Перед началом
Прежде чем приступить к этой задаче, нужно зарегистрировать субъект-службу в Azure Active Directory.

[Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../active-directory/develop/howto-create-service-principal-portal.md)   
[Создание субъекта-службы с помощью PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Использование среды SQL Server Management Studio

Администраторы сервера можно настроить с помощью SQL Server Management Studio (SSMS). Для выполнения этой задачи вам потребуются разрешения [администратора сервера](analysis-services-server-admins.md) на сервере Azure Analysis Services. 

1. В среде SSMS подключитесь к серверу Azure Analysis Services.
2. Выберите **Свойства сервера** > **Безопасность** и нажмите кнопку **Добавить**.
3. В окне **Выберите пользователя или группу** выполните поиск своего зарегистрированного приложения по имени, выберите его и нажмите кнопку **Добавить**.

    ![Поиск учетной записи субъекта-службы](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Проверьте идентификатор учетной записи субъекта-службы и нажмите кнопку **ОК**.
    
    ![Поиск учетной записи субъекта-службы](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Использование шаблона диспетчер ресурсов

Также можно настроить администраторов сервера, развернув сервер Analysis Services с помощью шаблона Azure Resource Manager. Удостоверение, выполняющее развертывание, должно принадлежать роли **участника** для ресурса в [контроле доступа на основе ролей (RBAC) Azure](../role-based-access-control/overview.md).

> [!IMPORTANT]
> Субъект-служба необходимо добавить с помощью `app:{service-principal-client-id}@{azure-ad-tenant-id}`формата.

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

## <a name="related-information"></a>Дополнительные сведения

* [Скачивание модуля PowerShell SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Скачивание SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


