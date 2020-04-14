---
title: Устранение неполадок службы файлов Azure в Windows | Документация Майкрософт
description: Сведения об устранении неполадок со службой файлов Azure в Windows.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 3237fe7d87ad058f255d1c77cb6d814bcd1c292e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262253"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Устранение неполадок службы файлов Azure в Windows

В этой статье приведен список распространенных проблем, возникающих в службе файлов Microsoft Azure при подключении из клиентов Windows. Кроме того, здесь представлены возможные причины этих проблем и способы их устранения. В дополнение к шагам по устранению неполадок в этой статье, вы также можете использовать [AzFileDiagnostics,](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) чтобы убедиться, что клиентская среда Windows имеет правильные предпосылки. AzFileDiagnostics автоматизирует обнаружение большинства симптомов, упомянутых в этой статье, и помогает настроить среду для достижения оптимальной производительности. Вы также можете найти эту информацию в [акции Azure Files Troubleshooter,](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) которая предоставляет шаги, помогающие вам в решении проблем, связанных с подключением/картографией/монтажом акций Azure Files.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Ошибка 5 при подключении файлового ресурса Azure

При попытке подключить файловый ресурс может выдаваться следующая ошибка:

- "Произошла системная ошибка 5. Отказано в доступе".

### <a name="cause-1-unencrypted-communication-channel"></a>Причина 1: незашифрованный коммуникационный канал

Из соображений безопасности, если коммуникационный канал не зашифрован, а попытка подключения осуществляется не из того же центра обработки данных, в котором находятся файловые ресурсы Azure, то подключение к ним Azure будет заблокировано. Незашифрованные подключения в одном центре обработки данных также могут быть заблокированы, если в учетной записи хранения включен параметр [Требуется безопасное перемещение](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer). Шифрование коммуникационного канала выполняется, только если клиентская ОС пользователя поддерживает шифрование SMB.

Windows 8, Windows Server 2012 или более поздние версии этих ОС отправляют запрос на согласование, который включает протокол в себя SMB 3.0, поддерживающий шифрование.

### <a name="solution-for-cause-1"></a>Решение для причины 1

1. Подключитесь из клиента, поддерживающего шифрование SMB (Windows 8, Windows Server 2012 или более поздней версии), или из виртуальной машины, размещенной в том же центре обработки данных, что и учетная запись хранения Azure, используемая для файлового ресурса Azure.
2. Убедитесь, что параметр [Требуется безопасное перемещение](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) отключен в учетной записи хранения, если клиент не поддерживает шифрование SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Причина 2: Виртуальная сеть или правила брандмауэра включены в учетную запись хранения 

Если для учетной записи хранения настроены правила виртуальной сети и брандмауэра, доступ сетевого трафика будет запрещен, если IP-адрес клиента или виртуальная сеть не разрешают доступ.

### <a name="solution-for-cause-2"></a>Решение для причины 2

