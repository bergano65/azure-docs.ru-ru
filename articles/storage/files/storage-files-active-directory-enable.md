---
title: Включение аутентификации Azure Active Directory по протоколу SMB для службы файлов Azure (предварительная версия) — служба хранилища Azure
description: Узнайте, как включить аутентификацию на основе удостоверений по протоколу SMB (предварительная версия) для службы файлов Azure через доменные службы Azure Active Directory (Azure AD). В результате присоединенные к домену виртуальные машины Windows будут иметь доступ к файловым ресурсам Azure с помощью учетных данных Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: cd4c952caa336f2602d3c30e0db3e10ebee59cb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696126"
---
# <a name="enable-azure-active-directory-domain-service-authentication-over-smb-for-azure-files-preview"></a>Включить проверку подлинности доменная служба Active Directory Azure по протоколу SMB для службы файлов Azure (Предварительная версия)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Общие сведения об аутентификации Azure AD по протоколу SMB для службы файлов Azure см. в разделе [Обзор проверки подлинности Azure Active Directory по протоколу SMB для файлов Azure (предварительная версия)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Обзор рабочего процесса
Прежде чем включить проверку подлинности Azure AD DS по протоколу SMB для службы файлов Azure, убедитесь, что Azure AD и правильность настройки среды хранения Azure. Рекомендуется ознакомиться с [предварительными требованиями](#prerequisites), чтобы убедиться, что вы выполнили все необходимые шаги. 

Затем предоставьте доступ к ресурсам службы файлов Azure с помощью учетных данных Azure AD, выполнив следующее: 

1. Включите проверку подлинности Доменных служб Azure по протоколу SMB для вашей учетной записи для регистрации учетной записи хранения в соответствующее развертывание доменных служб Azure AD.
2. Назначьте права доступа к общему ресурсу для удостоверения Azure AD (пользователя, группы или субъекта-службы).
3. Настройте разрешения NTFS по протоколу SMB для каталогов и файлов.
4. Подключите файловый ресурс Azure из виртуальной машины, присоединенной к домену.

На следующей схеме показан рабочий процесс end-to-end для включения проверки подлинности Доменных служб Azure по протоколу SMB для службы файлов Azure. 

![Схема, показывающая рабочий процесс включения Azure AD по протоколу SMB для службы файлов Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>предварительные требования 

Прежде чем включать Azure AD по протоколу SMB для службы файлов Azure, убедитесь, что выполнены следующие предварительные требования.

1.  **Выберите или создайте клиент Azure AD.**

    Вы можете использовать новый или существующий клиент для выполнения аутентификации Azure AD по протоколу SMB. Клиент и файловый ресурс, к которому вы хотите получить доступ, должны быть связаны с одной и той же подпиской.

    Чтобы создать новый клиент Azure AD, можно [добавить клиент и подписку Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Если у вас уже есть клиент Azure AD, но вы хотите создать новый для использования со службой файлов Azure, перейдите к статье [Как получить клиент Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Включите доменные службы Azure AD в клиенте Azure AD**.

    Чтобы поддерживать аутентификацию с помощью учетных данных Azure AD, нужно включить доменные службы Azure AD для вашего клиента Azure AD. Если вы не администратор клиента Azure AD, обратитесь к администратору и следуйте пошаговым инструкциям, чтобы [включить доменные службы Azure Active Directory на портале Azure](../../active-directory-domain-services/create-instance.md).

    Для развертывания доменных служб Azure AD обычно требуется около 15 минут. Прежде чем переходить к следующему шагу, убедитесь, что для доменных служб Azure AD отображается состояние работоспособности **Выполняется** и включена синхронизация хэша паролей.

3.  **Присоедините виртуальную машину Azure к домену доменных служб Azure AD.**

    Чтобы получить доступ к файловому ресурсу с использованием учетных данных Azure AD с виртуальной машины, ваша виртуальная машина должна быть подключена к домену доменных служб Azure AD. Дополнительные сведения см. в статье [Присоединение виртуальной машины Windows Server к управляемому домену](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Проверка подлинности Azure AD DS по протоколу SMB с файлами Azure поддерживается только на виртуальных машинах Azure под управлением версий операционной системы, предшествующей Windows 7 или Windows Server 2008 R2.

4.  **Выберите или создайте файловый ресурс Azure**.

    Выберите новый или существующий файловый ресурс, связанный с той же подпиской, что и ваш клиент Azure AD. Дополнительные сведения см. в статье [Создание общей папки в службе файлов Azure](storage-how-to-create-file-share.md). 
    Для обеспечения оптимальной производительности корпорация Майкрософт рекомендует, чтобы файловый ресурс находился в том же регионе, что и виртуальная машина, из которой вы планируете получать доступ к ресурсу.

5.  **Проверьте сетевое подключение службы файлов Azure путем подключения файловых ресурсов Azure с использованием ключа учетной записи хранения.**

    Чтобы убедиться, что ваша виртуальная машина и файловый ресурс настроены правильно, попробуйте установить общий доступ к файлу с помощью ключа учетной записи хранения. Дополнительные сведения см. в статье [Использование общей папки Azure в Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Включить проверку подлинности Azure AD DS для вашей учетной записи

Включение Доменных служб Azure по протоколу SMB для службы файлов Azure, вы можно задать свойства для учетных записей хранения, созданной после 24 сентября 2018 г., с помощью портала Azure, Azure PowerShell или интерфейса командной строки Azure. В результате установки этого свойства учетная запись хранения регистрируется в соответствующем развертывании доменных служб Azure AD. Проверка подлинности Azure AD DS по протоколу SMB включается для всех новых и существующих общих папок в учетной записи хранения. 

Имейте в виду, что вы можете включить проверку подлинности Azure AD DS по протоколу SMB, только после успешного развертывания доменных служб Azure AD для вашего клиента Azure AD. Дополнительные сведения см. в разделе с [предварительными требованиями](#prerequisites).

### <a name="azure-portal"></a>Портал Azure

Чтобы включить проверку подлинности Azure AD DS по сравнению с использованием SMB [портала Azure](https://portal.azure.com), выполните следующие действия:

1. На портале Azure перейдите к существующей учетной записи хранения или [создайте ее](../common/storage-quickstart-create-account.md).
2. В разделе **Параметры** выберите **Конфигурация**.
3. Включите **аутентификацию Azure Active Directory для службы файлов Azure (предварительная версия)** .

На рисунке ниже показано, как включить аутентификацию Azure AD по протоколу SMB для учетной записи хранения.

![Включение аутентификации Azure AD по протоколу SMB на портале Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Включение проверки подлинности Доменных служб Azure по протоколу SMB с помощью Azure PowerShell. Во-первых установите последнюю версию модуля Az (2.4 или более поздней версии) или модуля Az.Storage (1.5 или более поздняя). Дополнительные сведения об установке PowerShell см. в разделе [установить Azure PowerShell в Windows с помощью PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Создайте новое хранилище учетной записи, а затем вызовите [набора AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) и задайте **EnableAzureActiveDirectoryDomainServicesForFile** параметр **true**. В приведенном ниже примере не забудьте заменить значения заполнителей собственными значениями. (При использовании предыдущей предварительной версии модуля, параметр Включение функции является **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
Чтобы включить эту функцию на существующих учетных записей хранения, используйте следующую команду:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
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
```

## <a name="assign-access-permissions-to-an-identity"></a>Назначение разрешений на доступ для удостоверения 

Чтобы получить доступ к ресурсам службы файлов Azure, используя учетные данные Azure AD, у удостоверения (пользователя, группы или субъекта-службы) должны быть необходимые разрешения на уровне общего ресурса. Этот процесс похож на указание разрешений для общей папки Windows, где вы указываете тип доступа, который имеет данный пользователь. В инструкциях в этом разделе описывается, как назначить для удостоверения права доступа на чтение, запись или удаление к файловому ресурсу.

Мы представили два Azure встроенные роли для предоставления пользователям разрешения на уровне общего ресурса: Модуля чтения общего ресурса SMB хранилища файлов данных и общей папки SMB участник для хранения файла данных. 

- **Модуль чтения общего ресурса SMB хранилища файл данных** разрешает доступ на чтение в общих папок службы хранилища Azure через SMB
- **Общий ресурс SMB участник для данных хранилища файл** позволяет чтение, запись и удаление общих папок службы хранилища Azure через SMB

> [!IMPORTANT]
> Для полного административного управления файловым ресурсом, включая возможность присвоения роли удостоверению, требуется использовать ключ учетной записи хранения. Административный элемент управления не поддерживается при использовании учетных данных Azure AD. 

Для назначения встроенных ролей пользователя удостоверения Azure AD для предоставления разрешения на уровне общего ресурса можно использовать портал Azure, PowerShell или Azure CLI. 

#### <a name="azure-portal"></a>Портал Azure
Чтобы назначить роль RBAC для удостоверения Azure AD с помощью [портала Azure](https://portal.azure.com), выполните следующие действия:

1. На портале Azure перейдите к файловому ресурсу, или [Создание файлового ресурса в службе файлов Azure](storage-how-to-create-file-share.md).
2. Выберите **Управление доступом (IAM)** .
3. Выберите **добавить назначение роли**
4. В **добавить назначение роли** колонке выберите соответствующую роль встроенные (модуля чтения общего ресурса SMB хранилища файлов данных, общей папки SMB участник для хранения файла данных) из **роли** раскрывающегося списка. Сохранить **назначение доступа к** по умолчанию: «Azure AD пользователя, группы или субъекта-службы» и выберите целевой объект удостоверения Azure AD, имя или адрес электронной почты. 
5. Наконец, выберите **Сохранить** для завершения операции назначения роли.

#### <a name="powershell"></a>PowerShell

Следующая команда PowerShell показано, как назначение роли RBAC для удостоверения Azure AD, на основе имени входа в систему. Дополнительные сведения о назначении ролей RBAC с помощью PowerShell см. в статье [Управление доступом с помощью RBAC и Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

При запуске следующего примера сценария не забудьте заменить значения заполнителей, включая скобки, собственными значениями.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Следующую команду интерфейса командной строки 2.0 показано, как назначение роли RBAC для удостоверения Azure AD, на основе имени входа в систему. Дополнительные сведения о назначении ролей RBAC с помощью Azure CLI см. в статье [Управление доступом с помощью RBAC и Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

При запуске следующего примера сценария не забудьте заменить значения заполнителей, включая скобки, собственными значениями.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
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

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о службе файлов Azure и использовании Azure AD по протоколу SMB см. в следующих ресурсах:

- [Общие сведения о службе файлов Azure](storage-files-introduction.md)
- [Обзор проверки подлинности Azure Active Directory по протоколу SMB для файлов Azure (предварительная версия)](storage-files-active-directory-overview.md)
- [Часто задаваемые вопросы](storage-files-faq.md)
