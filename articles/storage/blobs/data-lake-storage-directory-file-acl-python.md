---
title: Использование Python для файлов & ACL в Azure Data Lake Storage 2-го поколения (Предварительная версия)
description: Использование Python для управления каталогами и файлами и списками управления доступом к каталогам (ACL) в учетных записях хранения, в которых включено иерархическое пространство имен (HNS).
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a65e8b2c5fbcdd4f6df0f8f39b24fa97b8deeca3
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534431"
---
# <a name="use-python-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Использование Python для файлов & ACL в Azure Data Lake Storage 2-го поколения (Предварительная версия)

В этой статье показано, как использовать Python для создания каталогов, файлов и разрешений в учетных записях хранения с включенным иерархическое пространством имен (HNS) и управления ими. 

> [!IMPORTANT]
> Клиентская библиотека Azure Data Lake Storage для Python в настоящее время доступна в общедоступной предварительной версии.

[Пакет (индекс пакета Python)](https://pypi.org/project/azure-storage-file-datalake/) | [примеры](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [Справочник по API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html) | [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Отправить отзыв](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранения, в которой включено иерархическое пространство имен (HNS). Чтобы создать его [, выполните следующие](data-lake-storage-quickstart-create-account.md) инструкции.

## <a name="set-up-your-project"></a>Настройка проекта

Установите клиентскую библиотеку Azure Data Lake Storage для Python с помощью [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake --pre
```

Добавьте эти операторы импорта в начало файла кода.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
```

## <a name="connect-to-the-account"></a>Подключение к учетной записи

Чтобы использовать фрагменты кода в этой статье, необходимо создать экземпляр **даталакесервицеклиент** , представляющий учетную запись хранения. Самый простой способ получить его — использовать ключ учетной записи. 

В этом примере используется ключ учетной записи для создания экземпляра **даталакесервицеклиент** , представляющего учетную запись хранения. 

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Замените значение заполнителя `storage_account_name` именем вашей учетной записи хранения.

- Замените значение заполнителя `storage-account-key` ключом доступа к учетной записи хранения.

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система выступает в качестве контейнера для файлов. Его можно создать, вызвав метод **филесистемдаталакесервицеклиент. create_file_system** .

В этом примере создается файловая система с именем `my-file-system`.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>создать каталог;

Создайте ссылку на каталог, вызвав метод **филесистемклиент. create_directory** .

В этом примере в файловую систему добавляется каталог с именем `my-directory`. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуйте или переместите каталог, вызвав метод **даталакедиректориклиент. rename_directory** . Передайте путь к нужному каталогу параметру. 

В этом примере подкаталог переименовывается в имя `my-subdirectory-renamed`.

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог, вызвав метод **даталакедиректориклиент. delete_directory** .

В этом примере удаляется каталог с именем `my-directory`.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Управление разрешениями каталога

Получите список управления доступом (ACL) каталога, вызвав метод **даталакедиректориклиент. get_access_control** и ЗАДАВ список ACL, вызвав метод **даталакедиректориклиент. set_access_control** .

В этом примере получается и задается список ACL для каталога с именем `my-directory`. Строка `rwxr-xrw-` предоставляет владельцам разрешения на чтение, запись и выполнение, предоставляет группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным разрешение на чтение и запись.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>Передача файла в каталог 

Сначала создайте ссылку на файл в целевом каталоге, создав экземпляр класса **даталакефилеклиент** . Отправьте файл, вызвав метод **даталакефилеклиент. append_data** . Обязательно завершите передачу, вызвав метод **даталакефилеклиент. flush_data** .

В этом примере текстовый файл перегружается в каталог с именем `my-directory`.   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Управление разрешениями для файлов

Получите список управления доступом (ACL) файла, вызвав метод **даталакефилеклиент. get_access_control** и ЗАДАВ список ACL, вызвав метод **даталакефилеклиент. set_access_control** .

В этом примере получается и задается список управления доступом для файла с именем `my-file.txt`. Строка `rwxr-xrw-` предоставляет владельцам разрешения на чтение, запись и выполнение, предоставляет группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным разрешение на чтение и запись.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>Скачать из каталога 

Откройте локальный файл для записи. Затем создайте экземпляр **даталакефилеклиент** , представляющий файл, который требуется скачать. Вызовите **даталакефилеклиент. read_file** , чтобы прочитать байты из файла, а затем запишите эти байты в локальный файл. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Перечислите содержимое каталога, вызвав метод **филесистемклиент. get_paths** , а затем перечислить результаты.

В этом примере выводится путь к каждому подкаталогу и файлу, расположенному в каталоге с именем `my-directory`.

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>См. также

* [Справочная документация по API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Пакет (индекс пакета Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Примеры](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Сопоставление Gen1 с Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Отправить отзыв](https://github.com/Azure/azure-sdk-for-python/issues)
