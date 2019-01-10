---
title: Обновление поставщика ресурсов SQL в Azure Stack | Документация Майкрософт
description: Узнайте, как обновить поставщика ресурсов SQL в Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: jeffgilb
ms.reviewer: georgel
ms.openlocfilehash: b39cc799218a4c6f865acac8b98f5fb977c83bdc
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117798"
---
# <a name="update-the-sql-resource-provider"></a>Обновление поставщика ресурсов SQL

*Область применения: интегрированные системы Azure Stack*.

При обновлении сборки Azure Stack может быть выпущен новый поставщик ресурсов SQL. Существующий адаптер может продолжать работать. Тем не менее мы рекомендуем как можно скорее обновить его до последней сборки.

> [!IMPORTANT]
> Обновления необходимо устанавливать в порядке их выпуска. Пропускать версии нельзя. Список версий см. в разделе с [предварительными требованиями для развертывания поставщика ресурсов](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

## <a name="overview"></a>Обзор

Чтобы обновить поставщик ресурсов, воспользуйтесь скриптом *UpdateSQLProvider.ps1*. Этот скрипт входит в состав скачиваемых ресурсов нового поставщика ресурсов SQL. Процесс обновления осуществляется так же, как и [развертывание поставщика ресурсов](./azure-stack-sql-resource-provider-deploy.md). Скрипт обновления использует те же аргументы, что и скрипт DeploySqlProvider.ps1, и вам потребуется предоставить сведения о сертификате.

### <a name="update-script-processes"></a>Процессы скрипта обновления

Скрипт *UpdateSQLProvider.ps1* создает виртуальную машину с кодом последней версии поставщика ресурсов.

> [!NOTE]
> Мы рекомендуем загрузить последний образ основных компонентов Windows Server 2016 из служб управления Marketplace. Если необходимо установить обновление, **один** MSU-пакет можно поместить в локальный каталог зависимого элемента. Если поместить несколько MSU-файлов в этом расположении, выполнение скрипта завершится ошибкой.

Когда скрипт *UpdateSQLProvider.ps1* создаст виртуальную машину, он перенесет следующие параметры из старой виртуальной машины поставщика:

* сведения о базе данных;
* сведения о сервере размещения;
* необходимые записи DNS.

### <a name="update-script-powershell-example"></a>Пример скрипта обновления на PowerShell

Вы можете изменить и выполнить следующий скрипт в интегрированной среде сценариев Windows PowerShell с повышенными правами. 

Не забудьте изменить данные учетной записи и пароли, если это требуется для вашей среды.

> [!NOTE]
> Этот процесс обновления применим только к интегрированным системам Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="updatesqlproviderps1-parameters"></a>Параметры UpdateSQLProvider.ps1

Следующие параметры можно указать в командной строке при выполнении скрипта. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать требуемые параметры.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ |
| **AzCredential** | Учетные данные для учетной записи администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ |
| **VMLocalCredential** | Учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _Обязательный_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ |
| **AzureEnvironment** | Среда Azure службы учетной записи администратора, которая использовалась для развертывания Azure Stack. Требуется только для развертываний Azure AD. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, в случае использования Azure AD для Китая, — **AzureChinaCloud**. | AzureCloud; |
| **DependencyFilesLocalPath** | В этот каталог также нужно поместить PFX-файл сертификата. | _Необязательно для одного узла, но обязательно для нескольких узлов._ |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ |
| **MaxRetryCount** | Число повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** |Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы. | Нет  |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет  |

## <a name="next-steps"></a>Дополнительная информация

[Обслуживание поставщика ресурсов SQL](azure-stack-sql-resource-provider-maintain.md)
