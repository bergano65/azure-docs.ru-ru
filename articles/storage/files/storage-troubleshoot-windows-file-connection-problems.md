---
title: Устранение неполадок службы файлов Azure в Windows | Документация Майкрософт
description: Сведения об устранении неполадок со службой файлов Azure в Windows.
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 9849b8209db0a4aa73a80d461b67bda9b0b3656a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59049733"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Устранение неполадок службы файлов Azure в Windows

В этой статье приведен список распространенных проблем, возникающих в службе файлов Microsoft Azure при подключении из клиентов Windows. Кроме того, здесь представлены возможные причины этих проблем и способы их устранения. Помимо действий по устранению неполадок, описываемых в этой статье, можно также использовать [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) , чтобы обеспечить выполнение необходимых условий для среды клиента Windows. AzFileDiagnostics автоматизирует обнаружение большинства симптомов, упомянутых в этой статье, и помогает настроить среду для достижения оптимальной производительности. Эту информацию можно также найти в статье [Troubleshooter for Azure Files storage problems](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) (Устранение неполадок в хранилище файлов Azure), где приведены пошаговые инструкции по устранению проблем с подключением и сопоставлением общих ресурсов Azure.

<a id="error5"></a>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-5-when-you-mount-an-azure-file-share"></a>Ошибка 5 при подключении файлового ресурса Azure

При попытке подключить файловый ресурс может выдаваться следующая ошибка:

- "Произошла системная ошибка 5. Отказано в доступе".

### <a name="cause-1-unencrypted-communication-channel"></a>Причина 1. Незашифрованный коммуникационный канал

Из соображений безопасности, если коммуникационный канал не зашифрован, а попытка подключения осуществляется не из того же центра обработки данных, в котором находятся файловые ресурсы Azure, то подключение к ним Azure будет заблокировано. Незашифрованные подключения в одном центре обработки данных также могут быть заблокированы, если в учетной записи хранения включен параметр [Требуется безопасное перемещение](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer). Шифрование коммуникационного канала выполняется, только если клиентская ОС пользователя поддерживает шифрование SMB.

Windows 8, Windows Server 2012 или более поздние версии этих ОС отправляют запрос на согласование, который включает протокол в себя SMB 3.0, поддерживающий шифрование.

### <a name="solution-for-cause-1"></a>Решение для причины 1

1. Подключитесь из клиента, поддерживающего шифрование SMB (Windows 8, Windows Server 2012 или более поздней версии), или из виртуальной машины, размещенной в том же центре обработки данных, что и учетная запись хранения Azure, используемая для файлового ресурса Azure.
2. Убедитесь, что параметр [Требуется безопасное перемещение](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) отключен в учетной записи хранения, если клиент не поддерживает шифрование SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Причина 2. Правила виртуальной сети или брандмауэра включены для учетной записи хранения 

Если для учетной записи хранения настроены правила виртуальной сети и брандмауэра, доступ сетевого трафика будет запрещен, если IP-адрес клиента или виртуальная сеть не разрешают доступ.

### <a name="solution-for-cause-2"></a>Решение для причины 2

