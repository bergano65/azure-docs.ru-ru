---
title: Включить активную проверку подлинности каталога по сравнению с SMB для файлов Azure
description: Узнайте, как включить проверку подлинности на основе идентификации по сравнению с SMB для файлов Azure через Active Directory. Виртуальные машины Windows, присоединенные к домену(VMs), могут получать доступ к файловым долям Azure, используя учетные данные AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: rogarana
ms.openlocfilehash: 44debc299054568769bfbe6cfc089cc528594274
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677075"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Включить проверку подлинности доменных служб Active Directory Domain Services по сравнению с SMB для файлов Azure

[Azure Files](storage-files-introduction.md) поддерживает аутентификацию на основе идентификации по сравнению с блоком сообщений сервера (SMB) через два типа служб домена: службы домена Active Directory (Azure AD DS) и на местные службы домена Active Directory Domain Services (AD DS) (предварительный просмотр). В этой статье основное внимание уделяется недавно введенной (предварительной) поддержке службы доменов Active Directory для проверки подлинности для файлов Azure. Если вы заинтересованы в включении проверки подлинности Azure AD DS (GA) для файлов Azure, обратитесь к [нашей статье по этому вопросу.](storage-files-identity-auth-active-directory-domain-service-enable.md)

> [!NOTE]
> Файлы Azure поддерживают аутентификацию только в отношении одной службы домена, либо службы активных доменов Azure Active Directory Domain Service (Azure AD DS) или на месте Active Directory Domain Services (AD DS). 
>
> Идентификаторы DS, используемые для проверки подлинности файлов Azure, должны быть синхронизированы с Azure AD. Синхронизация хэша паролей не является обязательной. 
> 
> Проверка подлинности AD DS не поддерживает аутентификацию в отношении учетных записей компьютеров, созданных в AD DS. 
> 
> На территории AD DS аутентификация может быть поддержана только в отношении одного леса AD, где зарегистрирована учетная запись хранилища. Доступ к файлам Azure можно получить только с учетными данными DS AD из одного леса по умолчанию. Если вам необходимо получить доступ к файлу Azure из другого леса, убедитесь, что у вас есть надлежащий лесной траст, [см.](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)  
> 
> Аутентификация AD DS для доступа к SMB и сохранение ACL поддерживается для файлов Azure, управляемых Azure File Sync.
>
> Azure Files поддерживает аутентификацию Kerberos с помощью шифрования AD с помощью RC4-HMAC. Шифрование AES Kerberos пока не поддерживается.

При включании AD DS для файлов Azure через SMB, при стыковые к AD DS машины могут монтировать файлы Azure, используя существующие учетные данные AD. Эта возможность может быть включена с помощью среды AD DS, размещенной либо в машинах на прем,или в Azure.

Идентификаторы, используемые для доступа к файловым долям Azure, должны быть синхронизированы с Azure AD для обеспечения соблюдения разрешений файлов уровня общего доступа с помощью модели [управления доступом на основе ролей (RBAC).](../../role-based-access-control/overview.md) [DACLs в стиле Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) на файлах/каталогах, переносимых с существующих файловых серверов, будут сохранены и приведены в исполнение. Эта функция обеспечивает бесшовную интеграцию с вашей корпоративной средой AD DS. При замене файловых серверов на преме на файлы Azure существующие пользователи могут получать доступ к файлам Azure от своих текущих клиентов с одним опытом вхождения без каких-либо изменений в учетных данных, накоторых они используются.  

> [!NOTE]
> Чтобы помочь настроить аутентификацию Azure Files AD для некоторых распространенных случаев использования, мы опубликовали [два видео](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) с шаг за шагом:
> - Замена файловых серверов на файлы Azure (включая настройку на частной ссылке для файлов и аутентификацию AD)
> - Использование файлов Azure в качестве контейнера профиля для виртуального рабочего стола Windows (включая настройку аутентификации AD и конфигурацию FsLogix)

## <a name="prerequisites"></a>Предварительные требования 

Прежде чем включить аутентификацию AD DS для файлов Azure, убедитесь, что вы выполнили следующие предпосылки: 

