---
title: Включить активную проверку подлинности каталога по сравнению с SMB для файлов Azure
description: Узнайте, как включить проверку подлинности на основе идентификации по сравнению с SMB для файлов Azure через Active Directory. Виртуальные машины Windows, присоединенные к домену(VMs), могут получать доступ к файловым долям Azure, используя учетные данные AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: rogarana
ms.openlocfilehash: 0bf8960f1e97de45d5369f69c698311d0b4e3dbb
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584514"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Включить активную проверку подлинности каталога по сравнению с SMB для файлов Azure

[Azure Files](storage-files-introduction.md) поддерживает аутентификацию на основе идентификации по block Server Message Block (SMB) через два типа служб домена: Службы домена Azure Active Directory Domain (Azure AD DS) (GA) и Active Directory (AD) (предварительный просмотр). В этой статье основное внимание уделяется недавно введенной (предварительной) поддержке службы доменов Active Directory для проверки подлинности для файлов Azure. Если вы заинтересованы в включении проверки подлинности Azure AD DS (GA) для файлов Azure, обратитесь к [нашей статье по этому вопросу.](storage-files-identity-auth-active-directory-domain-service-enable.md)

> [!NOTE]
> Файлы Azure поддерживают аутентификацию только в отношении одной службы домена, либо службы активных доменов Azure Active Directory (Azure AD DS) или Active Directory (AD). 
>
> Идентификаторы DD, используемые для проверки подлинности файла Azure, должны быть синхронизированы с Azure AD. Синхронизация хэша паролей не является обязательной. 
> 
> Аутентификация AD не поддерживает аутентификацию в отношении учетных записей компьютеров, созданных в AD. 
> 
> Аутентификация AD может быть поддержана только в одном лесу AD, где зарегистрирована учетная запись хранилища. Доступ к файлам Azure можно получить только с учетными данными AD из одного леса AD по умолчанию. Если вам необходимо получить доступ к файлу Azure из другого леса, убедитесь, что у вас есть надлежащий лесной траст, [см.](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)  
> 
> Проверка подлинности AD для доступа к SMB и сохранения ACL поддерживается для файлов Azure, управляемых Azure File Sync.
>
> Azure Files поддерживает аутентификацию Kerberos с помощью шифрования AD с помощью RC4-HMAC. Шифрование AES Kerberos пока не поддерживается.

При вмененных aD-файлов Azure через SMB, объединенные в домены AD компьютеры могут монтировать файлы Azure, используя существующие учетные данные AD. Эта возможность может быть включена с помощью среды AD, размещенной либо в машинах на прем,или в Azure.

Идентификаторы DD, используемые для доступа к файловым долям Azure, должны быть синхронизированы с Azure AD для обеспечения соблюдения разрешений файлов уровня общего доступа через стандартную [ролевую модель управления доступом (RBAC).](../../role-based-access-control/overview.md) [DACLs в стиле Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) на файлах/каталогах, переносимых с существующих файловых серверов, будут сохранены и приведены в исполнение. Эта функция обеспечивает бесшовную интеграцию с инфраструктурой домена AD предприятия. При замене файловых серверов на преме на файлы Azure существующие пользователи могут получать доступ к файлам Azure от своих текущих клиентов с одним опытом вхождения без каких-либо изменений в учетных данных, накоторых они используются.  
 
## <a name="prerequisites"></a>Предварительные требования 

Прежде чем включить аутентификацию AD для файлов Azure, убедитесь, что вы выполнили следующие предпосылки: 

- Выберите или создайте среду AD и синхронизируйте ее с Azure AD. 

    Функцию можно включить в новую или существующую среду AD. Идентификаторы, используемые для доступа, должны быть синхронизированы с Azure AD. Арендатор Azure AD и раздел файла, к которым вы хотите получить доступ, должны быть связаны с одной и той же подпиской. 

    Для настройки среды домена AD обратитесь в [обзор Службы доменов Active Directory Domain.](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) Если вы не синхронизировали AD с Azure AD, следуйте инструкциям в [«Что такое гибридный идентификатор» с Активным каталогом Azure?,](../../active-directory/hybrid/whatis-hybrid-identity.md) чтобы определить предпочтительный метод проверки подлинности и опцию настройки Azure AD Connect. 

