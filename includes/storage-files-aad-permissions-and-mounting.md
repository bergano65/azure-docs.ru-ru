---
title: включить файл
description: включить файл
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 23550c83e76631e44d5036e0a038f01b61a79f1b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208232"
---
## <a name="assign-access-permissions-to-an-identity"></a>Назначение разрешений на доступ для удостоверения

Чтобы получить доступ к ресурсам службы файлов Azure с проверкой подлинности на основе удостоверений, удостоверение (пользователь, группа или субъект-служба) должно иметь необходимые разрешения на уровне общего ресурса. Этот процесс аналогичен указанию разрешений для общей папки Windows, где указывается тип доступа, который определенный пользователь имеет в общей папке. В инструкциях в этом разделе описывается, как назначить для удостоверения права доступа на чтение, запись или удаление к файловому ресурсу.

Мы предоставили три встроенные роли Azure для предоставления пользователям разрешений на уровне общего доступа:

- **Файл хранилища. средство чтения общего ресурса SMB** предоставляет доступ на чтение в файловых ресурсах службы хранилища Azure через SMB.
- **Участник общей папки SMB данных файлов хранилища** предоставляет доступ на чтение, запись и удаление в общих файловых ресурсах службы хранилища Azure через SMB.
- **Участник с повышенными правами общего ресурса SMB для данных файлов хранилища** позволяет читать, записывать, удалять и изменять разрешения NTFS в общих файловых ресурсах службы хранилища Azure по протоколу SMB.

> [!IMPORTANT]
> Полный административный контроль над файловым ресурсом, включая возможность стать владельцем файла, требует использования ключа учетной записи хранения. Административный элемент управления не поддерживается при использовании учетных данных Azure AD.

Вы можете использовать портал Azure, PowerShell или Azure CLI, чтобы назначить встроенные роли удостоверению пользователя Azure AD для предоставления разрешений на уровне общего доступа.

> [!NOTE]
> Не забудьте синхронизировать учетные данные AD с Azure AD, если вы планируете использовать AD для проверки подлинности. Синхронизация хэша паролей из AD в Azure AD необязательна. Разрешение на уровне общего ресурса будет предоставлено удостоверению Azure AD, которое синхронизируется из AD.

#### <a name="azure-portal"></a>Портал Azure
Чтобы назначить роль RBAC удостоверению Azure AD, используйте [портал Azure](https://portal.azure.com)выполните следующие действия.

1. В портал Azure перейдите к общей папке или [Создайте общую папку](../articles/storage/files/storage-how-to-create-file-share.md).
2. Выберите **Управление доступом (IAM)** .
3. Выберите **добавить назначение роли**
4. В колонке **Добавление назначения роли** выберите подходящую встроенную роль (средство чтения общих ресурсов SMB для данных файлов хранилища, участника общего ресурса SMB данных файлов хранилища) из списка **ролей** . Оставьте значение параметра **назначить доступ** в параметре по умолчанию: **пользователь, группа или субъект-служба Azure AD**. Выберите целевое удостоверение Azure AD по имени или адресу электронной почты.
5. Нажмите кнопку **сохранить** , чтобы завершить операцию назначения роли.

#### <a name="powershell"></a>PowerShell

В следующем примере PowerShell показано, как назначить роль RBAC удостоверению Azure AD на основе имени для входа. Дополнительные сведения о назначении ролей RBAC с помощью PowerShell см. в статье [Управление доступом с помощью RBAC и Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Перед запуском следующего примера сценария не забудьте заменить значения заполнителей, включая квадратные скобки, собственными значениями.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Следующая команда CLI 2,0 показывает, как назначить роль RBAC удостоверению Azure AD на основе имени для входа. Дополнительные сведения о назначении ролей RBAC с Azure CLI см. в разделе [Управление доступом с помощью RBAC и Azure CLI](../articles/role-based-access-control/role-assignments-cli.md). 

Перед запуском следующего примера сценария не забудьте заменить значения заполнителей, включая квадратные скобки, собственными значениями.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Настройка разрешений NTFS по протоколу SMB 
После назначения разрешений на уровне общего ресурса с помощью RBAC необходимо назначить соответствующие разрешения NTFS на уровне корневой папки, каталога или на уровне файла. Разрешения уровня общего доступа можно рассматривать как привратник высокого уровня, который определяет, может ли пользователь получить доступ к общей папке. В то время как разрешения NTFS работают на более детализированном уровне, чтобы определить, какие операции пользователь может выполнять на уровне каталога или файла.

Служба файлов Azure поддерживает полный набор основных и дополнительных разрешений NTFS. Вы можете просматривать и настраивать разрешения NTFS для каталогов и файлов в файловом ресурсе Azure, подключив общую папку, а затем используя проводник Windows или выполнив команду Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) или [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

Чтобы настроить NTFS с разрешениями суперпользователя, необходимо подключить общий ресурс с помощью ключа учетной записи хранения на виртуальной машине, присоединенной к домену. Следуйте инструкциям в следующем разделе, чтобы подключить файловый ресурс Azure из командной строки и соответствующим образом настроить разрешения NTFS.

На уровне корневой папки файлового ресурса поддерживается следующий набор разрешений:

- BUILTIN\Administrators:(OI)(CI)(F);
- NT AUTHORITY\SYSTEM:(OI)(CI)(F);
- BUILTIN\Users:(RX);
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE);
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M);
- NT AUTHORITY\SYSTEM:(F);
- CREATOR OWNER:(OI)(CI)(IO)(F).

