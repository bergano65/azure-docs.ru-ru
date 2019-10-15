---
title: Краткое руководство Azure. Создание большого двоичного объекта в хранилище объектов с помощью Python | Документация Майкрософт
description: В рамках этого краткого руководства вы создадите учетную запись хранения и контейнер в хранилище объектов (больших двоичных объектов). Затем используйте клиентскую библиотеку службы хранилища для языка Python, чтобы отправить большой двоичный объект в службу хранилища Azure, скачать его и составить список больших двоичных объектов в контейнере.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/11/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: a6c0ae8c63739d37ad8417d1dff2d0f7181e6ae9
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981225"
---
# <a name="quickstart-upload-download-and-list-blobs-with-python"></a>Краткое руководство. Отправка, скачивание и составление списка больших двоичных объектов с помощью Python

Из этого краткого руководства вы узнаете, как использовать Python для отправки, скачивания и перечисления блочных BLOB-объектов в контейнере в хранилище BLOB-объектов Azure. Большие двоичные объекты — это объекты, которые могут содержать большие объемы текстовых или двоичных данных, включая изображения, документы, потоковую передачу мультимедиа и архивные данные. Большие двоичные объекты в службе хранилища Azure отличаются от файловых ресурсов, таблиц без схемы и очередей сообщений.  Дополнительную информацию см. в разделе [Общие сведения о службе хранилища Azure](/azure/storage/common/storage-introduction).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Прежде чем приступить к работе, убедитесь, что у вас установлены следующие дополнительные компоненты:

