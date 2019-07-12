---
title: Проблемы, с помощью журналов Отправка Azure данных об устранении неполадок диск Data Box | Документация Майкрософт
description: Описывает, как использовать журналы и устранение проблем, возникающих при передаче данных для диска Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807495"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Понять журналы для устранения неполадок загрузки данных в диск Azure Data Box

В этой статье относится к диска Microsoft Azure Data Box и описание проблем, которые вы видите, при передаче данных в Azure.

## <a name="about-upload-logs"></a>О журналах передачи

При отправке данных в Azure в центре обработки данных, `_error.xml` и `_verbose.xml` файлы создаются для каждой учетной записи хранения. Эти журналы отправляются в учетную запись хранения, который использовался для отправки данных. 

Оба журналы находятся в том же формате и содержат XML-описания событий, возникших при копировании данных с диска в учетную запись хранения Azure.

Подробный журнал содержит полные сведения о состоянии операции копирования для каждого большого двоичного объекта или файла, а журнал ошибок содержит только сведения о больших двоичных объектов или файлов, которые обнаружены ошибки во время загрузки.

Журнал ошибок имеет ту же структуру, что подробный журнал, но отфильтровывает успешных операций.

## <a name="download-logs"></a>Скачивание журналов

Выполните следующие действия для поиска журналов передачи.

1. При возникновении ошибки при их загрузке в Azure, на портале отображается путь к папке, где находятся журналы диагностики.

    ![Ссылка на журналы на портале](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Перейдите к **waies**.

    ![ошибок и подробного журнала](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

В каждом случае см. в журналах ошибок и подробного журнала. Выберите каждый журнал и загрузите локальную копию.

## <a name="sample-upload-logs"></a>Примеры отправки журналов

Образец `_verbose.xml` показан ниже. В этом случае порядок успешно завершена без ошибок.

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

Для одного заказа, являются примером `_error.xml` показан ниже.

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

Образец `_error.xml` ниже where порядок выполнена с ошибками. 

Файл ошибок в этом случае имеет `Summary` раздел и другой раздел, содержащий файл уровень ошибки. 

`Summary` Содержит `ValidationErrors` и `CopyErrors`. В этом случае 8 файлов или папок были переданы в Azure, и были устранены все ошибки проверки. Если данные скопированы в учетную запись хранения Azure, 5 файлов или папок успешно отправлены. Оставшиеся 3 файлы или папки были переименованы в соответствии с соглашения об именовании контейнеров Azure и затем успешно отправлены в Azure.

Состояние уровня файла находятся в `BlobStatus` , описывающий каких-либо действий, необходимое для отправки больших двоичных объектов. В этом случае три контейнера переименовываются, так как папки, к которым был скопирован данных не соответствует Azure соглашения об именовании для контейнеров. Для больших двоичных объектов, загружены в этих контейнерах включены имя нового контейнера, пути большого двоичного объекта в Azure, исходное недопустимый путь к файлу и размер большого двоичного объекта.
    
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

## <a name="data-upload-errors"></a>Ошибки при отправке данных

В следующей таблице приведены ошибки, создаваемые при их загрузке в Azure.

| Код ошибки | Описание                   |
|-------------|------------------------------|
|`None` |  Успешно завершено.           |
|`Renamed` | Успешно переименован большого двоичного объекта.   |
|`CompletedWithErrors` | Отправка завершена с ошибками. Сведения о файлах с ошибками, включаются в файле журнала.  |
|`Corrupted`|CRC, вычисляемые во время приема данных не соответствует CRC, вычисляемых во время передачи.  |  
|`StorageRequestFailed` | Не удалось выполнить запрос службы хранилища Azure.   |     
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


## <a name="next-steps"></a>Следующие шаги

- [Откройте запрос в службу поддержки, диск Data Box проблем](data-box-disk-contact-microsoft-support.md).
