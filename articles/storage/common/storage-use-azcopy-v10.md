---
title: Копирование или перемещение данных в службу хранилища Azure с помощью AzCopy версии 10 (предварительная версия) | Документация Майкрософт
description: Скопируйте или переместите данные в содержимое больших двоичных объектов, Data Lake и файлов или из этого содержимого с помощью AzCopy версии 10 (предварительная версия). Копируйте данные в хранилище Azure из локальных файлов, а также внутри учетной записи хранения и из одной такой учетной записи в другую. Легко переносите данные в хранилище Azure.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.component: common
ms.openlocfilehash: 2d0f3292a12505249ebc8594c58234a0c6e81a8a
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54212504"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>Передача данных с помощью AzCopy версии 10 (предварительная версия)

AzCopy версии 10 (предварительная версия) — это служебная программа командной строки следующего поколения, используемая для копирования данных в хранилище BLOB-объектов Microsoft Azure и хранилище файлов, а также из них. Она предлагает обновленный интерфейс командной строки и новую архитектуру для высокопроизводительных надежных операций передачи данных. С помощью AzCopy вы можете перемещать данные между файловой системой и учетной записью хранения или между разными учетными записями хранения.

## <a name="whats-new-in-azcopy-v10"></a>Новые возможности в AzCopy (версия 10)

- Синхронизация файловой системы с большим двоичным объектом Azure или наоборот. C помощью `azcopy sync <source> <destination>`. Идеально подходит для сценариев добавочного копирования.
- Поддержка API в Azure Data Lake Storage 2-го поколения. Используйте `myaccount.dfs.core.windows.net` в качестве URI для вызова API Azure Data Lake Storage 2-го поколения.
- Поддержка копирования всей учетной записи (только для службы BLOB-объектов) в другую учетную запись.
- Теперь для копирования учетной записи используется новый API [Put из URL ](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url). Данные не нужно передавать клиенту — это ускоряет передачу.
- Вывод списка файлов и больших двоичных объектов по заданному пути и их удаление.
- Поддержка шаблонов подстановочных знаков в пути, а также флагов --include и --exclude.
- Улучшенная устойчивость: каждый экземпляр AzCopy создает заказ по заданию и связанный с ним файл журнала. Вы можете просматривать и перезапускать предыдущие задания, а также возобновлять неудачные. Кроме того, AzCopy автоматически повторит попытку передачи после сбоя.
- Общие улучшения производительности.

## <a name="download-and-install-azcopy"></a>Скачивание и установка AzCopy

### <a name="latest-preview-version-v10"></a>Новейшая предварительная версия (10)

Скачайте новейшую предварительную версию AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>Новейшая рабочая версия (8.1)