Убедитесь, что правила виртуальной сети и брандмауэра настроены надлежащим образом для учетной записи хранения. Чтобы проверить, связана ли проблема с правилами виртуальной сети или брандмауэра, временно задайте для учетной записи хранения параметр **Разрешить доступ из всех сетей**. Подробнее см. в статье [Настройка брандмауэров службы хранилища Azure и виртуальных сетей (предварительная версия)](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Ошибка 53, 67 или 87 при попытке подключения или отключения файлового ресурса Azure

При попытке подключить файловый ресурс из локальной среды или другого центра обработки данных могут возникнуть следующие ошибки:

- "Произошла системная ошибка 53. Сетевой путь не найден".
- "Произошла системная ошибка 67. Не найдено сетевое имя".
- "Произошла системная ошибка 87. Неправильный параметр".

### <a name="cause-1-port-445-is-blocked"></a>Причина 1. Порт 445 заблокирован

Системная ошибка 53 или 67 может произойти, если исходящие подключения через порт 445 к центру обработки данных службы файлов Azure заблокированы. Чтобы просмотреть сведения о поставщиках услуг Интернета, поддерживающих (или не поддерживающих) подключение через порт 445, перейдите на сайт [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Чтобы проверить, не блокирует ли брандмауэр или поставщик услуг Интернета порт 445, используйте средство [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) или командлет `Test-NetConnection`. 

Чтобы использовать `Test-NetConnection` командлет Azure PowerShell, необходимо установить модуль, см. в разделе [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps) Дополнительные сведения. Не забудьте заменить `<your-storage-account-name>` и `<your-resource-group-name>` соответствующими именами для вашей учетной записи хранения.

   
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

#### <a name="solution-1---use-azure-file-sync"></a>Решение 1 — использование службы синхронизации файлов Azure
Служба синхронизации файлов Azure можно преобразует локальной Windows Server в быстрый кэш файловом ресурсе Azure. Для локального доступа к данным вы можете использовать любой протокол, доступный в Windows Server, в том числе SMB, NFS и FTPS. Служба синхронизации файлов Azure работает через порт 443 и таким образом может использоваться в качестве обходного пути для доступа к службе файлов Azure из клиентов, имеющих порт 445 заблокирован. [Сведения о настройке службы синхронизации файлов Azure](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Решение 2 — использование VPN
Настроить VPN-Подключение для определенной учетной записи хранения, трафик проходит через защищенный туннель в отличие от через Интернет. Выполните [инструкции для настройки VPN](https://github.com/Azure-Samples/azure-files-samples/tree/master/point-to-site-vpn-azure-files
) для доступа к службе файлов Azure из Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Решение 3 — разблокировать порт 445 с помощью поставщика услуг Интернета или ИТ-администратор
Обратитесь к ИТ-отдел или поставщик услуг Интернета, чтобы открыть порт 445, исходящие подключения к [диапазоны IP-адресов Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Решение 4 — с использованием REST API на основе средства как хранилища Explorer или Powershell
Служба файлов Azure также поддерживает REST, кроме SMB. REST доступ работает через порт 443 (стандартный tcp). Существуют различные инструменты, написанные с помощью REST API, которые обеспечивают богатые возможности пользовательского интерфейса. [Обозреватель хранилищ](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) — один из них. [Скачать и установить обозреватель службы хранилища](https://azure.microsoft.com/en-us/features/storage-explorer/) и подключитесь к общей папке с резервными файлами Azure. Можно также использовать [PowerShell](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-powershell) которого также пользователя REST API.


### <a name="cause-2-ntlmv1-is-enabled"></a>Причина 2. Включена аутентификация NTLMv1

Системная ошибка 53 или 87 может произойти, если в клиенте включена аутентификация NTLMv1. Служба файлов Azure поддерживает только проверку подлинности NTLMv2. Протокол проверки подлинности NTLMv1 делает клиент менее безопасным, в результате чего взаимодействие со службой файлов Azure блокируется. 

Чтобы определить, заключается ли причина ошибки в этом, убедитесь, что для следующего подраздела реестра задано значение 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Дополнительные сведения см. в статье [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) на сайте TechNet.

### <a name="solution-for-cause-2"></a>Решение для причины 2

Верните параметру **LmCompatibilityLevel** значение по умолчанию (3) в следующем подразделе реестра:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Ошибка 1816 "Недостаточно квот для обработки команды" при копировании в файловый ресурс Azure

### <a name="cause"></a>Причина:

Ошибка 1816 возникает, когда достигнуто максимальное количество одновременно открытых дескрипторов для файла на компьютере, к которому подключается файловый ресурс.

### <a name="solution"></a>Решение

Сократите количество одновременно открытых дескрипторов, закрыв некоторые из них, и повторите попытку. Дополнительные сведения см. в статье [Производительность хранилища Microsoft Azure и контрольный список масштабируемости](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="accessdeniedportal"></a>
## <a name="error-access-denied-when-browsing-to-an-azure-file-share-in-the-portal"></a>Ошибка "Доступ запрещен" при переходе к общему файловому ресурсу Azure на портале

При переходе к общему файловому ресурсу Azure на портале, может появиться следующая ошибка.

Доступ запрещен  
Нет доступа  
Похоже, у вас нет доступа к этому контенту. Чтобы получить его, обратитесь к владельцу.  

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Причина 1. Учетная запись пользователя не имеет доступа к учетной записи хранения

### <a name="solution-for-cause-1"></a>Решение для причины 1

Перейдите в учетную запись хранения, в которой размещается общий файловый ресурс Azure, щелкните **Управление доступом (IAM)** и убедитесь, что учетная запись пользователя имеет доступ к учетной записи хранения. Подробнее см. в статье [Защита учетной записи хранения с помощью управления доступом на основе ролей (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Причина 2. Правила виртуальной сети или брандмауэра включены для учетной записи хранения

### <a name="solution-for-cause-2"></a>Решение для причины 2

Убедитесь, что правила виртуальной сети и брандмауэра настроены надлежащим образом для учетной записи хранения. Чтобы проверить, связана ли проблема с правилами виртуальной сети или брандмауэра, временно задайте для учетной записи хранения параметр **Разрешить доступ из всех сетей**. Подробнее см. в статье [Настройка брандмауэров службы хранилища Azure и виртуальных сетей (предварительная версия)](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Медленное копирование файлов в службе файлов Azure и из нее в Windows

При попытке передать файлы в службу файлов Azure можно наблюдать низкую производительность.

- Если определенные требования к минимальному размеру операций ввода-вывода отсутствуют, для оптимальной производительности мы рекомендуем использовать 1 МиБ.
-   Если окончательный размер файла, в который выполняются операции записи, известен, а у программного обеспечения нет проблем совместимости, и если еще не записанный заключительный фрагмент файла содержит нули, укажите размер файла заранее вместо того, чтобы расширять его для каждой операции записи.
-   Используйте правильный метод копирования:
    -   Используйте [AZCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) для передачи данных между двумя файловыми ресурсами.
    -   Используйте [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) для передачи данных между файловыми ресурсами и локальным компьютером.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Рекомендации для Windows 8.1 или Windows Server 2012 R2

Для клиентов, использующих Windows 8.1 или Windows Server 2012 R2, следует установить исправление [KB3114025](https://support.microsoft.com/help/3114025). Это исправление повышает производительность операций создания и закрытия дескрипторов.

Выполните следующий сценарий, чтобы проверить, установлено ли это исправление.

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Если это так, выводятся следующие данные:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Начиная с декабря 2015 года в образах Windows Server 2012 R2, содержащихся в Azure Marketplace, исправление KB3114025 установлено по умолчанию.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>В области **Мой компьютер** отсутствует папка с буквой диска

Если вы сопоставили файловый ресурс Azure от имени администратора с помощью команды net use, то может показаться, что он отсутствует.

### <a name="cause"></a>Причина:

По умолчанию проводник не запускается от имени администратора. При выполнении команды net use из командной строки администрирования пользователь подключает сетевой диск от имени администратора. Подключенные диски ориентированы на пользователя. Если для их подключения использовалась одна учетная запись, а пользователь вошел в систему с помощью другой, то диски отображаться не будут.

### <a name="solution"></a>Решение
Подключите общую папку из командной строки без прав администратора. Кроме того, настройте значение регистра **EnableLinkedConnections**, следуя указаниям в [этой статье TechNet](https://technet.microsoft.com/library/ee844140.aspx).

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Если учетная запись хранения содержит косую черту (/), то выполнение команды net use завершается сбоем

### <a name="cause"></a>Причина:

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

### <a name="cause"></a>Причина:

Диски подключаются для каждого пользователя. Если приложение или служба выполняется не под той учетной записью, к которой относится подключенный диск, то приложение не увидит этот диск.

### <a name="solution"></a>Решение

Используйте одно из следующих решений.

-   Используйте для подключения дисков учетную запись пользователя, содержащую это приложение. Можно использовать такой инструмент, как PsExec.
- Передайте имя и ключ учетной записи хранения в параметры имени пользователя и пароля команды net use.
- Чтобы добавить учетные данные в диспетчере учетных данных, используйте команду cmdkey. Выполните эту команду в командной строке в контексте учетной записи службы с помощью интерактивного входа или запуска от имени.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Сопоставьте общий ресурс напрямую, не используя букву подключенного диска. Если используется буква диска, некоторые приложения могут неправильно подключиться к диску, поэтому надежнее использовать полный UNC-путь. 

  `net use * \\storage-account-name.file.core.windows.net\share`

После выполнения этих инструкций при выполнении команды net use для учетной записи системы или сети может появиться следующее сообщение об ошибке: "Произошла системная ошибка 1312. A specified logon session does not exist. Возможно, он уже завершен". В этом случае убедитесь, что имя пользователя, переданное в net use, содержит сведения о домене (например, [имя_учетной_записи_хранения].file.core.windows.net).

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Ошибка "Вы копируете файл в место, которое не поддерживает шифрование"

Когда файл копируется по сети, он расшифровывается на исходном компьютере, передается в виде обычного текста и повторно шифруется в месте назначения. Тем не менее может появиться следующая ошибка при попытке скопировать зашифрованный файл: "Вы копируете файл в место, которое не поддерживает шифрование".

### <a name="cause"></a>Причина:
Эта проблема может возникнуть при использовании шифрованной файловой системы (EFS). Файлы с шифрованием BitLocker нельзя копировать в службу файлов Azure. Однако эта служба не поддерживает шифрованную файловую систему (EFS) NTFS.

### <a name="workaround"></a>Возможное решение
Чтобы скопировать файл по сети, его сначала необходимо расшифровать. Используйте один из следующих методов.

- Используйте команду **copy /d**. Она позволяет сохранить зашифрованные файлы как расшифрованные файлы в месте назначения.
- Настройте следующий раздел реестра:
  - путь: HKLM\Software\Policies\Microsoft\Windows\System;
  - тип значения: DWORD;
  - Name = CopyFileAllowDecryptedRemoteDestination;
  - Value = 1.

Помните, что настройка раздела реестра влияет на все операции копирования в сетевые общие папки.

## <a name="slow-enumeration-of-files-and-folders"></a>Медленное перечисление файлов и папок

### <a name="cause"></a>Причина:

Эта проблема может возникнуть, если на клиентском компьютере для больших каталогов недостаточно кэша.

### <a name="solution"></a>Решение

Чтобы устранить эту проблему, настройте значение реестра **DirectoryCacheEntrySizeMax** разрешать кэшировать большие списки каталогов на компьютере клиента.

- Расположение. HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Имя значения: DirectoryCacheEntrySizeMax 
- Тип значения: DWORD
 
 
Например, его можно присвоить 0x100000 и просмотреть станет ли производительность лучше.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Ошибка AadDsTenantNotFound при включении аутентификации Azure Active Directory для службы "Файлы Azure": Unable to locate active tenants with tenant Id aad-tenant-id (Не удалось найти активного клиента с идентификатором aad-tenant-id)

### <a name="cause"></a>Причина:

Ошибка AadDsTenantNotFound происходит при попытке [включить аутентификацию Azure Active Directory (AAD) для службы "Файлы Azure"](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable) в учетной записи хранения, где [доменная служба AAD (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) не создана в клиенте AAD связанной подписки.  

### <a name="solution"></a>Решение

Включите доменную службу AAD в клиенте AAD подписки, в которой развернута учетная запись хранения. Вам необходимы права администратора клиента AAD, чтобы создать управляемый домен. Если вы не администратор клиента Azure AD, обратитесь к администратору и следуйте пошаговым инструкциям, чтобы [включить доменные службы Azure Active Directory на портале Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.
Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.
