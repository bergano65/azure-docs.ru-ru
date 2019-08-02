---
title: Получение значений для аутентификации приложения для подключения к базе данных SQL Azure | Документация Майкрософт
description: Создание субъекта-службы для получения доступа к базе данных SQL из кода.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: d7c8c6788a8699c5b57c39731c148454ad8dcfcf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569320"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Получение необходимых значений для проверки подлинности приложения при предоставлении доступа к базе данных SQL из кода

Чтобы создать базу данных SQL и управлять ею из кода, необходимо зарегистрировать приложение в домене Azure Active Directory (AAD) в подписке, где были созданы ресурсы Azure.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Создание субъекта-службы для доступа к ресурсам из приложения

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны.

Следующий сценарий PowerShell создает приложение Active Directory (AD) и субъект-службу, которые необходимы для проверки подлинности нашего приложения C#. Сценарий выводит значения, необходимые для предыдущего примера на C#. Дополнительные сведения см. в статье [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

    # Sign in to Azure.
    Connect-AzAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create an AAD app
    $azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>См. также
* [Создание базы данных SQL с помощью C#](sql-database-get-started-csharp.md)
* [Подключение к базе данных SQL с использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md)

