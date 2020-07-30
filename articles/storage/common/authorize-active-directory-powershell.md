---
title: Выполнение команд PowerShell с учетными данными Azure AD для доступа к данным BLOB-объектов или очередей
titleSuffix: Azure Storage
description: PowerShell поддерживает вход с использованием учетных данных Azure AD для выполнения команд в хранилище BLOB-объектов Azure и очередей данных. Маркер доступа предоставляется для сеанса и позволяет авторизовать вызов операций. Разрешения зависят от роли RBAC, назначенной субъекту безопасности Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: b7e93f54c1aa0eaa5edf3b3fcbfbf8bd9a6442d7
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417545"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Выполнение команд PowerShell с учетными данными Azure AD для доступа к данным BLOB-объектов или очередей

Служба хранилища Azure предоставляет расширения для PowerShell, которые позволяют входить в систему и выполнять команды сценариев с учетными данными Azure Active Directory (Azure AD). При входе в PowerShell с учетными данными Azure AD возвращается маркер доступа OAuth 2,0. Этот маркер автоматически используется PowerShell для авторизации последующих операций с данными в хранилище BLOB-объектов или очередей. Для поддерживаемых операций больше не требуется передавать ключ учетной записи или маркер SAS с помощью команды.

Вы можете назначать разрешения для данных BLOB-объектов и очередей субъекту безопасности Azure AD через Управление доступом на основе ролей (RBAC). Дополнительные сведения о ролях RBAC в службе хранилища Azure см. в статье [Управление правами доступа к данным службы хранилища Azure с помощью RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Поддерживаемые операции

Расширения службы хранилища Azure поддерживаются для операций с данными BLOB-объектов и очередей. Действия, которые можно вызывать, зависят от разрешений, предоставленных субъекту безопасности Azure AD, с помощью которого вы входите в PowerShell. Разрешения для контейнеров или очередей службы хранилища Azure назначаются через RBAC. Например, если вы назначили роль **читателя данных большого двоичного объекта** , то можете выполнять команды сценариев, считывающие данные из контейнера или очереди. Если вы назначили роль **участника данных большого двоичного объекта** , то можете выполнять команды сценариев, которые считывают, записывают или удаляют контейнер или очередь или содержащиеся в них данные.

Дополнительные сведения о разрешениях, необходимых для каждой операции службы хранилища Azure в контейнере или очереди, см. в разделе [операции хранилища вызовов с токенами OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Вызов команд PowerShell с использованием учетных данных Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Чтобы использовать Azure PowerShell для входа и выполнения последующих операций в службе хранилища Azure с помощью учетных данных Azure AD, создайте контекст хранилища для ссылки на учетную запись хранения и включите `-UseConnectedAccount` параметр.

В следующем примере показано, как создать контейнер в новой учетной записи хранения из Azure PowerShell с помощью учетных данных Azure AD. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

1. Войдите в свою учетную запись Azure с помощью команды [Connect-азаккаунт](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Дополнительные сведения о входе в Azure с помощью PowerShell см. [в разделе Вход с помощью Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Создайте группу ресурсов Azure, вызвав командлет [New-азресаурцеграуп](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Создайте учетную запись хранения, вызвав командлет [New-азсторажеаккаунт](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Получите контекст учетной записи хранения, указывающий новую учетную запись хранения, вызвав [New-азсторажеконтекст](/powershell/module/az.storage/new-azstoragecontext). При работе с учетной записью хранения можно ссылаться на контекст, а не повторять передачу учетных данных. Включите `-UseConnectedAccount` параметр для вызова любых последующих операций с данными с использованием учетных данных Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Прежде чем создавать контейнер, назначьте себе роль [Участник данных в хранилище BLOB-объектов](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor). Несмотря на то, что вы являетесь владельцем учетной записи, вам нужны явные разрешения на выполнение операций с данными с учетной записью хранения. Дополнительные сведения о назначении ролей RBAC см. [в разделе Предоставление доступа к данным BLOB-объектов и очередей Azure с помощью RBAC в портал Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Распространение назначений ролей Azure может занять несколько минут.

1. Создайте контейнер, вызвав [New-азсторажеконтаинер](/powershell/module/az.storage/new-azstoragecontainer). Так как этот вызов использует контекст, созданный на предыдущих шагах, контейнер создается с использованием учетных данных Azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Дальнейшие действия

- [Назначение роли RBAC доступа к данным большого двоичного объекта и очереди с помощью PowerShell](storage-auth-aad-rbac-powershell.md)
- [Авторизация доступа к данным BLOB-объектов и очередей с помощью управляемых удостоверений для ресурсов Azure](storage-auth-aad-msi.md)
