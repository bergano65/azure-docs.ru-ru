---
title: Azure Data Lake Storage 2-го поколения PowerShell для файлов & ACL
description: Используйте командлеты PowerShell для управления каталогами и списками управления доступом к файлам и каталогам (ACL) в учетных записях хранения с включенным иерархическое пространством имен (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: db098210d6de28d9dc1db7e264459f57bc0f4d86
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2020
ms.locfileid: "82161029"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Использование PowerShell для управления каталогами, файлами и списками ACL в Azure Data Lake Storage 2-го поколения

В этой статье показано, как использовать PowerShell для создания каталогов, файлов и разрешений в учетных записях хранения с включенным иерархическое пространством имен (HNS) и управления ими. 

[Gen1 to Gen2 mapping](#gen1-gen2-map) | [Обратное](https://github.com/Azure/azure-powershell/issues) сопоставление Gen1 с Gen2

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранения, в которой включено иерархическое пространство имен (HNS). Чтобы создать его [, выполните следующие](data-lake-storage-quickstart-create-account.md) инструкции.
> * .NET Framework установлен или больше 4.7.2. См. раздел [Download .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Версия `5.1` PowerShell или выше.

## <a name="install-the-powershell-module"></a>Установка модуля PowerShell

1. Убедитесь, что установленная версия PowerShell `5.1` или более поздняя, с помощью следующей команды.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Сведения об обновлении версии PowerShell см. в разделе [обновление существующих Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) .
    
2. Установите **AZ. Storage** Module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Дополнительные сведения об установке модулей PowerShell см. [в статье Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) .

## <a name="connect-to-the-account"></a>Подключение к учетной записи

Откройте командное окно Windows PowerShell, а затем войдите в подписку Azure с помощью `Connect-AzAccount` команды и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

Если ваше удостоверение связано с более чем одной подпиской, установите активную подписку на подписку учетной записи хранения, которая будет создавать каталоги и управлять ими в. В этом примере замените значение `<subscription-id>` заполнителя идентификатором подписки.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Затем выберите способ, которым команды будут получать права доступа к учетной записи хранения. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Вариант 1. получение авторизации с помощью Azure Active Directory (AD)

При таком подходе система гарантирует, что учетная запись пользователя имеет соответствующие назначения управления доступом на основе ролей (RBAC) и разрешения ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Вариант 2. получение авторизации с помощью ключа учетной записи хранения

При таком подходе система не проверяет разрешения RBAC или ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система выступает в качестве контейнера для файлов. Его можно создать с помощью `New-AzDatalakeGen2FileSystem` командлета. 

В этом примере создается файловая система `my-file-system`с именем.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на каталог с помощью `New-AzDataLakeGen2Item` командлета. 

В этом примере в файловую `my-directory` систему добавляется каталог с именем.

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

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуйте или переместите каталог с помощью `Move-AzDataLakeGen2Item` командлета.

В этом примере имя каталога `my-directory` переименовывается в имя. `my-new-directory`

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Используйте параметр `-Force` , если требуется перезаписать без запросов.

В этом примере каталог с именем `my-directory` перемещается в подкаталог с `my-directory-2` именем `my-subdirectory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог с помощью `Remove-AzDataLakeGen2Item` командлета.

В этом примере удаляется каталог `my-directory`с именем. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

С помощью `-Force` параметра можно удалить файл без запроса.

## <a name="download-from-a-directory"></a>Скачать из каталога

Скачайте файл из каталога с помощью `Get-AzDataLakeGen2ItemContent` командлета.

В этом примере загружается `upload.txt` файл с именем из `my-directory`каталога с именем. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Перечислите содержимое каталога с помощью `Get-AzDataLakeGen2ChildItem` командлета. Можно использовать необязательный параметр `-OutputUserPrincipalName` , чтобы получить имя (вместо идентификатора объекта) пользователей.

В этом примере выводится содержимое каталога с `my-directory`именем.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

В следующем примере перечисляются `ACL`свойства `Permissions`, `Group`, и `Owner` для каждого элемента в каталоге. `-FetchProperty` Параметр необходим для получения значений для `ACL` свойства. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Чтобы получить список содержимого файловой системы, не указывайте `-Path` параметр команды.

## <a name="upload-a-file-to-a-directory"></a>Передача файла в каталог

Отправьте файл в каталог с помощью `New-AzDataLakeGen2Item` командлета.

В этом примере загружается файл с `upload.txt` именем в каталог с `my-directory`именем. 

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

В этом примере удаляется файл `upload.txt`с именем. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

С помощью `-Force` параметра можно удалить файл без запроса.

## <a name="manage-access-permissions"></a>Управление разрешениями на доступ

Можно получать, задавать и обновлять разрешения на доступ к файловым системам, каталогам и файлам. Эти разрешения фиксируются в списках управления доступом (ACL).

> [!NOTE]
> Если вы используете Azure Active Directory (Azure AD) для авторизации команд, убедитесь, что участнику безопасности назначена [роль владельца данных BLOB-объекта хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и их влиянии на их изменение см. в разделе [Контроль доступа в Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Получение списка ACL

Получите список ACL для каталога или файла с помощью `Get-AzDataLakeGen2Item`командлета.

Этот пример возвращает список ACL **файловой системы** , а затем ВЫВОДИТ список ACL на консоль.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Этот пример возвращает список ACL для **каталога**, а затем ВЫВОДИТ список ACL на консоль.

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

На следующем рисунке показаны выходные данные после получения списка управления доступом для каталога.

![Получить выходные данные ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

В этом примере пользователь-владелец имеет разрешения на чтение, запись и выполнение. Группа-владелец имеет только разрешения на чтение и выполнение. Дополнительные сведения о списках управления доступом см. [в разделе Контроль доступа в Azure Data Lake Storage 2-го поколения](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Настройка ACL

Используйте `set-AzDataLakeGen2ItemAclObject` командлет, чтобы создать список ACL для пользователя-владельца, группы-владельца или других пользователей. Затем используйте `Update-AzDataLakeGen2Item` командлет для фиксации ACL.

Этот пример задает список ACL в **файловой системе** для пользователя-владельца, группы-владельца или других пользователей, а затем ВЫВОДИТ список ACL на консоль.

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

На следующем рисунке показаны выходные данные после установки списка управления доступом для файла.

![Получить выходные данные ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

В этом примере пользователь-владелец и группа-владелец имеют только разрешения на чтение и запись. Все остальные пользователи имеют разрешения на запись и выполнение. Дополнительные сведения о списках управления доступом см. [в разделе Контроль доступа в Azure Data Lake Storage 2-го поколения](data-lake-storage-access-control.md).


### <a name="set-acls-on-all-items-in-a-file-system"></a>Установка списков управления доступом для всех элементов в файловой системе

Параметр и можно использовать вместе с `Update-AzDataLakeGen2Item` командлетом для рекурсивного задания ACL всех каталогов и файлов в файловой системе. `Get-AzDataLakeGen2Item` `-Recurse` 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
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

<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Сопоставление Gen1 с Gen2

В следующей таблице показано, как командлеты, используемые для Data Lake Storage 1-го поколения сопоставляться с командлетами для Data Lake Storage 2-го поколения.

|Командлет Data Lake Storage 1-го поколения| Командлет Data Lake Storage 2-го поколения| Примечания |
|--------|---------|-----|
|Get-Аздаталакесторечилдитем|Get-AzDataLakeGen2ChildItem|По умолчанию командлет Get-AzDataLakeGen2ChildItem перечисляет только дочерние элементы первого уровня. Параметр-recursive Рекурсивно перечисляет дочерние элементы. |
|Get-Аздаталакестореитем<br>Get-Аздаталакестореитемаклентри<br>Get-Аздаталакестореитемовнер<br>Get-Аздаталакестореитемпермиссион|Get-AzDataLakeGen2Item|Выходные элементы командлета Get-AzDataLakeGen2Item имеют следующие свойства: ACL, владелец, группа, разрешение.|
|Get-Аздаталакестореитемконтент|Get-AzDataLakeGen2FileContent|Командлет Get-AzDataLakeGen2FileContent скачивает содержимое файла в локальный файл.|
|Move-Аздаталакестореитем|Move-AzDataLakeGen2Item||
|New-Аздаталакестореитем|New-AzDataLakeGen2Item|Этот командлет передает содержимое нового файла из локального файла.|
|Remove-Аздаталакестореитем|Remove-AzDataLakeGen2Item||
|Set-Аздаталакестореитемовнер<br>Set-Аздаталакестореитемпермиссион<br>Set-Аздаталакестореитемакл|Update-AzDataLakeGen2Item|Командлет Update-AzDataLakeGen2Item обновляет только один элемент, а не рекурсивно. Если требуется выполнить рекурсивное обновление, перечислите элементы с помощью командлета Get-Аздаталакесторечилдитем, а затем выполните конвейер для командлета Update-AzDataLakeGen2Item.|
|Test-Аздаталакестореитем|Get-AzDataLakeGen2Item|Если элемент не существует, командлет Get-AzDataLakeGen2Item сообщит об ошибке.|

## <a name="see-also"></a>См. также

* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Командлеты PowerShell для службы хранилища](/powershell/module/az.storage)
