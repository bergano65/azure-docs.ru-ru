---
title: Копирование или перемещение данных в службу хранилища Azure с помощью AzCopy v10 (Предварительная версия) | Документация Майкрософт
description: Используйте AzCopy v10 программы командной строки (Предварительная версия) для перемещения или копирования данных и из BLOB-объектов, data lake и содержимое файла. Копируйте данные в хранилище Azure из локальных файлов, а также внутри учетной записи хранения и из одной такой учетной записи в другую. Легко переносите данные в хранилище Azure.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 02/24/2019
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: ca7081bdfedae3abb5ec426a9d3ec0a7867a2ef9
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337023"
---
# <a name="transfer-data-with-azcopy-v10-preview"></a>Передача данных с помощью AzCopy v10 (Предварительная версия)

AzCopy v10 (Предварительная версия) — это программа командной строки для копирования данных из хранилища BLOB-объектов Microsoft Azure и файл. AzCopy v10 предлагает улучшенный интерфейс командной строки и передает новую архитектуру для надежных данных. С помощью AzCopy, можно скопировать данные между файловой системой и учетной записи хранения или между учетными записями хранения.

## <a name="whats-new-in-azcopy-v10"></a>Новые возможности в AzCopy (версия 10)

- Синхронизирует файловых систем в хранилище BLOB-объектов Azure или наоборот. C помощью `azcopy sync <source> <destination>`. Идеально подходит для сценариев добавочного копирования.
- Поддержка API в Azure Data Lake Storage 2-го поколения. Используйте `myaccount.dfs.core.windows.net` как URI для вызова API Gen2 хранилища Озера данных.
- Поддержка копирования всей учетной записи (только для службы BLOB-объектов) в другую учетную запись.
- Использует новую [Put Block с URL-адреса](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) интерфейсам API для поддержки копирования учетной записи для учетной записи. Передача данных происходит быстрее, так как передача клиенту не требуется.
- Выводит список или удаляет файлы и большие двоичные объекты в заданном пути.
- Поддерживает шаблоны с подстановочными знаками в путь, а--флаги исключения.
- Создает заказ на задания и файл журнала, связанные с каждый экземпляр AzCopy. Можно просмотреть и перезапустите предыдущие задания и возобновление заданий с ошибками. Кроме того, AzCopy автоматически повторит попытку передачи после сбоя.
- Усовершенствования функции общей производительности.

## <a name="download-and-install-azcopy"></a>Скачивание и установка AzCopy

### <a name="latest-preview-version-v10"></a>Новейшая предварительная версия (10)

Скачайте новейшую предварительную версию AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar-файл)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-production-version-v81"></a>Новейшая рабочая версия (8.1)

