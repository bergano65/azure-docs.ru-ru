---
title: Azure Data Lake Storage 2-го поколения PowerShell для файлов & ACL
description: Используйте командлеты PowerShell для управления каталогами и списками управления доступом к файлам и каталогам (ACL) в учетных записях хранения с включенным иерархическое пространством имен (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e2138fc03fcdb614daef2051b7fc1a840e421658
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359737"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Использование PowerShell для управления каталогами, файлами и списками ACL в Azure Data Lake Storage 2-го поколения

В этой статье показано, как использовать PowerShell для создания каталогов, файлов и разрешений в учетных записях хранения с включенным иерархическое пространством имен (HNS) и управления ими. 

[Справочные материалы](https://docs.microsoft.com/powershell/module/Az.Storage/)  |  Сопоставление Gen1 с [Gen2](#gen1-gen2-map)  |  [Отправить отзыв](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Обязательные условия

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * В учетной записи хранения включено иерархическое пространство имен. Выполните [эти](data-lake-storage-quickstart-create-account.md) инструкции, чтобы создать учетную запись.
> * .NET Framework установлен или больше 4.7.2. См. раздел [Download .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Версия PowerShell `5.1` или выше.

## <a name="install-the-powershell-module"></a>Установка модуля PowerShell

1. Убедитесь, что установленная версия PowerShell `5.1` или более поздняя, с помощью следующей команды.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Сведения об обновлении версии PowerShell см. в разделе [обновление существующих Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell) .
    
2. Установите **AZ. Storage** Module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Дополнительные сведения об установке модулей PowerShell см. [в статье Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) .

## <a name="connect-to-the-account"></a>Подключение к учетной записи

Откройте командное окно Windows PowerShell, а затем войдите в подписку Azure с помощью `Connect-AzAccount` команды и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

Если ваше удостоверение связано с более чем одной подпиской, установите активную подписку на подписку учетной записи хранения, которая будет создавать каталоги и управлять ими в. В этом примере замените `<subscription-id>` значение ЗАПОЛНИТЕЛЯ идентификатором подписки.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Затем выберите способ, которым команды будут получать права доступа к учетной записи хранения. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Вариант 1. получение авторизации с помощью Azure Active Directory (AD)

При таком подходе система гарантирует, что ваша учетная запись пользователя имеет соответствующие разрешения на управление доступом на основе ролей Azure (Azure RBAC) и ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Вариант 2. получение авторизации с помощью ключа учетной записи хранения

При таком подходе система не проверяет разрешения Azure RBAC или ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-container"></a>Создание контейнера

Контейнер выступает в качестве файловой системы для файлов. Его можно создать с помощью `New-AzStorageContainer` командлета. 

В этом примере создается контейнер с именем `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на каталог с помощью `New-AzDataLakeGen2Item` командлета. 

В этом примере в контейнер добавляется каталог с именем `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

В этом примере добавляется тот же каталог, но также устанавливаются разрешения, umask, значения свойств и значения метаданных. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Отображение свойств каталога

Этот пример получает каталог с помощью `Get-AzDataLakeGen2Item` командлета, а затем выводит значения свойств на консоль.

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
> [!NOTE]
> Чтобы получить корневой каталог контейнера, опустите `-Path` параметр.

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуйте или переместите каталог с помощью `Move-AzDataLakeGen2Item` командлета.

В этом примере имя каталога переименовывается в имя `my-directory` `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Используйте `-Force` параметр, если требуется перезаписать без запросов.

В этом примере каталог с именем перемещается `my-directory` в подкаталог с `my-directory-2` именем `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог с помощью `Remove-AzDataLakeGen2Item` командлета.

В этом примере удаляется каталог `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

С помощью параметра можно `-Force` удалить файл без запроса.

## <a name="download-from-a-directory"></a>Скачивание из каталога

Скачайте файл из каталога с помощью `Get-AzDataLakeGen2ItemContent` командлета.

В этом примере из каталога `my-directory` скачивается файл `upload.txt`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Перечислите содержимое каталога с помощью `Get-AzDataLakeGen2ChildItem` командлета. Можно использовать необязательный параметр, `-OutputUserPrincipalName` чтобы получить имя (вместо идентификатора объекта) пользователей.

В этом примере выводится содержимое каталога с именем `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

В следующем примере перечисляются `ACL` `Permissions` свойства,, `Group` и `Owner` для каждого элемента в каталоге. `-FetchProperty`Параметр необходим для получения значений для `ACL` Свойства. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Чтобы получить список содержимого корневого каталога контейнера, не указывайте `-Path` параметр.

## <a name="upload-a-file-to-a-directory"></a>Отправка файла в каталог

Отправьте файл в каталог с помощью `New-AzDataLakeGen2Item` командлета.

В этом примере в каталог `my-directory` отправляется файл `upload.txt`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

В этом примере загружается тот же файл, а затем задаются разрешения, umask, значения свойств и значения метаданных целевого файла. Этот пример также выводит эти значения на консоль.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Чтобы передать файл в корневой каталог контейнера, опустите `-Path` параметр.

## <a name="show-file-properties"></a>Отображение свойств файла

Этот пример получает файл с помощью `Get-AzDataLakeGen2Item` командлета, а затем выводит значения свойств на консоль.

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

Удалите файл с помощью `Remove-AzDataLakeGen2Item` командлета.

В этом примере удаляется файл с именем `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

С помощью параметра можно `-Force` удалить файл без запроса.

## <a name="manage-access-control-lists-acls"></a>Управление списками управления доступом (ACL)

Вы можете получать, задавать и обновлять разрешения на доступ к каталогам и файлам.

> [!NOTE]
> Если вы используете Azure Active Directory для авторизации команд, убедитесь, что субъекту безопасности назначена [роль владельца данных BLOB-объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и последствиях их изменения см. на странице [Контроль доступа в Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Получение списка ACL

Получите список ACL для каталога или файла с помощью `Get-AzDataLakeGen2Item` командлета.

Этот пример возвращает список ACL корневого каталога **контейнера** , а затем ВЫВОДИТ список ACL на консоль.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Этот пример возвращает список ACL для **каталога** , а затем ВЫВОДИТ список ACL на консоль.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Этот пример возвращает список ACL для **файла** , а затем ВЫВОДИТ список ACL на консоль.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

На следующем изображении показаны выходные данные после получения списка ACL для каталога.

![Получение выходных данных ACL для каталога](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

В этом примере владелец имеет разрешения на чтение, запись и выполнение. Группа-владелец имеет разрешения только на чтение и выполнение. Дополнительные сведения о списках управления доступом в Azure Data Lake Storage 2-го поколения см. на [этой странице](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Настройка списка ACL

Используйте `set-AzDataLakeGen2ItemAclObject` командлет, чтобы создать список ACL для пользователя-владельца, группы-владельца или других пользователей. Затем используйте `Update-AzDataLakeGen2Item` командлет для фиксации ACL.

Этот пример задает список управления доступом для корневого каталога **контейнера** для пользователя-владельца, группы-владельца или других пользователей, а затем ВЫВОДИТ список ACL на консоль.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Этот пример задает список ACL для **каталога** пользователя-владельца, группы-владельца или других пользователей, а затем ВЫВОДИТ список ACL на консоль.

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
В этом примере список ACL задается для **файла** , владеющего пользователем, группой-владельцем или другими пользователями, а затем на консоль выводится список ACL.

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

На следующем изображении показаны выходные данные после установки списка ACL для файла.

![Получить выходные данные ACL для файла](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

В этом примере владелец и группа-владелец имеют разрешения только на чтение и запись. У всех остальных пользователей есть разрешения на запись и выполнение. Дополнительные сведения о списках управления доступом в Azure Data Lake Storage 2-го поколения см. на [этой странице](data-lake-storage-access-control.md).

### <a name="add-or-update-an-acl-entry"></a>Добавление или обновление записи ACL

Сначала получите список ACL. Затем с помощью `set-AzDataLakeGen2ItemAclObject` командлета добавьте или обновите запись ACL. Используйте `Update-AzDataLakeGen2Item` командлет для фиксации ACL.

В этом примере создается или обновляется список ACL для пользователя в **каталоге** .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

### <a name="remove-an-acl-entry"></a>Удаление записи ACL

В этом примере удаляется запись из существующего списка ACL.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="set-an-acl-recursively"></a>Рекурсивное задание списка ACL

Можно рекурсивно добавлять, обновлять и удалять списки управления доступом для существующих дочерних элементов родительского каталога без необходимости вносить эти изменения отдельно для каждого дочернего элемента. Дополнительные сведения см. в разделе [Настройка списков управления доступом (ACL) рекурсивно для Azure Data Lake Storage 2-го поколения](recursive-access-control-lists.md).

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Сопоставление Gen1 с Gen2

В следующей таблице показано, как командлеты, используемые для Data Lake Storage 1-го поколения сопоставляться с командлетами для Data Lake Storage 2-го поколения.

|Командлет Data Lake Storage 1-го поколения| Командлет Data Lake Storage 2-го поколения| Примечания |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|По умолчанию командлет Get-AzDataLakeGen2ChildItem перечисляет только дочерние элементы первого уровня. Параметр-recursive Рекурсивно перечисляет дочерние элементы. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Выходные элементы командлета Get-AzDataLakeGen2Item имеют следующие свойства: ACL, владелец, группа, разрешение.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Командлет Get-AzDataLakeGen2FileContent загрузить содержимое файла в локальный файл.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Этот командлет передает содержимое нового файла из локального файла.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Командлет Update-AzDataLakeGen2Item обновляет только один элемент, а не рекурсивно. Если требуется обновлять рекурсивно, перечислите элементы с помощью командлета Get-AzDataLakeStoreChildItem, а затем выполните конвейер в командлете Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Если элемент не существует, командлет Get-AzDataLakeGen2Item сообщит об ошибке.|

## <a name="see-also"></a>См. также статью

* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Командлеты PowerShell для службы хранилища](/powershell/module/az.storage)
