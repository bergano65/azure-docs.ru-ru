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
ms.openlocfilehash: c88f5a4dd4f2997ce01b1f6a3ae192c62f530e76
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011432"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. Назначить разрешения на доступ к удостоверению личности

Для доступа к ресурсам Azure Files с аутентификацией на основе идентификации личность (пользователь, группа или директор службы) должны иметь необходимые разрешения на уровне общего доступа. Этот процесс аналогичен указанию разрешений на совместное использование Windows, где указан тип доступа, который имеет конкретный пользователь к доле файла. В инструкциях в этом разделе описывается, как назначить для удостоверения права доступа на чтение, запись или удаление к файловому ресурсу. 

Мы ввели три встроенные роли Azure для предоставления разрешений на уровне общего участия пользователям:

- **Хранение файлов SMB SMB Share Reader** позволяет читать доступ в разделах файлов Azure Storage по сравнению с SMB.
- **Хранение файлов SMB Доля Вкладчик** позволяет читать, писать и удалять доступ в Azure хранения файлов акций по sMB.
- **Хранение файлов SMB Доля Доля Повышенный Вкладчик** позволяет читать, писать, удалять и изменять ntFS разрешений в Azure Хранения файлов акций по сравнению с SMB.

> [!IMPORTANT]
> Полный административный контроль над долей файла, включая возможность владения файлом, требует использования ключа учетной записи хранилища. Административный элемент управления не поддерживается при использовании учетных данных Azure AD.

Можно использовать портал Azure, PowerShell или Azure CLI для присвоения встроенных ролей идентификационным данным Azure AD пользователя для предоставления разрешений на уровне общего использования.

> [!NOTE]
> Не забудьте синхронизировать учетные данные AD с Azure AD, если вы планируете использовать AD для проверки подлинности. Синхронизация хэша паролей от AD до Azure AD не является обязательной. Разрешение уровня общего нахай будет предоставлено иденционносции Azure AD, синхронизированной с AD.

Общая рекомендация заключается в использовании разрешения уровня общего доступа для управления доступом высокого уровня в группу AD, представляющую группу пользователей и идентификаторов, а затем использовать разрешения NTFS для детального управления доступом на уровне каталога/файла. 

#### <a name="azure-portal"></a>Портал Azure
Чтобы присвоить роль RBAC иждивещу Azure AD, с помощью [портала Azure](https://portal.azure.com)выполните следующие действия:

1. На портале Azure перейдите на раздел файла или [создайте общий файл.](../articles/storage/files/storage-how-to-create-file-share.md)
2. Выберите **элемент управления доступом (IAM)**.
3. Выберите **Добавить назначение роли**
4. В лезвии **назначения роли Добавления** выберите соответствующую встроенную роль (Хранение данных SMB Share Reader, Вклад в обмен данными о файлах хранения) из списка **ролей.** Оставьте **доступ к** при настройке по умолчанию: **пользователь Azure AD, группа или основной сервис.** Выберите целевую идентификацию Azure AD по имени или адресу электронной почты.
5. Выберите **Сохранить** для завершения операции назначения ролей.

#### <a name="powershell"></a>PowerShell

В следующем примере PowerShell показано, как присвоить роль RBAC идентификационным данным Azure AD на основе имени ввне. Дополнительные сведения о назначении ролей RBAC с помощью PowerShell см. в статье [Управление доступом с помощью RBAC и Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Перед запуском следующего образца скрипта не забудьте заменить значения заполнителя, включая скобки, на собственные значения.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Следующая команда CLI 2.0 показывает, как присвоить роль RBAC идентификационным данным Azure AD на основе имени ввне. Для получения дополнительной информации о назначении ролей RBAC с Azure CLI см. [Управление доступом с помощью RBAC и Azure CLI.](../articles/role-based-access-control/role-assignments-cli.md) 

Перед запуском следующего образца скрипта не забудьте заменить значения заполнителя, включая скобки, на собственные значения.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. Настройка разрешений NTFS на SMB 
После назначения разрешений на уровне общего ресурса с помощью RBAC необходимо назначить соответствующие разрешения NTFS на уровне корневой папки, каталога или на уровне файла. Думайте о разрешениях уровня общего доступа как о высоком уровне привратника, который определяет, может ли пользователь получить доступ к этой акции. В то время как разрешения NTFS действуют на более детальном уровне, чтобы определить, какие операции пользователь может делать на уровне каталога или файла.

Служба файлов Azure поддерживает полный набор основных и дополнительных разрешений NTFS. Вы можете просматривать и настраивать разрешения NTFS в каталогах и файлах в файле Azure, устанавливая долю, а затем используя Windows File Explorer или запустив команду Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) или [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 

Чтобы настроить NTFS с помощью разрешений суперпользователя, необходимо смонтировать долю, используя ключ учетной записи хранилища из вашего домена, связанного с VM. Следуйте инструкциям в следующем разделе, чтобы смонтировать общую часть файла Azure из запроса команды и настроить соответственно разрешения NTFS.

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

Для получения дополнительной информации о том, как использовать icacls для установки [the command-line reference for icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)разрешений NTFS и о различных типах поддерживаемых разрешений, см.

### <a name="mount-a-file-share-from-the-command-prompt"></a>Подключение файлового ресурса Azure из командной строки

Используйте команду Windows **net use** для подключения файлового ресурса Azure. Не забудьте заменить значения заполнителя в следующем примере своими собственными значениями. Для получения дополнительной информации о сборе файлов [см.](../articles/storage/files/storage-how-to-use-files-windows.md)

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Настройка разрешений NTFS с помощью Windows File Explorer
Используйте Windows File Explorer для предоставления полного разрешения всем каталогам и файлам, наданным в рамках раздела файлов, включая корневой каталог.

1. Откройте Windows File Explorer и нажмите правое нажатие на файл/каталог и выберите **Свойства**
2. Нажмите на вкладку **безопасности**
3. Нажмите на **Edit..**. кнопка для изменения разрешений
4. Вы можете изменить разрешение существующих пользователей или нажать на **Добавить...,** чтобы предоставить разрешения новым пользователям
5. В оперативном окне для добавления новых пользователей введите имя целевого пользователя, которое вы хотите предоставить в **Введите имена объектов для выбора** коробки, и нажмите на **Check Names,** чтобы найти полное имя пользователя UPN.
7.  Нажмите на **OK**
8.  Во вкладке "Безопасность" выберите все разрешения, которые вы хотите предоставить недавно добавленному пользователю
9.  Нажмите кнопку **Применить**.

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. Смонтировать файл долю из домена, примкнувного к VM

Следующий процесс проверяет, что ваша доля файла и разрешения доступа были настроены правильно и что вы можете получить доступ к совместной акции Azure File из домена, объединенного VM. Имейте в виду, что назначение ролей уровня доли RBAC может занять некоторое время, чтобы быть в силе. 

Восвай в VM, используя иденцию Azure AD, на которую вы предоставили разрешения, как показано на следующем изображении. Если вы включили проверку подлинности AD для файлов Azure, используйте учетные данные AD. Для проверки подлинности Azure AD DS войдите в систему с помощью учетных данных Azure AD.

![Снимок экрана с окном входа в Azure AD для выполнения аутентификации пользователя](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Используйте следующую команду для установки общего файла Azure. Не забудьте заменить значения заполнителей собственными значениями. Поскольку вы прошли аутентификации, вам не нужно предоставлять ключ учетной записи хранилища, учетные данные AD или учетные данные Azure AD. Единый опыт регистрации поддерживается для проверки подлинности с помощью AD или Azure AD DS.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
