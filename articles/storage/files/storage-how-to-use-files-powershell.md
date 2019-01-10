---
title: Краткое руководство по управлению общими файловыми ресурсами Azure с помощью Azure PowerShell
description: Используйте это краткое руководство, чтобы узнать, как управлять общими файловыми ресурсами Azure с помощью Azure PowerShell.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: a8ac01850c090b36a5b9d896f6de6c122edfbcaa
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628431"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Краткое руководство. Создание общих файловых ресурсов Azure и управление ими с помощью Azure PowerShell 
В этом руководстве рассматриваются основы работы с [общими файловыми ресурсами Azure](storage-files-introduction.md) с помощью PowerShell. Общие файловые ресурсы Azure отличаются от других ресурсов тем, что хранятся в облаке и поддерживаются платформой Azure. Общие файловые ресурсы Azure поддерживают отраслевой протокол SMB и позволяют совместно использовать файлы на нескольких компьютерах, а также в нескольких приложениях и экземплярах. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Если вы хотите установить и использовать PowerShell локально для работы с этим руководством, вам понадобится Az модуля Azure PowerShell 0.7 или более поздней версии. Чтобы узнать, какую версию модуля Azure PowerShell вы используете, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить `Login-AzAccount`, чтобы войти в учетную запись Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. Если у вас еще нет группы ресурсов Azure, вы можете создать ее с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в регионе "Восточная часть США".

```azurepowershell-interactive
New-AzResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>Создание учетной записи хранения
Учетная запись хранения представляет собой общий пул носителей, который можно использовать для развертывания файловых ресурсов Azure или других ресурсов хранения, например больших двоичных объектов или очередей. Учетная запись хранения может содержать любое количество общих ресурсов, а общий ресурс может содержать любое количество файлов, насколько позволяет емкость учетной записи хранения.

В этом примере создается учетная запись хранения с помощью командлета [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Учетная запись хранения называется *mystorageaccount<random number>*, и к переменной **$storageAcct** добавляется ссылка на эту учетную запись. Имена учетных записей хранения должны быть уникальными. Чтобы сделать имя уникальным, используйте команду `Get-Random` для добавления к нему номера. 

```azurepowershell-interactive 
$storageAcct = New-AzStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>создать файловый ресурс Azure;
Теперь можно создать первый файловый ресурс Azure. Вы можете создать файловый ресурс с помощью командлета [New-AzStorageShare](/powershell/module/azure.storage/new-AzStorageshare). В этом примере создается общий ресурс с именем `myshare`.

```azurepowershell-interactive
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

Имена общих ресурсов должны содержать только строчные буквы, цифры и отдельные дефисы, и они не могут начинаться с дефиса. Дополнительные сведения о присвоении имен общим папкам и файлам см. в статье [Именование общих ресурсов, каталогов, файлов и метаданных и ссылка на них](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Использование общего файлового ресурса Azure
Служба файлов Azure обеспечивает два способа работы с файлами и папками в общем файловом ресурсе Azure: стандартный отраслевой [протокол Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) и [протокол File REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Чтобы установить общий файловый ресурс с помощью SMB, ознакомьтесь со следующим документом (с учетом вашей ОС):
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Использование общего файлового ресурса Azure с протоколом File REST 
С протоколом File REST вы можете работать напрямую (т. е. вручную обрабатывать вызовы HTTP REST), но самым распространенным способом использования протокола File REST является использование модуля Azure PowerShell, [Azure CLI](storage-how-to-use-files-cli.md) или пакета SDK для службы хранилища Azure. Все это обеспечивает хорошую оболочку вокруг протокола File REST на языке сценариев или программирования по вашему выбору.  

В большинстве случаев используется файловый ресурс Azure по протоколу SMB, так как это позволяет использовать имеющиеся приложения и инструменты, на использование которых у вас должна быть возможность, но есть несколько причин, по которым выгодно использовать File REST API, а не SMB, например:

- Вы подключаетесь к общему файловому ресурсу из PowerShell Cloud Shell (которая не может подключать общие файловые ресурсы по SMB).
- Вам необходимо выполнить сценарий или приложение из клиента, который не может подключать ресурсы SMB, например локальные клиенты, у которых нет разблокированного порта 445.
- Вы используете бессерверные ресурсы, такие как [Функции Azure](../../azure-functions/functions-overview.md). 

В следующих примерах показано, как использовать модуль Azure PowerShell для управления файловым ресурсом Azure с помощью протокола File REST. 

#### <a name="create-directory"></a>Создание каталога
Чтобы создать каталог с именем *myDirectory* в корне файлового ресурса Azure, используйте командлет [New-AzStorageDirectory](/powershell/module/azure.storage/new-AzStoragedirectory).

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Отправка файла
Чтобы продемонстрировать, как отправить файл с помощью командлета [Set-AzStorageFileContent](/powershell/module/azure.storage/set-AzStoragefilecontent), сначала необходимо создать нужный файл на временном диске PowerShell Cloud Shell. 

Этот пример указывает текущую дату и время в новом файле на временном диске, а затем загружает этот файл в файловый ресурс.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

При работе в PowerShell локально `C:\Users\ContainerAdministrator\CloudDrive\` следует заменить путем, который имеется на вашем компьютере.

После отправки файла вы можете использовать командлет [Get-AzStorageFile](/powershell/module/Azure.Storage/Get-AzStorageFile), чтобы убедиться, что файл отправлен в файловый ресурс Azure. 

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>скачать файл;
С помощью командлета [Get-AzStorageFileContent](/powershell/module/azure.storage/get-AzStoragefilecontent) вы можете скачать копию только что отправленного файла на временный диск Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

После загрузки файла вы можете использовать команду `Get-ChildItem`, чтобы увидеть, что файл был загружен на временный диск PowerShell Cloud Shell.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>Копирование файлов
Одна из распространенных задач заключается в копировании файлов из одного файлового ресурса в другой или из контейнера хранилища BLOB-объектов Azure и в него. Чтобы продемонстрировать эту функцию, вы можете создать общий ресурс и скопировать только что отправленный в него файл с помощью командлета [Start-AzStorageFileCopy](/powershell/module/azure.storage/start-AzStoragefilecopy). 

```azurepowershell-interactive
New-AzStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Если сейчас вывести список файлов в новом файловом ресурсе, вы увидите скопированный файл.

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Хотя командлет `Start-AzStorageFileCopy` удобен для ad-hoc-перемещения файлов между файловыми ресурсами Azure и контейнерами хранилища BLOB-объектов Azure, рекомендуем использовать AzCopy для больших перемещений (с точки зрения количества или размера перемещаемых файлов). См. дополнительные сведения об использовании [AzCopy для Windows](../common/storage-use-azcopy.md) and [AzCopy для Linux](../common/storage-use-azcopy-linux.md). Программу AzCopy нужно установить локально — она недоступна в Cloud Shell. 

