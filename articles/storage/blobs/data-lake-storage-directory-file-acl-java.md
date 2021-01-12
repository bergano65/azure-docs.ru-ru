---
title: Azure Data Lake Storage 2-го поколения пакета SDK для Java для файлов & ACL
description: Используйте библиотеки службы хранилища Azure для Java для управления каталогами и списками управления доступом к файлам и каталогам (ACL) в учетных записях хранения с включенным иерархическое пространством имен (HNS).
author: normesta
ms.service: storage
ms.date: 01/11/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 1cc6954569c509c977634a8e1cdd52c5c55b2100
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108133"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Использование Java для управления каталогами, файлами и списками ACL в Azure Data Lake Storage 2-го поколения

В этой статье показано, как использовать Java для создания каталогов, файлов и разрешений в учетных записях хранения с включенным иерархическое пространством имен (HNS) и управления ими. 

[Пакет (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Примеры](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [Справочник](/java/api/overview/azure/storage-file-datalake-readme)  |  по API Сопоставление Gen1 с [Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Отправить отзыв](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * В учетной записи хранения включено иерархическое пространство имен. Выполните [эти](../common/storage-account-create.md) инструкции, чтобы создать учетную запись.

## <a name="set-up-your-project"></a>Настройка проекта

Чтобы приступить к работе, откройте [эту страницу](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) и найдите последнюю версию библиотеки Java. Затем откройте файл *pom.xml* в текстовом редакторе. Добавьте элемент зависимости, который ссылается на эту версию.

Если вы планируете выполнять проверку подлинности клиентского приложения с помощью Azure Active Directory (AD), добавьте зависимость в клиентскую библиотеку секретного кода Azure. См. раздел  [Добавление пакета секретной клиентской библиотеки в проект](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Затем добавьте эти операторы Imports в файл кода.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Подключение к учетной записи 

Чтобы использовать фрагменты кода в этой статье, необходимо создать экземпляр **даталакесервицеклиент** , представляющий учетную запись хранения. 

### <a name="connect-by-using-an-account-key"></a>Подключение с помощью ключа учетной записи

Это самый простой способ подключения к учетной записи. 

В этом примере создается экземпляр **даталакесервицеклиент** с помощью ключа учетной записи.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Подключение с помощью Azure Active Directory (Azure AD)

Для проверки подлинности приложения в Azure AD можно использовать [клиентскую библиотеку удостоверений Azure для Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

В этом примере создается экземпляр **даталакесервицеклиент** с использованием идентификатора клиента, секрета клиента и идентификатора клиента.  Чтобы получить эти значения, см. статью [Получение маркера из Azure AD для авторизации запросов из клиентского приложения](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Дополнительные примеры см. в документации по [клиентской библиотеке удостоверений Azure для Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .


## <a name="create-a-container"></a>Создание контейнера

Контейнер выступает в качестве файловой системы для файлов. Его можно создать, вызвав метод **даталакесервицеклиент. креатефилесистем** .

В этом примере создается контейнер с именем `my-file-system` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на каталог, вызвав метод **даталакефилесистемклиент. createDirectory** .

В этом примере добавляется каталог с именем `my-directory` в контейнер, а затем добавляется подкаталог с именем `my-subdirectory` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуйте или переместите каталог, вызвав метод **даталакедиректориклиент. Rename** . Передайте путь к нужному каталогу параметру. 

В этом примере подкаталог переименовывается в имя `my-subdirectory-renamed` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

В этом примере каталог с именем перемещается `my-subdirectory-renamed` в подкаталог каталога с именем `my-directory-2` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог, вызвав метод **даталакедиректориклиент. делетевисреспонсе** .

В этом примере удаляется каталог `my-directory`.   

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Отправка файла в каталог

Сначала создайте ссылку на файл в целевом каталоге, создав экземпляр класса **даталакефилеклиент** . Отправьте файл, вызвав метод **даталакефилеклиент. append** . Обязательно завершите передачу, вызвав метод **даталакефилеклиент. FlushAsync** .

В этом примере текстовый файл перегружается в каталог с именем `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> Если размер файла большой, код должен выполнить несколько вызовов метода **даталакефилеклиент. append** . Вместо этого рекомендуется использовать метод **даталакефилеклиент. uploadFromFile** . Таким образом, вы можете передать весь файл в одном вызове. 
>
> Пример см. в следующем разделе.

## <a name="upload-a-large-file-to-a-directory"></a>Отправка большого файла в каталог

Используйте метод **даталакефилеклиент. uploadFromFile** для отправки больших файлов без выполнения нескольких вызовов к методу **даталакефилеклиент. append** .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Скачивание из каталога

Сначала создайте экземпляр **даталакефилеклиент** , представляющий файл, который требуется скачать. Для чтения файла используйте метод **даталакефилеклиент. Read** . Используйте любой API-интерфейс обработки файлов .NET для сохранения байтов из потока в файл. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

В этом примере выводятся имена каждого файла, расположенного в каталоге с именем `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>Управление списками управления доступом (ACL)

Вы можете получать, задавать и обновлять разрешения на доступ к каталогам и файлам.

> [!NOTE]
> Если вы используете Azure Active Directory (Azure AD) для авторизации доступа, убедитесь, что участнику безопасности назначена [роль владельца данных BLOB-объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и последствиях их изменения см. на странице [Контроль доступа в Azure Data Lake Storage 2-го поколения](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Управление ACL каталога

В этом примере получается и затем задается список ACL для каталога с именем `my-directory` . В этом примере предоставляются права владельца «чтение», «запись» и «выполнение», которая предоставляет группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным доступ для чтения.

> [!NOTE]
> Если приложение разрешает доступ с помощью Azure Active Directory (Azure AD), убедитесь, что участнику безопасности, используемому приложением для авторизации доступа, назначена [роль владельца данных BLOB-объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и последствиях их изменения см. на странице [Контроль доступа в Azure Data Lake Storage 2-го поколения](./data-lake-storage-access-control.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Также можно получить и задать список управления доступом для корневого каталога контейнера. Чтобы получить корневой каталог, передайте в `""` метод **Даталакефилесистемклиент. жетдиректориклиент** пустую строку ().

### <a name="manage-a-file-acl"></a>Управление ACL файла

Этот пример получает и затем задает список управления доступом для файла с именем `upload-file.txt` . В этом примере предоставляются права владельца «чтение», «запись» и «выполнение», которая предоставляет группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным доступ для чтения.

> [!NOTE]
> Если приложение разрешает доступ с помощью Azure Active Directory (Azure AD), убедитесь, что участнику безопасности, используемому приложением для авторизации доступа, назначена [роль владельца данных BLOB-объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и последствиях их изменения см. на странице [Контроль доступа в Azure Data Lake Storage 2-го поколения](./data-lake-storage-access-control.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-an-acl-recursively"></a>Рекурсивное задание списка ACL

Можно рекурсивно добавлять, обновлять и удалять списки управления доступом для существующих дочерних элементов родительского каталога без необходимости вносить эти изменения отдельно для каждого дочернего элемента. Дополнительные сведения см. в разделе [Настройка списков управления доступом (ACL) рекурсивно для Azure Data Lake Storage 2-го поколения](recursive-access-control-lists.md).

## <a name="see-also"></a>См. также статью

* [Справочная документация по API](/java/api/overview/azure/storage-file-datalake-readme)
* [Пакет (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Примеры](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Сопоставление 1-го и 2-го поколения](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Отправить отзыв](https://github.com/Azure/azure-sdk-for-java/issues)