---
title: Данные о проблеме загружают с помощью журналов
titleSuffix: Azure Data Box Disk
description: Описывает, как использовать журналы и устранение неполадок, увиденных при загрузке данных на Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260141"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Понимание журналов для устранения проблем с устранением проблем с данными в Azure Data Box Disk

Эта статья относится к Microsoft Azure Data Box Disk и описывает проблемы, которые вы видите при загрузке данных в Azure.

## <a name="about-upload-logs"></a>О загрузке журналов

Когда данные загружаются в Azure `_error.xml` `_verbose.xml` в центре обработки данных и создаются файлы для каждой учетной записи хранилища. Эти журналы загружаются в ту же учетную запись хранения, которая использовалась для загрузки данных. 

Оба журнала находятся в одном формате и содержат XML описания событий, произошедших при копировании данных с диска в учетную запись хранения Azure.

Многословный журнал содержит полную информацию о состоянии операции копирования для каждого капли или файла, в то время как журнал ошибок содержит только информацию о каплях или файлах, которые столкнулись с ошибками во время загрузки.

В журнале ошибок есть та же структура, что и многословный журнал, но отфильтровывает успешные операции.

## <a name="download-logs"></a>Скачивание журналов

следующие шаги, чтобы найти журналы загрузки.

1. При наличии каких-либо ошибок при загрузке данных в Azure портал отображает путь к папке, в которой находятся журналы диагностики.

    ![Ссылка на журналы на портале](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Перейти к **waies**.

    ![ошибки и многословные журналы](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

В каждом случае вы видите журналы ошибок и многословные журналы. Выберите каждый журнал и загрузите локальную копию.

## <a name="sample-upload-logs"></a>Образцы журналов загрузки

Образец приведен `_verbose.xml` ниже. В этом случае заказ успешно выполнен без ошибок.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Для того же порядка ниже `_error.xml` показан образец приведен.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Образец приведен `_error.xml` ниже, где заказ завершен с ошибками. 

Файл ошибки в этом `Summary` случае имеет раздел и другой раздел, содержащий все ошибки уровня файла. 

Содержит `Summary` `ValidationErrors` и `CopyErrors`. В этом случае 8 файлов или папок были загружены в Azure и не было ошибок проверки. При копировании данных в учетную запись Azure Storage успешно загружены 5 файлов или папок. Остальные 3 файла или папки были переименованы в конвенции о наименовании контейнеров Azure, а затем успешно загружены в Azure.

Статус уровня файла `BlobStatus` в том, что описывает любые действия, предпринятые для загрузки капли. В этом случае три контейнера переименовываются, поскольку папки, с копировавшие данные, не соответствовали конвенциям о наименованиях Azure для контейнеров. Для капли, загруженные в эти контейнеры, включены новое название контейнера, путь капли в Azure, исходный недействительный путь файла и размер капли.
    
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

## <a name="data-upload-errors"></a>Ошибки загрузки данных

Ошибки, создаваемые при загрузке данных в Azure, обобщены в следующей таблице.

| Код ошибки | Описание                   |
|-------------|------------------------------|
|`None` |  Успешно завершено.           |
|`Renamed` | Успешно переименован капли.   |
|`CompletedWithErrors` | Загрузка завершена с ошибками. Детали файлов по ошибке включены в файл журнала.  |
|`Corrupted`|CRC, вычисленный при проглатывания данных, не соответствует CRC, вычисленных во время загрузки.  |  
|`StorageRequestFailed` | Запрос на хранение Azure не удался.   |     
|`LeasePresent` | Этот элемент сдается в аренду и используется другим пользователем. |
|`StorageRequestForbidden` |Не удалось загрузить из-за проблем с аутентификацией. |
|`ManagedDiskCreationTerminalFailure` | Не удалось загрузить в качестве управляемых дисков. Файлы доступны в учетной записи хранения в виде капли страницы. Вы можете вручную конвертировать капли страницы в управляемые диски.  |
|`DiskConversionNotStartedTierInfoMissing` | Так как файл VHD был скопирован за пределами заранее созданных папок уровня, управляемый диск не был создан. Файл загружается как капля страницы в учетную запись хранения, как указано при создании заказа. Вы можете преобразовать его вручную на управляемый диск.|
|`InvalidWorkitem` | Не удалось загрузить данные, так как они не соответствуют именованиям Azure и ограничивают конвенции.|
|`InvalidPageBlobUploadAsBlockBlob` | Загружено как блок капли в контейнере с приставкой `databoxdisk-invalid-pb-`.|
|`InvalidAzureFileUploadAsBlockBlob` | Загружено как блок капли в контейнере с префиксом `databoxdisk-invalid-af`-.|
|`InvalidManagedDiskUploadAsBlockBlob` | Загружено как блок капли в контейнере с префиксом `databoxdisk-invalid-md`-.|
|`InvalidManagedDiskUploadAsPageBlob` |Загружено как капли страницы в `databoxdisk-invalid-md-`контейнере с приставкой. |
|`MovedToOverflowShare` |Загруженные файлы на новую долю по мере того, как исходный размер доли превышал максимальный предел размера Azure. Новое имя общего файла имеет оригинальное `-2`имя, суффиксированное с .   |
|`MovedToDefaultAzureShare` |Загруженные файлы, которые не были частью какой-либо папки на долю по умолчанию. Имя доли начинается с `databox-`. |
|`ContainerRenamed` |Контейнер для этих файлов не соответствовал соглашениям о присвоении имен azure и был переименован. Новое название начинается с `databox-` и суффиксируется с SHA1 хэш оригинального названия |
|`ShareRenamed` |Доля этих файлов не соответствовала конвенциям о присвоении имен Azure и была переименована. Новое название начинается с `databox-` и суффиксируется с sha1 хэш оригинального названия. |
|`BlobRenamed` |Эти файлы не соответствовали конвенциям о присвоении имен Azure и были переименованы. Проверьте `BlobPath` поле на наличие нового имени. |
|`FileRenamed` |Эти файлы не соответствовали конвенциям о присвоении имен Azure и были переименованы. Проверьте `FileStoragePath` поле на наличие нового имени. |
|`DiskRenamed` |Эти файлы не соответствовали конвенциям о присвоении имен Azure и были переименованы. Проверьте `BlobPath` поле на наличие нового имени. |


## <a name="next-steps"></a>Дальнейшие действия

- [Откройте билет поддержки для вопросов data Box Disk.](data-box-disk-contact-microsoft-support.md)
