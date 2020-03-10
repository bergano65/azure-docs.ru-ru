---
title: Включение проверки подлинности Active Directory в SMB для файлов Azure
description: Узнайте, как включить проверку подлинности на основе удостоверений по протоколу SMB для файловых ресурсов Azure с помощью Active Directory. Затем присоединенные к домену виртуальные машины Windows могут получить доступ к файловым ресурсам Azure с помощью учетных данных AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: rogarana
ms.openlocfilehash: 223906343c2391d03d8526026d3daad3174791ed
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362376"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Включение проверки подлинности Active Directory в SMB для файловых ресурсов Azure

Служба [файлов Azure](storage-files-introduction.md) поддерживает проверку подлинности на основе удостоверений через протокол SMB с помощью двух типов доменных служб: Azure Active Directory доменных служб (Azure AD DS) и Active Directory (AD) (Предварительная версия). В этой статье рассматривается новая (Предварительная версия) поддержка использования службы домен Active Directory для проверки подлинности в файловых ресурсах Azure. Если вы хотите включить проверку подлинности Azure AD DS (общедоступная версия) для файловых ресурсов Azure, см. [статью по этой теме](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Файловые ресурсы Azure поддерживают проверку подлинности только для одной службы домена: Azure Active Directory службы домена (Azure AD DS) или Active Directory (AD). 
>
> Удостоверения AD, используемые для проверки подлинности в общей папке Azure, должны быть синхронизированы с Azure AD. Синхронизация хэша паролей является необязательной. 
> 
> Проверка подлинности Active Directory не поддерживает проверку подлинности для учетных записей компьютеров, созданных в AD. 
> 
> Аутентификация AD может поддерживаться только для одного леса AD, в котором зарегистрирована учетная запись хранения. Доступ к общим файловым ресурсам Azure с учетными данными AD из одного леса AD по умолчанию можно получить только в одном лесу Active Directory. Если вам нужно получить доступ к файловому ресурсу Azure из другого леса, убедитесь, что настроено правильное доверие леса. Дополнительные сведения см. в разделе " [вопросы и ответы](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) ".  
> 
> Проверка подлинности Active Directory для доступа SMB и сохраняемости ACL поддерживается для файловых ресурсов Azure, управляемых Синхронизация файлов Azure.
>
> Служба файлов Azure поддерживает проверку подлинности Kerberos с помощью шифрования RC4-HMAC. Шифрование AES Kerberos пока не поддерживается.

При включении AD для файловых ресурсов Azure через SMB компьютеры, присоединенные к домену AD, могут подключать файловые ресурсы Azure с помощью существующих учетных данных AD. Эту возможность можно включить с помощью среды AD, размещенной на локальных компьютерах или размещенных в Azure.

Удостоверения AD, используемые для доступа к файловым ресурсам Azure, должны быть синхронизированы с Azure AD, чтобы обеспечить предоставление разрешений на доступ к файлам уровня общего доступа через стандартную модель [управления доступом на основе ролей (RBAC)](../../role-based-access-control/overview.md) . [Списки DACL в стиле Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) для файлов и каталогов, перенесенных из существующих файловых серверов, будут сохранены и применены. Эта функция обеспечивает тесную интеграцию с корпоративной инфраструктурой доменных служб Active Directory. При замене локальных файловых серверов на файловые ресурсы Azure существующие пользователи могут получить доступ к общим файловым ресурсам Azure с их текущих клиентов с помощью единого входа, не внося изменения в используемые учетные данные.  
 
## <a name="prerequisites"></a>Предварительные требования 

Перед включением проверки подлинности AD для файловых ресурсов Azure убедитесь, что выполнены следующие предварительные требования: 

- Выберите или создайте среду AD и синхронизируйте ее с Azure AD. 

    Эту функцию можно включить в новой или существующей среде AD. Удостоверения, используемые для доступа, должны быть синхронизированы с Azure AD. Клиент Azure AD и общая папка, к которым вы обращаетесь, должны быть связаны с одной и той же подпиской. 

    Сведения о настройке среды домена AD см. в статье [Обзор служб домен Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Если вы не синхронизируете AD с Azure AD, следуйте указаниям в статье [что такое Гибридная идентификация с Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md) , чтобы определить предпочтительный метод проверки подлинности и Azure AD Connect параметр установки. 

- Домен — присоединение локального компьютера или виртуальной машины Azure к AD (также называется AD DS). 

    Чтобы получить доступ к общей папке с помощью учетных данных AD с компьютера или виртуальной машины, устройство должно быть присоединено к домену Active Directory. Сведения о присоединении к домену AD см. в разделе [Присоединение компьютера к домену](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Выберите или создайте учетную запись хранения Azure в [поддерживаемом регионе](#regional-availability). 

    Убедитесь, что учетная запись хранения, содержащая общие файловые ресурсы, еще не настроена для проверки подлинности Azure AD DS. Если в учетной записи хранения включена проверка подлинности AD DS Azure для службы "файлы Azure", ее необходимо отключить перед изменением на использование AD. Это означает, что существующие списки управления доступом, настроенные в среде AD DS Azure, необходимо будет перенастроить для надлежащего применения разрешений.
    
    Сведения о создании общей папки см. [в статье Создание общей папки в службе файлов Azure](storage-how-to-create-file-share.md).
    
    Для оптимальной производительности рекомендуется развернуть учетную запись хранения в том же регионе, что и виртуальная машина, из которой планируется доступ к общей папке. 

- Проверьте подключение, подключив файловые ресурсы Azure с помощью ключа учетной записи хранения. 

    Чтобы убедиться, что устройство и общая папка настроены правильно, попробуйте подключить общую папку с помощью ключа учетной записи хранения. Дополнительные сведения см. в статье [использование файлового ресурса Azure с Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Доступность по регионам

Проверка подлинности Active Directory для файлов Azure (Предварительная версия) доступна в [большинстве общедоступных регионов](https://azure.microsoft.com/global-infrastructure/regions/).

Проверка подлинности Active Directory для службы файлов Azure недоступна в:
- Запад США
- западная часть США 2
- Восток США
- восточная часть США 2
- Западная Европа
- Северная Европа

## <a name="workflow-overview"></a>Обзор рабочего процесса

Перед включением проверки подлинности Active Directory через SMB для файловых ресурсов Azure рекомендуется выполнить [Предварительные требования](#prerequisites) и убедиться, что все действия выполнены. Предварительные требования проверяют правильность настройки сред хранения AD, Azure AD и службы хранилища Azure. 

Затем предоставьте доступ к ресурсам службы файлов Azure с помощью учетных данных AD: 

- Включите проверку подлинности AD для службы файлов Azure в учетной записи хранения.  

- Назначьте разрешения на доступ к общему ресурсу удостоверению Azure AD (пользователю, группе или субъекту-службе), который синхронизирован с целевым удостоверением AD. 

- Настройка списков управления доступом по протоколу SMB для каталогов и файлов. 

- Подключите файловый ресурс Azure из виртуальной машины, присоединенной к домену AD. 

На следующей схеме показан комплексный рабочий процесс для включения аутентификации Azure AD через SMB для файловых ресурсов Azure. 

![Схема рабочего процесса AD файлов](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Проверка подлинности AD через SMB для файловых ресурсов Azure поддерживается только на компьютерах или виртуальных машинах, работающих в версиях ОС более поздних, чем Windows 7 или Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Включение проверки подлинности AD для учетной записи 

Чтобы включить проверку подлинности Active Directory через SMB для файловых ресурсов Azure, необходимо сначала зарегистрировать учетную запись хранения в AD, а затем задать необходимые свойства домена в учетной записи хранения. Если эта функция включена в учетной записи хранения, она применяется ко всем новым и существующим файловым ресурсам в учетной записи. Чтобы включить эту функцию, используйте `join-AzStorageAccountForAuth`. Подробное описание рабочего процесса можно найти в разделе ниже. 

> [!IMPORTANT]
> Командлет `join-AzStorageAccountForAuth` внесет изменения в среду AD. Ознакомьтесь со следующим описанием, чтобы лучше понять, что именно необходимо для выполнения команды и что примененные изменения соответствуют политикам соответствия и безопасности. 

Командлет `join-AzStorageAccountForAuth` выполняет вызов, аналогичный присоединению автономного домена от имени указанной учетной записи хранения. Она создаст учетную запись в домене AD, [учетную запись компьютера](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) или [учетную запись входа в службу](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Созданная учетная запись AD представляет учетную запись хранения в домене AD. Если учетная запись AD создана в подразделении Active Directory, которая обеспечивает истечение срока действия пароля, необходимо обновить пароль до максимального срока действия пароля. Сбой при обновлении пароля учетной записи AD приведет к ошибкам проверки подлинности при доступе к файловым ресурсам Azure. Сведения об обновлении пароля см. в разделе [Обновление пароля учетной записи AD](#update-ad-account-password).

Для регистрации и включения функции можно использовать следующий скрипт. Кроме того, можно вручную выполнить операции, выполняемые сценарием. Эти операции описаны в разделе, следующем за сценарием. Вам не нужно выполнять оба действия.

### <a name="1-check-prerequisites"></a>1. Проверка предварительных требований
- [Скачайте и Распакуйте модуль Азфилешибрид](https://github.com/Azure-Samples/azure-files-samples/releases)
- Установите и выполните модуль на устройстве, присоединенном к домену AD, с учетными данными AD, имеющими разрешения на создание учетной записи входа в службу или учетной записи компьютера в целевом AD.
-  Запустите скрипт, используя учетные данные AD, которые синхронизируются с Azure AD. Учетные данные AD должны иметь разрешения владельца учетной записи хранения или роли RBAC участника.
- Убедитесь, что ваша учетная запись хранения находится в [поддерживаемом регионе](#regional-availability).

### <a name="2-execute-ad-enablement-script"></a>2. выполнение скрипта включения AD
Не забудьте заменить значения заполнителей собственными значениями в указанных ниже параметрах перед выполнением в PowerShell.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

#Register the target storage account with your active directory environment under the target OU
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationalUnitName "<ou-name-here>"
```

В следующем описании перечислены все действия, выполняемые при выполнении командлета `join-AzStorageAccountForAuth`. Вы можете выполнить эти действия вручную, если не хотите использовать команду:

> [!NOTE]
> Если вы уже выполнили приведенный выше сценарий Join-Азсторажеаккаунтфораус, перейдите к следующему разделу "3. Убедитесь, что функция включена. Операции, приведенные ниже, выполнять не нужно.

#### <a name="a-checking-environment"></a>а. Проверка среды

Во первых, он проверяет вашу среду. В частности, он проверяет, установлен ли [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) и выполняется ли оболочка с правами администратора. Затем он проверяет, установлен ли [модуль AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) , и устанавливает его, если это не так. Если эти проверки пройдены, он проверит ваше рекламное объявление на наличие [учетной записи компьютера](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (по умолчанию) или [учетной записи входа службы](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) , которая уже создана с именем субъекта-службы или UPN, как CIFS/My-Storage-Account-Name-here. File. Core. Windows. NET. Если учетная запись не существует, она будет создана, как описано в разделе b ниже.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>б. Создание удостоверения, представляющего учетную запись хранения в AD вручную

Чтобы создать эту учетную запись вручную, создайте ключ Kerberos для учетной записи хранения с помощью `New-AzStorageAccountKey -KeyName kerb1`. Затем используйте этот ключ Kerberos в качестве пароля для вашей учетной записи. Этот ключ используется только во время настройки и не может использоваться для операций управления или плоскости данных в учетной записи хранения.

Получив этот ключ, создайте учетную запись службы или компьютера в подразделении. Используйте следующую спецификацию: SPN: CIFS/My-Storage-Name-здесь. File. Core. Windows. NET "пароль: ключ Kerberos для вашей учетной записи хранения.

Если подразделение использует срок действия пароля, необходимо обновить пароль до максимального срока действия пароля, чтобы предотвратить сбои проверки подлинности при доступе к файловым ресурсам Azure. Дополнительные сведения см. в [статье об обновлении пароля учетной записи AD](#update-ad-account-password) .

Сохраним идентификатор безопасности только что созданной учетной записи, он понадобится для следующего шага.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>в. Включение функции в учетной записи хранения

Затем скрипт включит функцию в вашей учетной записи хранения. Чтобы выполнить эту установку вручную, укажите сведения о конфигурации для свойств домена в следующей команде, а затем запустите ее. Идентификатор безопасности учетной записи хранения, необходимый для следующей команды, — это идентификатор безопасности удостоверения, созданного в AD (см. раздел б выше).

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServiesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Убедитесь, что эта функция включена

Чтобы проверить, включена ли эта функция в вашей учетной записи хранения, можно использовать следующий скрипт:

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Вы успешно включили функцию в учетной записи хранения. Несмотря на то, что эта функция включена, вам все равно потребуется выполнить дополнительные действия, чтобы иметь возможность правильно использовать эту функцию.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Вы успешно включили проверку подлинности Active Directory через SMB и назначили настраиваемую роль, которая предоставляет доступ к общей папке Azure с удостоверением AD. Чтобы предоставить дополнительным пользователям доступ к общему файловому ресурсу, следуйте инструкциям в разделе [Назначение прав доступа](#assign-access-permissions-to-an-identity) для использования удостоверения и [Настройка разрешений NTFS](#configure-ntfs-permissions-over-smb) в разделах SMB.

## <a name="update-ad-account-password"></a>Обновление пароля учетной записи AD

Если вы зарегистрировали удостоверение AD или учетную запись, представляющую вашу учетную запись хранения в подразделении, которое обеспечивает срок действия пароля, необходимо сменить пароль до максимального срока. Если не обновить пароль учетной записи AD, это приведет к ошибкам проверки подлинности при доступе к общим файловым ресурсам Azure.  

Для активации смены пароля можно выполнить команду `Update-AzStorageAccountADObjectPassword` из модуля Азфилешибрид. Командлет выполняет такие действия, как смена ключа учетной записи хранения. Он получает второй ключ Kerberos учетной записи хранения и использует его для обновления пароля зарегистрированной учетной записи в AD. Затем он повторно создает целевой ключ Kerberos учетной записи хранения и обновляет пароль зарегистрированной учетной записи в AD. Этот командлет необходимо выполнить в среде, присоединенной к домену AD.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о службе файлов Azure и использовании AD через SMB см. в следующих ресурсах:

- [Обзор поддержки проверки подлинности на основе удостоверений в службе файлов Azure для доступа по протоколу SMB](storage-files-active-directory-overview.md)
- [Часто задаваемые вопросы](storage-files-faq.md)
