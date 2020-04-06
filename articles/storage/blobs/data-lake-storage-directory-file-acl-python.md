---
title: Лазурное хранилище озер Gen2 Python SDK для файлов & ACLs
description: Используйте Python управлять каталогами и списками управления доступом файлов и каталогов (ACL) в учетных записях хранения с включенным иерархическим пространством имен (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a00713df2cdda626a76cc648826f7e56df214232
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668725"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Используйте Python для управления каталогами, файлами и ACL в Azure Data Lake Storage 2

В этой статье показано, как использовать Python для создания и управления каталогами, файлами и разрешениями в учетных записях хранения с включенным иерархическим пространством имен (HNS). 

[Пакет (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [Образцы](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API ссылка](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html) | [Gen1 на Gen2 отображение](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | Дать[обратную связь](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранилища с включенным иерархическим пространством имен (HNS). Следуйте [этим](data-lake-storage-quickstart-create-account.md) инструкциям, чтобы создать один.

## <a name="set-up-your-project"></a>Настройка проекта

Установите клиентскую библиотеку Azure Data Lake Storage для Python с помощью [пипсов.](https://pypi.org/project/pip/)

```
pip install azure-storage-file-datalake --pre
```

Добавьте эти инструкции по импорту в верхнюю часть файла кода.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Подключение к учетной записи

Чтобы использовать фрагменты в этой статье, необходимо создать экземпляр **DataLakeServiceClient,** представляющий учетную запись хранилища. 

### <a name="connect-by-using-an-account-key"></a>Подключение с помощью ключа учетной записи

Это самый простой способ подключения к учетной записи. 

Этот пример создает экземпляр **DataLakeServiceClient** с помощью ключа учетной записи.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Замените значение заполнителя `storage_account_name` именем вашей учетной записи хранения.

- Замените `storage_account_key` значение заполнителя ключом доступа к учетной записи хранилища.

### <a name="connect-by-using-azure-active-directory-ad"></a>Подключение с помощью активного каталога Azure (AD)

Для проверки подлинности приложения с помощью Azure AD можно использовать [библиотеку клиентов итога Azure](https://pypi.org/project/azure-identity/) для Python.

Этот пример создает экземпляр **DataLakeServiceClient,** используя идентификатор клиента, секрет клиента и идентификатор клиента.  Чтобы получить эти значения, [см. Приобрести токен из Azure AD для авторизации запросов из клиентского приложения.](../common/storage-auth-aad-app.md)

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Для получения дополнительных примеров можно найти [в библиотеке клиентов итога Azure для](https://pypi.org/project/azure-identity/) документации Python.

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система действует как контейнер для файлов. Вы можете создать его, позвонив в **метод FileSystemDataLakeServiceClient.create_file_system.**

Этот пример создает файловую систему под названием `my-file-system`.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на каталог, позвонив в метод **FileSystemClient.create_directory.**

Этот пример добавляет каталог, названный `my-directory` в файловую систему. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуй или переместите каталог, позвонив в метод **DataLakeDirectoryClient.rename_directory.** Передайте путь желаемого каталога параметру. 

Этот пример переименовывает подкаталог `my-subdirectory-renamed`к имени .

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

Удалить каталог, позвонив в **DataLakeDirectoryClient.delete_directory** метод.

Этот пример удаляет каталог `my-directory`с именем .  

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

Получить список управления доступом (ACL) каталога, позвонив **dataLakeDirectoryClient.get_access_control** метод и установить ACL, позвонив **DataLakeDirectoryClient.set_access_control** метод.

> [!NOTE]
> Если приложение разрешает доступ с помощью Active Directory Azure (Azure AD), убедитесь, что принципбезопасности, который приложение использует для авторизации доступа, был назначен [роль владельца данных Storage Blob.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Чтобы узнать больше о том, как применяются разрешения [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ACL и о последствиях их изменения, см.

Этот пример получает и устанавливает ACL `my-directory`каталога с именем . Строка `rwxr-xrw-` дает пользователю-владельцеу читать, писать и выполнять разрешения, дает группе владельцев только читать и выполнять разрешения, а также дает всем остальным разрешение на чтение и запись.

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

## <a name="upload-a-file-to-a-directory"></a>Загрузить файл в каталог 

Во-первых, создайте ссылку на файл в целевом каталоге, создав экземпляр класса **DataLakeFileClient.** Загрузите файл, позвонив в **метод DataLakeFileClient.append_data.** Убедитесь в том, чтобы завершить загрузку, позвонив **dataLakeFileClient.flush_data** метод.

Этот пример загружает текстовый `my-directory`файл в каталог с именем .   

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

> [!TIP]
> Если размер файла большой, код должен будет сделать несколько звонков на метод **DataLakeFileClient.append_data.** Вместо этого рассмотрим метод **DataLakeFileClient.upload_data.** Таким образом, вы можете загрузить весь файл в один вызов. 

## <a name="upload-a-large-file-to-a-directory"></a>Загрузить большой файл в каталог

Используйте метод **DataLakeFileClient.upload_data** для загрузки больших файлов без необходимости совершать несколько звонков на метод **DataLakeFileClient.append_data.**

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Управление разрешениями файлов

Получить список управления доступом (ACL) файла, позвонив **dataLakeFileClient.get_access_control** метод и установить ACL, позвонив **DataLakeFileClient.set_access_control** метод.

> [!NOTE]
> Если приложение разрешает доступ с помощью Active Directory Azure (Azure AD), убедитесь, что принципбезопасности, который приложение использует для авторизации доступа, был назначен [роль владельца данных Storage Blob.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Чтобы узнать больше о том, как применяются разрешения [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ACL и о последствиях их изменения, см.

Этот пример получает и устанавливает ACL `my-file.txt`файла с именем. Строка `rwxr-xrw-` дает пользователю-владельцеу читать, писать и выполнять разрешения, дает группе владельцев только читать и выполнять разрешения, а также дает всем остальным разрешение на чтение и запись.

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

Откройте локальный файл для записи. Затем создайте экземпляр **DataLakeFileClient,** представляющий файл, который вы хотите загрузить. Позвоните **DataLakeFileClient.read_file** читать байты из файла, а затем написать эти байты в местный файл. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Перечислите содержимое каталога, позвонив в метод **FileSystemClient.get_paths,** а затем перечислив результаты.

Этот пример печатает путь каждого субдиректора и файла, который `my-directory`находится в каталоге с именем .

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
* [Пакет (Индекс упаковки Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Примеры](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Отображение Gen1 до Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Дайте обратную связь](https://github.com/Azure/azure-sdk-for-python/issues)
