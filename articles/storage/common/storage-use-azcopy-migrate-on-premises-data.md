---
title: Руководство по Миграция локальных данных в службу хранилища Azure с помощью AzCopy | Документация Майкрософт
description: В этом руководстве вы используете AzCopy для перемещения или копирования данных в большие двоичные объекты, таблицы, файлы и обратно. Вы можете легко перенести данные из локального хранилища в службу хранилища Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 5c10edc4f11aad23801045011b67592b6cc537e4
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149063"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Руководство по Миграция локальных данных в службу хранилища Azure с помощью AzCopy

Средство командной строки AzCopy принимает простые команды, позволяющие копировать данные в хранилища BLOB-объектов Azure, файлов Azure и таблиц Azure, а также из этих хранилищ. Команды рассчитаны на оптимальную производительность. С помощью AzCopy вы можете перемещать данные между файловой системой и учетной записью хранения или между разными учетными записями хранения. AzCopy может использоваться для копирования локальных данных в учетную запись хранения.

Загрузите версию AzCopy, подходящую для вашей операционной системы:

* [AzCopy для Linux](storage-use-azcopy-linux.md) создана на основе платформы .NET Core Framework. Она предназначена для платформы Linux и поддерживает параметры командной строки в стиле POSIX. 
* [AzCopy для Windows](storage-use-azcopy.md) создана на основе платформы .NET Framework. Она предлагает параметры командной строки в стиле Windows. 
 
Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создайте учетную запись хранения. 
> * использование AzCopy для отправки всех данных;
> * изменение данных для целей тестирования;
> * создание запланированных заданий или задач cron для поиска новых файлов для отправки.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить инструкции из этого руководства, скачайте последнюю версию AzCopy для [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) или [Windows](https://aka.ms/downloadazcopy).

Если вы работаете в Windows, вам потребуется средство [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx), так как в этом руководстве оно используется для планирования задач. Пользователи Linux вместо этого будут использовать команду crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Создание контейнера

Первый шаг — создать контейнер, потому что большие двоичные объекты должны всегда загружаться в контейнер. Контейнеры выступают средством упорядочения групп больших двоичных объектов подобно тому, как папки используются для упорядочения файлов на компьютере. 

Чтобы создать контейнер, сделайте следующее.

1. Нажмите кнопку **Учетные записи хранения** на главной странице и выберите созданную учетную запись хранилища.
2. Выберите **Большие двоичные объекты** в разделе **Службы**, а затем выберите **Контейнер**. 

   ![Создание контейнера](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Имя контейнера должно начинаться с буквы или цифры. Эти имена могут содержать только буквы, цифры и знак дефиса (-). Дополнительные сведения об именовании больших двоичных объектов и контейнеров см. в статье [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Отправка содержимого папки в хранилище BLOB-объектов

С помощью AzCopy вы можете передать все файлы из определенной папки в [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) или [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download) в хранилище больших двоичных объектов. Чтобы передать все большие двоичные объекты, содержащиеся в папке, введите следующую команду AzCopy:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S
---

Вместо `<key>` и `key` укажите ключ учетной записи. Ключ учетной записи вы можете узнать на портале Azure, выбрав пункт **Ключи доступа** в разделе **Параметры** для учетной записи хранения. Выберите ключ и вставьте его в команду AzCopy. Если указанный контейнер назначения не существует, AzCopy создаст его и передаст в него файл. В качестве исходного пути укажите путь к нужному каталогу данных, а в URL-адресе назначения замените **myaccount** именем учетной записи хранилища.

Чтобы рекурсивно передать все содержимое определенного каталога в хранилище больших двоичных объектов, укажите параметр `--recursive` (для Linux) или `/S` (для Windows). При запуске с этими параметрами AzCopy передает все вложенные папки и файлы.

## <a name="upload-modified-files-to-blob-storage"></a>Отправка измененных файлов в хранилище BLOB-объектов

Вы можете использовать AzCopy для [передачи файлов](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) с учетом времени их последнего изменения. Чтобы проверить этот режим, измените или создайте несколько тестовых файлов в исходном каталоге. Чтобы отправить только те файлы, которые обновлены или добавлены, укажите параметр `--exclude-older` (для Linux) или `/XO` (для Windows) в команде AzCopy.

Если вы хотите скопировать только те ресурсы, которые существуют в папке источника и отсутствуют в целевой папке, укажите параметры `--exclude-older` и `--exclude-newer` (для Linux) или `/XO` и `/XN` (для Windows) в команде AzCopy. AzCopy передаст только обновленные данные, учитывая отметки времени файлов.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S /XO
---

## <a name="create-a-scheduled-task"></a>Создание запланированной задачи

Вы можете создать запланированную задачу или задание cron, которые выполняют скрипт с командой AzCopy. Этот скрипт обнаруживает и отправляет в облачное хранилище обновленные локальные данные через определенные промежутки времени.

Скопируйте команду AzCopy в текстовый редактор. Замените значения параметров в команде AzCopy подходящими значениями. Сохраните файл AzCopy с именем `script.sh` (для Linux) или `script.bat` (для Windows).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy может выполняться в режиме подробных сведений, если указан параметр `--verbose` (для Linux) или `/V` (для Windows). Вывод перенаправляется в файл журнала.

В этом руководстве мы применим [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) для создания в Windows запланированной задачи. Команда [Crontab](http://crontab.org/) позволяет создать задание cron в Linux.
 **Schtasks** позволяет администратору создавать, удалять, просматривать, изменять, выполнять или завершать запланированные задачи на локальном или удаленном компьютере. **Cron** в Linux и Unix дает пользователям возможность выполнять команды или скрипты в определенное время определенного дня с помощью [выражений cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Чтобы создать в Linux задание cron, введите в терминале следующую команду:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Если в команде указано выражение cron `*/5 * * * *`, то скрипт оболочки `script.sh` будет выполняться каждые пять минут. Вы можете назначить выполнение скрипта на определенное время ежедневно, ежемесячно или ежегодно. Дополнительные сведения о настройке даты и времени для выполнения заданий вы найдете в документации по [выражениям cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Чтобы создать в Windows назначенную задачу, введите в командной строке или в PowerShell следующую команду:

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

Эта команда использует следующие параметры:
- `/SC` позволяет указать расписание в минутах;
- `/MO` обозначает интервал длительностью пять минут;
- `/TN` позволяет указать имя задачи;
- `/TR` позволяет указать путь к файлу `script.bat`.

Дополнительные сведения о создании запланированной задачи в Windows см. в документации по [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---

Чтобы проверить правильность работы запланированных задач и (или) заданий cron, создайте в каталоге `myfolder` новые файлы. Подождите пять минут, чтобы новые файлы успели переместиться в учетную запись хранения. Перейдите в каталог журнала и проверьте содержимое журналов для запланированной задачи или задания cron.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о способах перемещения данных из локальной среды в службу хранилища Azure и обратно см. по этой ссылке:

> [!div class="nextstepaction"]
> [Перемещение данных в службу хранилища Azure и обратно](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)  

Для получения дополнительных сведений об AzCopy выберите подходящую статью для своей операционной системы:

> [!div class="nextstepaction"]
> [Перенос данных с помощью AzCopy для Windows](storage-use-azcopy.md)
> [Перенос данных с помощью AzCopy для Linux](storage-use-azcopy-linux.md)
