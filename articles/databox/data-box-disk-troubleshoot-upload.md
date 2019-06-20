---
title: Проблемы, с помощью журналов Отправка Azure данных об устранении неполадок диск Data Box | Документация Майкрософт
description: Описывает, как использовать журналы и устранение проблем, возникающих при передаче данных для диска Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 6f3ac38c3eac968bd2f7ec2aada435466d3ff279
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148313"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Понять журналы для устранения неполадок загрузки данных в диск Azure Data Box

В этой статье относится к диска Microsoft Azure Data Box и описание проблем, которые вы видите, при передаче данных в Azure.

## <a name="data-upload-logs"></a>Отправка данных журналов

При отправке данных в Azure в центре обработки данных, `_error.xml` и `_verbose.xml` создаются файлы. Эти журналы отправляются в учетную запись хранения, который использовался для отправки данных. Образец `_error.xml` показан ниже.
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="download-logs"></a>Скачивание журналов

Чтобы найти и скачать журналы диагностики двумя способами.

- При возникновении ошибки при их загрузке в Azure, на портале отображается путь к папке, где находятся журналы диагностики.

    ![Ссылка на журналы на портале](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

- Перейдите к учетной записи хранения, связанной с заказ Data Box. Перейдите в раздел **Служба BLOB-объектов > Просмотреть BLOB-объекты** и найдите BLOB-объект, соответствующий учетной записи хранения. Перейдите к **waies**.

    ![Копирование журналов 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

В каждом случае см. в журналах ошибок и подробного журнала. Выберите каждый журнал и загрузите локальную копию.


## <a name="data-upload-errors"></a>Ошибки при отправке данных

В следующей таблице приведены ошибки, создаваемые при их загрузке в Azure.

| Код ошибки | Описание                        |
|-------------|------------------------------|
|`None` |  Успешно завершено.           |
|`Renamed` | Успешно переименован большого двоичного объекта.  |                                                            |
|`CompletedWithErrors` | Отправка завершена с ошибками. Сведения о файлах с ошибками, включаются в файле журнала.  |
|`Corrupted`|CRC, вычисляемые во время приема данных не соответствует CRC, вычисляемых во время передачи.  |  
|`StorageRequestFailed` | Не удалось выполнить запрос службы хранилища Azure.   |     |
|`LeasePresent` | Этот элемент выделяется и используется другим пользователем. |
|`StorageRequestForbidden` |Не удалось отправить из-за проблем проверки подлинности. |
|`ManagedDiskCreationTerminalFailure` | Не удалось отправить как управляемые диски. Файлы доступны в промежуточной учетной записи хранения как страничные. Можно вручную преобразовать страничных BLOB-объектов для использования управляемых дисков.  |
|`DiskConversionNotStartedTierInfoMissing` | Так как VHD-файл был скопирован вне папки предварительно созданных уровня, не была создана управляемого диска. Файл передается в качестве страничного большого двоичного объекта в промежуточную учетную запись хранения, как указано во время создания заказа. Его можно преобразовать вручную в управляемый диск.|
|`InvalidWorkitem` | Не удалось отправить данные, так как он не соответствует Azure именования и ограничивает соглашения.|
|`InvalidPageBlobUploadAsBlockBlob` | Отправить в качестве блочных BLOB-объектов в контейнере с префиксом `databoxdisk-invalid-pb-`.|
|`InvalidAzureFileUploadAsBlockBlob` | Отправить в качестве блочных BLOB-объектов в контейнере с префиксом `databoxdisk-invalid-af`-.|
|`InvalidManagedDiskUploadAsBlockBlob` | Отправить в качестве блочных BLOB-объектов в контейнере с префиксом `databoxdisk-invalid-md`-.|
|`InvalidManagedDiskUploadAsPageBlob` |Отправить в качестве страничных BLOB-объектов в контейнере с префиксом `databoxdisk-invalid-md-`. |
|`MovedToOverflowShare` |Переданные файлы в новую общую папку как исходный размер общей папки превысил максимальный размер Azure. Новое имя файла с исходным именем, заканчивающиеся `-2`.   |
|`MovedToDefaultAzureShare` |Переданные файлы, которые не были частью любой папки в общую папку по умолчанию. Имя общего ресурса начинается с `databox-`. |
|`ContainerRenamed` |Контейнер для этих файлов не соответствуют соглашениям об именовании Azure и переименовывается. Новое имя начинается с `databox-` и имеет суффикс хэш SHA1 исходное имя |
|`ShareRenamed` |Общего ресурса для этих файлов не соответствуют соглашениям об именовании Azure, а переименовывается. Новое имя начинается с `databox-` и имеет суффикс хэш SHA1 исходное имя. |
|`BlobRenamed` |Эти файлы не соответствуют соглашениям об именовании Azure и были переименованы. Проверьте `BlobPath` для нового имени. |
|`FileRenamed` |Эти файлы не соответствуют соглашениям об именовании Azure и были переименованы. Проверьте `FileStoragePath` для нового имени. |
|`DiskRenamed` |Эти файлы не соответствуют соглашениям об именовании Azure и были переименованы. Проверьте `BlobPath` для нового имени. |


## <a name="next-steps"></a>Дальнейшие действия

- [Откройте запрос в службу поддержки, диск Data Box проблем](data-box-disk-contact-microsoft-support.md).
