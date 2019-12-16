---
title: Краткое руководство. Создание большого двоичного объекта с помощью PowerShell
titleSuffix: Azure Storage
description: В рамках этого краткого руководства вы используете Azure PowerShell в хранилище объектов (больших двоичных объектов). отправите большой двоичный объект в службу хранилища Azure, скачаете его и составите список больших двоичных объектов в контейнере с помощью Azure PowerShell.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c5d32b91043d310e51143357ad51631463f7e991
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892504"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Краткое руководство. Отправка, скачивание и составление списка больших двоичных объектов с помощью PowerShell

С помощью модуля Azure PowerShell создайте ресурсы Azure и управляйте ими. Процессы создания ресурсов Azure и управления ими можно выполнить с помощью командной строки PowerShell или скриптов. В этом руководстве рассматривается использование PowerShell для передачи файлов между локальным диском и хранилищем BLOB-объектов Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Предварительные требования

Для доступа к службе хранилища Azure требуется подписка Azure. Если у вас еще нет подписки, вы можете [создать бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Для работы с этим кратким руководством требуется модуль Azure PowerShell 0.7 или более поздней версии. Чтобы узнать версию, выполните команду `Get-InstalledModule -Name Az -AllVersions | select Name,Version`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Создание контейнера

Большие двоичные объекты всегда отправляются в контейнер. Вы можете упорядочивать группы больших двоичных объектов аналогично организации файлов в папках на компьютере.

Задайте имя контейнера, а затем создайте контейнер с помощью командлета [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer). Задайте разрешения `blob`, чтобы предоставить открытый доступ к файлам. В этом примере контейнеру присвоено имя *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Отправка BLOB-объектов в контейнер

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. VHD-файлы, поддерживаемые для резервных виртуальных машин IaaS, являются страничными BLOB-объектами. Используйте добавочные большие двоичные объекты для ведения журнала, например, если требуется выполнять запись в файл и добавлять дополнительные сведения. Большинство файлов, находящихся в хранилище BLOB-объектов, представляют собой блочные BLOB-объекты. 

Для передачи файла в блочный BLOB-объект получите ссылку на контейнер, а затем получите ссылку на блочный BLOB-объект в этом контейнере. При наличии ссылки на большой двоичный объект вы можете отправлять в него данные с помощью командлета [Set-AzStorageBlobContent](/powershell/module/az.storage/set-AzStorageblobcontent). Эта операция создает большой двоичный объект, если он еще не существует, или заменяет имеющийся.

В следующих примерах показана отправка файлов *Image001.jpg* и *Image002.png* из папки *D:\\_TestImages* на локальном диске в созданный контейнер.

```powershell
# upload a file
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Прежде чем продолжить, отправьте нужное количество файлов.

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Получите список больших двоичных объектов в контейнере с помощью командлета [Get-AzStorageBlob](/powershell/module/az.storage/get-AzStorageblob). В этом примере показаны только имена отправленных BLOB-объектов.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Скачивание больших двоичных объектов

Скачайте большие двоичные объекты на локальный диск. Для каждого скачиваемого большого двоичного объекта задайте имя и вызовите командлет [Get-AzStorageBlobContent](/powershell/module/az.storage/get-AzStorageblobcontent).

В этом примере показано скачивание больших двоичных объектов на локальный диск *D:\\_TestImages\Downloads*. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Передача данных с помощью AzCopy

Программа [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) является дополнительным способом высокопроизводительной передачи данных с поддержкой сценариев для хранилища Azure. С помощью AzCopy можно передавать данные из хранилища BLOB-объектов, таблиц и файлов и обратно.

В качестве краткого примера здесь приводится команда AzCopy для отправки файла *myfile.txt* в контейнер *mystoragecontainer* в окне PowerShell.

```powershell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите все созданные ресурсы. Самым простым способом удалить ресурсы является удаление группы ресурсов. Удаление группы ресурсов также приведет к удалению всех ресурсов, которые она содержит. В следующем примере при удалении группы ресурсов удаляется учетная запись хранения и сама группа ресурсов.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководством вы передали файлы между локальным диском и хранилищем BLOB-объектов Azure. Дополнительные сведения о работе с хранилищем BLOB-объектов с помощью PowerShell см. в статье "Использование Azure PowerShell со Службой хранилища Azure".

> [!div class="nextstepaction"]
> [Использование Azure PowerShell со службой хранилища Azure](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Справочник по командлетам PowerShell для службы хранилища Microsoft Azure

* [Командлеты PowerShell для службы хранилища](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Обозреватель службы хранилища Microsoft Azure

* [Обозреватель хранилищ Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.
