---
title: Использование PowerShell для файлов & ACL в Azure Data Lake Storage 2-го поколения (Предварительная версия)
description: Используйте командлеты PowerShell для управления каталогами и списками управления доступом к файлам и каталогам (ACL) в учетных записях хранения с включенным иерархическое пространством имен (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: f2a2eaa3224fff117a30dfb742b4f8a35196dba4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973906"
---
# <a name="use-powershell-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Использование PowerShell для файлов & ACL в Azure Data Lake Storage 2-го поколения (Предварительная версия)

В этой статье показано, как использовать PowerShell для создания каталогов, файлов и разрешений в учетных записях хранения с включенным иерархическое пространством имен (HNS) и управления ими. 

> [!IMPORTANT]
> Модуль PowerShell, представленный в этой статье, сейчас находится в общедоступной предварительной версии.

[Gen1 Gen2 сопоставления](#gen1-gen2-map) | [Отправить отзыв](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Технические условия

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранения, в которой включено иерархическое пространство имен (HNS). Чтобы создать его, выполните [следующие](data-lake-storage-quickstart-create-account.md) инструкции.
> * .NET Framework установлен или больше 4.7.2. См. раздел [Download .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Версия PowerShell `5.1` или более поздняя.

## <a name="install-powershell-modules"></a>Установка модулей PowerShell

1. Убедитесь, что установленная версия PowerShell `5.1` или выше, с помощью следующей команды. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    Сведения об обновлении версии PowerShell см. в разделе [обновление существующих Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) .
    
2. Установите последнюю версию модуля **PowershellGet** . Затем закройте и снова откройте консоль PowerShell.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Установите **AZ. Storage,** выполнив предварительную версию модуля.

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    Дополнительные сведения об установке модулей PowerShell см. [в статье Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) .

## <a name="connect-to-the-account"></a>Подключение к учетной записи

1. Откройте командное окно Windows PowerShell.

2. Войдите в подписку Azure с помощью команды `Connect-AzAccount` и следуйте инструкциям на экране.

   ```powershell
   Connect-AzAccount
   ```

3. Если ваше удостоверение связано с более чем одной подпиской, установите активную подписку на подписку учетной записи хранения, которая будет создавать каталоги и управлять ими в.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```

   Замените значение заполнителя `<subscription-id>` ИДЕНТИФИКАТОРом подписки.

4. Получите учетную запись хранения.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   ```

   * Замените значение заполнителя `<resource-group-name>` именем группы ресурсов.

   * Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

5. Получение контекста учетной записи хранения.

   ```powershell
   $ctx = $storageAccount.Context
   ```

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система выступает в качестве контейнера для файлов. Его можно создать с помощью командлета `New-AzDatalakeGen2FileSystem`. 

В этом примере создается файловая система с именем `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>создать каталог;

Создайте ссылку на каталог с помощью командлета `New-AzDataLakeGen2Item`. 

В этом примере в файловую систему добавляется каталог с именем `my-directory`.

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

Этот пример получает каталог с помощью командлета `Get-AzDataLakeGen2Item`, а затем выводит значения свойств на консоль.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуйте или переместите каталог с помощью командлета `Move-AzDataLakeGen2Item`.

В этом примере каталог `my-directory` переименовывается в имя `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

В этом примере каталог с именем `my-directory` перемещается в подкаталог `my-directory-2` с именем `my-subdirectory`. Этот пример также применяет umask к подкаталогу.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог с помощью командлета `Remove-AzDataLakeGen2Item`.

В этом примере удаляется каталог с именем `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Можно использовать параметр `-Force`, чтобы удалить файл без запроса.

## <a name="download-from-a-directory"></a>Скачать из каталога

Скачайте файл из каталога с помощью командлета `Get-AzDataLakeGen2ItemContent`.

В этом примере скачивается файл с именем `upload.txt` из каталога с именем `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Перечислите содержимое каталога с помощью командлета `Get-AzDataLakeGen2ChildItem`.

В этом примере выводится содержимое каталога с именем `my-directory`. 

Чтобы получить список содержимого файловой системы, не указывайте параметр `-Path` из команды.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

В этом примере выводится содержимое каталога с именем `my-directory`, а также списки управления доступом в списке. Он также использует параметр `-Recurse` для перечисления содержимого всех подкаталогов.

Чтобы получить список содержимого файловой системы, не указывайте параметр `-Path` из команды.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
```

## <a name="upload-a-file-to-a-directory"></a>Передача файла в каталог

Отправьте файл в каталог с помощью командлета `New-AzDataLakeGen2Item`.

В этом примере файл с именем `upload.txt` перегружается в каталог с именем `my-directory`. 

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
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Отображение свойств файла

Этот пример получает файл с помощью командлета `Get-AzDataLakeGen2Item`, а затем выводит на консоль значения свойств.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>Удаление файла

Удалите файл с помощью командлета `Remove-AzDataLakeGen2Item`.

В этом примере удаляется файл с именем `upload.txt`. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Можно использовать параметр `-Force`, чтобы удалить файл без запроса.

## <a name="manage-access-permissions"></a>Управление разрешениями на доступ

Можно получать, задавать и обновлять разрешения на доступ к каталогам и файлам.

### <a name="get-directory-and-file-permissions"></a>Получение разрешений для каталогов и файлов

Получите список ACL для каталога или файла с помощью командлета `Get-AzDataLakeGen2Item`.

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

### <a name="set-directory-and-file-permissions"></a>Задать разрешения для каталогов и файлов

Используйте командлет `New-AzDataLakeGen2ItemAclObject`, чтобы создать список ACL для пользователя-владельца, группы-владельца или других пользователей. Затем используйте командлет `Update-AzDataLakeGen2Item`, чтобы зафиксировать список ACL.

Этот пример задает список ACL для **каталога** пользователя-владельца, группы-владельца или других пользователей, а затем ВЫВОДИТ список ACL на консоль.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
В этом примере список ACL задается для **файла** , владеющего пользователем, группой-владельцем или другими пользователями, а затем на консоль выводится список ACL.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

На следующем рисунке показаны выходные данные после установки списка управления доступом для файла.

![Получить выходные данные ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

В этом примере пользователь-владелец и группа-владелец имеют только разрешения на чтение и запись. Все остальные пользователи имеют разрешения на запись и выполнение. Дополнительные сведения о списках управления доступом см. [в разделе Контроль доступа в Azure Data Lake Storage 2-го поколения](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Обновление каталога и разрешений на файлы

Используйте командлет `Get-AzDataLakeGen2Item`, чтобы получить список ACL для каталога или файла. Затем используйте командлет `New-AzDataLakeGen2ItemAclObject`, чтобы создать новую запись ACL. Используйте командлет `Update-AzDataLakeGen2Item`, чтобы применить новый список ACL.

Этот пример дает пользователю разрешение на запись и выполнение в каталоге.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```
Этот пример дает пользователю разрешение на запись и выполнение для файла.

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $acl
```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Задание разрешений для всех элементов в файловой системе

Можно использовать `Get-AzDataLakeGen2Item` и параметр `-Recurse` вместе с командлетом `Update-AzDataLakeGen2Item`, чтобы рекурсивно задавать список управления доступом для всех каталогов и файлов в файловой системе. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Сопоставление Gen1 с Gen2

В следующей таблице показано, как командлеты, используемые для Data Lake Storage 1-го поколения сопоставляться с командлетами для Data Lake Storage 2-го поколения.

|Командлет Data Lake Storage 1-го поколения| Командлет Data Lake Storage 2-го поколения| Заметки |
|--------|---------|-----|
|Get-Аздаталакесторечилдитем|Get-AzDataLakeGen2ChildItem|По умолчанию командлет Get-AzDataLakeGen2ChildItem перечисляет только дочерние элементы первого уровня. Параметр-recursive Рекурсивно перечисляет дочерние элементы. |
|Get-Аздаталакестореитем<br>Get-Аздаталакестореитемаклентри<br>Get-Аздаталакестореитемовнер<br>Get-Аздаталакестореитемпермиссион|Get-AzDataLakeGen2Item|Выходные элементы командлета Get-AzDataLakeGen2Item имеют следующие свойства: ACL, владелец, группа, разрешение.|
|Get-Аздаталакестореитемконтент|Get-AzDataLakeGen2FileContent|Командлет Get-AzDataLakeGen2FileContent скачивает содержимое файла в локальный файл.|
|Move-Аздаталакестореитем|Move-AzDataLakeGen2Item||
|New-Аздаталакестореитем|New-AzDataLakeGen2Item|Этот командлет передает содержимое нового файла из локального файла.|
|Remove-Аздаталакестореитем|Remove-AzDataLakeGen2Item||
|Set-Аздаталакестореитемовнер<br>Set-Аздаталакестореитемпермиссион<br>Set-Аздаталакестореитемакл|Update-AzDataLakeGen2Item|Командлет Update-AzDataLakeGen2Item обновляет только один элемент, а не рекурсивно. Если требуется выполнить рекурсивное обновление, перечислите элементы с помощью командлета Get-Аздаталакесторечилдитем, а затем выполните конвейер для командлета Update-AzDataLakeGen2Item.|
|Test-Аздаталакестореитем|Get-AzDataLakeGen2Item|Если элемент не существует, командлет Get-AzDataLakeGen2Item сообщит об ошибке.|



## <a name="see-also"></a>Дополнительные материалы

* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Использование Azure PowerShell со службой хранилища Azure](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Командлеты службы хранилища PowerShell](/powershell/module/az.storage).

