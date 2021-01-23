---
title: Azure Data Lake Storage 2-го поколения пакета SDK для Python для файлов & ACL
description: Использование Python для управления каталогами и файлами и списками управления доступом к каталогам (ACL) в учетных записях хранения, в которых включено иерархическое пространство имен (HNS).
author: normesta
ms.service: storage
ms.date: 01/22/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 5036930c7bb49578582fbc1b347b11518579b53e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740624"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Использование Python для управления каталогами, файлами и списками ACL в Azure Data Lake Storage 2-го поколения

В этой статье показано, как использовать Python для создания каталогов, файлов и разрешений в учетных записях хранения с включенным иерархическое пространством имен (HNS) и управления ими. 

[Пакет (индекс пакета Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Примеры](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Справочник](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  по API Сопоставление Gen1 с [Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Отправить отзыв](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * В учетной записи хранения включено иерархическое пространство имен. Выполните [эти](../common/storage-account-create.md) инструкции, чтобы создать учетную запись.

## <a name="set-up-your-project"></a>Настройка проекта

Установите клиентскую библиотеку Azure Data Lake Storage для Python с помощью [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Добавьте эти операторы импорта в начало файла кода.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Подключение к учетной записи

Чтобы использовать фрагменты кода в этой статье, необходимо создать экземпляр **даталакесервицеклиент** , представляющий учетную запись хранения. 

### <a name="connect-by-using-an-account-key"></a>Подключение с помощью ключа учетной записи

Это самый простой способ подключения к учетной записи. 

В этом примере создается экземпляр **даталакесервицеклиент** с помощью ключа учетной записи.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Замените значение заполнителя `storage_account_name` именем вашей учетной записи хранения.

- Замените `storage_account_key` значение заполнителя ключом доступа к учетной записи хранения.

### <a name="connect-by-using-azure-active-directory-ad"></a>Подключение с помощью Azure Active Directory (AD)

Для проверки подлинности приложения в Azure AD можно использовать [клиентскую библиотеку удостоверений Azure для Python](https://pypi.org/project/azure-identity/) .

В этом примере создается экземпляр **даталакесервицеклиент** с использованием идентификатора клиента, секрета клиента и идентификатора клиента.  Чтобы получить эти значения, см. статью [Получение маркера из Azure AD для авторизации запросов из клиентского приложения](../common/storage-auth-aad-app.md).

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Дополнительные примеры см. в документации по [клиентской библиотеке Azure Identity для Python](https://pypi.org/project/azure-identity/) .

## <a name="create-a-container"></a>Создание контейнера

Контейнер выступает в качестве файловой системы для файлов. Его можно создать, вызвав метод **FileSystemDataLakeServiceClient.create_file_system** .

В этом примере создается контейнер с именем `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на каталог, вызвав метод **FileSystemClient.create_directory** .

В этом примере в контейнер добавляется каталог с именем `my-directory` . 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуйте или переместите каталог, вызвав метод **DataLakeDirectoryClient.rename_directory** . Передайте путь к нужному каталогу параметру. 

В этом примере подкаталог переименовывается в имя `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог, вызвав метод **DataLakeDirectoryClient.delete_directory** .

В этом примере удаляется каталог `my-directory`.  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Отправка файла в каталог 

Сначала создайте ссылку на файл в целевом каталоге, создав экземпляр класса **даталакефилеклиент** . Отправьте файл, вызвав метод **DataLakeFileClient.append_data** . Обязательно завершите передачу, вызвав метод **DataLakeFileClient.flush_data** .

В этом примере текстовый файл перегружается в каталог с именем `my-directory` .   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Если размер файла большой, код должен выполнить несколько вызовов метода **DataLakeFileClient.append_data** . Вместо этого рекомендуется использовать метод **DataLakeFileClient.upload_data** . Таким образом, вы можете передать весь файл в одном вызове. 

## <a name="upload-a-large-file-to-a-directory"></a>Отправка большого файла в каталог

Используйте метод **DataLakeFileClient.upload_data** для отправки больших файлов без выполнения нескольких вызовов метода **DataLakeFileClient.append_data** .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Скачивание из каталога 

Откройте локальный файл для записи. Затем создайте экземпляр **даталакефилеклиент** , представляющий файл, который требуется скачать. Вызовите **DataLakeFileClient.read_file** , чтобы прочитать байты из файла, а затем запишите эти байты в локальный файл. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Перечислите содержимое каталога, вызвав метод **FileSystemClient.get_paths** , а затем перечислите результаты.

В этом примере выводится путь к каждому подкаталогу и файлу, расположенному в каталоге с именем `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>Управление списками управления доступом (ACL)

Вы можете получать, задавать и обновлять разрешения на доступ к каталогам и файлам.

> [!NOTE]
> Если вы используете Azure Active Directory (Azure AD) для авторизации доступа, убедитесь, что участнику безопасности назначена [роль владельца данных BLOB-объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и последствиях их изменения см. на странице [Контроль доступа в Azure Data Lake Storage 2-го поколения](./data-lake-storage-access-control.md).

### <a name="manage-directory-acls"></a>Управление списками ACL каталога

Получите список управления доступом (ACL) каталога, вызвав метод **DataLakeDirectoryClient.get_access_control** и ЗАДАВ список ACL, вызвав метод **DataLakeDirectoryClient.set_access_control** .

> [!NOTE]
> Если приложение разрешает доступ с помощью Azure Active Directory (Azure AD), убедитесь, что участнику безопасности, используемому приложением для авторизации доступа, назначена [роль владельца данных BLOB-объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и последствиях их изменения см. на странице [Контроль доступа в Azure Data Lake Storage 2-го поколения](./data-lake-storage-access-control.md).

Этот пример возвращает и задает список ACL для каталога с именем `my-directory` . Эта строка `rwxr-xrw-` предоставляет владельцам разрешения на чтение, запись и выполнение, предоставляя группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным разрешение на чтение и запись.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

Также можно получить и задать список управления доступом для корневого каталога контейнера. Чтобы получить корневой каталог, вызовите метод **FileSystemClient._get_root_directory_client** .

### <a name="manage-file-permissions"></a>Управление разрешениями для файлов

Получите список управления доступом (ACL) файла, вызвав метод **DataLakeFileClient.get_access_control** и ЗАДАВ список ACL, вызвав метод **DataLakeFileClient.set_access_control** .

> [!NOTE]
> Если приложение разрешает доступ с помощью Azure Active Directory (Azure AD), убедитесь, что участнику безопасности, используемому приложением для авторизации доступа, назначена [роль владельца данных BLOB-объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и последствиях их изменения см. на странице [Контроль доступа в Azure Data Lake Storage 2-го поколения](./data-lake-storage-access-control.md).

Этот пример возвращает и задает список управления доступом для файла с именем `my-file.txt` . Эта строка `rwxr-xrw-` предоставляет владельцам разрешения на чтение, запись и выполнение, предоставляя группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным разрешение на чтение и запись.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

### <a name="set-an-acl-recursively"></a>Рекурсивное задание списка ACL

Можно рекурсивно добавлять, обновлять и удалять списки управления доступом для существующих дочерних элементов родительского каталога без необходимости вносить эти изменения отдельно для каждого дочернего элемента. Дополнительные сведения см. в разделе [Настройка списков управления доступом (ACL) рекурсивно для Azure Data Lake Storage 2-го поколения](recursive-access-control-lists.md).

## <a name="see-also"></a>См. также раздел

* [Справочная документация по API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
* [Пакет (индекс пакета Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Примеры](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Сопоставление 1-го и 2-го поколения](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Отправить отзыв](https://github.com/Azure/azure-sdk-for-python/issues)