* [Python](https://www.python.org/downloads/)

* [Пакет SDK службы хранилища Azure для Python](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>Загрузка примера приложения

[Пример приложения](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git), используемый в этом кратком руководстве, является простым приложением Python.  

Используйте следующую команду [git](https://git-scm.com/), чтобы скачать приложение в среду разработки. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Чтобы проверить программу Python, откройте файл *example.py* в корне репозитория.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища

В приложении нужно указать имя и ключ учетной записи хранения, чтобы создать объект `BlockBlobService`.

1. Откройте файл *example.py* с помощью обозревателя решений в IDE.

1. Замените значения `accountname` и `accountkey` именем и ключом учетной записи хранения.

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Сохраните и закройте файл.

## <a name="run-the-sample"></a>Запуск примера

Пример программы создает тестовый файл в папке *Документы*, отправляет файл в хранилище BLOB-объектов, выводит список BLOB-объектов в файле и скачивает файл с новым именем.

1. Установка зависимостей:

    ```console
    pip install azure-storage-blob
    ```

1. Перейдите к примеру приложения:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Запустите пример:

    ```console
    python example.py
    ```

    Результат будет выглядеть примерно так:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. Прежде чем продолжить, проверьте наличие двух файлов в папке *Документы*.

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_\<universally-unique-identifier\>DOWNLOADED*

1. Вы можете открыть их и убедиться, что они идентичны.

    Также можно воспользоваться таким средством, как [Обозреватель службы хранилища Azure](https://storageexplorer.com). Это удобно для просмотра файлов в хранилище BLOB-объектов. Обозреватель службы хранилища Azure — это бесплатное кроссплатформенное средство для доступа к данным учетной записи хранения. 

1. После проверки файлов нажмите любую клавишу для завершения демонстрации и удаления тестовых файлов.

## <a name="learn-about-the-sample-code"></a>Посмотреть сведения о примере кода

Теперь вы знаете, что делает этот пример. Давайте откроем файл *example.py* и изучим его код.

### <a name="get-references-to-the-storage-objects"></a>Получение ссылок на объекты хранилища

В этом разделе вы создадите экземпляры объектов и контейнер, а затем зададите для контейнера разрешения на общий доступ к BLOB-объектам. Вы вызываете контейнер `quickstartblobs`. 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

Сначала создайте ссылки на объекты, используемые для доступа к хранилищу BLOB-объектов и управления им. Эти объекты зависят друг от друга, и каждый из них используется следующим в списке объектом.

* Создайте экземпляр объекта **BlockBlobService**, указывающий на службу BLOB-объектов в учетной записи хранения. 

* Создайте объект **CloudBlobContainer**, представляющий контейнер, к которому выполняется доступ. Система использует контейнеры для организации больших двоичных объектов аналогично папкам для упорядочения файлов на компьютере.

Теперь у вас есть облачный контейнер BLOB-объектов. Вы можете создать объект **CloudBlockBlob**, который указывает на конкретный интересующий вас BLOB-объект, и выполнять с ним различные операции: отправку, скачивание или копирование.

> [!IMPORTANT]
> Имена контейнеров должны состоять из знаков нижнего регистра. Дополнительные сведения об именовании контейнеров и больших двоичных объектов см. в статье [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

### <a name="upload-blobs-to-the-container"></a>Отправка BLOB-объектов в контейнер

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. Блочные BLOB-объекты могут иметь размер 4,7 ТБ и представлять собой любые объекты, начиная от электронных таблиц Excel до больших видеофайлов. Вы можете использовать добавочные большие двоичные объекты для ведения журнала, например, если требуется выполнять запись в файл и добавлять дополнительные сведения. Страничные BLOB-объекты в основном используются для файлов виртуального жесткого диска (VHD), которые поддерживают виртуальные машины с инфраструктурой как услугой (ВМ IaaS). Чаще всего используются блочные BLOB-объекты. В этом кратком руководстве используются блочные BLOB-объекты.

Чтобы отправить файл в большой двоичный объект, получите полный путь к файлу, соединив имя каталога и имя файла на локальном диске. Затем вы можете передать файл по указанному пути, используя метод `create_blob_from_path`. 

Этот пример кода создает локальный файл, который система использует для отправки и скачивания. Он сохраняет отправляемый системой файл в переменной *full_path_to_file*, а имя BLOB-объекта — в переменной *local_file_name*. Этот пример файла отправляется в контейнер с именем `quickstartblobs`:

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Существует несколько методов отправки, которые можно использовать с хранилищем BLOB-объектов. Например, если у вас есть поток памяти, вы можете использовать метод `create_blob_from_stream`, а не `create_blob_from_path`. 

### <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Следующий код создает `generator` для метода `list_blobs`. Код просматривает список больших двоичных объектов в контейнере и выводит их имена в консоль.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Скачивание больших двоичных объектов


Чтобы скачать большие двоичные объекты на локальный диск, используйте метод `get_blob_to_path`.
Следующий код скачивает BLOB-объект, который мы передали в предыдущем разделе. К имени BLOB-объекта система добавляет *_DOWNLOADED*, поэтому на локальном диске присутствуют оба файла.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Очистка ресурсов
Если большие двоичные объекты, отправленные при работе с этим руководством, больше не нужны, удалите весь контейнер с помощью метода `delete_container`. Для удаления отдельных файлов используйте метод `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Ресурсы для разработки приложений Python с большими двоичными объектами

Ознакомьтесь со следующими дополнительными ресурсами для разработки Python с использованием хранилища BLOB-объектов:

### <a name="binaries-and-source-code"></a>Двоичные файлы и исходный код

- Просматривайте, скачивайте и устанавливайте [исходный код клиентской библиотеки Python](https://github.com/Azure/azure-storage-python) для службы хранилища Azure в GitHub.

### <a name="client-library-reference-and-samples"></a>Справочник по клиентской библиотеке и примеры

- Дополнительные сведения о клиентской библиотеке Python см. в разделе [Библиотеки службы хранилища Azure для Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Изучите [примеры для хранилища BLOB-объектов](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob), написанные с использованием клиентской библиотеки Python.

## <a name="next-steps"></a>Дополнительная информация
 
Из этого краткого руководства вы узнали, как передавать файлы между локальным диском и хранилищем BLOB-объектов Azure с помощью Python. 

Дополнительные сведения об Обозревателе службы хранилища и BLOB-объектах см. в статье [Управление ресурсами хранилища BLOB-объектов Azure с помощью обозревателя хранилищ](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