## <a name="create-and-manage-share-snapshots"></a>Создание моментальных снимков общих ресурсов и управление ими
Еще одна полезная задача, которую можно выполнить с файловым ресурсом Azure, — создать его моментальные снимки. Моментальный снимок сохраняет состояние файлового ресурса Azure на момент определенной точки во времени. Моментальные снимки общих ресурсов аналогичны тем, которые создаются с помощью уже знакомых вам технологий операционной системы, например:
- [служба теневого копирования томов (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) для файловых систем Windows, таких как NTFS и ReFS;
- [диспетчер логических томов (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) для систем Linux;
- [файловая система Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) для macOS. 
 Вы можете создать моментальный снимок, используя метод `Snapshot` для объекта PowerShell для общего файлового ресурса, полученного с помощью командлета [Get-AzStorageShare](/powershell/module/azure.storage/get-AzStorageshare). 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Просмотр снимков общего ресурса
Вы можете просмотреть содержимое моментального снимка, передав ссылку на моментальный снимок (`$snapshot`) в параметр `-Share` командлета `Get-AzStorageFile`.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Вывод списка моментальных снимков общих ресурсов
С помощью следующей команды можно получить список сделанных моментальных снимков общего ресурса.

```azurepowershell-interactive
Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Восстановление из моментального снимка общего ресурса
Файл можно восстановить с помощью команды `Start-AzStorageFileCopy`, которая использовалась ранее. В целях этого краткого руководства мы сначала удалим ранее отправленный файл `SampleUpload.txt`. Теперь его можно восстановить из моментального снимка.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Удаление моментального снимка общего ресурса
Моментальный снимок общего ресурса вы можете удалить с помощью командлета [Remove-AzStorageShare](/powershell/module/azure.storage/remove-AzStorageshare) с переменной, содержащей ссылку `$snapshot` в параметре `-Share`.

```azurepowershell-interactive
Remove-AzStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Закончив работу, вы можете удалить группу ресурсов и все связанные с ней ресурсы с помощью командлета [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Кроме того, ресурсы можно удалить по одному.

- Чтобы удалить файловые ресурсы Azure, созданные для этого краткого руководства:

    ```azurepowershell-interactive
    Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
        Remove-AzStorageShare -Context $storageAcct.Context -Name $_.Name
    }
    ```

- Чтобы удалить учетную запись хранения (при этом будут неявно удалены созданные файловые ресурсы Azure, а также другие созданные ресурсы хранения, такие как контейнер хранилища BLOB-объектов Azure).

    ```azurepowershell-interactive
    Remove-AzStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Что представляет собой служба "Файлы Azure"?](storage-files-introduction.md)