- Выберите или создайте среду AD DS и [синхронизируйте ее с Azure AD.](../../active-directory/hybrid/how-to-connect-install-roadmap.md) 

    Функцию можно включить в новую или существующую среду AD DS. Идентификаторы, используемые для доступа, должны быть синхронизированы с Azure AD. Арендатор Azure AD и раздел файла, к которым вы хотите получить доступ, должны быть связаны с одной и той же подпиской. 

    Чтобы настроить среду домена AD, обратитесь к [Обзору служб доменов Active Directory Domain.](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) Если вы не синхронизировали AD с Azure AD, следуйте инструкциям в [Azure AD Connect и Azure AD Connect Health,](../../active-directory/hybrid/how-to-connect-install-roadmap.md) чтобы настроить и настроить Azure AD Connect. 

- Домен-присоединение к предприимчезной машине или Azure VM к предприимчезной AD DS. 

    Чтобы получить доступ к файлу, используя учетные данные AD из машины или VM, устройство должно быть соединено с AD DS. Для получения информации о том, как к домену-соединению, обратитесь [к Присоединиться к компьютеру домена](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Выберите или создайте учетную запись хранения Azure в [поддерживаемом регионе.](#regional-availability) 

    Убедитесь, что учетная запись хранения, содержащая ваши файлы, еще не настроена для azure AD DS Authentication. Если аутентификация Azure Files Azure AD DD включена в учетную запись хранения, ее необходимо отключить перед изменением в использовании припредетенного AD DS. Это означает, что существующие AC-центры, настроенные в среде Azure AD DS, должны быть перенастроены для правильного обеспечения соблюдения разрешений.
    
    Для получения информации о создании нового файла можно [узнать о создании раздела файлов в Azure Files.](storage-how-to-create-file-share.md)
    
    Для оптимальной производительности мы рекомендуем развернуть учетную запись хранилища в том же регионе, что и VM, с которого вы планируете получить доступ к акции. 

- Проверьте подключение путем установки файлов Azure с помощью ключа учетной записи хранилища. 

    Чтобы убедиться, что устройство и доля файлов настроены правильно, попробуйте установить долю файла с помощью ключа учетной записи хранилища. Для получения дополнительной информации [см.](storage-how-to-use-files-windows.md)

## <a name="regional-availability"></a>Доступность по регионам

Аутентификация файлов Azure с помощью AD DS (предварительный просмотр) доступна во [всех публичных регионах и регионах Azure Gov.](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="workflow-overview"></a>Обзор рабочего процесса

Прежде чем включить аутентификацию AD DS по сравнению с SMB для файлов Azure, мы рекомендуем вам прочитать и завершить раздел [предпосылки.](#prerequisites) Предпосылки подтверждают правильное настройку сред AD, Azure AD и Azure Storage. 

Если вы планируете включить какие-либо сетевые конфигурации в вашей доле файла, мы рекомендуем вам сначала оценить [рассмотрение сети](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) и завершить соответствующую конфигурацию, прежде чем включить аутентификацию AD DS.

Далее выполните ниже приведенные ниже шаги по настройке файлов Azure для аутентификации АД: 

1. Включить проверку подлинности AD DS файлов Azure в учетной записи хранения. 

2. Присвоить разрешения доступа для доступа к идентации Azure AD (пользователь, группа или основной службы), которая синхронизируется с целевым идентатором AD. 

3. Налаживание ACL по сравнению с SMB для каталогов и файлов. 
 
4. Установите совместное сфайлом файла Azure в VM, присоединенном к AD DS. 

5. Обновление пароля вашей учетной записи хранения в AD DS.

Следующая диаграмма иллюстрирует сквозной рабочий процесс для обеспечения аутентификации Azure AD по сравнению с SMB для файлов Azure. 

![Диаграмма рабочего процесса AD файлов](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Аутентификация AD DS по сравнению с SMB для файлов Azure поддерживается только на машинах или вс-м внедрена на версиях ОС, более новых, чем Windows 7 или Windows Server 2008 R2. 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. Включить аутентификацию AD для вашей учетной записи 

Чтобы включить аутентификацию AD DS по сравнению с SMB для файлов azure, необходимо сначала зарегистрировать учетную запись хранилища в AD DS, а затем установить необходимые свойства домена на учетной записи хранилища. Когда функция включена на учетной записи хранилища, она применяется ко всем новым и существующим файловым долям в учетной записи. Используйте `join-AzStorageAccountForAuth` для включения функции. Подробное описание сквозного рабочего процесса можно найти в этом разделе. 

> [!IMPORTANT]
> Cmdlet `Join-AzStorageAccountForAuth` внесет изменения в вашу среду AD. Прочитайте следующее объяснение, чтобы лучше понять, что он делает, чтобы убедиться, что у вас есть соответствующие разрешения для выполнения команды и что применяемые изменения согласуются с политиками соответствия и безопасности. 

Cmdlet `Join-AzStorageAccountForAuth` выполнит эквивалент автономного домена, примкнувного к указанному счету хранения. Скрипт использует cmdlet для создания учетной записи в домене AD, либо [учетной записи компьютера](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (по умолчанию) или [учетной записи логона службы.](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) Если вы решите сделать это вручную, вы должны выбрать учетную запись, наиболее подходящую для вашей среды.

Учетная запись AD DS, созданная cmdlet, представляет учетную запись хранилища в домене AD. Если учетная запись AD DS создана в рамках организационного подразделения (OU), которое обеспечивает истечение срока действия пароля, необходимо обновить пароль до достижения максимального возраста пароля. Необновление пароля учетной записи приведет к сбоям в проверке подлинности при доступе к файлам Azure. Чтобы узнать, как обновить пароль, смотрите [пароль учетной записи Обновления DS.](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

Вы можете использовать следующий скрипт для выполнения регистрации и включения функции или, в качестве альтернативы, вы можете вручную выполнять операции, которые будет скрипт. Эти операции описаны в разделе, следуя сценарию. Вам не нужно делать и то, и другое.

### <a name="11-script-prerequisites"></a>1.1 Предпосылки сценария
- [Скачать и распаковать модуль AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Установка и выполнение модуля в устройстве, которое является доменом, объединенным с напредстанием AD DS с учетными данными AD DS, которые имеют разрешения на создание учетной записи входа в службу услуг или учетную запись компьютера в целевой AD.
-  Запустите скрипт с помощью предварительного dS-учетных данных AD DS, синхронизированных с Azure AD. В учетных данных AD DS должны быть либо владелец учетной записи хранилища, либо разрешения на роль участника RBAC.
- Убедитесь, что ваша учетная запись хранилища находится в [поддерживаемом регионе.](#regional-availability)

### <a name="12-domain-join-your-storage-account"></a>1.2 Домен присоединиться к вашей учетной записи хранения
Не забудьте заменить значения заполнителя на ваши собственные в параметрах ниже, прежде чем выполнять его в PowerShell.
> [!IMPORTANT]
> Соединение домена cmdlet создаст учетную запись AD для представления учетной записи хранилища (файл-доля) в AD. Вы можете зарегистрироваться в качестве учетной записи [FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) компьютера или учетной записи логона службы, см. Для учетных записей компьютеров возраст истечения срока действия пароля по умолчанию установлен в AD в 30 дней. Аналогичным образом, учетная запись входа в систему службы может иметь возраст истечения срока действия пароля по умолчанию в домене AD или Организационной группе (OU).
> Для обоих типов учетных записей мы настоятельно рекомендуем вам проверить, какой возраст истечения срока действия пароля настроен в вашей среде AD, и планируем [обновить пароль вашей учетной записи хранилища в AD-аккаунте](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) AD ниже до максимального возраста пароля. Неспособность обновить пароль учетной записи AD приведет к сбоям в проверке подлинности при доступе к файлам Azure. Можно рассмотреть возможность [создания нового организационного подразделения AD (OU) в AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) и отключить политику истечения срока действия паролей в [учетных записях компьютеров](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) или учетных записей логинов службы соответственно. 

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

В следующем описании суммируются все действия, выполняемые при выполнении `Join-AzStorageAccountForAuth` cmdlet. Вы можете выполнять эти действия вручную, если вы предпочитаете не использовать команду:

> [!NOTE]
> Если вы уже успешно `Join-AzStorageAccountForAuth` выполнили приведенный выше сценарий, перейдите в следующий раздел "1.3 Подтвердите, что функция включена". Вам не нужно выполнять операции ниже снова.

#### <a name="a-checking-environment"></a>а. Проверка среды

Во-первых, скрипт проверяет вашу среду. В частности, он проверяет, устанавливается ли [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) и выполняется ли оболочка с привилегиями администратора. Затем он проверяет, установлен ли [модуль Az.Storage 1.11.1-preview,](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) и устанавливает его, если это не так. Если эти проверки проходят, то он будет проверять ваш AD DS, чтобы увидеть, если есть либо [учетная запись компьютера](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (по умолчанию) или [службы логин счета,](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) который уже был создан с SPN / UPN как "cifs / ваш-хранение-имя-имя-here.file.core.windows.net". Если учетная запись не существует, она создаст один, как описано в разделе b ниже.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Создание удостоверения, представляющего учетную запись хранилища в AD вручную

Чтобы создать эту учетную запись вручную, создайте новый ключ Kerberos для вашей учетной записи хранения с помощью. `New-AzStorageAccountKey -KeyName kerb1` Затем используйте ключ Kerberos в качестве пароля для вашей учетной записи. Этот ключ используется только во время настройки и не может использоваться для любых операций плоскости управления или данных против учетной записи хранилища.

Если у вас есть этот ключ, создайте либо учетную запись службы или компьютера под OU. Используйте следующую спецификацию: SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Пароль: ключ Kerberos для вашей учетной записи хранения.

Если ваш OU обеспечивает истечение пароля, необходимо обновить пароль до достижения максимального возраста пароля, чтобы предотвратить сбои в проверке подлинности при доступе к файлам Azure. Для получения подробной информации можно узнать о [пароле вашей учетной записи хранилища в AD DS.](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

Храните SID вновь созданной учетной записи, она вам понадобится для следующего шага. Созданная учетная запись не должна синхронизироваться с Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Включить функцию в учетной записи хранилища

Затем скрипт включит функцию в учетной записи хранилища. Для выполнения этой настройки вручную предоставьте некоторые сведения о конфигурации для свойств домена в следующей команде, а затем запустите ее. Учетная запись хранилища SID, требуемая в следующей команде, — это SID созданной вами идентификации в AD DS в [предыдущем разделе.](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually)

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 Подтвердите, что функция включена

Вы можете проверить, включена ли функция в учетной записи хранилища, вы можете использовать следующий скрипт:

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

Теперь вы успешно включили функцию в учетную запись хранения. Теперь, когда функция включена, есть больше шагов, которые необходимо выполнить, чтобы использовать эту функцию.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Теперь вы успешно включили аутентификацию AD DS по сравнению с SMB и назначили пользовательскую роль, которая обеспечивает доступ к файлу Azure с идентификатором AD DS. Чтобы предоставить дополнительный доступ пользователям к вашей совместной части файла, следуйте инструкциям в [разрешении доступа Назначить](#2-assign-access-permissions-to-an-identity) использовать идентификационные данные и настроить разрешения NTFS по разделам [SMB.](#3-configure-ntfs-permissions-over-smb)

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. Обновление пароля вашей учетной записи хранения в AD DS

Если вы зарегистрировали идентификационный знак/аккаунт AD DS, представляющий вашу учетную запись хранения в соответствии с OU, которая обеспечивает истечение срока действия пароля, необходимо повернуть пароль до достижения максимального возраста пароля. Неспособность обновить пароль учетной записи AD DS приведет к сбоям в проверке подлинности при доступе к файлам Azure.  

Чтобы вызвать вращение пароля, можно запустить `Update-AzStorageAccountADObjectPassword` команду из модуля AzFilesHybrid. Cmdlet выполняет действия, аналогичные вращению ключа ключа учетной записи хранилища. Он получает второй ключ Kerberos учетной записи и использует его для обновления пароля зарегистрированной учетной записи в AD DS. Затем он регенерирует целевой ключ Kerberos учетной записи хранения и обновляет пароль зарегистрированной учетной записи в AD DS. Вы должны запустить этот cmdlet в предварительном домене AD DS, объединенном среде.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительной информации о Файлах Azure и о том, как использовать AD над SMB, см.

- [Обзор поддержки аутентификации на основе файлов Azure на основе SMB для доступа к SMB](storage-files-active-directory-overview.md)
- [Вопросы и ответы](storage-files-faq.md)
