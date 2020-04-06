---
title: Используйте службы домена Azure AD для авторизации доступа к файловым данным по СМБ
description: Узнайте, как включить проверку подлинности на основе идентификации по block Server Message Block (SMB) для файлов Azure через службы домена Active Directory. Виртуальные машины Windows, присоединенные к домену( могут получить доступ к файловым долям Azure, используя учетные данные Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cb173bcbf7cd163dca16c211d45018e0fe056edd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666849"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Включить проверку функциональности домена службы активного каталога Azure в файлах Azure

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Для обзора аутентификации Azure AD по sMB [Overview of Azure Active Directory authentication over SMB for Azure Files](storage-files-active-directory-overview.md)для файлов Azure см. В этой статье основное внимание уделяется тому, как включить аутентификацию с помощью служб ы идочебной информации Azure Active Directory Domain Services (Azure AD DS) в файлах Azure.

> [!NOTE]
> Azure Files поддерживает проверку подлинности Kerberos с помощью Azure AD DS с помощью шифрования RC4-HMAC. Шифрование AES Kerberos пока не поддерживается.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем включить Azure AD над SMB для файлов Azure, убедитесь, что вы выполнили следующие предпосылки:

1.  **Выберите или создайте клиент Azure AD.**

    Вы можете использовать новый или существующий клиент для выполнения аутентификации Azure AD по протоколу SMB. Клиент и файловый ресурс, к которому вы хотите получить доступ, должны быть связаны с одной и той же подпиской.

    Чтобы создать новый клиент Azure AD, можно [добавить клиент и подписку Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Если у вас есть существующий арендатор Azure AD, но вы хотите создать нового арендатора для использования с акциями файлов Azure, [см.](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)

1.  **Включите доменные службы Azure AD в клиенте Azure AD**.

    Чтобы поддерживать аутентификацию с помощью учетных данных Azure AD, нужно включить доменные службы Azure AD для вашего клиента Azure AD. Если вы не администратор клиента Azure AD, обратитесь к администратору и следуйте пошаговым инструкциям, чтобы [включить доменные службы Azure Active Directory на портале Azure](../../active-directory-domain-services/tutorial-create-instance.md).

    Обычно развертывание Azure AD DS занимает около 15 минут. Убедитесь, что состояние работоспособности Azure AD DS показывает **Running,** с включенной синхронизацией хэша паролей, прежде чем перейти к следующему шагу.

1.  **Домен-присоединиться к Azure VM с Azure AD DS.**

    Чтобы получить доступ к файлу, используя учетные данные Azure AD из VM, ваш VM должен быть соединен доменом с Azure AD DS. Дополнительные сведения см. в статье [Присоединение виртуальной машины Windows Server к управляемому домену](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Аутентификация Azure AD DS по сравнению с SMB с файлами Azure поддерживается только на VMs Azure, работающих на версиях ОС выше Windows 7 или Windows Server 2008 R2.

1.  **Выберите или создайте файловый ресурс Azure**.

    Выберите новый или существующий файловый ресурс, связанный с той же подпиской, что и ваш клиент Azure AD. Дополнительные сведения см. в статье [Создание общей папки в службе файлов Azure](storage-how-to-create-file-share.md).
    Для оптимальной производительности мы рекомендуем, чтобы ваша доля файлов была в том же регионе, что и VM, с которого вы планируете получить доступ к этой акции.

1.  **Проверьте сетевое подключение службы файлов Azure путем подключения файловых ресурсов Azure с использованием ключа учетной записи хранения.**

    Чтобы убедиться, что ваша виртуальная машина и файловый ресурс настроены правильно, попробуйте установить общий доступ к файлу с помощью ключа учетной записи хранения. Дополнительные сведения см. в статье [Использование общей папки Azure в Windows](storage-how-to-use-files-windows.md).

## <a name="overview-of-the-workflow"></a>Обзор рабочего процесса

Прежде чем включить azure AD DS Authentication через SMB для файлов Azure, убедитесь, что ваши среды хранения Azure AD и Azure были правильно настроены. Мы рекомендуем вам пройти через [предпосылки,](#prerequisites) чтобы убедиться, что вы выполнили все необходимые шаги.

Далее, сделайте следующие вещи, чтобы предоставить доступ к ресурсам Azure Files с учетными данными Azure AD:

1. Включить аутентификацию Azure AD DS по сравнению с SMB для учетной записи хранилища для регистрации учетной записи хранилища с помощью связанного развертывания Azure AD DD.
2. Назначьте права доступа к общему ресурсу для удостоверения Azure AD (пользователя, группы или субъекта-службы).
3. Настройте разрешения NTFS по протоколу SMB для каталогов и файлов.
4. Подключите файловый ресурс Azure из виртуальной машины, присоединенной к домену.

Следующая диаграмма иллюстрирует сквозной рабочий процесс для обеспечения аутентификации Azure AD DS по сравнению с SMB для файлов Azure.

![Схема, показывающая рабочий процесс включения Azure AD по протоколу SMB для службы файлов Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. Включить аутентификацию Azure AD DS для вашей учетной записи

Чтобы включить аутентификацию Azure AD DS по сравнению с SMB для файлов Azure, можно настроить свойство на учетных записях хранения с помощью портала Azure, Azure PowerShell или Azure CLI. Установка этого свойства косвенно "домен присоединяется" к учетной записи хранения с соответствующим развертыванием Azure AD DS. Проверка подлинности Azure AD DS по сравнению с SMB включена для всех новых и существующих файлов в учетной записи хранилища.

Имейте в виду, что аутентификация Azure AD DS по сравнению с SMB может быть переведена только после успешного развертывания DD-dS Azure AD для вашего ad-нанимателя Azure AD. Для получения дополнительной [информации, см.](#prerequisites)

### <a name="azure-portal"></a>Портал Azure

Чтобы включить аутентификацию Azure AD DS через SMB с [помощью портала Azure,](https://portal.azure.com)выполните следующие действия:

1. На портале Azure перейдите на существующую учетную запись хранения или [создайте учетную запись хранения.](../common/storage-account-create.md)
1. В разделе **Параметры** выберите **Конфигурация**.
1. Под **идентификационным доступом для файловых акций** переключите переключатель для **службы доступа Active Directory Domain Service (AAD DS)** в **Enabled.**
1. Щелкните **Сохранить**.

На следующем изображении показано, как включить аутентификацию Azure AD DS по сравнению с SMB для вашей учетной записи хранения.

![Включить аутентификацию Azure AD DS над SMB на портале Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Чтобы включить аутентификацию Azure AD DS по сравнению с SMB с помощью Azure PowerShell, установите новейший модуль Az (2.4 или более новый) или модуль Az.Storage (1,5 или более новый). Для получения дополнительной информации об установке PowerShell [см.](https://docs.microsoft.com/powershell/azure/install-Az-ps)

Чтобы создать новую учетную запись хранения, позвоните в [New-AzStorageAccount,](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)а затем **true**установите параметр **EnableAzureActiveDirectoryDomainForForForFile.** В следующем примере не забудьте заменить значения заполнителя на свои собственные значения. (Если вы использовали предыдущий модуль предварительного просмотра, параметр для включения функции **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Чтобы включить эту функцию в существующие учетные записи хранилища, используйте следующую команду:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Чтобы включить аутентификацию Azure AD по сравнению с SMB с помощью Azure CLI, установите последнюю версию CLI (версия 2.0.70 или более новая). Для получения дополнительной информации об установке Azure CLI [см.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

Чтобы создать новую учетную запись хранения, позвоните[по учетной записи хранилища az](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)и установите свойство `--enable-files-aadds` на **истину.** В следующем примере не забудьте заменить значения заполнителя на свои собственные значения. (Если вы использовали предыдущий модуль предварительного просмотра, параметр для включения функции — **файл-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Чтобы включить эту функцию в существующие учетные записи хранилища, используйте следующую команду:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Теперь вы успешно включили аутентификацию Azure AD DD по сравнению с SMB и назначили пользовательскую роль, которая обеспечивает доступ к файлу Azure с идентификатором Azure AD. Чтобы предоставить дополнительный доступ пользователям к вашей совместной части файла, следуйте инструкциям в [разрешении доступа Назначить](#2-assign-access-permissions-to-an-identity) для использования идентификации и [настроить разрешения NTFS по разделам SMB.](#3-configure-ntfs-permissions-over-smb)

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительной информации о файлах Azure и о том, как использовать Azure AD над SMB, см.

- [Обзор поддержки аутентификации на основе файлов Azure на основе SMB для доступа к SMB](storage-files-active-directory-overview.md)
- [Вопросы и ответы](storage-files-faq.md)