- Домен-присоединение к предприимчезной машине или Azure VM к AD (также называемому AD DS). 

    Чтобы получить доступ к файлу, используя учетные данные AD из машины или VM, устройство должно быть присоединено к AD. Для получения информации о том, как домен-присоединиться к AD, обратитесь [к Присоединиться к компьютеру домена](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Выберите или создайте учетную запись хранения Azure в [поддерживаемом регионе.](#regional-availability) 

    Убедитесь, что учетная запись хранения, содержащая ваши файлы, еще не настроена для azure AD DS Authentication. Если в учетной записи хранилища включена аутентификация Azure Files AD DD, ее необходимо отключить перед изменением использования AD. Это означает, что существующие AC-центры, настроенные в среде Azure AD DS, должны быть перенастроены для правильного обеспечения соблюдения разрешений.
    
    Для получения информации о создании нового файла можно [узнать о создании раздела файлов в Azure Files.](storage-how-to-create-file-share.md)
    
    Для оптимальной производительности мы рекомендуем развернуть учетную запись хранилища в том же регионе, что и VM, с которого вы планируете получить доступ к акции. 

- Проверьте подключение путем установки файлов Azure с помощью ключа учетной записи хранилища. 

    Чтобы убедиться, что устройство и доля файлов настроены правильно, попробуйте установить долю файла с помощью ключа учетной записи хранилища. Для получения дополнительной информации [см.](storage-how-to-use-files-windows.md)

## <a name="regional-availability"></a>Доступность по регионам

Проверка подлинности AD AD Azure Files (предварительный просмотр) доступна во [всех регионах в общедоступном облаке.](https://azure.microsoft.com/global-infrastructure/regions/)

## <a name="workflow-overview"></a>Обзор рабочего процесса

Прежде чем включить aD Authentication по сравнению с SMB для файлов Azure, мы рекомендуем вам пройти через [предпосылки](#prerequisites) и убедиться, что вы выполнили все этапы. Предпосылки подтверждают правильное настройку сред AD, Azure AD и Azure Storage. 

Далее предоставите доступ к ресурсам Azure Files с учетными данными AD: 

- Включить аутентификацию AD файлов Azure в учетной записи хранения.  

- Присвоить разрешения доступа для доступа к идентации Azure AD (пользователь, группа или основной службы), которая синхронизируется с целевым идентатором AD. 

- Налаживание ACL по сравнению с SMB для каталогов и файлов. 

- Установите совместное приложение Azure из домена AD, примкнувке к VM. 

Следующая диаграмма иллюстрирует сквозной рабочий процесс для обеспечения аутентификации Azure AD по сравнению с SMB для файлов Azure. 

![Диаграмма рабочего процесса AD файлов](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Проверка подлинности AD над SMB для файлов Azure поддерживается только на машинах или vMs, работающих на версиях ОС, более новых, чем Windows 7 или Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Включить аутентификацию AD для вашей учетной записи 

Чтобы включить аутентификацию AD по сравнению с SMB для файлов Azure, необходимо сначала зарегистрировать учетную запись хранилища в AD, а затем установить необходимые свойства домена на учетной записи хранилища. Когда функция включена на учетной записи хранилища, она применяется ко всем новым и существующим файловым долям в учетной записи. Используйте `join-AzStorageAccountForAuth` для включения функции. Подробное описание сквозного рабочего процесса можно найти в разделе ниже. 

> [!IMPORTANT]
> Cmdlet `Join-AzStorageAccountForAuth` внесет изменения в вашу среду AD. Прочитайте следующее объяснение, чтобы лучше понять, что он делает, чтобы убедиться, что у вас есть соответствующие разрешения для выполнения команды и что применяемые изменения согласуются с политиками соответствия и безопасности. 

Cmdlet `Join-AzStorageAccountForAuth` выполнит эквивалент автономного домена, примкнувного к указанному счету хранения. Это создаст учетную запись в домене AD, либо [учетную запись компьютера](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) или [учетную запись журнала службы.](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) Созданная учетная запись AD представляет учетную запись хранилища в домене AD. Если учетная запись AD создана в рамках организационной группы AD (OU), которая обеспечивает истечение срока действия пароля, необходимо обновить пароль до достижения максимального возраста пароля. Неспособность обновить пароль учетной записи AD приведет к сбоям в проверке подлинности при доступе к файлам Azure. Чтобы узнать, как обновить пароль, [см.](#update-ad-account-password)

Вы можете использовать следующий скрипт для выполнения регистрации и включения функции или, в качестве альтернативы, вы можете вручную выполнять операции, которые будет скрипт. Эти операции описаны в разделе, следуя сценарию. Вам не нужно делать и то, и другое.

### <a name="1-check-prerequisites"></a>1. Проверка предпосылок
- [Скачать и распаковать модуль AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Установка и выполнение модуля в устройстве, которое является доменом, объединенным с AD-учетными данными, которые имеют разрешения на создание учетной записи входа в службу услуг или учетную запись компьютера в целевой AD.
-  Запустите скрипт с помощью учетных данных AD, синхронизированных с Azure AD. Учетные данные AD должны иметь либо разрешения владельца учетной записи хранилища, либо разрешения на роль участника RBAC.
- Убедитесь, что ваша учетная запись хранилища находится в [поддерживаемом регионе.](#regional-availability)

### <a name="2-domain-join-your-storage-account"></a>2. Домен присоединяется к вашей учетной записи хранилища
Не забудьте заменить значения заполнителя на ваши собственные в параметрах ниже, прежде чем выполнять его в PowerShell.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
Join-AzStorageAccountForAuth `
        -ResourceGroupName "<resource-group-name-here>" `
        -Name "<storage-account-name-here>" `
        -DomainAccountType "ComputerAccount" `
        -OrganizationalUnitName "<ou-name-here>"
```

В следующем описании суммируются все действия, выполняемые при выполнении `Join-AzStorageAccountForAuth` cmdlet. Вы можете выполнять эти действия вручную, если вы предпочитаете не использовать команду:

> [!NOTE]
> Если вы уже успешно `Join-AzStorageAccountForAuth` выполнили сценарий выше, перейдите к следующему разделу "3. Подтвердите, что функция включена». Вам не нужно выполнять операции ниже снова.

#### <a name="a-checking-environment"></a>а. Проверка среды

Во-первых, он проверяет вашу среду. В частности, он проверяет, установлен ли [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) и выполняется ли оболочка с привилегиями администратора. Затем он проверяет, установлен ли [модуль Az.Storage 1.11.1-preview,](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) и устанавливает его, если это не так. Если эти проверки проходят, то он будет проверять ваш AD, чтобы увидеть, если есть либо [учетная запись компьютера](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (по умолчанию) или [служба логин счета,](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) который уже был создан с SPN / UPN как "cifs / ваш-хранение-аккаунт-имя-here.file.core.windows.net". Если учетная запись не существует, она создаст один, как описано в разделе b ниже.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Создание удостоверения, представляющего учетную запись хранилища в AD вручную

Чтобы создать эту учетную запись вручную, создайте новый ключ kerberos для вашей учетной записи хранения с помощью. `New-AzStorageAccountKey -KeyName kerb1` Затем используйте ключ kerberos в качестве пароля для вашей учетной записи. Этот ключ используется только во время настройки и не может использоваться для любых операций плоскости управления или данных против учетной записи хранилища.

Если у вас есть этот ключ, создайте либо учетную запись службы или компьютера под OU. Используйте следующую спецификацию: SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Пароль: ключ Kerberos для вашей учетной записи хранения.

Если ваш OU обеспечивает истечение пароля, необходимо обновить пароль до достижения максимального возраста пароля, чтобы предотвратить сбои в проверке подлинности при доступе к файлам Azure. Подробнее о том, как прочитать [пароль учетной записи Update AD.](#update-ad-account-password)

Храните SID вновь созданной учетной записи, она вам понадобится для следующего шага. Только что созданный AD-иноеудостоверения, представляющий учетную запись хранения, не требуется синхронизировать с Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Включить функцию в учетной записи хранилища

Затем скрипт включит функцию в учетной записи хранилища. Для выполнения этой настройки вручную предоставьте некоторые сведения о конфигурации для свойств домена в следующей команде, а затем запустите ее. Учетная запись хранилища SID, требуемая в следующей команде, — это SID созданной вами идентификации в AD (раздел b выше).

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


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Подтвердите, что функция включена

Вы можете проверить, включена ли функция в учетной записи хранилища, вы можете использовать следующий скрипт:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Теперь вы успешно включили функцию в учетную запись хранения. Несмотря на то, что функция включена, вам все равно нужно выполнить дополнительные действия, чтобы иметь возможность правильно использовать эту функцию.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Теперь вы успешно включили аутентификацию AD по сравнению с SMB и назначили пользовательскую роль, которая обеспечивает доступ к файлу Azure с идентификацией AD. Чтобы предоставить дополнительный доступ пользователям к вашей совместной части файла, следуйте инструкциям в [разрешении доступа Назначить](#assign-access-permissions-to-an-identity) использовать идентификационные данные и настроить разрешения NTFS по разделам [SMB.](#configure-ntfs-permissions-over-smb)

## <a name="update-ad-account-password"></a>Обновление пароля учетной записи AD

Если вы зарегистрировали идентификационный знак/аккаунт AD, представляющий вашу учетную запись хранения в соответствии с OU, которая обеспечивает соблюдение срока действия пароля, необходимо повернуть пароль до достижения максимального возраста пароля. Неспособность обновить пароль учетной записи AD приведет к сбоям в проверке подлинности при доступе к файлам Azure.  

Чтобы вызвать вращение пароля, можно запустить `Update-AzStorageAccountADObjectPassword` команду из модуля AzFilesHybrid. Cmdlet выполняет действия, аналогичные вращению ключа ключа учетной записи хранилища. Он получает второй ключ Kerberos учетной записи и использует его для обновления пароля зарегистрированной учетной записи в AD. Затем он регенерирует целевой ключ Kerberos учетной записи хранения и обновляет пароль зарегистрированной учетной записи в AD. Вы должны запустить этот cmdlet в домене AD, объединенном среде.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительной информации о Файлах Azure и о том, как использовать AD над SMB, см.

- [Обзор поддержки аутентификации на основе файлов Azure на основе SMB для доступа к SMB](storage-files-active-directory-overview.md)
- [Вопросы и ответы](storage-files-faq.md)
