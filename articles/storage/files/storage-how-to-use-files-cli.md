---
title: Краткое руководство по управлению файловыми ресурсами Azure с помощью Azure CLI
description: Используйте это краткое руководство, чтобы узнать, как управлять файловыми ресурсами Azure с помощью Azure CLI.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 82d1a83dfd96dd6d4c2b37567c745998a6f0cbdb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473516"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Краткое руководство. Создание файловых ресурсов Azure и управление ими с помощью Azure CLI
В этом руководстве рассматриваются основы работы с [файловыми ресурсами Azure](storage-files-introduction.md) с помощью Azure CLI. Общие файловые ресурсы Azure отличаются от других ресурсов тем, что хранятся в облаке и поддерживаются платформой Azure. Общие файловые ресурсы Azure поддерживают отраслевой протокол SMB и позволяют совместно использовать файлы на нескольких компьютерах, а также в нескольких приложениях и экземплярах. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы хотите установить и использовать Azure CLI локально, для выполнения всех действий из данной статьи требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать, как версия Azure CLI установлена, выполните команду **az --version**. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

По умолчанию команды Azure CLI возвращают ответ в формате JSON. Это стандартный способ отправки и получения сообщений из REST API. Для облегчения работы с ответами в формате JSON в некоторых примерах в этой статье с командами Azure CLI используется параметр *запроса*. Этот параметр использует [язык запросов JMESPath](http://jmespath.org/) для синтаксического анализа JSON. Дополнительные сведения о том, как обрабатывать результаты выполнения команд Azure CLI с применением языка запросов JMESPath, см. в [руководстве по JMESPath](http://jmespath.org/tutorial.html).

## <a name="sign-in-to-azure"></a>Вход в Azure
Если вы используете Azure CLI локально, откройте строку и войдите в Azure, если вы еще этого не сделали.

```bash 
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. Если у вас еще нет группы ресурсов Azure, ее можно создать с помощью команды [az group create](/cli/azure/group#create). 

В следующем примере создается группа ресурсов под названием *myResourceGroup* в расположении *Восточная часть США*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Создание учетной записи хранения
Учетная запись хранения представляет собой общий пул носителей, который можно использовать для развертывания файловых ресурсов Azure или других ресурсов хранения, например больших двоичных объектов или очередей. Учетная запись хранения может содержать неограниченное количество файловых ресурсов. В общем ресурсе может храниться любое число файлов, насколько это позволяет емкость учетной записи хранения.

В следующем примере с помощью команды [az storage account create](/cli/azure/storage/account#create) создается учетная запись хранения с именем *mystorageaccount\<случайное число\>*, а затем имя этой учетной записи помещается в переменную `$STORAGEACCT`. Имена учетных записей хранения должны быть уникальными. Используйте `$RANDOM`, чтобы добавить уникальный номер в конец имени учетной записи хранения. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Получение ключа учетной записи хранения
Ключи учетной записи хранения определяют доступ к хранящихся в ней ресурсам. Эти ключи автоматически создаются при создании учетной записи хранения. Ключи учетной записи хранения можно получить с помощью команды [az storage account keys list](/cli/azure/storage/account/keys#list). 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>создать файловый ресурс Azure;
Теперь можно создать первый файловый ресурс Azure. Используйте для этого команду [az storage share create](/cli/azure/storage/share#create). В этом примере создается файловый ресурс Azure с именем *myshare*. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

Имена общих ресурсов должны содержать только строчные буквы, цифры и отдельные дефисы, и они не могут начинаться с дефиса. Дополнительные сведения о присвоении имен общим папкам и файлам см. в статье [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Именование общих ресурсов, каталогов, файлов и метаданных и ссылка на них).

## <a name="use-your-azure-file-share"></a>Использование общего файлового ресурса Azure
Служба файлов Azure обеспечивает два способа работы с файлами и папками в общем файловом ресурсе Azure: стандартный отраслевой [протокол Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) и [протокол File REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Чтобы установить общий файловый ресурс с помощью SMB, ознакомьтесь со следующим документом (с учетом вашей ОС):
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Использование общего файлового ресурса Azure с протоколом File REST 
С протоколом File REST можно работать напрямую (т. е. вручную обрабатывать вызовы HTTP REST), но чаще всего он используется с помощью Azure CLI, [модуля Azure PowerShell](storage-how-to-use-files-powershell.md) или пакета SDK для службы хранилища Azure. Все это обеспечивает хорошую оболочку вокруг протокола File REST на любом языке скриптов или программирования.  

Ожидается, что в большинстве случаев служба файлов Azure будет использовать общий файловый ресурс Azure по протоколу SMB, так как это позволяет использовать имеющиеся приложения и инструменты, на использование которых у вас должна быть возможность, но есть несколько причин, по которым выгодно использовать File REST API, а не SMB, например:

- Вы подключаетесь к общему файловому ресурсу из Azure Bash Cloud Shell (которая не может подключать общие файловые ресурсы по SMB).
- Вам необходимо выполнить сценарий или приложение из клиента, который не может подключать ресурсы SMB, например локальные клиенты, у которых нет разблокированного порта 445.
- Вы используете бессерверные ресурсы, такие как [Функции Azure](../../azure-functions/functions-overview.md). 

В следующих примерах показано, как использовать Azure CLI для управления общим файловым ресурсом Azure с помощью протокола File REST. 

### <a name="create-a-directory"></a>создать каталог;
Чтобы создать каталог с именем *myDirectory* в корне файлового ресурса Azure, выполните команду [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create).

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Отправка файла
Чтобы продемонстрировать передачу файла с помощью команды [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload), сначала создайте файл для отправки на временном диске Cloud Shell. В следующем примере создается, а затем отправляется файл.

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

При работе в Azure CLI локально следует заменить `~/clouddrive` путем, который имеется на вашем компьютере.

После отправки файла можно использовать команду [`az storage file list`](/cli/azure/storage/file#az_storage_file_list), чтобы убедиться, что файл отправлен в файловый ресурс Azure.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>скачать файл;
С помощью команды [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) можно загрузить копию файла, отправленного на временный диск Cloud Shell.

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Копирование файлов
Одна из распространенных задач заключается в копировании файлов из одного файлового ресурса в другой или из контейнера хранилища BLOB-объектов Azure и в него. Для демонстрации этой функциональности создайте новый файловый ресурс. Скопируйте файл, отправленный в только что созданный общий ресурс, с помощью команды [az storage file copy](/cli/azure/storage/file/copy). 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Если сейчас вывести список файлов в новом файловом ресурсе, вы увидите скопированный файл.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Хотя команда `az storage file copy start` удобна для перемещения файлов между файловыми ресурсами Azure и контейнерами хранилища BLOB-объектов Azure, для массовых перемещений рекомендуется использовать AzCopy. (Массовые с точки зрения количества или размера перемещаемых файлов.) См. дополнительные сведения о [AzCopy для Linux](../common/storage-use-azcopy-linux.md) and [AzCopy для Windows](../common/storage-use-azcopy.md). AzCopy должна быть установлена локально. AzCopy недоступна в Cloud Shell. 

## <a name="create-and-manage-share-snapshots"></a>Создание моментальных снимков общих ресурсов и управление ими
Еще одна полезная задача, которую можно выполнить с файловым ресурсом Azure, — создать его моментальные снимки. Моментальный снимок сохраняет состояние общего файлового ресурса Azure на момент определенной точки во времени. Моментальные снимки файловых ресурсов аналогичны тем, которые создаются с помощью уже знакомых вам технологий, например:

- [диспетчер логических томов (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) для систем Linux;
- [файловая система Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) для macOS;
- [служба теневого копирования томов (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) для файловых систем Windows, например NTFS и ReFS, которая позволяет создать снимок общего ресурса с помощью команды [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot):

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Просмотр содержимого моментального снимка общих ресурсов
Чтобы просмотреть содержимое моментального снимка общего ресурса, передайте его метку времени команде `az storage file list` в атрибуте переменной `$SNAPSHOT`.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Вывод списка моментальных снимков общих ресурсов
Список имеющихся моментальных снимков общего ресурса можно получить с помощью следующей команды.

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Восстановление из моментального снимка общего ресурса
Файл можно восстановить с помощью команды `az storage file copy start`, использовавшейся ранее. Сначала удалите отправленный файл SampleUpload.txt, чтобы его можно было восстановить из моментального снимка.

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"
 # Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')
 URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT
 # Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Удаление моментального снимка общего ресурса
Моментальный снимок общего ресурса можно удалить с помощью команды [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete). Воспользуйтесь переменной, содержащей ссылку `$SNAPSHOT` на параметр `--snapshot`.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Закончив работу, удалите группу ресурсов и все связанные с ней ресурсы с помощью команды [`az group delete`](/cli/azure/group#delete). 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Кроме того, ресурсы можно удалить по отдельности.
- Чтобы удалить файловые ресурсы Azure, созданные в ходе выполнения примеров из данной статьи:

    ```azurecli-interactive
    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare" \
        --delete-snapshots include

    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare2" \
        --delete-snapshots include
    ```

- Чтобы удалить саму учетную запись хранения. (При этом опосредованно удаляются созданные файловые ресурсы Azure, а также другие созданные ресурсы хранения, например контейнер службы хранилища BLOB-объектов Azure.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Что представляет собой служба "Файлы Azure"?](storage-files-introduction.md)