Скачайте [новейшую рабочую версию AzCopy для Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>Версия AzCopy, поддерживающая службу хранилища таблиц (7.3)

Скачайте [версию AzCopy (7.3), поддерживающую копирование данных из службы хранилища таблиц Microsoft Azure или в нее](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Действия после установки

AzCopy (версия 10) не требует установки. Откройте предпочитаемое приложение командной строки и перейдите к папке, в которой находится исполняемый файл `azcopy.exe`. При необходимости можно добавить расположение папки AzCopy к системному пути.

## <a name="authentication-options"></a>Параметры аутентификации

AzCopy (версия 10) позволяет использовать следующие параметры при аутентификации с помощью службы хранилища Azure:
- **Azure Active Directory [поддерживается в хранилище BLOB-объектов и ADLS 2-го поколения]**. При использовании Azure Active Directory выполняйте вход в систему с помощью ```.\azcopy login```.  Пользователю должна быть назначена роль [Участник для данных больших двоичных объектов хранилища](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) для записи в хранилище BLOB-объектов с использованием аутентификации Azure Active Directory.
- **Маркеры SAS [поддерживаются в службе BLOB-объектов и службе файлов]**. Чтобы использовать маркер SAS, добавьте его в путь к большому двоичному объекту в командной строке. Вы можете создать маркер SAS с помощью портала Azure, [Обозревателя службы хранилища](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken) или других инструментов по своему усмотрению. Дополнительные сведения см. в статье [Подписанные URL-адреса. Часть 2: создание и использование подписанного URL-адреса в службе BLOB-объектов](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Приступая к работе

AzCopy (версия 10) имеет простой самостоятельно документированный синтаксис. Во время входа в Azure Active Directory общий синтаксис выглядит следующим образом:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you are using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

Список доступных команд можно получить следующим образом:

```azcopy
.\azcopy -help
# Using the alias instead
.\azcopy -h
```

Чтобы просмотреть страницу справки и примеры для конкретной команды, выполните следующую команду:

```azcopy
.\azcopy <cmd> -help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Создание контейнера больших двоичных объектов или общего файлового ресурса 

**Создание контейнера больших двоичных объектов**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Создание общего файлового ресурса**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Создание контейнера больших двоичных объектов с помощью ADLS 2-го поколения**

Если вы включили иерархические пространства имен в учетной записи хранилища BLOB-объектов, можно использовать следующую команду для создания новой файловой системы (контейнера больших двоичных объектов), в которую можно передавать файлы.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Копирование данных в службу хранилища Azure

Используйте команду копирования для передачи данных из источника в место назначения. Источником или местом назначения может быть:
- локальная файловая система;
- URI большого двоичного объекта, виртуального каталога или контейнера Azure;
- URI файла, каталога или файлового ресурса Azure;
- URI файловой системы, каталога или файла Azure Data Lake Storage 2-го поколения.

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Следующая команда рекурсивно передает все файлы из папки `C:\local\path` в контейнер `mycontainer1`, создавая каталог `path` в контейнере:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Следующая команда передает все файлы из папки `C:\local\path` (без рекурсии в подкаталоги) в контейнер `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Чтобы получить дополнительные примеры, используйте следующую команду:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Копирование данных между двумя учетными записями хранения

При копировании данных между двумя учетными записями хранения используется API [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) и не используется пропускная способность сети компьютера клиента. Данные копируются между двумя серверами службы хранилища Azure непосредственно, а AzCopy просто координирует операцию копирования. Сейчас эта возможность поддерживается только для хранилища BLOB-объектов.

Чтобы скопировать данные между двумя учетными записями хранения, используйте следующую команду:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> Команда перечислит все контейнеры больших двоичных объектов и скопирует их в целевую учетную запись. В настоящее время AzCopy (версия 10) поддерживает копирование только блочных BLOB-объектов между двумя учетными записями. Остальные объекты учетной записи хранения (добавочные большие двоичные объекты, страничные BLOB-объекты, файлы, таблицы и очереди) будут пропущены.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Копирование образа виртуального жесткого диска (VHD) в учетную запись хранения

По умолчанию AzCopy (версия 10) передает данные в блочные BLOB-объекты. Однако, если исходный файл имеет расширение VHD, AzCopy версии 10 по умолчанию отправит их в страничный BLOB-объект. Такое поведение сейчас не настраивается.

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Синхронизация: добавочное копирование и удаление (исключительно для хранилища BLOB-объектов)

> [!NOTE]
> Команда синхронизации синхронизирует содержимое из источника в место назначения. Этот процесс включает удаление конечных файлов, если их нет в источнике. Убедитесь, что вы используете место назначения, которое планируете синхронизировать.

Чтобы синхронизировать локальную файловую систему с учетной записью хранения, используйте следующую команду:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Таким же образом вы можете синхронизировать контейнер больших двоичных объектов с локальной файловой системой:

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Команда позволяет выполнять добавочную синхронизацию источника с местом назначения на основе последних измененных меток времени. Если вы добавите файл в источник или удалите его, AzCopy (версия 10) сделает то же самое в месте назначения. Перед удалением AzCopy предложит подтвердить удаление файлов.

## <a name="advanced-configuration"></a>Расширенная конфигурация

### <a name="configure-proxy-settings"></a>Настройка параметров прокси

Чтобы настроить параметры прокси-сервера для AzCopy (версия 10), задайте переменную среды https_proxy с помощью следующей команды:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Оптимизация пропускной способности

Задайте переменную среды AZCOPY_CONCURRENCY_VALUE, чтобы настроить количество одновременных запросов, а также управлять производительностью и потреблением ресурсов. По умолчанию установлено значение 300. Уменьшение значения ограничит пропускную способность и ЦП, используемые AzCopy версии 10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
```

## <a name="troubleshooting"></a>Устранение неполадок

AzCopy (версия 10) создает файлы журналов и планов для всех заданий. Вы можете использовать журналы для исследования и устранения возможных проблем. Журналы будут содержать состояния сбоя (UPLOADFAILED, COPYFAILED и DOWNLOADFAILED), полный путь и причину сбоя. Журналы заданий и файлы плана находятся в папке %USERPROFILE\\.azcopy.

### <a name="review-the-logs-for-errors"></a>Проверка журналов на наличие ошибок

Следующая команда получит все ошибки с состоянием UPLOADFAILED из журнала 04dc9ca9-158f-7945-5933-564021086c79:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

### <a name="view-and-resume-jobs"></a>Просмотр и возобновление задания

В рамках каждой операции передачи будет создано задание AzCopy. Вы можете просмотреть историю заданий с помощью следующей команды:

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

Вы можете возобновить неудавшееся или отмененное задание, используя его идентификатор вместе с маркером SAS (в целях безопасности он не сохраняется):

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

### <a name="change-the-default-log-level"></a>Изменение уровня ведения журнала по умолчанию

По умолчанию для уровня ведения журнала AzCopy присваивается значение INFO. Если вы хотите уменьшить уровень детализации журнала для экономии места на диске, перезапишите настройку, используя параметр ``--log-level``. Ниже перечислены доступные уровни ведения журнала: DEBUG, INFO, WARNING, ERROR, PANIC и FATAL.

## <a name="next-steps"></a>Дополнительная информация

Мы всегда рады вашим отзывам. Если у вас возникли вопросы, проблемы или вы просто хотите оставить отзыв, обращайтесь по адресу https://github.com/Azure/azure-storage-azcopy. Благодарим вас!