Скачайте [новейшую рабочую версию AzCopy для Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>Версия AzCopy, поддерживающая службу хранилища таблиц (7.3)

Скачайте [версию AzCopy (7.3), поддерживающую копирование данных из службы хранилища таблиц Microsoft Azure или в нее](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Действия после установки

AzCopy v10 не требует установки. Откройте предпочитаемый приложения командной строки и перейдите к папке, где `azcopy.exe` находится. При необходимости можно добавить расположение папки AzCopy к системному пути для удобства использования.

## <a name="authentication-options"></a>Параметры проверки подлинности

При проверке подлинности с помощью службы хранилища Azure, AzCopy v10 поддерживает следующие параметры:
- **Azure Active Directory** (поддерживается для **службы BLOB-объектов и Gen2 хранилища Озера данных**). Используйте ```.\azcopy login``` выполнить вход с использованием Azure Active Directory.  Пользователь должен иметь [назначена роль «Участник данных хранилища BLOB-объектов»](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) для записи в хранилище BLOB-объектов с помощью проверки подлинности Azure Active Directory. Для проверки подлинности с помощью управляемых удостоверений для ресурсов Azure, использовать `azcopy login --identity`.
- **Общие маркеры подписи доступа [поддерживаются для службы BLOB-объектов и файлов]**. Добавьте URL-адреса (SAS) токен общего доступа к пути больших двоичных объектов в командной строке, для его использования. Позволяет создавать маркеры SAS с помощью портала Azure, [обозреватель хранилищ](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), или другие средства по своему усмотрению. Дополнительные сведения см. в статье [Подписанные URL-адреса. Часть 2: создание и использование подписанного URL-адреса в службе BLOB-объектов](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Приступая к работе

> [!TIP]
> **Предпочитаете использовать графический пользовательский интерфейс?**
>
> [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/), настольный клиент, который упрощает управление данными из хранилища Azure, теперь использует AzCopy для ускорения передачи данных и из хранилища Azure.
>
> Включить AzCopy в обозревателе службы хранилища в разделе **предварительной версии** меню.
> ![Включить AzCopy как механизм передачи в обозревателе службы хранилища Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 имеет самостоятельно документированных синтаксис. После входа систему Azure Active Directory, общий синтаксис выглядит следующим образом:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

Список доступных команд можно получить следующим образом:

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

Чтобы просмотреть страницу справки и примеры по определенной команде, выполните следующую команду:

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Создайте общую папку контейнера или файла BLOB-объектов 

**Создайте контейнер больших двоичных объектов**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Создание файлового ресурса**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Создайте контейнер больших двоичных объектов с помощью Gen2 хранилища Озера данных Azure**

Если вы включили функцию иерархических пространств имен в вашей учетной записи хранилища BLOB-объектов, можно использовать следующую команду, чтобы создать контейнер больших двоичных объектов для передачи файлов.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Копирование данных в службу хранилища Azure

Используйте команду копирования для передачи данных из источника в место назначения. Источник или назначение могут быть:
- локальная файловая система;
- URI большого двоичного объекта, виртуального каталога или контейнера Azure;
- URI файла, каталога или файлового ресурса Azure;
- URI файловой системы, каталога или файла Azure Data Lake Storage 2-го поколения.

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Следующая команда загружает все файлы в папке `C:\local\path` рекурсивно в контейнер `mycontainer1`, создавая `path` каталог в контейнере:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Следующая команда передает все файлы из папки `C:\local\path` (без рекурсии в подкаталоги) в контейнер `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Чтобы найти дополнительные примеры, используйте следующую команду:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Копирование данных между двумя учетными записями хранения

Копирование данных между двумя учетными записями хранения использует [поместить блок из URL-адрес](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API и не использует пропускную способность сети на компьютере клиента. Данные копируются между двумя серверами службы хранилища Azure напрямую, хотя AzCopy просто координирует операции копирования. Этот параметр в данный момент доступна только для хранилища BLOB-объектов.

Чтобы скопировать данные между двумя учетными записями хранения, используйте следующую команду:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> Эта команда перечисляет все контейнеры больших двоичных объектов и скопируйте их в целевую учетную запись. В настоящее время AzCopy v10 поддерживает копирование только блочные BLOB-объекты между двумя учетными записями хранения. Она пропустит все другие хранилища учетной записи объекты (такие как добавление больших двоичных объектов, страничных BLOB-объектов, файлов, таблиц и очередей).

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Копирование образа виртуального жесткого диска (VHD) в учетную запись хранения

По умолчанию AzCopy (версия 10) передает данные в блочные BLOB-объекты. Тем не менее если исходный файл имеет `.vhd` расширение, по умолчанию AzCopy v10 передачу в страничный большой двоичный объект. В настоящее время это действие не настраивается.

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Синхронизация: добавочное копирование и удаление (исключительно для хранилища BLOB-объектов)

Команда синхронизации синхронизирует содержимое исходного каталога в каталог назначения, сравнение имен файлов и последнего изменения отметки времени. Эта операция включает необязательный удаление целевых файлов, если их не существует в источнике при `--delete-destination=prompt|true` флаг. По умолчанию отключено поведение удаления. 

> [!NOTE] 
> Используйте `--delete-destination` флаг осторожно. Включить [обратимое удаление](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) компонентов, прежде чем включить поведение при удалении синхронизации для предотвращения случайного удаления в вашей учетной записи. 

> Когда `--delete-destination` имеет значение true, AzCopy приведет к удалению файлов, которые не существуют в источнике из места назначения без запроса пользователя. Если вы хотите появится запрос на подтверждение, используйте `--delete-destination=prompt`.

Чтобы синхронизировать локальную файловую систему с учетной записью хранения, используйте следующую команду:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Вы также можете синхронизировать контейнер больших двоичных объектов вниз к локальной файловой системе:

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Эта команда синхронизирует постепенно источника в место назначения, в зависимости от последней измененной отметки времени. Если вы добавите файл в источник или удалите его, AzCopy (версия 10) сделает то же самое в месте назначения. Перед удалением AzCopy предложит подтвердить.

## <a name="advanced-configuration"></a>Расширенная конфигурация

### <a name="configure-proxy-settings"></a>Настройка параметров прокси

Чтобы настроить параметры прокси-сервера для AzCopy v10, задайте https_proxy переменной среды с помощью следующей команды:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Оптимизация пропускной способности

Задайте переменную среды AZCOPY_CONCURRENCY_VALUE настроить количество одновременных запросов, а также для управления пропускной способностью производительность и потребление ресурсов. По умолчанию установлено значение 300. Уменьшение значения ограничит пропускную способность и ЦП, используемые AzCopy версии 10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>Изменение расположения файлов журнала

Вы можете изменить расположение файлов журнала, если это необходимо, или во избежание переполнения диска ОС.

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>Изменение уровня ведения журнала по умолчанию 

По умолчанию уровень ведения журнала AzCopy присваивается INFO. Если вы хотите уменьшить уровень детализации журнала для экономии места на диске, перезапишите настройку, используя параметр ``--log-level``. Ниже перечислены доступные уровни ведения журнала: DEBUG, INFO, предупреждение, ошибка, АВАРИЯ и Неустранимая ошибка.

### <a name="review-the-logs-for-errors"></a>Проверка журналов на наличие ошибок

Следующая команда получит все ошибки с состоянием UPLOADFAILED из журнала 04dc9ca9-158f-7945-5933-564021086c79:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>Устранение неполадок

AzCopy v10 создает файлы журналов и план для каждого задания. Вы можете использовать журналы для исследования и устранения возможных проблем. Журналы будут содержать состояния сбоя (UPLOADFAILED, COPYFAILED и DOWNLOADFAILED), полный путь и причину сбоя. Журналы задания и файлы плана находятся в папке %USERPROFILE\\.azcopy в Windows или в папке $HOME\\.azcopy в Mac и Linux.

> [!IMPORTANT]
> При отправке запроса поддержки Microsoft (или устранении неполадок, связанных с третьим лицам), совместно использовать исправленная версия версия команды, которую требуется выполнить. Это гарантирует, что подписанный URL-адрес не предоставлен случайно любой пользователь. Вы можете найти исправленную версию в начале файла журнала.

### <a name="view-and-resume-jobs"></a>Просмотр и возобновление задания

В рамках каждой операции передачи будет создано задание AzCopy. Чтобы просмотреть журнал заданий, используйте следующую команду:

```azcopy
.\azcopy jobs list
```

Чтобы просмотреть статистику задания, используйте следующую команду:

```azcopy
.\azcopy jobs show <job-id>
```

Чтобы отфильтровать операции передачи по состоянию, используйте следующую команду:

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

Используйте следующую команду, чтобы возобновить задание сбой или отменено. Эта команда использует свой идентификатор, а также маркер SAS. Это не так постоянных по соображениям безопасности:

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>Дальнейшие действия

Если у вас есть вопросы, проблемы и отзывах общего характера, сообщите об этом [на сайте GitHub](https://github.com/Azure/azure-storage-azcopy.).


