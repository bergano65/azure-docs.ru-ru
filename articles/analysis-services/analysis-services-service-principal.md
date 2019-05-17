---
title: Автоматизация задач Azure Analysis Services с помощью субъектов-служб | Документация Майкрософт
description: Сведения о создании субъекта-службы для автоматизации задач Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c034ed7164e67183b9a848d5210dcaf377476c6a
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518162"
---
# <a name="automation-with-service-principals"></a>Автоматизация с помощью субъектов-служб

Субъект-служба — это ресурс приложения Azure Active Directory, созданный в клиенте для выполнения автоматических операций с ресурсом и уровнем обслуживания. Он представляет собой уникальный тип *идентификатора пользователя* с идентификатором приложения и паролем или сертификатом. У субъекта-службы есть разрешения на выполнение только тех задач, которые определены ролями и назначенными ему привилегиями. 

В Analysis Services субъекты-службы применяются для автоматизации типичных задач с использованием службы автоматизации Azure, автоматического режима PowerShell, настраиваемых клиентских приложений и веб-приложений. Например, подготовка серверов, развертывание моделей, обновление данных, увеличение и уменьшение масштаба, остановка и возобновление могут быть автоматизированы с помощью субъектов-служб. Разрешения присваиваются субъектам-службам через членство в ролях, так же как и в обычных учетных записях Azure AD UPN.

Службы Analysis Services также поддерживают операции, выполняемые управляемыми удостоверениями с помощью субъектов-служб. Дополнительные сведения см. в разделе [управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) и [служб Azure, поддерживающие аутентификацию Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).

## <a name="create-service-principals"></a>Создание субъектов-служб
 
Субъекты-службы можно создать на портале Azure или с помощью PowerShell. Дополнительные сведения см. на следующих ресурсах:

[Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../active-directory/develop/howto-create-service-principal-portal.md)   
[Создание субъекта-службы с помощью PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Хранение учетных данных и сертификатов в службе автоматизации Azure

Учетные данные и сертификаты субъектов-служб могут храниться в службе автоматизации Azure для выполнения операций с модулями runbook. Дополнительные сведения см. на следующих ресурсах:

[Ресурсы учетных данных в службе автоматизации Azure](../automation/automation-credentials.md)   
[Сертификация активов в службе автоматизации Azure](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Добавление субъектов-служб к роли администратора сервера

Прежде чем выполнять операции управления сервером Analysis Services с помощью субъекта-службы, необходимо добавить его к роли администраторов серверов. Дополнительные сведения см. в статье [Добавление субъекта-службы к роли администратора сервера](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Субъекты-службы в строках подключения

Идентификатор приложения и пароль или сертификат субъекта-службы могут использоваться в строках подключения, точно так же как и имя участника-пользователя.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="a-nameazmodule-using-azanalysisservices-module"></a><a name="azmodule" />С помощью модуля Az.AnalysisServices

При использовании субъекта-службы для операций управления ресурсами с помощью [Az.AnalysisServices](/powershell/module/az.analysisservices) модуля, используйте `Connect-AzAccount` командлета. 

В следующем примере appID и пароль используются для выполнения операции уровня управления для синхронизации реплик только для чтения и масштабирования вверх/out:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>С помощью модуля SQLServer

В следующем примере идентификатор приложения и пароль используются для выполнения операции обновления шаблона базы данных:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>Объекты AMO и ADOMD 

При подключении к клиентским приложениям и веб-приложениям устанавливаемые пакеты от NuGet [клиентских библиотек AMO и ADOMD](analysis-services-data-providers.md) версии 15.0.2 и выше поддерживают субъекты-службы в строках подключения с помощью следующего синтаксиса: `app:AppID` и пароль или `cert:thumbprint`. 

В следующем примере `appID` и `password` используются для выполнения операции обновления шаблона базы данных:

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Дальнейшие действия
[Sign in with Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)  (Вход в Azure PowerShell)  
[Добавление субъекта-службы к роли администратора сервера](analysis-services-addservprinc-admins.md)   
