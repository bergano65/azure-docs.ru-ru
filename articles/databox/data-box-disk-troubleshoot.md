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
ms.openlocfilehash: f725f38a335972ae8e0a8b8402a99202caa54a70
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147087"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Устранение неполадок проверки в диска Azure Data Box с помощью журналов

Эта статья относится к диска Microsoft Azure Data Box. Статьи описывается использование журналов для устранения неполадок проверки, которые можно видеть при развертывании этого решения.

## <a name="validation-tool-log-files"></a>Файлы журнала для средства проверки

При проверке данных на дисках, используя [средство проверки](data-box-disk-deploy-copy-data.md#validate-data), *error.xml* создается для регистрации ошибок. Файл журнала находится в `Drive:\DataBoxDiskImport\logs` каталога диска. При выполнении проверки, предоставляется ссылка в журнале ошибок.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Если выполняется несколько сеансов для проверки, один журнал ошибок создается за сеанс.

- Ниже приведен пример журнала ошибок при загрузке данных в `PageBlob` папка не выровнен по 512 — байт. Любые данные в PageBlob должен быть 512-байтовое выравнивание, например, VHD или VHDX. Ошибки в этом файле находятся в `<Errors>` и предупреждений в `<Warnings>`.

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

- Ниже приведен пример журнала ошибок, если имя контейнера не является допустимым. Папка, созданная в разделе `BlockBlob`, `PageBlob`, или `AzureFile` папки на диске становится контейнер в вашей учетной записи хранения Azure. Имя контейнера должно соответствовать [соглашениям об именовании Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

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

## <a name="validation-tool-errors"></a>Ошибки средства проверки

Ошибки, содержащихся в *error.xml* с соответствующим рекомендуемые действия приведены в следующей таблице.

| Код ошибки| Описание                       | Рекомендованные действия               |
|------------|--------------------------|-----------------------------------|
| `None` | Успешно прошли проверку данных. | Никаких действий не требуется. |
| `InvalidXmlCharsInPath` |Не удалось создать файл манифеста, как путь к файлу содержит символы, которые являются недопустимыми. | Удалите эти символы, чтобы продолжить.  |
| `OpenFileForReadFailed`| Не удалось обработать файл. Это могло произойти повреждение доступа информацию о проблеме или файл системы.|Не удалось прочитать файл из-за ошибки. Сведения об ошибке, в исключении. |
| `Not512Aligned` | Этот файл имеет недопустимый формат для папки PageBlob.| Только данные передачи — 512 байт, выровненным по `PageBlob` папки. Удалите файл из папки PageBlob или переместите его в папку BlockBlob. Повторите проверку.|
| `InvalidBlobPath` | Путь к файлу не сопоставлен с путь допустимым BLOB-объектов в облаке согласно соглашения об именовании BLOB-объектов Azure.|Выполните Azure рекомендации по именованию для переименования пути к файлу. |
| `EnumerationError` | Не удалось перечислить файл для проверки. |Может быть несколько причин возникновения данной ошибки. Наиболее вероятная причина — доступ к файлу. |
| `ShareSizeExceeded` | Этот файл вызвал Azure размер общей папки к превышению Azure ограничение в 5 ТБ.|Уменьшить размер данных в общей папке, что он соответствует [ограничений на размер объекта Azure](data-box-disk-limits.md#azure-object-size-limits). Повторите проверку. |
| `AzureFileSizeExceeded` | Размер файла превышает ограничения на размеры файлов Azure.| Уменьшить размер файла или данных, таким образом, чтобы он соответствует [ограничений на размер объекта Azure](data-box-disk-limits.md#azure-object-size-limits). Повторите проверку.|
| `BlockBlobSizeExceeded` | Размер файла превышает пределы размера блочного большого двоичного объекта в Azure. | Уменьшить размер файла или данных, таким образом, чтобы он соответствует [ограничений на размер объекта Azure](data-box-disk-limits.md#azure-object-size-limits). Повторите проверку. |
| `ManagedDiskSizeExceeded` | Размер файла превышает пределы размера управляемого диска Azure. | Уменьшить размер файла или данных, таким образом, чтобы он соответствует [ограничений на размер объекта Azure](data-box-disk-limits.md#azure-object-size-limits). Повторите проверку. |
| `PageBlobSizeExceeded` | Размер файла превышает пределы размера управляемого диска Azure. | Уменьшить размер файла или данных, таким образом, чтобы он соответствует [ограничений на размер объекта Azure](data-box-disk-limits.md#azure-object-size-limits). Повторите проверку. |
| `InvalidShareContainerFormat`  |Имена каталогов не соответствуют соглашениям об именовании Azure для контейнеров или общие папки.         |Первая папка, созданные в существующей папки на диске становится контейнер в вашей учетной записи хранения. Это имя общего ресурса или контейнера не соответствует соглашениям об именовании Azure. Переименуйте файл, чтобы он соответствует [соглашениям об именовании Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Повторите проверку.   |
| `InvalidBlobNameFormat` | Путь к файлу не сопоставлен с путь допустимым BLOB-объектов в облаке согласно соглашения об именовании BLOB-объектов Azure.|Переименуйте файл, чтобы он соответствует [соглашениям об именовании Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Повторите проверку. |
| `InvalidFileNameFormat` | Путь к файлу не сопоставлен с допустимый путь к файлу в облаке согласно файлов Azure, соглашения об именовании. |Переименуйте файл, чтобы он соответствует [соглашениям об именовании Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Повторите проверку. |
| `InvalidDiskNameFormat` | Путь к файлу не сопоставлен с именем допустимое диск в облаке согласно соглашения об именовании управляемого диска Azure. |Переименуйте файл, чтобы он соответствует [соглашениям об именовании Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Повторите проверку.       |
| `NotPartOfFileShare` | Не удалось отправить файлы, так как путь передачи является недопустимым. Передача файлов в папку в службе файлов Azure.   | Удалить файлы с ошибками и передача файлов в предварительно созданных папку. Повторите проверку. |
| `NonVhdFileNotSupportedForManagedDisk` | Не VHD-файла не может быть загружен как управляемого диска. |Удалите не VHD-файлы, так как они не поддерживаются. Повторите проверку. |


## <a name="next-steps"></a>Дальнейшие действия

- Устранение неполадок [ошибки при отправке данных](data-box-disk-troubleshoot-upload.md).
