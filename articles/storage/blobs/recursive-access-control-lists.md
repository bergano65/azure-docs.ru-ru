---
title: Рекурсивно задать списки управления доступом для Azure Data Lake Storage 2-го поколения | Документация Майкрософт
description: Можно рекурсивно добавлять, обновлять и удалять списки управления доступом для существующих дочерних элементов родительского каталога.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 10/29/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 56a797864b70cb1be4a1bc5d4b79c44348d43dae
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2020
ms.locfileid: "93144432"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Рекурсивное задание списков управления доступом (ACL) для Azure Data Lake Storage 2-го поколения

Наследование ACL уже доступно для новых дочерних элементов, созданных в родительском каталоге. Теперь можно также добавлять, обновлять и удалять списки управления доступом для существующих дочерних элементов родительского каталога без необходимости вносить эти изменения отдельно для каждого дочернего элемента.

> [!NOTE]
> Можно рекурсивно задавать списки доступа в общедоступной предварительной версии и доступны во всех регионах.  

[Библиотеки](#libraries)  |  [Примеры](#code-samples)  |  [Рекомендации](#best-practice-guidelines)  |  [Отправить отзыв](#provide-feedback)

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

- В учетной записи хранения включено иерархическое пространство имен. Выполните [эти](data-lake-storage-quickstart-create-account.md) инструкции, чтобы создать учетную запись.

- Правильные разрешения для выполнения рекурсивного процесса ACL. Правильное разрешение включает один из следующих элементов: 

  - [Участник безопасности](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) подготовленного Azure Active Directory (AD), которому назначена роль [владельца данных BLOB-объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) в области целевого контейнера, родительской группы ресурсов или подписки.   

  - Владелец целевого контейнера или каталога, к которому планируется применить рекурсивный процесс ACL. Сюда входят все дочерние элементы в целевом контейнере или каталоге. 

- Понимание того, как списки управления доступом применяются к каталогам и файлам. См. раздел [Контроль доступа в Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

См. раздел **Настройка проекта** этой статьи, чтобы просмотреть руководство по установке для PowerShell, пакета SDK для .NET и пакета SDK для Python.

## <a name="set-up-your-project"></a>Настройка проекта

Установите необходимые библиотеки.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Убедитесь, что установленная версия PowerShell `5.1` или более поздняя, с помощью следующей команды.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Сведения об обновлении версии PowerShell см. в разделе [обновление существующих Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell) .
    
2. Установите **AZ. Storage** Module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Дополнительные сведения об установке модулей PowerShell см. [в статье Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Откройте [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) или, если вы [установили](https://docs.microsoft.com/cli/azure/install-azure-cli) Azure CLI локально, командное консольное приложение (например, Windows PowerShell).

2. Убедитесь, что установлена версия Azure CLI`2.14.0` или выше, выполнив следующую команду.

   ```azurecli
    az --version
   ```
   Если ваша версия Azure CLI ниже чем `2.14.0`, установите более позднюю версию. Подробнее см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="net"></a>[.NET](#tab/dotnet)

1. Откройте командное окно (например, Windows PowerShell).

2. В каталоге проекта установите пакет Azure. Storage. Files. Data Lake Preview с помощью `dotnet add package` команды.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Добавьте эти операторы using в начало файла кода.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="java"></a>[Java](#tab/java)

Чтобы приступить к работе, откройте [эту страницу](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) и найдите последнюю версию библиотеки Java. Затем откройте файл *pom.xml* в текстовом редакторе. Добавьте элемент зависимости, который ссылается на эту версию.

Если вы планируете выполнять проверку подлинности клиентского приложения с помощью Azure Active Directory (AD), добавьте зависимость в клиентскую библиотеку секретного кода Azure. См. раздел  [Добавление пакета секретной клиентской библиотеки в проект](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Затем добавьте эти операторы Imports в файл кода.

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. Скачайте [клиентскую библиотеку Azure Data Lake Storage для Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0).

2. Установите библиотеку, скачанную с помощью [PIP](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

3. Добавьте эти операторы импорта в начало файла кода.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.core._match_conditions import MatchConditions
   from azure.storage.filedatalake._models import ContentSettings
   ```

---

## <a name="connect-to-your-account"></a>Подключение к учетной записи

Подключиться можно с помощью Azure Active Directory (AD) или с помощью ключа учетной записи. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Откройте командное окно Windows PowerShell, а затем войдите в подписку Azure с помощью `Connect-AzAccount` команды и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

Если ваше удостоверение связано с более чем одной подпиской, установите активную подписку на подписку учетной записи хранения, которая будет создавать каталоги и управлять ими в. В этом примере замените `<subscription-id>` значение ЗАПОЛНИТЕЛЯ идентификатором подписки.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Затем выберите способ, которым команды будут получать права доступа к учетной записи хранения. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Вариант 1. получение авторизации с помощью Azure Active Directory (AD)

При таком подходе система гарантирует, что ваша учетная запись пользователя имеет соответствующие разрешения на управление доступом на основе ролей Azure (Azure RBAC) и ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

В следующей таблице показаны все поддерживаемые роли и их возможности настройки ACL.

|Роль|Возможность настройки ACL|
|--|--|
|[владелец данных BLOB-объектов хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner);|Все каталоги и файлы в учетной записи.|
|[участник данных BLOB-объектов хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor);|Только каталоги и файлы, принадлежащие субъекту безопасности.|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Вариант 2. получение авторизации с помощью ключа учетной записи хранения

При таком подходе система не проверяет разрешения Azure RBAC или ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Если вы используете Azure CLI локально, выполните команду для входа.

   ```azurecli
   az login
   ```

   Если в CLI можно запустить браузер по умолчанию, откроется браузер со страницей входа.

   В противном случае самостоятельно откройте в браузере страницу [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и введите код авторизации, отображаемый в терминале. Затем выполните вход в браузере с помощью учетных данных.

   Дополнительные сведения о различных методах аутентификации см. на странице [Авторизация доступа к данным BLOB-объектов или очередей с помощью Azure CLI](../common/authorize-data-operations-cli.md).

2. Если удостоверение связано с несколькими подписками, установите активную подписку в качестве подписки учетной записи хранения, в которой будет размещен статический веб-сайт.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Замените значение заполнителя `<subscription-id>` идентификатором своей подписки.

> [!NOTE]
> В примере этой статьи показана авторизация Azure Active Directory. Дополнительные сведения о методах аутентификации см. на странице [Авторизация доступа к данным BLOB-объектов или очередей с помощью Azure CLI](../common/authorize-data-operations-cli.md).

### <a name="net"></a>[.NET](#tab/dotnet)

Чтобы использовать фрагменты кода в этой статье, необходимо создать экземпляр [даталакесервицеклиент](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) , представляющий учетную запись хранения.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Подключение с помощью Azure Active Directory (AD)

Для проверки подлинности приложения в Azure AD можно использовать [клиентскую библиотеку удостоверений Azure для .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

После установки пакета добавьте этот оператор using в начало файла кода.

```csharp
using Azure.Identity;
```

Получите идентификатор клиента, секрет клиента и идентификатор клиента. Для этого см. статью [Получение маркера из Azure AD для авторизации запросов из клиентского приложения](../common/storage-auth-aad-app.md). В рамках этого процесса вам потребуется назначить одну из следующих ролей Azure [RBAC (Управление доступом на основе ролей)](../../role-based-access-control/overview.md) для субъекта безопасности. 

|Роль|Возможность настройки ACL|
|--|--|
|[владелец данных BLOB-объектов хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner);|Все каталоги и файлы в учетной записи.|
|[участник данных BLOB-объектов хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor);|Только каталоги и файлы, принадлежащие субъекту безопасности.|

В этом примере создается экземпляр [даталакесервицеклиент](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) с использованием идентификатора клиента, секрета клиента и идентификатора клиента.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>Подключение с помощью ключа учетной записи

Этот подход является самым простым способом подключения к учетной записи. 

В этом примере создается экземпляр [даталакесервицеклиент](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) с помощью ключа учетной записи.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> Дополнительные примеры см. в документации по [клиентской библиотеке удостоверений Azure для .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="java"></a>[Java](#tab/java)

Чтобы использовать фрагменты кода в этой статье, необходимо создать экземпляр **даталакесервицеклиент** , представляющий учетную запись хранения. 

#### <a name="connect-by-using-an-account-key"></a>Подключение с помощью ключа учетной записи

Это самый простой способ подключения к учетной записи. 

В этом примере создается экземпляр **даталакесервицеклиент** с помощью ключа учетной записи.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Подключение с помощью Azure Active Directory (Azure AD)

Для проверки подлинности приложения в Azure AD можно использовать [клиентскую библиотеку удостоверений Azure для Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

В этом примере создается экземпляр **даталакесервицеклиент** с использованием идентификатора клиента, секрета клиента и идентификатора клиента.  Чтобы получить эти значения, см. статью [Получение маркера из Azure AD для авторизации запросов из клиентского приложения](../common/storage-auth-aad-app.md).

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Дополнительные примеры см. в документации по [клиентской библиотеке удостоверений Azure для Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

### <a name="python"></a>[Python](#tab/python)

Чтобы использовать фрагменты кода в этой статье, необходимо создать экземпляр **даталакесервицеклиент** , представляющий учетную запись хранения. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Подключение с помощью Azure Active Directory (AD)

Для проверки подлинности приложения в Azure AD можно использовать [клиентскую библиотеку удостоверений Azure для Python](https://pypi.org/project/azure-identity/) .

В этом примере создается экземпляр **даталакесервицеклиент** с использованием идентификатора клиента, секрета клиента и идентификатора клиента.  Чтобы получить эти значения, см. статью [Получение маркера из Azure AD для авторизации запросов из клиентского приложения](../common/storage-auth-aad-app.md). В рамках этого процесса вам потребуется назначить одну из следующих ролей Azure [RBAC (Управление доступом на основе ролей)](../../role-based-access-control/overview.md) для субъекта безопасности. 

|Роль|Возможность настройки ACL|
|--|--|
|[владелец данных BLOB-объектов хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner);|Все каталоги и файлы в учетной записи.|
|[участник данных BLOB-объектов хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor);|Только каталоги и файлы, принадлежащие субъекту безопасности.|

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
> Дополнительные примеры см. в документации по [клиентской библиотеке Azure Identity для Python](https://pypi.org/project/azure-identity/) .

### <a name="connect-by-using-an-account-key"></a>Подключение с помощью ключа учетной записи

Это самый простой способ подключения к учетной записи. 

В этом примере создается экземпляр **даталакесервицеклиент** с помощью ключа учетной записи.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Замените значение заполнителя `storage_account_name` именем вашей учетной записи хранения.

- Замените `storage_account_key` значение заполнителя ключом доступа к учетной записи хранения.

---

## <a name="set-an-acl-recursively"></a>Рекурсивное задание списка ACL

При *задании* ACL **заменяется** весь список ACL, включая все его записи. Если вы хотите изменить уровень разрешений субъекта безопасности или добавить новый субъект безопасности в список управления доступом, не затрагивая другие существующие записи, следует *Обновить* список управления доступом. Чтобы обновить список управления доступом вместо его замены, см. раздел [Обновление списка ACL в рекурсивной](#update-an-acl-recursively) области этой статьи.   

Этот раздел содержит примеры настройки ACL 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Рекурсивно задайте список ACL с помощью командлета **Set-AzDataLakeGen2AclRecursive** .

В этом примере задается список ACL для каталога с именем `my-parent-directory` . Эти записи предоставляют владельцам разрешения на чтение, запись и выполнение, предоставляя группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным доступ. Последняя запись ACL в этом примере предоставляет пользователю с ИДЕНТИФИКАТОРом "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" разрешения на чтение и выполнение.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Если вы хотите задать запись ACL **по умолчанию** , используйте параметр **-DefaultScope** при выполнении команды **Set-AzDataLakeGen2ItemAclObject** . Например: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Настройте список ACL рекурсивно с помощью команды [AZ Storage FS Set-recursive](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

В этом примере задается список ACL для каталога с именем `my-parent-directory` . Эти записи предоставляют владельцам разрешения на чтение, запись и выполнение, предоставляя группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным доступ. Последняя запись ACL в этом примере предоставляет пользователю с ИДЕНТИФИКАТОРом "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" разрешения на чтение и выполнение.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Если вы хотите задать запись ACL **по умолчанию** , добавьте префикс `default:` в каждую запись. Например, `default:user::rwx` или `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`. 

### <a name="net"></a>[.NET](#tab/dotnet)

Настройте список ACL рекурсивно, вызвав метод **даталакедиректориклиент. сетакцессконтролрекурсивеасинк** . Передайте этот метод [List](/dotnet/api/system.collections.generic.list-1) в список [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Каждый [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) определяет запись ACL. 

Если вы хотите задать запись ACL **по умолчанию** , можно установить свойство [пасакцессконтролитем. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) для [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) в **значение true** . 

В этом примере задается список ACL для каталога с именем `my-parent-directory` . Этот метод принимает логический параметр с именем `isDefaultScope` , указывающий, следует ли ЗАДАВАТЬ ACL по умолчанию. Этот параметр используется в конструкторе объекта [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Записи списка управления доступом предоставляют владельцам разрешения на чтение, запись и выполнение, предоставляя группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным доступ. Последняя запись списка управления доступом в этом примере предоставляет конкретному пользователю с ИДЕНТИФИКАТОРом объекта "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "разрешения на чтение и выполнение.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None, isDefaultScope),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="java"></a>[Java](#tab/java)

Настройте список ACL рекурсивно, вызвав метод **даталакедиректориклиент. сетакцессконтролрекурсиве** . Передайте этот метод в [список](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) объектов [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Каждый [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) определяет запись ACL. 

Если вы хотите задать запись ACL **по умолчанию** , то можно вызвать метод **сетдефаултскопе** объекта [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) и передать значение **true** . 

В этом примере задается список ACL для каталога с именем `my-parent-directory` . Этот метод принимает логический параметр с именем `isDefaultScope` , указывающий, следует ли ЗАДАВАТЬ ACL по умолчанию. Этот параметр используется в каждом вызове метода **сетдефаултскопе** объекта [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Записи списка управления доступом предоставляют владельцам разрешения на чтение, запись и выполнение, предоставляя группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным доступ. Последняя запись ACL в этом примере предоставляет пользователю с ИДЕНТИФИКАТОРом "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" разрешения на чтение и выполнение.

```java
static public void SetACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){
    
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create owner entry.
    PathAccessControlEntry ownerEntry = new PathAccessControlEntry();

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    ownerEntry.setDefaultScope(isDefaultScope);
    ownerEntry.setAccessControlType(AccessControlType.USER);
    ownerEntry.setPermissions(ownerPermission);

    pathAccessControlEntries.add(ownerEntry);

    // Create group entry.
    PathAccessControlEntry groupEntry = new PathAccessControlEntry();

    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    groupEntry.setDefaultScope(isDefaultScope);
    groupEntry.setAccessControlType(AccessControlType.GROUP);
    groupEntry.setPermissions(groupPermission);

    pathAccessControlEntries.add(groupEntry);

    // Create other entry.
    PathAccessControlEntry otherEntry = new PathAccessControlEntry();

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setExecutePermission(false).setReadPermission(false).setWritePermission(false);

    otherEntry.setDefaultScope(isDefaultScope);
    otherEntry.setAccessControlType(AccessControlType.OTHER);
    otherEntry.setPermissions(otherPermission);

    pathAccessControlEntries.add(otherEntry);

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.setAccessControlRecursive(pathAccessControlEntries);        

}
```

### <a name="python"></a>[Python](#tab/python)

Настройте список ACL рекурсивно, вызвав метод **DataLakeDirectoryClient.set_access_control_recursive** .

Если вы хотите задать запись ACL **по умолчанию** , добавьте строку `default:` в начало каждой строки записи ACL. 

В этом примере задается список ACL для каталога с именем `my-parent-directory` . 

Этот метод принимает логический параметр с именем `is_default_scope` , указывающий, следует ли ЗАДАВАТЬ ACL по умолчанию. Если этот параметр имеет значение `True` , перед списком записей ACL добавляется строка `default:` . 

Записи списка управления доступом предоставляют владельцам разрешения на чтение, запись и выполнение, предоставляя группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным доступ. Последняя запись списка управления доступом в этом примере предоставляет конкретному пользователю с ИДЕНТИФИКАТОРом объекта "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "разрешения на чтение и выполнение. Эти записи предоставляют владельцам разрешения на чтение, запись и выполнение, предоставляя группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным доступ. Последняя запись списка управления доступом в этом примере предоставляет конкретному пользователю с ИДЕНТИФИКАТОРом объекта "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "разрешения на чтение и выполнение.

```python
def set_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>Рекурсивное обновление списка ACL

При *обновлении* списка управления доступом необходимо изменить список управления доступом вместо замены списка управления доступом. Например, можно добавить нового субъекта безопасности в список управления доступом, не затрагивая другие субъекты безопасности, перечисленные в списке ACL.  Чтобы заменить список управления доступом вместо обновления, см. раздел [Настройка списка ACL в рекурсивном](#set-an-acl-recursively) руководстве этой статьи. 

Чтобы обновить список управления доступом, создайте новый объект ACL с записью ACL, которую требуется обновить, а затем используйте этот объект в операции Update ACL. Не изменяйте существующий список ACL, просто предоставьте записи ACL для обновления.

В этом разделе содержатся примеры обновления списка управления доступом.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Рекурсивно обновите список ACL с помощью командлета  **Update-AzDataLakeGen2AclRecursive** . 

В этом примере обновляется запись ACL с разрешением на запись. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Если вы хотите обновить запись ACL **по умолчанию** , используйте параметр **-DefaultScope** при выполнении команды **Set-AzDataLakeGen2ItemAclObject** . Например: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Рекурсивно обновите список ACL с помощью команды  [AZ Storage FS доступ Update-recursive](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) . 

В этом примере обновляется запись ACL с разрешением на запись. 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Если вы хотите обновить запись ACL **по умолчанию** , добавьте префикс `default:` в каждую запись. Например, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

### <a name="net"></a>[.NET](#tab/dotnet)

Рекурсивно обновите список ACL, вызвав метод **даталакедиректориклиент. упдатеакцессконтролрекурсивеасинк** .  Передайте этот метод [List](/dotnet/api/system.collections.generic.list-1) в список [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Каждый [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) определяет запись ACL. 

Если требуется обновить запись ACL **по умолчанию** , можно установить свойство [пасакцессконтролитем. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) для [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) в **значение true** . 

В этом примере обновляется запись ACL с разрешением на запись. Этот метод принимает логический параметр с именем `isDefaultScope` , указывающий, следует ли обновлять ACL по умолчанию. Этот параметр используется в конструкторе объекта [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="java"></a>[Java](#tab/java)

Рекурсивно обновите список ACL, вызвав метод **даталакедиректориклиент. упдатеакцессконтролрекурсиве** .  Передайте этот метод в [список](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) объектов [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Каждый [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) определяет запись ACL. 

Если вы хотите обновить запись ACL **по умолчанию** , то можете использовать метод **сетдефаултскопе** [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) и передать значение **true** . 

В этом примере обновляется запись ACL с разрешением на запись. Этот метод принимает логический параметр с именем `isDefaultScope` , указывающий, следует ли обновлять ACL по умолчанию. Этот параметр используется в вызове метода **сетдефаултскопе** объекта [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

```java
static public void UpdateACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.updateAccessControlRecursive(pathAccessControlEntries);          
}
```

### <a name="python"></a>[Python](#tab/python)

Рекурсивно обновите список ACL, вызвав метод **DataLakeDirectoryClient.update_access_control_recursive** . Если вы хотите обновить запись ACL **по умолчанию** , добавьте строку `default:` в начало каждой строки записи ACL. 

В этом примере обновляется запись ACL с разрешением на запись.

В этом примере задается список ACL для каталога с именем `my-parent-directory` . Этот метод принимает логический параметр с именем `is_default_scope` , указывающий, следует ли обновлять ACL по умолчанию. Если этот параметр имеет значение `True` , то обновленной записи ACL предшествует строка `default:` .  

```python
def update_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'   

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>Рекурсивное удаление записей ACL

Можно рекурсивно удалить одну или несколько записей списка ACL. Чтобы удалить запись ACL, создайте новый объект ACL для удаляемой записи ACL, а затем используйте этот объект в операции удаления списка ACL. Не изменяйте существующий список ACL, просто предоставьте записи ACL для удаления. 

В этом разделе содержатся примеры удаления ACL. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Удалите записи ACL с помощью командлета **Remove-AzDataLakeGen2AclRecursive** . 

В этом примере удаляется запись ACL из корневого каталога контейнера.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Если вы хотите удалить запись ACL **по умолчанию** , используйте параметр **-DefaultScope** при выполнении команды **Set-AzDataLakeGen2ItemAclObject** . Например: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Удалите записи списков ACL с помощью команды [AZ Storage FS доступ recurs-recursive](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) . 

В этом примере удаляется запись ACL из корневого каталога контейнера.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Если вы хотите удалить запись ACL **по умолчанию** , добавьте префикс `default:` в каждую запись. Например, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="net"></a>[.NET](#tab/dotnet)

Удалите записи ACL, вызвав метод **даталакедиректориклиент. ремовеакцессконтролрекурсивеасинк** . Передайте этот метод [List](/dotnet/api/system.collections.generic.list-1) в список [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Каждый [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) определяет запись ACL. 

Если требуется удалить запись ACL **по умолчанию** , можно установить свойство [пасакцессконтролитем. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) для [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) в **значение true** . 

В этом примере удаляется запись ACL из списка ACL каталога с именем `my-parent-directory` . Этот метод принимает логический параметр с именем `isDefaultScope` , который указывает, следует ли удалить запись из ACL по умолчанию. Этот параметр используется в конструкторе объекта [пасакцессконтролитем](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient, isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="java"></a>[Java](#tab/java)

Удалите записи ACL, вызвав метод **даталакедиректориклиент. ремовеакцессконтролрекурсиве** . Передайте этот метод в [список](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) объектов [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Каждый [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) определяет запись ACL. 

Если вы хотите удалить запись ACL **по умолчанию** , то можете использовать метод **сетдефаултскопе** [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) и передать значение **true** .  

В этом примере удаляется запись ACL из списка ACL каталога с именем `my-parent-directory` . Этот метод принимает логический параметр с именем `isDefaultScope` , который указывает, следует ли удалить запись из ACL по умолчанию. Этот параметр используется в вызове метода **сетдефаултскопе** объекта [пасакцессконтролентри](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).


```java
static public void RemoveACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathRemoveAccessControlEntry> pathRemoveAccessControlEntries = 
        new ArrayList<PathRemoveAccessControlEntry>();

    // Create named user entry.
    PathRemoveAccessControlEntry userEntry = new PathRemoveAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"); 
    
    pathRemoveAccessControlEntries.add(userEntry);
    
    directoryClient.removeAccessControlRecursive(pathRemoveAccessControlEntries);      

}
```

### <a name="python"></a>[Python](#tab/python)

Удалите записи ACL, вызвав метод **DataLakeDirectoryClient.remove_access_control_recursive** . Если вы хотите удалить запись ACL **по умолчанию** , добавьте строку `default:` в начало строки записи ACL. 

В этом примере удаляется запись ACL из списка ACL каталога с именем `my-parent-directory` . Этот метод принимает логический параметр с именем `is_default_scope` , который указывает, следует ли удалить запись из ACL по умолчанию. Если этот параметр имеет значение `True` , то обновленной записи ACL предшествует строка `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>Восстановление после сбоев

Могут возникать ошибки времени выполнения или разрешения. Для ошибок времени выполнения перезапустите процесс с самого начала. Ошибки разрешений могут возникать, если субъект безопасности не имеет достаточных разрешений для изменения списка управления доступом к каталогу или файлу в изменяемой иерархии каталогов. Устраните проблему с разрешениями, а затем выберите либо возобновление процесса с точки сбоя с помощью токена продолжения, либо перезапуск процесса с начала. Если вы предпочитаете перезапустить с самого начала, вам не нужно использовать токен продолжения. Вы можете повторно применить записи ACL без отрицательного воздействия.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Возвращает результаты в переменную. Не удалось передать записи в отформатированную таблицу.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

На основе выходных данных таблицы можно исправить любые ошибки разрешений, а затем возобновить выполнение с помощью токена продолжения.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

В случае сбоя можно вернуть токен продолжения, задав `--continue-on-failure` для параметра значение `false` . После устранения ошибок можно возобновить процесс с точки сбоя, выполнив команду еще раз, а затем присвоив `--continuation` параметру маркер продолжения. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

Этот пример возвращает токен продолжения в случае сбоя. Приложение может снова вызвать этот пример метода после устранения ошибки и передать маркер продолжения. Если этот метод примера вызывается впервые, приложение может передать значение `null` для параметра токен продолжения. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, continuationToken: continuationToken, null);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="java"></a>[Java](#tab/java)

Этот пример возвращает токен продолжения в случае сбоя. Приложение может снова вызвать этот пример метода после устранения ошибки и передать маркер продолжения. Если этот метод примера вызывается впервые, приложение может передать значение `null` для параметра токен продолжения. 

```java
static public String ResumeSetACLRecursively(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList, 
String continuationToken){

    try{
        PathSetAccessControlRecursiveOptions options = new PathSetAccessControlRecursiveOptions(accessControlList);
        
        options.setContinuationToken(continuationToken);
    
        Response<AccessControlChangeResult> accessControlChangeResult =  
            directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

        if (accessControlChangeResult.getValue().getCounters().getFailedChangesCount() > 0)
        {
            continuationToken =
                accessControlChangeResult.getValue().getContinuationToken();
        }
    
        return continuationToken;

    }
    catch(Exception ex){
    
        System.out.println(ex.toString());
        return continuationToken;
    }
}
```

### <a name="python"></a>[Python](#tab/python)

Этот пример возвращает токен продолжения в случае сбоя. Приложение может снова вызвать этот пример метода после устранения ошибки и передать маркер продолжения. Если этот метод примера вызывается впервые, приложение может передать значение ``None`` для параметра токен продолжения. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

Если вы хотите, чтобы процесс завершился без прерывания с ошибками разрешений, можно указать это.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

В этом примере записи ACL задаются рекурсивно. Если этот код вызывает ошибку разрешения, он записывает этот сбой и возобновляет выполнение. В этом примере на консоль выводятся результаты (включая количество ошибок). 

```powershell
$ContinueOnFailure = $true

$TotalDirectoriesSuccess = 0
$TotalFilesSuccess = 0
$totalFailure = 0
$FailedEntries = New-Object System.Collections.Generic.List[System.Object]

$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы убедиться, что процесс завершается без прерывания, присвойте `--continue-on-failure` параметру значение `true` . 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

### <a name="net"></a>[.NET](#tab/dotnet)

Чтобы убедиться в том, что процесс завершается без прерывания, передайте объект **акцессконтролчанжедоптионс** и задайте для свойства **континуеонфаилуре** этого объекта значение ``true`` .

В этом примере записи ACL задаются рекурсивно. Если этот код вызывает ошибку разрешения, он записывает этот сбой и возобновляет выполнение. В этом примере на консоль выводится число сбоев. 

```cs
public async Task ContinueOnFailureAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient, 
    List<PathAccessControlItem> accessControlList)
{
    var accessControlChangeResult = 
        await directoryClient.SetAccessControlRecursiveAsync(
            accessControlList, null, new AccessControlChangeOptions() 
            { ContinueOnFailure = true });

    var counters = accessControlChangeResult.Value.Counters;

    Console.WriteLine("Number of directories changed: " +
        counters.ChangedDirectoriesCount.ToString());

    Console.WriteLine("Number of files changed: " +
        counters.ChangedFilesCount.ToString());

    Console.WriteLine("Number of failures: " +
        counters.FailedChangesCount.ToString());
}
```

### <a name="java"></a>[Java](#tab/java)

Чтобы убедиться в том, что процесс завершается без прерывания, вызовите метод **сетконтинуеонфаилуре** объекта [пассетакцессконтролрекурсивеоптионс](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) и передайте значение **true** .

В этом примере записи ACL задаются рекурсивно. Если этот код вызывает ошибку разрешения, он записывает этот сбой и возобновляет выполнение. В этом примере на консоль выводится число сбоев. 

```java
static public void ContinueOnFailure(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList){
    
    PathSetAccessControlRecursiveOptions options = 
        new PathSetAccessControlRecursiveOptions(accessControlList);
        
    options.setContinueOnFailure(true);
    
    Response<AccessControlChangeResult> accessControlChangeResult =  
        directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

    AccessControlChangeCounters counters = accessControlChangeResult.getValue().getCounters();

    System.out.println("Number of directories changes: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of files changed: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of failures: " + 
        counters.getChangedDirectoriesCount());
}
```

### <a name="python"></a>[Python](#tab/python)

Чтобы гарантировать, что процесс завершается без прерывания, не передавайте токен продолжения в метод **DataLakeDirectoryClient.set_access_control_recursive** .

В этом примере записи ACL задаются рекурсивно. Если этот код вызывает ошибку разрешения, он записывает этот сбой и возобновляет выполнение. В этом примере на консоль выводится число сбоев. 

```python
def continue_on_failure():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl)

        print("Summary: {} directories and {} files were updated successfully, {} failures were counted."
          .format(acl_change_result.counters.directories_successful, acl_change_result.counters.files_successful,
                  acl_change_result.counters.failure_count))
        
    except Exception as e:
     print(e)
```

---

## <a name="resources"></a>Ресурсы

Этот раздел содержит ссылки на библиотеки и примеры кода.

#### <a name="libraries"></a>Библиотеки

- [PowerShell](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)
- [Azure CLI](https://docs.microsoft.com/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>Примеры кода

- PowerShell: [образец файла readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [Sample](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- Azure CLI: [Пример](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- NET: [образец файла readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [Sample](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [образец файла readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [Sample](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)

## <a name="best-practice-guidelines"></a>Рекомендации

В этом разделе приведены рекомендации по рекурсивной настройке списков управления доступом. 

#### <a name="handling-runtime-errors"></a>Обработка ошибок времени выполнения

Ошибка времени выполнения может возникать по многим причинам (например, в случае сбоя или проблемы с подключением клиента). При возникновении ошибки во время выполнения перезапустите процесс рекурсивного ACL. Списки управления доступом можно повторно применить к элементам, не вызывая негативного воздействия. 

#### <a name="handling-permission-errors-403"></a>Обработка ошибок разрешений (403)

Если при выполнении рекурсивного процесса ACL возникает исключение управления доступом, возможно, [субъект безопасности](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) Active Directory не имеет достаточных разрешений для применения ACL к одному или нескольким дочерним элементам в иерархии каталогов. При возникновении ошибки в разрешении процесс останавливается и предоставляется маркер продолжения. Устраните проблему с разрешениями, а затем используйте токен продолжения для обработки оставшегося набора данных. Каталоги и файлы, которые уже были успешно обработаны, не должны обрабатываться повторно. Можно также выбрать перезапуск процесса рекурсивного ACL. Списки управления доступом можно повторно применить к элементам, не вызывая негативного воздействия. 

#### <a name="credentials"></a>Учетные данные 

Рекомендуется подготавливать субъект безопасности Azure AD, которому назначена роль [владельца данных BLOB-объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) , в области целевой учетной записи хранения или контейнера. 

#### <a name="performance"></a>Производительность 

Чтобы сократить задержку, рекомендуется запустить процесс рекурсивного ACL на виртуальной машине Azure, расположенной в том же регионе, что и учетная запись хранения. 

#### <a name="acl-limits"></a>Ограничения ACL

Максимальное число списков управления доступом, которое можно применить к каталогу или файлу, — 32 ACL для доступа к таблицам ACL и по умолчанию 32. Дополнительные сведения см. в статье [Контроль доступа в Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>Предоставление отзывов или отчетов о проблемах

Вы можете отправить отзыв или сообщить о вопросе по адресу  [recursiveACLfeedback@microsoft.com](mailto:recursiveACLfeedback@microsoft.com) .

## <a name="see-also"></a>См. также раздел

- [Контроль доступа в Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [Известные проблемы](data-lake-storage-known-issues.md)


