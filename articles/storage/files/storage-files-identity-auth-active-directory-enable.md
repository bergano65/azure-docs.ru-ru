---
title: Включение проверки подлинности Active Directory в SMB для файлов Azure
description: Узнайте, как включить проверку подлинности на основе удостоверений по протоколу SMB для файловых ресурсов Azure с помощью Active Directory. Затем присоединенные к домену виртуальные машины Windows могут получить доступ к файловым ресурсам Azure с помощью учетных данных AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: rogarana
ms.openlocfilehash: b2dd501344e1ea799db58ea749395aaed05d05f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106562"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Включение проверки подлинности локальных служб домен Active Directory Services по протоколу SMB для файловых ресурсов Azure

[Служба файлов](storage-files-introduction.md) Azure поддерживает проверку подлинности на основе удостоверений через протокол SMB с помощью двух типов доменных служб: Azure Active Directory доменных служб (Azure AD DS) и локальных домен Active Directory служб (AD DS) (Предварительная версия). В этой статье рассматривается новая (Предварительная версия) поддержка использования службы домен Active Directory для проверки подлинности в файловых ресурсах Azure. Если вы хотите включить проверку подлинности Azure AD DS (общедоступная версия) для файловых ресурсов Azure, см. [статью по этой теме](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Файловые ресурсы Azure поддерживают проверку подлинности только для одной службы домена: Azure Active Directory службы домена (Azure AD DS) или локальной домен Active Directory службы (AD DS). 
>
> Удостоверения AD DS, используемые для проверки подлинности в общей папке Azure, должны быть синхронизированы с Azure AD. Синхронизация хэша паролей является необязательной. 
> 
> Локальная AD DS проверка подлинности не поддерживает проверку подлинности учетных записей компьютеров, созданных в AD DS. 
> 
> Локальная проверка подлинности AD DS может поддерживаться только для одного леса AD, в котором зарегистрирована учетная запись хранения. Вы можете получить доступ к общим файловым ресурсам Azure только с помощью AD DS учетных данных из одного леса по умолчанию. Если вам нужно получить доступ к файловому ресурсу Azure из другого леса, убедитесь, что настроено правильное доверие леса. Дополнительные сведения см. в разделе " [вопросы и ответы](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) ".  
> 
> AD DS проверка подлинности для доступа SMB и сохраняемости ACL поддерживается для файловых ресурсов Azure, управляемых Синхронизация файлов Azure.
>
> Служба файлов Azure поддерживает проверку подлинности Kerberos с помощью шифрования RC4-HMAC. Шифрование AES Kerberos пока не поддерживается.

При включении AD DS для файловых ресурсов Azure по протоколу SMB компьютеры, присоединенные к AD DS, могут подключать файловые ресурсы Azure с помощью существующих учетных данных AD. Эту возможность можно включить с помощью AD DS среды, размещенной на локальных компьютерах или размещенных в Azure.

Удостоверения, используемые для доступа к файловым ресурсам Azure, должны быть синхронизированы с Azure AD для применения разрешений файла общего уровня через модель [управления доступом на основе ролей (RBAC)](../../role-based-access-control/overview.md) . [Списки DACL в стиле Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) для файлов и каталогов, перенесенных из существующих файловых серверов, будут сохранены и применены. Эта функция обеспечивает тесную интеграцию с корпоративной AD DSной средой. При замене локальных файловых серверов на файловые ресурсы Azure существующие пользователи могут получить доступ к общим файловым ресурсам Azure с их текущих клиентов с помощью единого входа, не внося изменения в используемые учетные данные.  

> [!NOTE]
> Чтобы помочь вам настроить проверку подлинности AD в службе файлов Azure для некоторых распространенных вариантов использования, мы опубликовали [два видео](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) с пошаговыми руководствами.
> - Замена локальных файловых серверов файлами Azure (включая установку на частном канале для файлов и аутентификации Active Directory)
> - Использование файлов Azure в качестве контейнера профилей для виртуальных рабочих столов Windows (включая установку на основе проверки подлинности AD и конфигурации Фслогикс)

## <a name="prerequisites"></a>Предварительные условия 

Перед включением проверки подлинности AD DS для файловых ресурсов Azure убедитесь, что выполнены следующие предварительные требования: 

- Выберите или создайте среду AD DS и [синхронизируйте ее с Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    Эту функцию можно включить в новой или существующей локальной среде AD DS. Удостоверения, используемые для доступа, должны быть синхронизированы с Azure AD. Клиент Azure AD и общая папка, к которым вы обращаетесь, должны быть связаны с одной и той же подпиской. 

    Сведения о настройке среды домена AD см. в статье [Обзор служб домен Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Если вы не синхронизируете AD с Azure AD, следуйте указаниям в статье [Azure AD Connect и Azure AD Connect Health плана установки](../../active-directory/hybrid/how-to-connect-install-roadmap.md) , чтобы настроить и настроить Azure AD Connect. 

- Домен — присоединение локального компьютера или виртуальной машины Azure к локальной AD DS. 

    Чтобы получить доступ к общей папке с помощью учетных данных AD с компьютера или виртуальной машины, устройство должно быть присоединено к домену AD DS. Сведения о том, как присоединиться к домену, см. в разделе [Присоединение компьютера к домену](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Выберите или создайте учетную запись хранения Azure в [поддерживаемом регионе](#regional-availability). 

    Убедитесь, что учетная запись хранения, содержащая общие файловые ресурсы, еще не настроена для проверки подлинности Azure AD DS. Если в учетной записи хранения включена проверка подлинности AD DS Azure для службы "файлы Azure", ее необходимо отключить, прежде чем переходить к использованию локальной AD DS. Это означает, что существующие списки управления доступом, настроенные в среде AD DS Azure, необходимо будет перенастроить для надлежащего применения разрешений.
    
    Сведения о создании общей папки см. [в статье Создание общей папки в службе файлов Azure](storage-how-to-create-file-share.md).
    
    Для оптимальной производительности рекомендуется развернуть учетную запись хранения в том же регионе, что и виртуальная машина, из которой планируется доступ к общей папке. 

- Проверьте подключение, подключив файловые ресурсы Azure с помощью ключа учетной записи хранения. 

    Чтобы убедиться, что устройство и общая папка настроены правильно, попробуйте подключить общую [папку](storage-how-to-use-files-windows.md) с помощью ключа учетной записи хранения. Если при подключении к службе файлов Azure возникают проблемы, обратитесь к [средству устранения неполадок, опубликованному для ошибок подключения к службе файлов Azure в Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5). Мы также предоставляем [рекомендации](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) по обойти сценарии, когда порт 445 заблокирован. 

## <a name="regional-availability"></a>Доступность по регионам

Проверка подлинности файлов Azure с помощью AD DS (Предварительная версия) доступна во [всех общедоступных регионах и в регионах Azure gov](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="workflow-overview"></a>Обзор рабочего процесса

Перед включением проверки подлинности AD DS по протоколу SMB для файловых ресурсов Azure рекомендуется прочитать и завершить раздел [необходимых компонентов](#prerequisites) . Предварительные требования проверяют правильность настройки сред хранения AD, Azure AD и службы хранилища Azure. 

Если планируется включить любые сетевые конфигурации в общем файловом ресурсе, рекомендуется оценить [Сетевое](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) отношение и сначала выполнить связанную конфигурацию, прежде чем включать проверку подлинности AD DS.

Затем выполните следующие действия, чтобы настроить службу файлов Azure для проверки подлинности AD. 

1. Включите проверку подлинности AD DS файлов Azure в учетной записи хранения. 

2. Назначьте разрешения на доступ к общему ресурсу удостоверению Azure AD (пользователю, группе или субъекту-службе), который синхронизирован с целевым удостоверением AD. 

3. Настройка списков управления доступом по протоколу SMB для каталогов и файлов. 
 
4. Подключите файловый ресурс Azure к виртуальной машине, присоединенной к AD DS. 

5. Обновите пароль для удостоверения учетной записи хранения в AD DS.

На следующей схеме показан комплексный рабочий процесс для включения аутентификации Azure AD через SMB для файловых ресурсов Azure. 

![Схема рабочего процесса AD файлов](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> AD DS проверка подлинности по протоколу SMB для файловых ресурсов Azure поддерживается только на компьютерах или виртуальных машинах, работающих в версиях ОС более поздних, чем Windows 7 или Windows Server 2008 R2. 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. Включение проверки подлинности AD для учетной записи 

Чтобы включить AD DSную проверку подлинности в SMB для файловых ресурсов Azure, необходимо сначала зарегистрировать учетную запись хранения в AD DS, а затем задать необходимые свойства домена в учетной записи хранения. Если эта функция включена в учетной записи хранения, она применяется ко всем новым и существующим файловым ресурсам в учетной записи. Используйте `join-AzStorageAccountForAuth` , чтобы включить эту функцию. Подробное описание сквозного рабочего процесса можно найти в скрипте в этом разделе. 

> [!IMPORTANT]
> `Join-AzStorageAccountForAuth` Командлет внесет изменения в среду AD. Ознакомьтесь со следующим описанием, чтобы лучше понять, что именно необходимо для выполнения команды и что примененные изменения соответствуют политикам соответствия и безопасности. 

`Join-AzStorageAccountForAuth` Командлет выполняет вызов, аналогичный присоединению автономного домена от имени указанной учетной записи хранения. Сценарий использует командлет для создания учетной записи в домене AD, [учетной записи компьютера](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (по умолчанию) или [учетной записи входа службы](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Если вы решите сделать это вручную, выберите учетную запись, которая лучше всего подходит для вашей среды.

Учетная запись AD DS, созданная командлетом, представляет учетную запись хранения в домене AD. Если учетная запись AD DS создана в подразделении (OU), которая обеспечивает истечение срока действия пароля, необходимо обновить пароль до максимального срока действия пароля. Сбой при обновлении пароля учетной записи приведет к ошибкам проверки подлинности при доступе к общим файловым ресурсам Azure. Сведения об обновлении пароля см. в разделе [Обновление пароля учетной записи AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds).

Для регистрации и включения функции можно использовать следующий скрипт. Кроме того, можно вручную выполнить операции, выполняемые сценарием. Эти операции описаны в разделе, следующем за сценарием. Вам не нужно выполнять оба действия.

### <a name="11-script-prerequisites"></a>Предварительные требования для сценариев 1,1
- [Скачайте и Распакуйте модуль Азфилешибрид](https://github.com/Azure-Samples/azure-files-samples/releases)
- Установите и выполните модуль на устройстве, присоединенном к локальному AD DS с учетными данными AD DS, имеющими разрешения на создание учетной записи входа в службу или учетной записи компьютера в целевом AD.
-  Запустите скрипт, используя локальные учетные данные AD DS, которые синхронизируются с Azure AD. Локальные учетные данные AD DS должны иметь разрешения владельца учетной записи хранения или роли RBAC участника.
- Убедитесь, что ваша учетная запись хранения находится в [поддерживаемом регионе](#regional-availability).

### <a name="12-domain-join-your-storage-account"></a>1,2. присоединение домена к учетной записи хранения
Не забудьте заменить значения заполнителей собственными значениями в указанных ниже параметрах перед выполнением в PowerShell.
> [!IMPORTANT]
> Командлет присоединение к домену создаст учетную запись AD для представления учетной записи хранения (общей папки) в AD. Вы можете зарегистрировать учетную запись компьютера или учетную запись входа службы. Дополнительные сведения см. в разделе [часто задаваемые вопросы](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) . Для учетных записей компьютеров срок действия пароля по умолчанию задается в AD через 30 дней. Аналогичным образом для учетной записи входа в службу может быть задан срок действия пароля по умолчанию в домене AD или подразделении (OU).
> Для обоих типов учетных записей настоятельно рекомендуется проверить срок действия пароля, настроенный в среде AD, и запланировать [Обновление пароля удостоверения учетной записи хранения в AD](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) учетной записи AD, приведенной ниже, до максимального срока действия пароля. Сбой при обновлении пароля учетной записи AD приведет к ошибкам проверки подлинности при доступе к файловым ресурсам Azure. Вы можете [создать новое подразделение Active Directory (OU) в AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) и отключить политику истечения срока действия паролей для [учетных записей компьютеров](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) или учетных записей службы входа в систему. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
#You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount"
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

В следующем описании перечислены все действия, `Join-AzStorageAccountForAuth` выполняемые при выполнении командлета. Вы можете выполнить эти действия вручную, если не хотите использовать команду:

> [!NOTE]
> Если вы уже выполнили приведенный выше `Join-AzStorageAccountForAuth` сценарий, перейдите к следующему разделу "1,3. Убедитесь, что эта функция включена". Операции, приведенные ниже, выполнять не нужно.

#### <a name="a-checking-environment"></a>a. Проверка среды

Во первых, сценарий проверяет вашу среду. В частности, он проверяет, установлен ли [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) и выполняется ли оболочка с правами администратора. Затем он проверяет, установлен ли [модуль AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) , и устанавливает его, если это не так. Если эти проверки пройдены, то проверяется AD DS, чтобы узнать, есть ли [учетная запись компьютера](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (по умолчанию) или [учетная запись входа службы](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) , которая уже создана с именем субъекта-службы или UPN, как CIFS/My-Storage-Account-Name-here. File. Core. Windows. NET. Если учетная запись не существует, она будет создана, как описано в разделе b ниже.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Создание удостоверения, представляющего учетную запись хранения в AD вручную

Чтобы создать эту учетную запись вручную, создайте ключ Kerberos для учетной записи хранения `New-AzStorageAccountKey -KeyName kerb1`с помощью. Затем используйте этот ключ Kerberos в качестве пароля для вашей учетной записи. Этот ключ используется только во время настройки и не может использоваться для операций управления или плоскости данных в учетной записи хранения.

Получив этот ключ, создайте учетную запись службы или компьютера в подразделении. Используйте следующую спецификацию: SPN: CIFS/My-Storage-Name-здесь. File. Core. Windows. NET "пароль: ключ Kerberos для вашей учетной записи хранения.

Если подразделение использует срок действия пароля, необходимо обновить пароль до максимального срока действия пароля, чтобы предотвратить сбои проверки подлинности при доступе к файловым ресурсам Azure. Дополнительные сведения см. [в статье об обновлении пароля учетной записи хранения в AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) .

Сохраним идентификатор безопасности только что созданной учетной записи, он понадобится для следующего шага. Созданное удостоверение, представляющее учетную запись хранения, не требуется синхронизировать с Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Включение функции в учетной записи хранения

Затем скрипт включит функцию в вашей учетной записи хранения. Чтобы выполнить эту установку вручную, укажите сведения о конфигурации для свойств домена в следующей команде, а затем запустите ее. Идентификатор безопасности учетной записи хранения, необходимый для следующей команды, — это идентификатор безопасности удостоверения, созданного в AD DS в [предыдущем разделе](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="13-confirm-that-the-feature-is-enabled"></a>1,3. Убедитесь, что функция включена

Чтобы проверить, включена ли эта функция в вашей учетной записи хранения, можно использовать следующий скрипт:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Вы успешно включили функцию в учетной записи хранения. Теперь, когда эта функция включена, для использования этой функции необходимо выполнить дополнительные действия.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Теперь вы успешно включили проверку подлинности AD DS по протоколу SMB и назначили настраиваемую роль, которая предоставляет доступ к файловому ресурсу Azure с удостоверением AD DS. Чтобы предоставить дополнительным пользователям доступ к общему файловому ресурсу, следуйте инструкциям в разделе [Назначение прав доступа](#2-assign-access-permissions-to-an-identity) для использования удостоверения и [Настройка разрешений NTFS](#3-configure-ntfs-permissions-over-smb) в разделах SMB.

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. Обновите пароль для удостоверения учетной записи хранения в AD DS

Если вы зарегистрировали удостоверение AD DS или учетную запись, представляющую вашу учетную запись хранения в подразделении, которое обеспечивает срок действия пароля, необходимо сменить пароль до максимального срока. Сбой при обновлении пароля учетной записи AD DS приведет к ошибкам проверки подлинности при доступе к общим файловым ресурсам Azure.  

Для активации смены пароля можно выполнить `Update-AzStorageAccountADObjectPassword` команду из модуля азфилешибрид. Командлет выполняет такие действия, как смена ключа учетной записи хранения. Он получает второй ключ Kerberos учетной записи хранения и использует его для обновления пароля зарегистрированной учетной записи в AD DS. Затем он повторно создает целевой ключ Kerberos учетной записи хранения и обновляет пароль зарегистрированной учетной записи в AD DS. Этот командлет необходимо выполнить в локальной среде, присоединенной к домену AD DS.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о службе файлов Azure и использовании AD через SMB см. в следующих ресурсах:

- [Обзор поддержки проверки подлинности на основе удостоверений в службе файлов Azure для доступа по протоколу SMB](storage-files-active-directory-overview.md)
- [Вопросы и ответы](storage-files-faq.md)
