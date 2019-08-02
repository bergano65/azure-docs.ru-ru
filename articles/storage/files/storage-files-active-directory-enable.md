---
title: Включение аутентификации Azure Active Directory по протоколу SMB для службы файлов Azure (предварительная версия) — служба хранилища Azure
description: Узнайте, как включить проверку подлинности на основе удостоверений через протокол SMB для службы файлов Azure с помощью доменных служб Azure Active Directory. Затем присоединенные к домену виртуальные машины Windows могут получить доступ к файловым ресурсам Azure с помощью учетных данных Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: c0cfb8b7f0d6e3988ccdfa51cae2748b7008308d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699768"
---
# <a name="enable-azure-active-directory-domain-services-authentication-over-smb-for-azure-files-preview"></a>Включение проверки подлинности Azure Active Directory доменных служб по протоколу SMB для службы файлов Azure (Предварительная версия)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Общие сведения о проверке подлинности Azure AD по протоколу SMB для службы файлов Azure см. в статье [обзор Azure Active Directory аутентификации по протоколу SMB для файлов Azure (Предварительная версия)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Обзор рабочего процесса
Перед включением проверки подлинности Azure AD DS в SMB для файлов Azure убедитесь, что среды Azure AD и службы хранилища Azure настроены правильно. Рекомендуется выполнить все [необходимые условия](#prerequisites) , чтобы убедиться, что выполнены все необходимые действия.

Затем предоставьте доступ к ресурсам службы файлов Azure с помощью учетных данных Azure AD, выполнив следующее: 

1. Включите проверку подлинности Azure AD DS по протоколу SMB, чтобы ваша учетная запись хранения регистрировала учетную запись хранения в связанном развертывании Azure AD DS.
2. Назначьте права доступа к общему ресурсу для удостоверения Azure AD (пользователя, группы или субъекта-службы).
3. Настройте разрешения NTFS по протоколу SMB для каталогов и файлов.
4. Подключите файловый ресурс Azure из виртуальной машины, присоединенной к домену.

На следующей схеме показан комплексный рабочий процесс для включения проверки подлинности Azure AD DS по протоколу SMB для службы файлов Azure. 

![Схема, показывающая рабочий процесс включения Azure AD по протоколу SMB для службы файлов Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>предварительные требования

Прежде чем включать Azure AD по протоколу SMB для службы файлов Azure, убедитесь, что выполнены следующие предварительные требования.

1.  **Выберите или создайте клиент Azure AD.**

    Вы можете использовать новый или существующий клиент для выполнения аутентификации Azure AD по протоколу SMB. Клиент и файловый ресурс, к которому вы хотите получить доступ, должны быть связаны с одной и той же подпиской.

    Чтобы создать новый клиент Azure AD, можно [добавить клиент и подписку Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Если у вас уже есть клиент Azure AD, но вы хотите создать новый для использования со службой файлов Azure, перейдите к статье [Как получить клиент Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Включите доменные службы Azure AD в клиенте Azure AD**.

    Для поддержки проверки подлинности с использованием учетных данных Azure AD необходимо включить Azure AD DS для вашего клиента Azure AD. Если вы не являетесь администратором клиента Azure AD, обратитесь к администратору и следуйте пошаговым инструкциям, чтобы [включить Azure Active Directory доменных служб с помощью портал Azure](../../active-directory-domain-services/create-instance.md).

    Для завершения развертывания AD DS Azure обычно требуется около 15 минут. Прежде чем перейти к следующему шагу, убедитесь, что для состояния работоспособности AD DS Azure указано состояние **работает**с включенной синхронизацией хэшей паролей.

3.  **Домен: присоединение виртуальной машины Azure к Azure AD DS.**

    Чтобы получить доступ к общей папке с помощью учетных данных Azure AD на виртуальной машине, виртуальная машина должна быть присоединена к домену Azure AD DS. Дополнительные сведения см. в статье [Присоединение виртуальной машины Windows Server к управляемому домену](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Проверка подлинности Azure AD DS через SMB с помощью файлов Azure поддерживается только на виртуальных машинах Azure, работающих в версиях ОС выше Windows 7 или Windows Server 2008 R2.

4.  **Выберите или создайте файловый ресурс Azure**.

    Выберите новый или существующий файловый ресурс, связанный с той же подпиской, что и ваш клиент Azure AD. Дополнительные сведения см. в статье [Создание общей папки в службе файлов Azure](storage-how-to-create-file-share.md). 
    Для оптимальной производительности рекомендуется, чтобы общая папка была в том же регионе, что и виртуальная машина, из которой планируется доступ к общей папке.

5.  **Проверьте сетевое подключение службы файлов Azure путем подключения файловых ресурсов Azure с использованием ключа учетной записи хранения.**

    Чтобы убедиться, что ваша виртуальная машина и файловый ресурс настроены правильно, попробуйте установить общий доступ к файлу с помощью ключа учетной записи хранения. Дополнительные сведения см. в статье [Использование общей папки Azure в Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Включение аутентификации Azure AD DS для учетной записи

Чтобы включить аутентификацию Azure AD DS по протоколу SMB для файлов Azure, можно задать свойство учетных записей хранения, созданных после 24 сентября 2018, с помощью портал Azure, Azure PowerShell или Azure CLI. Задание этого свойства регистрирует учетную запись хранения с помощью связанного развертывания Azure AD DS. Затем проверка подлинности Azure AD DS через SMB включается для всех новых и существующих файловых ресурсов в учетной записи хранения.

Помните, что вы можете включить проверку подлинности Azure AD DS по протоколу SMB только после успешного развертывания AD DS Azure в клиенте Azure AD. Дополнительные сведения см. в разделе [Предварительные требования](#prerequisites).

### <a name="azure-portal"></a>портала Azure

Чтобы включить проверку подлинности Azure AD DS в SMB с [портал Azure](https://portal.azure.com), выполните следующие действия.

1. В портал Azure перейдите к существующей учетной записи хранения или [Создайте учетную запись хранения](../common/storage-quickstart-create-account.md).
2. В разделе **Параметры** выберите **Конфигурация**.
3. Включение **проверки подлинности Azure Active Directory для службы файлов Azure (Предварительная версия)** .

На следующем рисунке показано, как включить проверку подлинности Azure AD DS по протоколу SMB для вашей учетной записи хранения.

![Включение аутентификации Azure AD по протоколу SMB на портале Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Чтобы включить проверку подлинности Azure AD DS в SMB с Azure PowerShell, установите последний модуль AZ (2,4 или более поздней версии) или AZ. Storage Module (1,5 или более поздней версии). Дополнительные сведения об установке PowerShell см. в статье [Установка Azure PowerShell в ОС Windows с помощью PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Создайте новую учетную запись хранения, вызовите [Set-азсторажеаккаунт](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount), а затем присвойте параметру **енаблеазуреактиведиректоридомаинсервицесфорфиле** **значение true**. В следующем примере не забудьте заменить значения заполнителей собственными значениями. (Если использовался предыдущий модуль предварительной версии, параметр для включения функции — **енаблеазурефилесаадинтегратионфорсмб**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
Чтобы включить эту функцию для существующих учетных записей хранения, используйте следующую команду:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Чтобы включить аутентификацию Azure AD по протоколу SMB из Azure CLI 2.0, сначала установите расширение `storage-preview`:

```cli-interactive
az extension add --name storage-preview
```
  
Затем создайте учетную запись хранения, вызовите [AZ Storage Account Update](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update)и присвойте `--file-aad` свойству значение **true**. В следующем примере не забудьте заменить значения заполнителей собственными значениями.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Назначение разрешений на доступ для удостоверения

Для доступа к ресурсам службы файлов Azure с помощью учетных данных Azure AD удостоверение (пользователь, группа или субъект-служба) должно иметь необходимые разрешения на уровне общего ресурса. Этот процесс аналогичен указанию разрешений для общей папки Windows, где указывается тип доступа, который определенный пользователь имеет в общей папке. В инструкциях в этом разделе описывается, как назначить для удостоверения права доступа на чтение, запись или удаление к файловому ресурсу.

Мы предоставили две встроенные роли Azure для предоставления пользователям разрешений на уровне общего доступа:

- **Файл хранилища. средство чтения общего ресурса SMB** предоставляет доступ на чтение в файловых ресурсах службы хранилища Azure через SMB.
- **Участник общей папки SMB данных файлов хранилища** предоставляет доступ на чтение, запись и удаление в общих файловых ресурсах службы хранилища Azure через SMB.

> [!IMPORTANT]
> Для полного административного управления файловым ресурсом, включая возможность присвоения роли удостоверению, требуется использовать ключ учетной записи хранения. Административный элемент управления не поддерживается при использовании учетных данных Azure AD.

Вы можете использовать портал Azure, PowerShell или Azure CLI, чтобы назначить встроенные роли удостоверению пользователя Azure AD для предоставления разрешений на уровне общего доступа.

#### <a name="azure-portal"></a>портала Azure
Чтобы назначить роль RBAC удостоверению Azure AD, используйте [портал Azure](https://portal.azure.com)выполните следующие действия.

1. В портал Azure перейдите к общей папке или [Создайте общую папку в службе файлов Azure](storage-how-to-create-file-share.md).
2. Выберите **Управление доступом (IAM)** .
3. Выберите **добавить назначение роли**
4. В колонке **Добавление назначения роли** выберите подходящую встроенную роль (средство чтения общих ресурсов SMB для данных файлов хранилища, участника общего ресурса SMB данных файлов хранилища) из списка **ролей** . Установите параметр **назначить доступ к** по умолчанию. **Пользователь, группа или субъект-служба Azure AD**. Выберите целевое удостоверение Azure AD по имени или адресу электронной почты.
5. Нажмите кнопку **сохранить** , чтобы завершить операцию назначения роли.

#### <a name="powershell"></a>PowerShell

Следующая команда PowerShell показывает, как назначить роль RBAC удостоверению Azure AD на основе имени для входа. Дополнительные сведения о назначении ролей RBAC с помощью PowerShell см. в статье [Управление доступом с помощью RBAC и Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Перед запуском следующего примера сценария не забудьте заменить значения заполнителей, включая квадратные скобки, собственными значениями.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Следующая команда CLI 2,0 показывает, как назначить роль RBAC удостоверению Azure AD на основе имени для входа. Дополнительные сведения о назначении ролей RBAC с Azure CLI см. в разделе [Управление доступом с помощью RBAC и Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Перед запуском следующего примера сценария не забудьте заменить значения заполнителей, включая квадратные скобки, собственными значениями.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Настройка разрешений NTFS по протоколу SMB 
После назначения разрешений на уровне общего ресурса с помощью RBAC необходимо назначить соответствующие разрешения NTFS на уровне корневой папки, каталога или на уровне файла. Разрешения уровня общего доступа можно рассматривать как привратник высокого уровня, который определяет, может ли пользователь получить доступ к общей папке. В то время как разрешения NTFS работают на более детализированном уровне, чтобы определить, какие операции пользователь может выполнять на уровне каталога или файла.

Служба файлов Azure поддерживает полный набор основных и дополнительных разрешений NTFS. Вы можете просматривать и настраивать разрешения NTFS для каталогов и файлов в файловом ресурсе Azure, подключив ресурс, а затем запустив команду Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) или [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl). 

> [!NOTE]
> В предварительной версии просматривать разрешения можно только с помощью проводника Windows. Разрешения на изменение пока не поддерживаются.

Чтобы настроить NTFS с разрешениями суперпользователя, необходимо подключить общий ресурс с помощью ключа учетной записи хранения на виртуальной машине, присоединенной к домену. Следуйте инструкциям в следующем разделе, чтобы подключить файловый ресурс Azure из командной строки и соответствующим образом настроить разрешения NTFS.

На уровне корневой папки файлового ресурса поддерживается следующий набор разрешений:

- BUILTIN\Administrators:(OI)(CI)(F);
- NT AUTHORITY\SYSTEM:(OI)(CI)(F);
- BUILTIN\Users:(RX);
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE);
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M);
- NT AUTHORITY\SYSTEM:(F);
- CREATOR OWNER:(OI)(CI)(IO)(F).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Подключение файлового ресурса Azure из командной строки

Используйте команду Windows **net use** для подключения файлового ресурса Azure. Не забудьте заменить значения заполнителей в следующем примере собственными значениями. Дополнительные сведения о подключении файловых ресурсов см. в статье [Использование общей папки Azure в Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>Настройка разрешений NTFS с помощью icacls
Используйте следующую команду Windows, чтобы предоставить полный набор разрешений для всех каталогов и файлов в файловом ресурсе, включая корневую папку. Не забудьте заменить значения заполнителей в примере собственными значениями.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Дополнительные сведения об использовании icacls для установки разрешений NTFS и о различных типах поддерживаемых разрешений см. [в справочнике по командной строке для icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Подключение файлового ресурса с виртуальной машины, присоединенной к домену

Следующий процесс проверяет правильность настройки учетных данных Azure AD и возможность доступа к файловому ресурсу Azure с виртуальной машины, присоединенной к домену: 

Войдите на виртуальную машину с помощью удостоверения Azure AD, которому предоставлены разрешения, как показано на следующем рисунке.

![Снимок экрана с окном входа в Azure AD для выполнения аутентификации пользователя](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Используйте следующую команду, чтобы подключить файловый ресурс Azure. Не забудьте заменить значения заполнителей собственными значениями. Так как вы уже прошли проверку подлинности, вам не нужно указывать ключ учетной записи хранения или имя пользователя и пароль Azure AD. Azure AD через SMB поддерживает единый вход с использованием учетных данных Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Вы успешно включили проверку подлинности Azure AD через SMB и назначили настраиваемую роль, которая предоставляет доступ к файловому ресурсу Azure с удостоверением Azure AD. Чтобы предоставить дополнительным пользователям доступ к общему файловому ресурсу, следуйте инструкциям в разделе [Назначение разрешений доступа удостоверению](#assign-access-permissions-to-an-identity) и [Настройка разрешений NTFS](#configure-ntfs-permissions-over-smb) в разделах SMB.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о службе файлов Azure и использовании Azure AD через SMB см. в следующих ресурсах:

- [Общие сведения о службе файлов Azure](storage-files-introduction.md)
- [Общие сведения о проверке подлинности Azure Active Directory по протоколу SMB для службы "Файлы Azure" (предварительная версия)](storage-files-active-directory-overview.md)
- [Часто задаваемые вопросы](storage-files-faq.md)
