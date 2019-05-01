---
title: Включение аутентификации Azure Active Directory по протоколу SMB для службы файлов Azure (предварительная версия) — служба хранилища Azure
description: Узнайте, как включить аутентификацию на основе удостоверений по протоколу SMB (предварительная версия) для службы файлов Azure через доменные службы Azure Active Directory (Azure AD). В результате присоединенные к домену виртуальные машины Windows будут иметь доступ к файловым ресурсам Azure с помощью учетных данных Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: rogarana
ms.openlocfilehash: 974a4341bd140da60c5e229a644657fe7ab02535
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721614"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Включение аутентификации Azure Active Directory по протоколу SMB для службы файлов Azure (предварительная версия)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Общие сведения об аутентификации Azure AD по протоколу SMB для службы файлов Azure см. в разделе [Обзор проверки подлинности Azure Active Directory по протоколу SMB для файлов Azure (предварительная версия)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Обзор рабочего процесса
Прежде чем включать Azure AD по протоколу SMB для службы файлов Azure, убедитесь, что среды Azure AD и службы хранилища Azure настроены должным образом. Рекомендуется ознакомиться с [предварительными требованиями](#prerequisites), чтобы убедиться, что вы выполнили все необходимые шаги. 

Затем предоставьте доступ к ресурсам службы файлов Azure с помощью учетных данных Azure AD, выполнив следующее: 

1. Включите аутентификацию Azure AD по протоколу SMB для своей учетной записи хранения, чтобы зарегистрировать эту учетную запись в соответствующем развертывании доменных служб Azure AD.
2. Назначьте права доступа к общему ресурсу для удостоверения Azure AD (пользователя, группы или субъекта-службы).
3. Настройте разрешения NTFS по протоколу SMB для каталогов и файлов.
4. Подключите файловый ресурс Azure из виртуальной машины, присоединенной к домену.

На приведенной ниже схеме показан комплексный рабочий процесс включения аутентификации Azure AD по протоколу SMB для службы файлов Azure. 

![Схема, показывающая рабочий процесс включения Azure AD по протоколу SMB для службы файлов Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Технические условия 

Прежде чем включать Azure AD по протоколу SMB для службы файлов Azure, убедитесь, что выполнены следующие предварительные требования.

1.  **Выберите или создайте клиент Azure AD.**

    Вы можете использовать новый или существующий клиент для выполнения аутентификации Azure AD по протоколу SMB. Клиент и файловый ресурс, к которому вы хотите получить доступ, должны быть связаны с одной и той же подпиской.

    Чтобы создать новый клиент Azure AD, можно [добавить клиент и подписку Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Если у вас уже есть клиент Azure AD, но вы хотите создать новый для использования со службой файлов Azure, перейдите к статье [Как получить клиент Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Включите доменные службы Azure AD в клиенте Azure AD**.

    Чтобы поддерживать аутентификацию с помощью учетных данных Azure AD, нужно включить доменные службы Azure AD для вашего клиента Azure AD. Если вы не администратор клиента Azure AD, обратитесь к администратору и следуйте пошаговым инструкциям, чтобы [включить доменные службы Azure Active Directory на портале Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md).

    Для развертывания доменных служб Azure AD обычно требуется около 15 минут. Прежде чем переходить к следующему шагу, убедитесь, что для доменных служб Azure AD отображается состояние работоспособности **Выполняется** и включена синхронизация хэша паролей.

3.  **Присоедините виртуальную машину Azure к домену доменных служб Azure AD.**

    Чтобы получить доступ к файловому ресурсу с использованием учетных данных Azure AD с виртуальной машины, ваша виртуальная машина должна быть подключена к домену доменных служб Azure AD. Дополнительные сведения см. в статье [Присоединение виртуальной машины Windows Server к управляемому домену](../../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal.md).

    > [!NOTE]
    > Аутентификации Azure AD по протоколу SMB в службе файлов Azure поддерживается только на виртуальных машинах Azure, работающих на ОС версий выше Windows 7 или Windows Server 2008 R2.

4.  **Выберите или создайте файловый ресурс Azure**.

    Выберите новый или существующий файловый ресурс, связанный с той же подпиской, что и ваш клиент Azure AD. Дополнительные сведения см. в статье [Создание общей папки в службе файлов Azure](storage-how-to-create-file-share.md). 
    Для обеспечения оптимальной производительности корпорация Майкрософт рекомендует, чтобы файловый ресурс находился в том же регионе, что и виртуальная машина, из которой вы планируете получать доступ к ресурсу.

5.  **Проверьте сетевое подключение службы файлов Azure путем подключения файловых ресурсов Azure с использованием ключа учетной записи хранения.**

    Чтобы убедиться, что ваша виртуальная машина и файловый ресурс настроены правильно, попробуйте установить общий доступ к файлу с помощью ключа учетной записи хранения. Дополнительные сведения см. в статье [Использование общей папки Azure в Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-authentication-for-your-account"></a>Включение аутентификации Azure AD для учетной записи

Чтобы включить аутентификацию Azure AD по протоколу SMB для службы файлов Azure, вы можете задать свойство в учетных записях хранения, созданных после 24 сентября 2018 года, с помощью портала Azure, Azure PowerShell или Azure CLI. В результате установки этого свойства учетная запись хранения регистрируется в соответствующем развертывании доменных служб Azure AD. Затем аутентификация Azure AD по протоколу SMB включается для всех новых и существующих файловых ресурсов в учетной записи хранения. 

Помните, что вы можете включить аутентификацию Azure AD по протоколу SMB только после успешного развертывания доменных служб Azure AD в вашем клиенте Azure AD. Дополнительные сведения см. в разделе с [предварительными требованиями](#prerequisites).

### <a name="azure-portal"></a>Портал Azure

Чтобы включить аутентификацию Azure AD по протоколу SMB с помощью [портала Azure](https://portal.azure.com), выполните следующие действия.

1. На портале Azure перейдите к существующей учетной записи хранения или [создайте ее](../common/storage-quickstart-create-account.md).
2. В разделе **Параметры** выберите **Конфигурация**.
3. Включите **аутентификацию Azure Active Directory для службы файлов Azure (предварительная версия)**.

На рисунке ниже показано, как включить аутентификацию Azure AD по протоколу SMB для учетной записи хранения.

![Включение аутентификации Azure AD по протоколу SMB на портале Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Чтобы включить аутентификацию Azure AD по протоколу SMB через Azure PowerShell, сначала установите предварительную сборку модуля `Az.Storage` с поддержкой Azure AD. Дополнительные сведения об установке PowerShell см. в статье [Установка Azure PowerShell в ОС Windows с помощью PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Затем создайте учетную запись хранения, вызовите [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) и задайте для параметра **EnableAzureFilesAadIntegrationForSMB** значение **true**. В приведенном ниже примере не забудьте заменить значения заполнителей собственными значениями.

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
```

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Чтобы включить аутентификацию Azure AD по протоколу SMB из Azure CLI 2.0, сначала установите расширение `storage-preview`:

```cli-interactive
az extension add --name storage-preview
```
  
Затем создайте учетную запись хранения, вызовите [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) и задайте для свойства `--file-aad` значение **true**. В приведенном ниже примере не забудьте заменить значения заполнителей собственными значениями.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Назначение разрешений на доступ для удостоверения 

Чтобы получить доступ к ресурсам службы файлов Azure, используя учетные данные Azure AD, у удостоверения (пользователя, группы или субъекта-службы) должны быть необходимые разрешения на уровне общего ресурса. В инструкциях в этом разделе описывается, как назначить для удостоверения права доступа на чтение, запись или удаление к файловому ресурсу.

> [!IMPORTANT]
> Для полного административного управления файловым ресурсом, включая возможность присвоения роли удостоверению, требуется использовать ключ учетной записи хранения. Административный элемент управления не поддерживается при использовании учетных данных Azure AD. 

### <a name="define-a-custom-role"></a>Определение пользовательской роли

Чтобы предоставить разрешения на уровне общего ресурса, определите пользовательскую роль RBAC и назначьте ее удостоверению для определенного файлового ресурса. Этот процесс похож на указание разрешений для общей папки Windows, где вы указываете тип доступа, который имеет данный пользователь.  

Шаблоны, показанные в следующих разделах, предоставляют разрешения на чтение или изменение файлового ресурса. Чтобы определить пользовательскую роль, создайте JSON-файл и скопируйте в него соответствующий шаблон. Дополнительные сведения об определении пользовательских ролей RBAC см. в статье [Пользовательские роли в Azure](../../role-based-access-control/custom-roles.md).

**Определение роли для разрешения изменения на уровне общего ресурса**  
Следующий шаблон пользовательской роли предоставляет разрешения на изменение на уровня общего ресурса, а именно: доступ на чтение, запись и удаление к ресурсу.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share over SMB",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [
    "*"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

**Определение роли для разрешения чтения на уровне общего ресурса**  
Следующий шаблон пользовательской роли предоставляет разрешения на чтение на уровне общего ресурса, а именно доступ на чтение к ресурсу.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure File Share over SMB",
  "Actions": [
    "*/read"
  ],
  "DataActions": [
    "*/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

### <a name="create-the-custom-role"></a>Создание настраиваемой роли

Для создания пользовательской роли можно использовать PowerShell или Azure CLI. 

#### <a name="powershell"></a>PowerShell

Следующая команда PowerShell создает пользовательскую роль на основе одного из примеров шаблонов.

```powershell
#Create a custom role based on the sample template above
New-AzRoleDefinition -InputFile "<custom-role-def-json-path>"
```

#### <a name="cli"></a>Интерфейс командной строки 

Следующая команда Azure CLI создает пользовательскую роль на основе одного из примеров шаблонов.

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
```

### <a name="assign-the-custom-role-to-the-target-identity"></a>Назначение пользовательской роли целевому удостоверению

Затем используйте PowerShell или Azure CLI для назначения роли удостоверению Azure AD. 

#### <a name="powershell"></a>PowerShell

Следующая команда PowerShell демонстрирует, как перечислить доступные пользовательские роли, а затем назначить пользовательскую роль удостоверению Azure AD на основе имени для входа. Дополнительные сведения о назначении ролей RBAC с помощью PowerShell см. в статье [Управление доступом с помощью RBAC и Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

При запуске следующего примера сценария не забудьте заменить значения заполнителей, включая скобки, собственными значениями.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Интерфейс командной строки
  
Следующая команда CLI 2.0 демонстрирует, как перечислить доступные пользовательские роли, а затем назначить пользовательскую роль удостоверению Azure AD на основе имени для входа. Дополнительные сведения о назначении ролей RBAC с помощью Azure CLI см. в статье [Управление доступом с помощью RBAC и Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

При запуске следующего примера сценария не забудьте заменить значения заполнителей, включая скобки, собственными значениями.

```azurecli-interactive
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custom-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Настройка разрешений NTFS по протоколу SMB 
После назначения разрешений на уровне общего ресурса с помощью RBAC необходимо назначить соответствующие разрешения NTFS на уровне корневой папки, каталога или на уровне файла. Рассматривайте разрешения на уровне общего ресурса как привратника высокого уровня, который определяет, может ли пользователь получать доступ к общему ресурсу, в то время как разрешения NTFS действуют на более низком уровне и определяют, какие операции пользователь может выполнять на уровне каталога или файла. 

Служба файлов Azure поддерживает полный набор основных и дополнительных разрешений NTFS. Вы можете просматривать и настраивать разрешения NTFS для каталогов и файлов в файловом ресурсе Azure, подключив ресурс, а затем запустив команду Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) или [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl). 

> [!NOTE]
> В предварительной версии просматривать разрешения можно только с помощью проводника Windows. Изменение разрешений в данный момент не поддерживается.

Чтобы настроить разрешения NTFS с привилегиями суперпользователя, нужно подключить общий ресурс с использованием ключа учетной записи хранения из виртуальной машины, присоединенной к домену. Следуйте инструкциям в следующем разделе, чтобы подключить файловый ресурс Azure из командной строки и соответствующим образом настроить разрешения NTFS.

На уровне корневой папки файлового ресурса поддерживается следующий набор разрешений:

- BUILTIN\Administrators:(OI)(CI)(F);
- NT AUTHORITY\SYSTEM:(OI)(CI)(F);
- BUILTIN\Users:(RX);
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE);
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M);
- NT AUTHORITY\SYSTEM:(F);
- CREATOR OWNER:(OI)(CI)(IO)(F).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Подключение файлового ресурса Azure из командной строки

Используйте команду Windows **net use** для подключения файлового ресурса Azure. Не забудьте заменить значения заполнителей в примере собственными значениями. Дополнительные сведения о подключении файловых ресурсов см. в статье [Использование общей папки Azure в Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>Настройка разрешений NTFS с помощью icacls
Используйте следующую команду Windows, чтобы предоставить полный набор разрешений для всех каталогов и файлов в файловом ресурсе, включая корневую папку. Не забудьте заменить значения заполнителе, показанные в примере в скобках, собственными значениями.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Дополнительные сведения об использовании icacls для настройки разрешений NTFS и поддерживаемых типах разрешений см. в [руководстве по использованию icacls в командной строке](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Подключение файлового ресурса с виртуальной машины, присоединенной к домену 

Теперь вы можете убедиться, что успешно выполнили приведенные выше шаги, используя учетные данные Azure AD для доступа к файловому ресурсу Azure из виртуальной машины, присоединенной к домену. Сначала войдите в систему виртуальной машины, используя удостоверение Azure AD, которому вы предоставили разрешения, как показано на следующем изображении.

![Снимок экрана с окном входа в Azure AD для выполнения аутентификации пользователя](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Затем используйте следующую команду, чтобы подключить файловый ресурс Azure. Не забудьте заменить значения заполнителей собственными значениями. Так как вы уже прошли аутентификацию, вам не нужно указывать ключ учетной записи хранения или имя пользователя и пароль Azure AD. Azure AD по протоколу SMB поддерживает единый вход с использованием учетных данных Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Теперь вы успешно включили аутентификацию Azure AD по протоколу SMB и назначили пользовательскую роль, которая обеспечивает доступ к файловому ресурсу для удостоверения Azure AD. Чтобы предоставить доступ к файловому ресурсу для дополнительных пользователей, следуйте инструкциям, приведенным на шаге 2 и 3.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о службе файлов Azure и использовании Azure AD по протоколу SMB см. в следующих ресурсах:

- [Общие сведения о службе файлов Azure](storage-files-introduction.md)
- [Обзор проверки подлинности Azure Active Directory по протоколу SMB для файлов Azure (предварительная версия)](storage-files-active-directory-overview.md)
- [Часто задаваемые вопросы](storage-files-faq.md)
