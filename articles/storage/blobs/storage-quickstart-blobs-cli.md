---
title: Краткое руководство Azure. Создание большого двоичного объекта в хранилище объектов с помощью Azure CLI | Документация Майкрософт
description: В этом кратком руководстве вы узнаете, как с помощью Azure CLI передать большой двоичный объект в службу хранилища Azure, скачать большой двоичный объект и вывести список больших двоичных объектов в контейнере.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 1f3143eced90f97c090c0005375ef50fe48c5f5f
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747920"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Краткое руководство. Передача, скачивание и составление списка больших двоичных объектов с помощью Azure CLI

Azure CLI — это интерфейс командной строки Azure для управления ресурсами Azure. Вы можете использовать его в браузере с Azure Cloud Shell. Его также можно установить в macOS, Linux или Windows и запускать из командной строки. В этом кратком руководстве содержатся сведения об использовании Azure CLI для отправки и скачивания данных в хранилище BLOB-объектов Azure и обратно.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций в этом руководстве вам понадобится Azure CLI 2.0.4 или более поздней версии. Выполните команду `az --version`, чтобы определить версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Создание контейнера

Большие двоичные объекты всегда отправляются в контейнер. Вы можете упорядочивать группы больших двоичных объектов аналогично организации файлов в папках на компьютере.

Создайте контейнер для хранения больших двоичных объектов с помощью команды [az storage container create](/cli/azure/storage/container).

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>Передача больших двоичных объектов

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. В примерах в этом кратком руководстве показано, как работать с блочными BLOB-объектами.

Сначала создайте файл для передачи в блочный BLOB-объект. Если вы используете Azure Cloud Shell, создайте этот файл с помощью следующей команды.

```bash
vi helloworld
```

Когда файл откроется, щелкните **Вставить**. Введите *Hello World*, а затем нажмите клавишу **ESC**. Затем введите *:x* и нажмите клавишу **ВВОД**.

В этом примере большой двоичный объект отправляется в контейнер, созданный на последнем шаге, с помощью команды [az storage blob upload](/cli/azure/storage/blob). Нет необходимости указывать путь к файлу, так как файл был создан в корневом каталоге.

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

С помощью этой операции создается большой двоичный объект, если он не был создан ранее, или же, если он имеется, происходит его замещение. Прежде чем продолжить, отправьте нужное количество файлов.

Чтобы одновременно отправить несколько файлов, используйте команду [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Выведите список больших двоичных объектов в контейнере, выполнив команду [az storage blob list](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>Загрузка BLOB-объектов

Выполните команду [az storage blob download](/cli/azure/storage/blob), чтобы скачать отправленный ранее большой двоичный объект.

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Передача данных с помощью AzCopy

Программа [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) является дополнительным способом высокопроизводительной передачи данных с поддержкой сценариев для хранилища Azure. AzCopy можно использовать для передачи данных из хранилища BLOB-объектов, таблиц и файлов и обратно.

В следующем примере используется AzCopy для передачи файла *myfile.txt* в контейнер *sample-container*. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны какие-либо ресурсы в группе ресурсов, включая учетную запись хранения, созданную в рамках этого краткого руководства, удалите группу ресурсов с помощью команды [az group delete](/cli/azure/group). Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как передавать файлы между локальной файловой системой и контейнером в хранилище BLOB-объектов Azure. Дополнительные сведения о работе с большими двоичными объектами в службе хранилища Azure см. в руководстве по работе с хранилищем BLOB-объектов Azure.

> [!div class="nextstepaction"]
> [How to: Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Azure CLI](storage-how-to-use-blobs-cli.md)
