---
title: Устранение неполадок диска Azure Data Box | Документация Майкрософт
description: В этой статье описывается, как устранить проблемы, обнаруженные на диске Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67805707"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Используйте журналы для устранения неполадок в проверке данных на диске ящика данных Azure

Эта статья относится к Microsoft Azure Data Box Disk. В статье описывается, как использовать журналы для устранения проблем проверки, которые можно было увидеть при развертывании этого решения.

## <a name="validation-tool-log-files"></a>Файлы журнала инструментов проверки

При проверке данных на дисках с помощью [инструмента проверки](data-box-disk-deploy-copy-data.md#validate-data)генерируется *ошибка.xml* генерируется для регистрации ошибок. Файл журнала находится в `Drive:\DataBoxDiskImport\logs` папке диска. При проверке проверки предоставляется ссылка на журнал ошибок.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Если вы запустите несколько сеансов для проверки, за сеанс генерируется один журнал ошибок.

- Вот пример журнала ошибок, когда данные, `PageBlob` загруженные в папку, не выровнены на 512 байтов. Любые данные, загруженные на PageBlob, должны быть выровнены на 512 байтов, например, VHD или VHDX. Ошибки в этом файле `<Errors>` находятся `<Warnings>`в и предупреждения в .

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Вот пример журнала ошибок, когда имя контейнера не действительно. Папка, которую `BlockBlob`вы `PageBlob`создаете под , или `AzureFile` папки на диске становится контейнером в вашей учетной записи хранения Azure. Название контейнера должно следовать [конвенциям о наименовании Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Ошибки инструмента проверки

Ошибки, содержащиеся в *error.xml* с соответствующими рекомендуемыми действиями, суммируются в следующей таблице.

| Код ошибки| Описание                       | Рекомендованные действия               |
|------------|--------------------------|-----------------------------------|
| `None` | Успешно проверили данные. | Никаких действий не требуется. |
| `InvalidXmlCharsInPath` |Не удалось создать файл манифеста, поскольку путь файла имеет символы, которые недействительны. | Удалите эти символы, чтобы продолжить.  |
| `OpenFileForReadFailed`| Не удалось обработать файл. Это может быть связано с проблемой доступа или порчафайлов.|Не удалось прочитать файл из-за ошибки. Детали ошибки находятся в исключении. |
| `Not512Aligned` | Этот файл не находится в действительном формате для папки PageBlob.| Только загрузить данные, которые 512 `PageBlob` байтов выровнены в папку. Удалите файл из папки PageBlob или переместите его в папку BlockBlob. Повторите проверку.|
| `InvalidBlobPath` | Путь файла не отображается на допустимом пути blob в облаке в рамках конвенций именования Azure Blob.|Следуйте рекомендациям по именованию Azure, чтобы переименовать путь файла. |
| `EnumerationError` | Не удалось перечислить файл для проверки. |Этой ошибки может быть несколько причин. Наиболее вероятной причиной является доступ к файлу. |
| `ShareSizeExceeded` | Этот файл привел к тому, что размер доли файла Azure превысил предел Azure, насм.|Уменьшите размер данных в доле, чтобы они [соответствовали ограничениям размера объекта Azure.](data-box-disk-limits.md#azure-object-size-limits) Повторите проверку. |
| `AzureFileSizeExceeded` | Размер файла превышает предельные размеры файла Azure.| Уменьшите размер файла или данных, чтобы он соответствовал [ограничениям размера объекта Azure.](data-box-disk-limits.md#azure-object-size-limits) Повторите проверку.|
| `BlockBlobSizeExceeded` | Размер файла превышает пределы размера Azure Block Blob. | Уменьшите размер файла или данных, чтобы он соответствовал [ограничениям размера объекта Azure.](data-box-disk-limits.md#azure-object-size-limits) Повторите проверку. |
| `ManagedDiskSizeExceeded` | Размер файла превышает пределы размера управляемого диска Azure. | Уменьшите размер файла или данных, чтобы он соответствовал [ограничениям размера объекта Azure.](data-box-disk-limits.md#azure-object-size-limits) Повторите проверку. |
| `PageBlobSizeExceeded` | Размер файла превышает пределы размера управляемого диска Azure. | Уменьшите размер файла или данных, чтобы он соответствовал [ограничениям размера объекта Azure.](data-box-disk-limits.md#azure-object-size-limits) Повторите проверку. |
| `InvalidShareContainerFormat`  |Имена каталогов не соответствуют конвенциям о наименованиях Azure для контейнеров или акций.         |Первая папка, созданная под уже существующими папками на диске, становится контейнером в учетной записи хранилища. Это имя доли или контейнера не соответствует соглашениям о назначении Azure. Переименуй файл так, чтобы он соответствовал [соглашениям о присвоении именовок Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Повторите проверку.   |
| `InvalidBlobNameFormat` | Путь файла не отображается на допустимом пути blob в облаке в рамках конвенций именования Azure Blob.|Переименуй файл так, чтобы он соответствовал [соглашениям о присвоении именовок Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Повторите проверку. |
| `InvalidFileNameFormat` | Путь файла не отображается на допустимом пути файла в облаке в рамках конвенций именования файлов Azure. |Переименуй файл так, чтобы он соответствовал [соглашениям о присвоении именовок Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Повторите проверку. |
| `InvalidDiskNameFormat` | Путь файла не отображается на действительное имя диска в облаке в рамках конвенций именования управляемого диска Azure. |Переименуй файл так, чтобы он соответствовал [соглашениям о присвоении именовок Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Повторите проверку.       |
| `NotPartOfFileShare` | Путь загрузки файлов недействителен. Загрузите файлы в папку в файлах Azure.   | Удалите файлы по ошибке и загрузите эти файлы в заранее созданную папку. Повторите проверку. |
| `NonVhdFileNotSupportedForManagedDisk` | Файл, не связанный с VHD, не может быть загружен в качестве управляемого диска. |Удалите файлы, `ManagedDisk` не связанные с VHD, из `PageBlob` папки, поскольку они не поддерживаются, или переместите эти файлы в папку. Повторите проверку. |


## <a name="next-steps"></a>Дальнейшие действия

- Ошибки [данных загрузки ошибок](data-box-disk-troubleshoot-upload.md).
