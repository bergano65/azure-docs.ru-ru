---
title: Использование Azure CLI для файлов & ACL в Azure Data Lake Storage 2-го поколения (Предварительная версия)
description: Используйте Azure CLI для управления каталогами и списками управления доступом к файлам и каталогам (ACL) в учетных записях хранения, имеющих иерархическое пространство имен.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: dcd75cfefd53b3c9104052146607869515e1c86e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534293"
---
# <a name="use-azure-cli-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Использование Azure CLI для файлов & ACL в Azure Data Lake Storage 2-го поколения (Предварительная версия)

В этой статье показано, как использовать [интерфейс командной строки Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) для создания каталогов, файлов и разрешений в учетных записях хранения с иерархическим пространством имен и управления ими. 

> [!IMPORTANT]
> Расширение `storage-preview`, представленное в этой статье, сейчас находится в общедоступной предварительной версии.

[Пример](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen1 Gen2 сопоставления](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [Отправить отзыв](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранения, в которой включено иерархическое пространство имен (HNS). Чтобы создать его [, выполните следующие](data-lake-storage-quickstart-create-account.md) инструкции.
> * Azure CLI версии `2.0.67` или более поздней.

## <a name="install-the-storage-cli-extension"></a>Установка расширения CLI хранилища

1. Откройте [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)или, если вы [установили](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI локально, Откройте консольное приложение командной строки, например Windows PowerShell.

2. Убедитесь, что установленная версия Azure CLI `2.0.67` или выше, с помощью следующей команды.

   ```azurecli
    az --version
   ```
   Если ваша версия Azure CLI меньше, чем `2.0.67`, установите более позднюю версию. См. статью [установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Установите расширение `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Подключение к учетной записи

1. Если вы используете Azure CLI локально, выполните команду Login.

   ```azurecli
   az login
   ```

   Если в CLI можно запустить браузер по умолчанию, откроется браузер со страницей входа.

   В противном случае откройте страницу браузера по адресу [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и введите код авторизации, отображаемый в терминале. Затем выполните вход с использованием учетных данных своей учетной записи в браузере.

   Дополнительные сведения о различных методах проверки подлинности см. в разделе Вход с помощью Azure CLI.

2. Если ваше удостоверение связано с более чем одной подпиской, установите активную подписку на подписку учетной записи хранения, в которой будет размещен статический веб-сайт.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Замените значение заполнителя `<subscription-id>` ИДЕНТИФИКАТОРом подписки.

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система выступает в качестве контейнера для файлов. Его можно создать с помощью команды `az storage container create`. 

В этом примере создается файловая система с именем `my-file-system`.

```azurecli
az storage container create --name my-file-system
```

## <a name="create-a-directory"></a>создать каталог;

Создайте ссылку на каталог с помощью команды `az storage blob directory create`. 

В этом примере добавляется каталог с именем `my-directory` в файловую систему с именем `my-file-system`, расположенную в учетной записи с именем `mystorageaccount`.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Отображение свойств каталога

Свойства каталога можно вывести на консоль с помощью команды `az storage blob show`.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуйте или переместите каталог с помощью команды `az storage blob directory move`.

В этом примере каталог `my-directory` переименовывается в имя `my-new-directory`.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог с помощью команды `az storage blob directory delete`.

В этом примере удаляется каталог с именем `my-directory`. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Проверка существования каталога

Определите, существует ли определенный каталог в файловой системе, с помощью команды `az storage blob directory exist`.

Этот пример показывает, существует ли каталог с именем `my-directory` в `my-file-system`ной файловой системе. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Скачать из каталога

Скачайте файл из каталога с помощью команды `az storage blob directory download`.

В этом примере скачивается файл с именем `upload.txt` из каталога с именем `my-directory`. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

В этом примере скачивается весь каталог.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Выведите список содержимого каталога с помощью команды `az storage blob directory list`.

В этом примере выводится содержимое каталога с именем `my-directory`, расположенного в `my-file-system` файловой системе учетной записи хранения с именем `mystorageaccount`. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Передача файла в каталог

Отправьте файл в каталог с помощью команды `az storage blob directory upload`.

В этом примере файл с именем `upload.txt` перегружается в каталог с именем `my-directory`. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

В этом примере загружается весь каталог.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Отображение свойств файла

Свойства файла можно вывести на консоль с помощью команды `az storage blob show`.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Переименование или перемещение файла

Переименуйте или переместите файл с помощью команды `az storage blob move`.

В этом примере файл переименовывается из имени `my-file.txt` в имя `my-file-renamed.txt`.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Удаление файла

Удалите файл с помощью команды `az storage blob delete`.

В этом примере удаляется файл с именем `my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Управление разрешениями

Можно получать, задавать и обновлять разрешения на доступ к каталогам и файлам.

### <a name="get-directory-and-file-permissions"></a>Получение разрешений для каталогов и файлов

Получите список ACL **каталога** с помощью команды `az storage blob directory access show`.

Этот пример возвращает список ACL для каталога, а затем выводит список ACL на консоль.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Получите разрешения на доступ к **файлу** с помощью команды `az storage blob access show`. 

Этот пример возвращает список ACL для файла, а затем выводит список ACL на консоль.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

На следующем рисунке показаны выходные данные после получения списка управления доступом для каталога.

![Получить выходные данные ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

В этом примере пользователь-владелец имеет разрешения на чтение, запись и выполнение. Группа-владелец имеет только разрешения на чтение и выполнение. Дополнительные сведения о списках управления доступом см. [в разделе Контроль доступа в Azure Data Lake Storage 2-го поколения](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Задать разрешения для каталогов и файлов

Используйте команду `az storage blob directory access set`, чтобы задать список управления доступом для **каталога**. 

Этот пример задает список ACL для каталога пользователя-владельца, группы-владельца или других пользователей, а затем выводит список ACL на консоль.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Чтобы задать список управления доступом для **файла**, используйте команду `az storage blob access set`. 

В этом примере список ACL задается для файла, владеющего пользователем, группой-владельцем или другими пользователями, а затем на консоль выводится список ACL.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
На следующем рисунке показаны выходные данные после установки списка управления доступом для файла.

![Получить выходные данные ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

В этом примере пользователь-владелец и группа-владелец имеют только разрешения на чтение и запись. Все остальные пользователи имеют разрешения на запись и выполнение. Дополнительные сведения о списках управления доступом см. [в разделе Контроль доступа в Azure Data Lake Storage 2-го поколения](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Обновление каталога и разрешений на файлы

Другим способом установки этого разрешения является использование команды `az storage blob directory access update` или `az storage blob access update`. 

Обновите список управления доступом к каталогу или файлу, установив параметр `-permissions` в краткой форме ACL.

В этом примере обновляется список управления доступом для **каталога**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

В этом примере обновляется список управления доступом для **файла**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Вы также можете обновить пользователя-владельца и группу каталога или файла, задав для параметров `--owner` или `group` идентификатор сущности или имя участника-пользователя (UPN) пользователя. 

В этом примере изменяется владелец каталога. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

В этом примере изменяется владелец файла. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Управление определяемыми пользователем метаданными

Пользовательские метаданные можно добавить в файл или каталог с помощью команды `az storage blob directory metadata update` с одной или несколькими парами "имя-значение".

В этом примере добавляются пользовательские метаданные для каталога с именем `my-directory` Directory.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

В этом примере показаны все пользовательские метаданные для каталога с именем `my-directory`.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>См. также

* [Пример](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Сопоставление Gen1 с Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Отправка отзывов](https://github.com/Azure/azure-cli-extensions/issues)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Исходный код](https://github.com/Azure/azure-cli-extensions/tree/master/src).