Убедитесь, что правила виртуальной сети и брандмауэра настроены надлежащим образом для учетной записи хранения. Чтобы проверить, связана ли проблема с правилами виртуальной сети или брандмауэра, временно задайте для учетной записи хранения параметр **Разрешить доступ из всех сетей**. Подробнее см. в статье [Настройка брандмауэров службы хранилища Azure и виртуальных сетей (предварительная версия)](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Причина 3: Разрешения на уровне общего использования проверки подлинности на основе идентификации

Если пользователи получают доступ к доле файла Azure с помощью Active Directory (AD) или Azure Active Directory Domain Domain Services (Azure AD DS), доступ к доле файла не будет допущен с ошибкой "Доступ отказано", если разрешения на уровне общего доступа неверны. 

### <a name="solution-for-cause-3"></a>Решение для причины 3

Для обновления разрешений уровня общего доступа [просимите присвоить разрешения доступа к идентификатору.](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity)

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Ошибка 53, 67 или 87 при попытке подключения или отключения файлового ресурса Azure

При попытке подключить файловый ресурс из локальной среды или другого центра обработки данных могут возникнуть следующие ошибки:

- "Произошла системная ошибка 53. Сетевой путь не найден".
- "Произошла системная ошибка 67. Не найдено сетевое имя".
- "Произошла системная ошибка 87. Неправильный параметр".

### <a name="cause-1-port-445-is-blocked"></a>Причина 1: Порт 445 заблокирован

Системная ошибка 53 или 67 может произойти, если исходящие подключения через порт 445 к центру обработки данных службы файлов Azure заблокированы. Чтобы просмотреть сведения о поставщиках услуг Интернета, поддерживающих (или не поддерживающих) подключение через порт 445, перейдите на сайт [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Чтобы проверить, не блокирует ли брандмауэр или поставщик услуг Интернета порт 445, используйте средство [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) или командлет `Test-NetConnection`. 

Для использования `Test-NetConnection` cmdlet модуль Azure PowerShell должен быть установлен, см. [Install Azure PowerShell module](/powershell/azure/install-Az-ps) Не забудьте заменить `<your-storage-account-name>` и `<your-resource-group-name>` соответствующими именами для вашей учетной записи хранения.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Если установка прошла успешно, отобразятся следующие выходные данные.
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> Вышеуказанная команда возвращает текущий IP-адрес учетной записи хранения. Не гарантируется, что IP-адрес будет постоянным. Он может измениться в любое время. Не следует жестко задавать этот IP-адрес в скриптах или в конфигурации брандмауэра.

### <a name="solution-for-cause-1"></a>Решение для причины 1

#### <a name="solution-1---use-azure-file-sync"></a>Решение 1. Использование Синхронизации файлов Azure
Синхронизация файлов Azure может превратить ваш предприимчивый Windows Server в быстрый кэш общего файла Azure. Для локального доступа к данным вы можете использовать любой протокол, доступный в Windows Server, в том числе SMB, NFS и FTPS. Azure File Sync работает над портом 443 и, таким образом, может использоваться в качестве обходного пути для доступа к файлам Azure от клиентов, заблокированных в порте 445. [Узнайте, как настроить синхронизацию файлов Azure.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers)

#### <a name="solution-2---use-vpn"></a>Решение 2. Использование VPN
Настройка VPN для вашей конкретной учетной записи хранения, трафик будет проходить через безопасный туннель, а не через Интернет. Следуйте [инструкциям по настройке VPN](storage-files-configure-p2s-vpn-windows.md) для доступа к файлам Azure из Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Решение 3. Разблокировка порта 445 с помощью поставщика услуг Интернета или ИТ-администратора
Работайте с ИТ-отделом или isP, чтобы открыть порт 445, исходящий в [ip-диапазоны Azure.](https://www.microsoft.com/download/details.aspx?id=41653)

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Решение 4. Использование таких средств на основе REST API, как Обозреватель службы хранилища или PowerShell
Лазурные файлы также поддерживают REST в дополнение к SMB. ДОСТУП REST работает над портом 443 (стандартный tcp). Существуют различные инструменты, которые написаны с помощью REST API, которые позволяют богатый опыт использования. [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) является одним из них. [Скачать и установить исследователь хранения](https://azure.microsoft.com/features/storage-explorer/) и подключиться к файлу доля при поддержке Azure файлов. Вы также можете использовать [PowerShell,](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) который также пользователь REST API.

### <a name="cause-2-ntlmv1-is-enabled"></a>Причина 2: включен NTLMv1

Системная ошибка 53 или 87 может произойти, если в клиенте включена аутентификация NTLMv1. Служба файлов Azure поддерживает только проверку подлинности NTLMv2. Протокол проверки подлинности NTLMv1 делает клиент менее безопасным, в результате чего взаимодействие со службой файлов Azure блокируется. 

Чтобы определить, заключается ли причина ошибки в этом, убедитесь, что для следующего подраздела реестра задано значение 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Дополнительные сведения см. в статье [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) на сайте TechNet.

### <a name="solution-for-cause-2"></a>Решение для причины 2

Верните параметру **LmCompatibilityLevel** значение по умолчанию (3) в следующем подразделе реестра:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Ошибка 1816 "Недостаточно квот для обработки команды" при копировании в файловый ресурс Azure

### <a name="cause"></a>Причина

Ошибка 1816 возникает, когда достигнуто максимальное количество одновременно открытых дескрипторов для файла на компьютере, к которому подключается файловый ресурс.

### <a name="solution"></a>Решение

Сократите количество одновременно открытых дескрипторов, закрыв некоторые из них, и повторите попытку. Для получения дополнительной информации смотрите [контрольный список производительности и масштабируемости хранилища Microsoft Azure.](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

Для просмотра открытых ручек для раздела файла, каталога или файла используйте cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.  

Чтобы закрыть открытые ручки для раздела файла, каталога или файла, используйте [cmdlet Close-AzStorageFile PowerShell.](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle)

> [!Note]  
> Смдеты Get-AzStorageFileHandle и Close-AzStorageFileHandle включены в версию модуля Az PowerShell 2.4 или позже. Чтобы установить новейший модуль Az PowerShell, смотрите [модуль Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-az-ps)

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Ошибка "Нет доступа", когда вы пытаетесь получить доступ или удалить раздел файла Azure  
При попытке получить доступ к файлу Azure или удалить на портале может возникнуть следующая ошибка:

Доступ отсутствует  
Код ошибки: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Причина 1: Виртуальная сеть или правила брандмауэра включены в учетную запись хранения

### <a name="solution-for-cause-1"></a>Решение для причины 1

Убедитесь, что правила виртуальной сети и брандмауэра настроены надлежащим образом для учетной записи хранения. Чтобы проверить, связана ли проблема с правилами виртуальной сети или брандмауэра, временно задайте для учетной записи хранения параметр **Разрешить доступ из всех сетей**. Подробнее см. в статье [Настройка брандмауэров службы хранилища Azure и виртуальных сетей (предварительная версия)](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Причина 2: Ваша учетная запись пользователя не имеет доступа к учетной записи хранилища

### <a name="solution-for-cause-2"></a>Решение для причины 2

Перейдите в учетную запись хранения, в которой размещается общий файловый ресурс Azure, щелкните **Управление доступом (IAM)** и убедитесь, что учетная запись пользователя имеет доступ к учетной записи хранения. Подробнее см. в статье [Защита учетной записи хранения с помощью управления доступом на основе ролей (RBAC)](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Не удалось удалить файл или каталог в общей папке Azure
При попытке удалить файл может возникнуть следующая ошибка:

Указанный ресурс помечен для удаления клиентом SMB.

### <a name="cause"></a>Причина
Эта проблема обычно возникает, если файл или каталог имеет открытую ручку. 

### <a name="solution"></a>Решение

Если клиенты SMB закрыли все открытые ручки и проблема продолжает возникать, выполните следующие:

- Для просмотра открытых ручек используйте cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.

- Для закрытия открытых ручек используйте cmdlet [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell. 

> [!Note]  
> Смдеты Get-AzStorageFileHandle и Close-AzStorageFileHandle включены в версию модуля Az PowerShell 2.4 или позже. Чтобы установить новейший модуль Az PowerShell, смотрите [модуль Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-az-ps)

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Медленное копирование файлов в службе файлов Azure и из нее в Windows

При попытке передать файлы в службу файлов Azure можно наблюдать низкую производительность.

- Если определенные требования к минимальному размеру операций ввода-вывода отсутствуют, для оптимальной производительности мы рекомендуем использовать 1 МиБ.
-   Если окончательный размер файла, в который выполняются операции записи, известен, а у программного обеспечения нет проблем совместимости, и если еще не записанный заключительный фрагмент файла содержит нули, укажите размер файла заранее вместо того, чтобы расширять его для каждой операции записи.
-   Используйте правильный метод копирования:
    -   Используйте [AZCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) для передачи данных между двумя файловыми ресурсами.
    -   Используйте [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) для передачи данных между файловыми ресурсами и локальным компьютером.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Рекомендации для Windows 8.1 или Windows Server 2012 R2

Для клиентов, использующих Windows 8.1 или Windows Server 2012 R2, следует установить исправление [KB3114025](https://support.microsoft.com/help/3114025). Это исправление повышает производительность операций создания и закрытия дескрипторов.

Выполните следующий сценарий, чтобы проверить, установлено ли это исправление.

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Если это так, выводятся следующие данные:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Начиная с декабря 2015 года в образах Windows Server 2012 R2, содержащихся в Azure Marketplace, исправление KB3114025 установлено по умолчанию.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Нет папки с дисковой буквы в "Мой компьютер" или "Этот компьютер"

Если вы сопоставили файловый ресурс Azure от имени администратора с помощью команды net use, то может показаться, что он отсутствует.

### <a name="cause"></a>Причина

По умолчанию проводник не запускается от имени администратора. При выполнении команды net use из командной строки администрирования пользователь подключает сетевой диск от имени администратора. Подключенные диски ориентированы на пользователя. Если для их подключения использовалась одна учетная запись, а пользователь вошел в систему с помощью другой, то диски отображаться не будут.

### <a name="solution"></a>Решение
Подключите общую папку из командной строки без прав администратора. Кроме того, настройте значение регистра **EnableLinkedConnections**, следуя указаниям в [этой статье TechNet](https://technet.microsoft.com/library/ee844140.aspx).

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Если учетная запись хранения содержит косую черту (/), то выполнение команды net use завершается сбоем

### <a name="cause"></a>Причина

Команда net use интерпретирует косую черту (/) как параметр командной строки. Если имя учетной записи пользователя начинается с косой черты, то сопоставление диска завершится сбоем.

### <a name="solution"></a>Решение

Чтобы обойти эту проблему, выполните одно из следующих действий.

- Выполните следующую команду PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Из пакетного файла выполнить команду можно следующим образом.

  `Echo new-smbMapping ... | powershell -command –`

- Заключите значение ключа в двойные прямые кавычки (если первый знак не является косой чертой). Если значение ключа начинается с косой черты (/), используйте интерактивный режим и введите пароль отдельно или повторно создайте ключи, которые не начинаются с этого знака.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Приложение или служба не может получить доступ к подключенному диску службы файлов Azure

### <a name="cause"></a>Причина

Диски подключаются для каждого пользователя. Если приложение или служба выполняется не под той учетной записью, к которой относится подключенный диск, то приложение не увидит этот диск.

### <a name="solution"></a>Решение

Используйте одно из следующих решений:

-   Используйте для подключения дисков учетную запись пользователя, содержащую это приложение. Можно использовать такой инструмент, как PsExec.
- Передайте имя и ключ учетной записи хранения в параметры имени пользователя и пароля команды net use.
- Чтобы добавить учетные данные в диспетчере учетных данных, используйте команду cmdkey. Выполните это из командной строки в контексте учетной `runas`записи службы, либо с помощью интерактивного входа или с помощью .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Сопоставьте общий ресурс напрямую, не используя букву подключенного диска. Если используется буква диска, некоторые приложения могут неправильно подключиться к диску, поэтому надежнее использовать полный UNC-путь. 

  `net use * \\storage-account-name.file.core.windows.net\share`

После выполнения этих инструкций при выполнении команды net use для учетной записи системы или сети может появиться следующее сообщение об ошибке: "Произошла системная ошибка 1312. A specified logon session does not exist. Возможно, он уже завершен". В этом случае убедитесь, что имя пользователя, переданное в net use, содержит сведения о домене (например, [имя_учетной_записи_хранения].file.core.windows.net).

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Ошибка "Вы копируете файл в место, которое не поддерживает шифрование"

Когда файл копируется по сети, он расшифровывается на исходном компьютере, передается в виде обычного текста и повторно шифруется в месте назначения. Тем не менее при попытке скопировать зашифрованный файл может появиться следующее сообщение об ошибке: "Вы копируете файл в место, которое не поддерживает шифрование".

### <a name="cause"></a>Причина
Эта проблема может возникнуть при использовании шифрованной файловой системы (EFS). Файлы с шифрованием BitLocker нельзя копировать в службу файлов Azure. Однако эта служба не поддерживает шифрованную файловую систему (EFS) NTFS.

### <a name="workaround"></a>Обходной путь
Чтобы скопировать файл по сети, его сначала необходимо расшифровать. Используйте один из следующих методов.

- Используйте команду **copy /d**. Она позволяет сохранить зашифрованные файлы как расшифрованные файлы в месте назначения.
- Настройте следующий раздел реестра:
  - путь: HKLM\Software\Policies\Microsoft\Windows\System;
  - тип значения: DWORD;
  - Name = CopyFileAllowDecryptedRemoteDestination;
  - Value = 1.

Помните, что настройка раздела реестра влияет на все операции копирования в сетевые общие папки.

## <a name="slow-enumeration-of-files-and-folders"></a>Медленное перечисление файлов и папок

### <a name="cause"></a>Причина

Эта проблема может возникнуть, если на клиентском компьютере для больших каталогов недостаточно кэша.

### <a name="solution"></a>Решение

Чтобы устранить эту проблему, настройте значение реестра **DirectoryCacheEntrySizeMax** разрешать кэшировать большие списки каталогов на компьютере клиента.

- Расположение: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Название значения: DirectoryCacheEntrySizeMax 
- Тип значения: DWORD
 
 
Например, его можно присвоить 0x100000 и просмотреть станет ли производительность лучше.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Ошибка AADDsTenantNotFound в предоставлении Azure Active Directory Domain Service (AAD DS) аутентификации для azure Files "Не удается найти активных арендаторов с арендатором Id aad-tenant-id"

### <a name="cause"></a>Причина

Ошибка AAdDsTenantNotFound происходит при попытке [включить службу аутентификации Azure Active Directory Domain Service (Azure AD DS) в файлах хранения](storage-files-identity-auth-active-directory-domain-service-enable.md) на учетной записи хранения данных, где [служба домена AAD (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) не создана на клиенте AAD связанной подписки.  

### <a name="solution"></a>Решение

Включите доменную службу AAD в клиенте AAD подписки, в которой развернута учетная запись хранения. Вам необходимы права администратора клиента AAD, чтобы создать управляемый домен. Если вы не администратор клиента Azure AD, обратитесь к администратору и следуйте пошаговым инструкциям, чтобы [включить доменные службы Azure Active Directory на портале Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Ошибка 'Система ошибка 1359 произошло. Внутренняя ошибка, полученная из-за доступа sMB к файловым долям с включенной аутентификацией Azure Active Directory Domain Service (AAD DS)

### <a name="cause"></a>Причина

Ошибка 'Система ошибка 1359 произошло. Внутренняя ошибка происходит при попытке подключения к файлу с помощью аутентификации AAD DS с включенной aAD DS с именой DNS домена, начинающимся с числового символа. Например, если ваше имя AAD DS Domain DNS — 1домен, вы получите эту ошибку при попытке смонтировать общую часть файлов с помощью учетных данных AAD. 

### <a name="solution"></a>Решение

В настоящее время можно рассмотреть возможность передислокации AAD DS с помощью нового домена DNS-имя, которое применяется с правилами ниже:
- Имена не могут начинаться с числового персонажа.
- Имена должны быть от 3 до 63 символов в длину.

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.
Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.
