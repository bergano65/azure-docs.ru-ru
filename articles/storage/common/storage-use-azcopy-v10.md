---
title: Копирование или перемещение данных в службу хранилища Azure с помощью AzCopy v10 | Документация Майкрософт
description: AzCopy — программа командной строки, который можно использовать для копирования данных, из или между учетными записями хранения. Эта статья поможет вам скачать AzCopy, подключиться к вашей учетной записи хранения и затем передачи файлов.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: bfa3e5a943ee59b1ed335f45e113a60f62572675
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735028"
---
# <a name="get-started-with-azcopy"></a>Начало работы с AzCopy

AzCopy — программа командной строки, который можно использовать для копирования больших двоичных объектов или файлов из учетной записи хранения. Эта статья поможет вам скачать AzCopy, подключиться к вашей учетной записи хранения и затем передачи файлов.

> [!NOTE]
> AzCopy **V10** — поддерживаемые в настоящее время версия AzCopy.
>
> Если вам нужно использовать AzCopy **v8.1**, см. в разделе [использовать предыдущую версию AzCopy](#previous-version) этой статьи.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Скачивание AzCopy

Во-первых Загрузите исполняемый файл AzCopy V10 в любой каталог на компьютере. 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (ZIP-файл)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (TAR-файл)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (ZIP-файл)

> [!NOTE]
> Если вы хотите скопировать данные из вашей [хранилища таблиц Azure](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) службы, а затем установить [AzCopy версии 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Запуск AzCopy

Для удобства рекомендуется добавить расположение каталога исполняемый файл AzCopy к системному пути для удобства использования. Таким образом, можно ввести `azcopy` из любого каталога в вашей системе.

Если вы не захотите добавить путь к каталогу AzCopy, необходимо будет измените каталоги на расположение исполняемый файл AzCopy и типа `azcopy` или `.\azcopy` в командной строке Windows PowerShell.

Чтобы просмотреть список команд, введите `azcopy -h` и нажмите клавишу ВВОД.

Дополнительные сведения о конкретной команды, достаточно включить имя команды (например: `azcopy list -h`).

![Встроенная справка](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Прежде чем сделать что-нибудь с помощью AzCopy, вам нужно решить, каким образом необходимо предоставить учетные данные авторизации в службе хранилища.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Выберите, каким образом необходимо предоставить учетные данные авторизации

Можно предоставить учетные данные авторизации с помощью Azure Active Directory (AD) или с помощью токена подписи общего доступа (SAS).

Используйте эту таблицу в качестве руководства:

| Тип хранилища | Поддерживаемые в настоящее время метод авторизации |
|--|--|
|**Хранилище BLOB-объектов** | Azure AD & SAS |
|**Хранилище BLOB-объектов (иерархические пространства имен)** | Только Azure AD |
|**Хранилище файлов** | Только SAS |

### <a name="option-1-use-azure-ad"></a>Вариант 1. Использование Azure AD

Уровень авторизации, который требуется основан на планируете ли вы отправить файлы или просто загрузите их.

#### <a name="authorization-to-upload-files"></a>Авторизации для отправки файлов

Убедитесь, что одной из этих ролей был назначен личности:

- [участник данных BLOB-объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor);
- [владелец данных BLOB-объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner);

Эти роли могут назначаться личности в любом из этих областей:

- Контейнер (файловая система)
- Учетная запись хранения
- Группа ресурсов
- Подписка

Чтобы узнать, как проверить и назначение ролей, см. в разделе [предоставить доступ к BLOB-объектов и очереди данных Azure с помощью RBAC на портале Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Не нужно иметь одну из этих ролей, присваиваемый свое удостоверение личности добавляется в список управления доступом (ACL) целевой контейнер или каталога. В списке управления Доступом личности требуется разрешение на запись для целевого каталога и разрешение выполнения для контейнера и каждый родительский каталог.

Дополнительные сведения см. в разделе [управление доступом в Azure Data Lake хранилища Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authorization-to-download-files"></a>Авторизация для загрузки файлов

Убедитесь, что одной из этих ролей был назначен личности:

- [читатель данных больших двоичных объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader).
- [участник данных BLOB-объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor);
- [владелец данных BLOB-объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner);

Эти роли могут назначаться личности в любом из этих областей:

- Контейнер (файловая система)
- Учетная запись хранения
- Группа ресурсов
- Подписка

Чтобы узнать, как проверить и назначение ролей, см. в разделе [предоставить доступ к BLOB-объектов и очереди данных Azure с помощью RBAC на портале Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Не нужно иметь одну из этих ролей, присваиваемый свое удостоверение личности добавляется в список управления доступом (ACL) целевой контейнер или каталога. В списке управления Доступом личности необходимо иметь разрешение на чтение каталога целевой и разрешение выполнения для контейнера и каждый родительский каталог.

Дополнительные сведения см. в разделе [управление доступом в Azure Data Lake хранилища Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-your-identity"></a>Проверки подлинности

Когда вы проверите, что ваше удостоверение получил нужный уровень авторизации, откройте командную строку, введите следующую команду и нажмите клавишу ВВОД.

```azcopy
azcopy login
```

Эта команда возвращает код проверки подлинности и URL-адрес веб-сайта. Откройте веб-сайт, укажите код и нажмите кнопку **Далее**.

![Создание контейнера](media/storage-use-azcopy-v10/azcopy-login.png)

Откроется окно входа. В этом окне войдите в свою учетную запись Azure с помощью соответствующих данных. Выполнив вход, можно закрыть окно браузера и начать работу с AzCopy.

### <a name="option-2-use-a-sas-token"></a>Вариант 2. Использование маркера SAS.

Маркер SAS можно добавить к каждой исходной или целевой URL-адрес, использовать в командах AzCopy.

Этом примере команда рекурсивно копирует данные из локального каталога в контейнер больших двоичных объектов. Вымышленная маркер SAS добавляется в конец URL-адреса контейнера.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Дополнительные сведения о маркерах SAS и как его получить, см. в разделе [использование подписи общего доступа (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Передача файлов

После проверки подлинности личности или полученный маркер SAS, можно начать передачу файлов.

Примеры команд см. в статье любой из следующих статей.

- [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md) (Передача данных с помощью AzCopy и хранилища BLOB-объектов)

- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)

- [Передача данных с помощью AzCopy и контейнеров Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Настроить, оптимизировать и устранение неполадок с AzCopy

См. в разделе [Настройка, оптимизации и устранении неполадок AzCopy](storage-use-azcopy-configure.md)

## <a name="use-azcopy-in-storage-explorer"></a>Использование AzCopy в обозревателе службы хранилища

Если вы хотите получить преимущества производительности AzCopy, но вы предпочитаете использовать обозреватель службы хранилища, а не командной строки для взаимодействия с файлами, включите AzCopy в обозревателе службы хранилища.

В обозревателе службы хранилища выберите **предварительной версии**->**использование AzCopy для Улучшенная передачи больших двоичных объектов и загрузки**.

![Включить AzCopy как механизм передачи в обозревателе службы хранилища Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Включите этот параметр, если вы включили функцию иерархического пространства имен в вашей учетной записи хранения не нужно. Том, что обозреватель хранилищ автоматически использует AzCopy в учетных записях хранения, имеющих иерархического пространства имен.  

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Использовать предыдущую версию AzCopy

Если необходимо использовать предыдущую версию AzCopy (AzCopy v8.1), см. в статье из следующих ссылок:

- [AzCopy для Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)
- [AzCopy для Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Дальнейшие действия

Если у вас есть вопросы, проблемы и отзывах общего характера, сообщите об этом [на сайте GitHub](https://github.com/Azure/azure-storage-azcopy) страницы.
