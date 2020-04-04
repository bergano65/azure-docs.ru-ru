---
title: Лазурное хранилище данных озера Gen2 PowerShell для файлов & ACLs (предварительный просмотр)
description: Используйте смдлеты PowerShell для управления каталогами и списками управления доступом файлов и каталогов (ACL) в учетных записях хранения с включенным иерархическим пространством имен (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 58548c5cb1aa6aba6dda09d5d420b36bb8154726
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656399"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Используйте PowerShell для управления каталогами, файлами и ACL в Azure Data Lake Storage 2 (предварительный просмотр)

В этой статье показано, как использовать PowerShell для создания и управления каталогами, файлами и разрешениями в учетных записях хранения с включенным иерархическим пространством имен (HNS). 

> [!IMPORTANT]
> Модуль PowerShell, который показан в этой статье, в настоящее время находится в открытом доступе.

[Gen1 к Gen2 отображение](#gen1-gen2-map) | [Дайте обратную связь](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранилища с включенным иерархическим пространством имен (HNS). Следуйте [этим](data-lake-storage-quickstart-create-account.md) инструкциям, чтобы создать один.
> * .NET Framework установлено 4.7.2 или более. Смотрите [Скачать .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Версия `5.1` PowerShell или выше.

## <a name="install-powershell-modules"></a>Установка модулей PowerShell

1. Убедитесь, что установленная версия `5.1` PowerShell или выше, используя следующую команду.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Чтобы обновить версию PowerShell, [см.](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Установите новейший модуль **PowershellGet.** Затем закройте и вновь откройте консоль PowerShell.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force 
   ```

3. Установка модуля предварительного просмотра **Az.Storage.**

   ```powershell
   Install-Module az.storage -RequiredVersion 1.13.3-preview -Repository PSGallery -AllowClobber -AllowPrerelease -Force 
   ```

   Для получения дополнительной информации о том, как установить модули PowerShell, смотрите [модуль Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Подключение к учетной записи

Откройте окно команды Windows PowerShell, а затем войдите `Connect-AzAccount` в подписку Azure с командой и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

Если ваша личность связана с более чем одной подпиской, то установите активную подписку на подписку на учетную запись хранения, в которую вы хотите создать и управлять каталогами. В этом примере `<subscription-id>` замените значение заполнителя идентификатором вашей подписки.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Затем выберите, как вы хотите, чтобы ваши команды получили авторизацию в учетную запись хранилища. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Вариант 1: Получить авторизацию с помощью active Directory Azure (AD)

При таком подходе система гарантирует, что ваша учетная запись пользователя имеет соответствующие назначения управления доступом на основе ролей (RBAC) и разрешения ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Вариант 2: Получить авторизацию с помощью ключа учетной записи хранилища

При таком подходе система не проверяет разрешения RBAC или ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система действует как контейнер для файлов. Вы можете создать один `New-AzDatalakeGen2FileSystem` с помощью cmdlet. 

Этот пример создает файловую систему под названием `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку каталога `New-AzDataLakeGen2Item` с помощью cmdlet. 

Этот пример добавляет каталог, названный `my-directory` в файловую систему.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Этот пример добавляет тот же каталог, но также устанавливает разрешения, umask, значения свойств и значения метаданных. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Показать свойства каталога

Этот пример получает каталог с `Get-AzDataLakeGen2Item` помощью cmdlet, а затем печатает значения свойств на консоли.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуйте или переместите каталог с помощью `Move-AzDataLakeGen2Item` cmdlet.

Этот пример переименовывает каталог `my-directory` от `my-new-directory`имени к имени .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Используйте `-Force` параметр, если вы хотите перезаписать без подсказок.

Этот пример перемещает каталог `my-directory` с именем `my-directory-2` в `my-subdirectory`подкаталог имени . Этот пример также применяет umask к субдиректору.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог с `Remove-AzDataLakeGen2Item` помощью cmdlet.

Этот пример удаляет каталог `my-directory`с именем . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Вы можете `-Force` использовать параметр для удаления файла без запроса.

## <a name="download-from-a-directory"></a>Скачать из каталога

Скачать файл из каталога `Get-AzDataLakeGen2ItemContent` с помощью cmdlet.

Этот пример загружает файл, `upload.txt` `my-directory`названный из каталога под названием . 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Перечислите содержимое каталога с `Get-AzDataLakeGen2ChildItem` помощью cmdlet. Можно использовать дополнительный `-OutputUserPrincipalName` параметр для получения имени (вместо идентификатора объекта) пользователей.

В этом примере перечислено `my-directory`содержимое каталога с именем .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

Ниже приведены `ACL`списки `Group`, `Owner` `Permissions`и свойства каждого элемента в каталоге. Параметр `-FetchProperty` необходим для получения значений `ACL` для свойства. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Чтобы перечислить содержимое файловой системы, испустите `-Path` параметр из команды.

## <a name="upload-a-file-to-a-directory"></a>Загрузить файл в каталог

Загрузите файл в каталог `New-AzDataLakeGen2Item` с помощью cmdlet.

Этот пример загружает файл с именем `upload.txt` в каталог под названием `my-directory`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Этот пример загружает тот же файл, но затем устанавливает значения разрешений, umask, значения свойств и значения метаданных файла назначения. Этот пример также печатает эти значения на консоли.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

## <a name="show-file-properties"></a>Показать свойства файла

Этот пример получает файл `Get-AzDataLakeGen2Item` с помощью cmdlet, а затем печатает значения свойств на консоли.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Удаление файла

Удалите файл `Remove-AzDataLakeGen2Item` с помощью cmdlet.

Этот пример удаляет файл `upload.txt`с именем . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Вы можете `-Force` использовать параметр для удаления файла без запроса.

## <a name="manage-access-permissions"></a>Управление разрешениями доступа

Вы можете получить, установить и обновить разрешения доступа файловых систем, каталогов и файлов.

> [!NOTE]
> Если вы используете Active Directory Azure (Azure AD) для авторизации команд, убедитесь, что директору безопасности была назначена [роль владельца данных Хранилища.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Чтобы узнать больше о том, как применяются разрешения [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ACL и о последствиях их изменения, см.

### <a name="get-permissions"></a>Получение разрешений

Получите ACL каталога или файла `Get-AzDataLakeGen2Item`с помощью cmdlet.


Этот пример получает ACL **файловой системы,** а затем печатает ACL на консоль.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Этот пример получает ACL **каталога,** а затем печатает ACL на консоль.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Этот пример получает ACL **файла,** а затем печатает ACL на консоль.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Следующее изображение показывает выход после получения ACL каталога.

![Получить выход ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

В этом примере пользователь-владелик прочитал, написал и выполнил разрешения. Группа владельцев только читала и выполняла разрешения. Для получения дополнительной информации о списках управления доступом смотрите [элемент управления доступом в Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-or-update-permissions"></a>Установка или обновление разрешений

Используйте `set-AzDataLakeGen2ItemAclObject` cmdlet для создания ACL для пользователя-владелеца, владеющего группой или другими пользователями. Затем используйте `Update-AzDataLakeGen2Item` cmdlet для фиксации ACL.

Этот пример устанавливает ACL в **файловой системе** для пользователя-владелеца, владеющего группой или другими пользователями, а затем печатает ACL на консоль.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Этот пример устанавливает ACL в **каталоге** для пользователя-владелеца, владеющего группой или другими пользователями, а затем печатает ACL на консоль.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
Этот пример устанавливает ACL на **файл** для пользователя-владелец, владеющий группой или другими пользователями, а затем печатает ACL на консоль.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Следующее изображение показывает вывод после установки ACL файла.

![Получить выход ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

В этом примере пользователь-владелик и группа владельцев только читают и пишут разрешения. Все остальные пользователи имеют разрешение на запись и выполнение. Для получения дополнительной информации о списках управления доступом смотрите [элемент управления доступом в Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).


### <a name="set-permissions-on-all-items-in-a-file-system"></a>Установка разрешений на все элементы файловой системы

Вы можете `Get-AzDataLakeGen2Item` использовать `-Recurse` параметр вместе `Update-AzDataLakeGen2Item` с cmdlet, чтобы повторно установить ACL всех каталогов и файлов в файловой системе. 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen1 в Gen2 Картпинг

В следующей таблице показано, как cmdlets, используемые для данных lake Storage Gen1 карты cmdlets для данных озера хранения Gen2.

|Хранение озера данных Gen1 cmdlet| Хранение озера данных Gen2 cmdlet| Примечания |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|По умолчанию в cmdlet Get-AzDataLakeGen2ChildItem перечислены только элементы ребенка первого уровня. Параметр -Recurse перечисляет элементы ребенка рекурсивно. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclentry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Выходные элементы Get-AzDataLakeGen2Item cmdlet имеют следующие свойства: Acl, Владелец, Группа, Разрешение.|
|Get-AzDataLakeStoreItemContentContent|Get-AzDataLakeGen2FileContent|Get-AzDataLakeGen2FileContent cmdlet загружает содержимое файлов в локальный файл.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|Нью-АзДатаЛейкСторИте|Нью-АзДатаЛейкГен2Комот|Это cmdlet загружает новое содержимое файла из локального файла.|
|Удалить-AzDataLakeStoreItem|Удалить-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Обновление-AzDataLakeGen2Item|Обновление-AzDataLakeGen2Item cmdlet обновляет только один элемент, а не рекурсивно. Если вы хотите обновить рекурсивно, перечислите элементы с помощью cmdlet Get-AzDataLakeStoreChild, а затем конвейер к обновлению-AzDataLakeGen2Item cmdlet.|
|Тест-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Смдlet Get-AzDataLakeGen2Item сообщит об ошибке, если элемент не существует.|



## <a name="see-also"></a>См. также

* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Использование Azure PowerShell с лазурным хранилищем.](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Хранение PowerShell cmdlets](/powershell/module/az.storage).