### <a name="configure-ntfs-permissions-with-icacls"></a>Настройка разрешений NTFS с помощью icacls
Используйте следующую команду Windows, чтобы предоставить полный набор разрешений для всех каталогов и файлов в файловом ресурсе, включая корневую папку. Не забудьте заменить значения заполнителей в примере собственными значениями.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Дополнительные сведения об использовании icacls для установки разрешений NTFS и о различных типах поддерживаемых разрешений см. [в справочнике по командной строке для icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Подключение файлового ресурса Azure из командной строки

Используйте команду Windows **net use** для подключения файлового ресурса Azure. Не забудьте заменить значения заполнителей в следующем примере собственными значениями. Дополнительные сведения о подключении общих файловых ресурсов см. [в статье использование файлового ресурса Azure с Windows](../articles/storage/files/storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Настройка разрешений NTFS с помощью проводника файлов Windows
Используйте проводник файлов Windows, чтобы предоставить полный доступ ко всем каталогам и файлам в общей папке, включая корневой каталог.

1. Откройте проводник Windows и щелкните правой кнопкой мыши файл или каталог и выберите пункт **Свойства** .
2. Перейдите на вкладку **Безопасность** .
3. Щелкните " **Изменить".** Кнопка для изменения разрешений
4. Можно изменить разрешения существующих пользователей или нажать кнопку **Добавить...** , чтобы предоставить разрешения новым пользователям.
5. В окне запроса для добавления новых пользователей введите имя целевого пользователя, которому нужно предоставить разрешение, в поле **Введите имена объектов** и щелкните **Проверить имена** , чтобы найти полное имя участника-пользователя.
7.  В меню «Параметры» щелкните пункт **ОК**
8.  На вкладке Безопасность выберите все разрешения, которые вы хотите предоставить новому пользователю.
9.  Нажмите кнопку **Применить**.

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Подключение файлового ресурса с виртуальной машины, присоединенной к домену

Следующий процесс проверяет правильность настройки общей папки и разрешений на доступ, а также доступ к файловому ресурсу Azure с виртуальной машины, присоединенной к домену.

Войдите на виртуальную машину с помощью удостоверения Azure AD, которому предоставлены разрешения, как показано на следующем рисунке. Если вы включили проверку подлинности Active Directory для файлов Azure, используйте учетные данные AD. Для аутентификации Azure AD DS Войдите с помощью учетных данных Azure AD.

![Снимок экрана с окном входа в Azure AD для выполнения аутентификации пользователя](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Используйте следующую команду, чтобы подключить файловый ресурс Azure. Не забудьте заменить значения заполнителей собственными значениями. Так как вы прошли проверку подлинности, вам не нужно указывать ключ учетной записи хранения, учетные данные AD или учетные данные Azure AD. Возможности единого входа поддерживаются для проверки подлинности с помощью AD или Azure AD DS.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
