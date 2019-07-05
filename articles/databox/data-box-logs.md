---
title: Отслеживания и регистрации Azure Data Box, Azure Data Box большой события | Документация Майкрософт
description: Описывает способ отслеживания и регистрации событий на различных этапах заказа Azure Data Box и Azure Data Box большой.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: ba08cd7fdecda99c04d5bb1007b3e5f61cd1bd5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446768"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Отслеживание и ведение журнала событий для Azure Data Box и интенсивной поле данных Azure

Заказ на Data Box или большой поле данных проходит через следующие действия: заказ, Настройка, данных скопируйте, возврата, передачи в Azure и проверки и стирания данных. Соответствующим образом для каждого шага в порядке, можно выполнить несколько действий для управления доступом к заказу, события аудита, отслеживания порядка и интерпретировать различные журналы, которые создаются.

Следующая таблица показывает сводку действия заказ Data Box или большой поле данных и средств, доступных для отслеживания и аудита порядок во время выполнения каждого шага.

| Этап порядок поле данных       | Средство для отслеживания и аудита                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Создание заказа               | [Настройка управления доступом для заказа с помощью RBAC](#set-up-access-control-on-the-order)                                                    |
| Порядок обработки            | [Отслеживания порядка](#track-the-order) через <ul><li> Портал Azure </li><li> Доставка веб-сайта перевозчика </li><li>Уведомления по электронной почте</ul> |
| Настройка устройства              | Устройства, учетные данные доступа, вход [журналы действий](#query-activity-logs-during-setup)                                              |
| Копирование данных на устройстве        | [Представление *error.xml* файлы](#view-error-log-during-data-copy) для копирования данных                                                             |
| Подготовка к отправке            | [Проверка файлов Спецификации](#inspect-bom-during-prepare-to-ship) или файлы на устройстве                                      |
| Отправка данных в Azure       | [Просмотрите *copylogs* ](#review-copy-log-during-upload-to-azure) ошибки во время данных можно загрузить в центре обработки данных Azure                         |
| Стирание данных с устройства   | [Просмотр цепочки обеспечения сохранности журналов](#get-chain-of-custody-logs-after-data-erasure) включая журналы аудита и журналом заказов                |

В этой статье подробно описываются различные механизмы или средств, доступных для отслеживания и аудита заказ Data Box или большой поле данных. Информация в этой статье относится, Data Box и интенсивной поле данных. В последующих разделах ссылки на поле данных применимы и к большой поле данных.

## <a name="set-up-access-control-on-the-order"></a>Настроить контроль доступа на порядок

Вы можете контролировать, кто имеет доступ к вашему заказу, при первом создании заказа. Настройка ролей для управления доступом на основе ролей (RBAC) в различных областях для управления доступом к заказ Data Box. Роль RBAC определяет тип доступа — для чтения и записи, только для чтения, чтения и записи для определенного набора операций.

Ниже приведены две роли, которые могут быть определены для службы Azure Data Box.

- **Модуль чтения данных поле** -имеют доступ только для чтения для заказов в соответствии с определением области действия. Они могут только просматривать сведения о заказе. Не могут получить доступ к любые другие сведения, относящиеся к учетным записям хранения и изменить сведения о заказе, такие как адрес и т. д.
- **Участник поле данных** -можно только создать заказ для передачи данных в указанной учетной записи хранения *если они уже имеют доступ на запись к учетной записи хранения*. Если они не имеют доступа к учетной записи хранения, они даже не удается создать заказ Data Box, чтобы скопировать данные в учетную запись. Эта роль не определяет любой учетной записи хранения связанных с разрешениями, и не предоставляет доступ к учетным записям хранения.  

Чтобы ограничить доступ к заказу, вы можете:

- Назначение роли на уровне порядка. Пользователь должен только эти разрешения, как определено ролями для взаимодействия с этой определенной заказ Data Box только и ничего более.
- Назначение роли на уровне группы ресурсов, пользователь имеет доступ для всех заказов поле данных в группе ресурсов.

Дополнительные сведения о предлагаемых используйте RBAC, см. в разделе [советы и рекомендации по RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Отслеживание заказа

Вы можете отслеживать ваш заказ через портал Azure и доставки веб-сайта перевозчика. Чтобы отследить заказ Data Box в любое время в являются следующие механизмы:

- Для отслеживания порядка, когда устройство находится в центре обработки данных Azure или локальной среде, перейдите к вашей **заказ Data Box > Обзор** на портале Azure.

    ![Просмотр состояния заказа и отслеживания нет](media/data-box-logs/overview-view-status-1.png)

- Для отслеживания порядка при работе устройства во время передачи, посетите веб-регионального поставщика, например, UPS веб-сайта в США. Укажите номер отслеживания, связанного с заказом.
- Data Box также отправляет уведомления по электронной почте каждый раз, когда изменяется состояние заказа в зависимости от сообщений электронной почты, предоставленный при создании заказа. Список всех состояний заказ Data Box, см. в разделе [просматривать состояние заказа](data-box-portal-admin.md#view-order-status). Чтобы изменить параметры уведомлений, связанного с заказом, см. в разделе [изменить сведения об уведомлении](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Журналы действий запроса во время установки

- В поле данных поступает на вашей локальной среды в заблокированном состоянии. Учетные данные устройства, доступные на портале Azure можно использовать для заказа.  

    При настройке Data Box, может потребоваться узнать, кто все получил доступ учетные данные устройства. Чтобы выяснить, кто получил доступ **учетные данные устройства** колонке, можно запросить журналы действий.  Любое действие, которое включает в себя доступ к **сведений об устройстве > учетные данные** колонке регистрируется в журналах действий как `ListCredentials` действие.

    ![Запрос журналов действий](media/data-box-logs/query-activity-log-1.png)

- Каждый вход в Data Box — вошедшего в режиме реального времени. Тем не менее, эта информация доступна только в [журналы аудита](#audit-logs) после успешного завершения порядок.

## <a name="view-error-log-during-data-copy"></a>Просмотр журнала ошибок во время копирования данных

Во время копирования данных для поля данных или высокой поле данных файл ошибок создается, если возникли проблемы с копируемых данных.

### <a name="errorxml-file"></a>Файл Error.XML

Убедитесь, что задания копирования завершено без ошибок. При возникновении ошибок во время копирования, загрузить журналы из **Connect и копирования** страницы.

- Если вы скопировали файл, который находится не выровнены в папку управляемого диска в среде данных 512 байт, файл не отправлен как страничный большой двоичный объект в промежуточную учетную запись хранения. Вы увидите сообщение об ошибке в журналах. Удалите файл и скопируйте файл,который выровнен по размеру 512 байт.
- Если вы скопировали vhdx-ФАЙЛ, или динамического виртуального жесткого диска или разностного виртуального жесткого диска (эти файлы не поддерживаются), вы увидите ошибку в журналах.

Ниже приведен пример *error.xml* для различных ошибок при копировании управляемые диски.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Ниже приведен пример *error.xml* для различных ошибок при копировании в страничных BLOB-объектов.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Ниже приведен пример *error.xml* для различных ошибок при копировании в блочных BLOB-объектов.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Ниже приведен пример *error.xml* для различных ошибок при копировании файлов Azure.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

В каждом из вышеперечисленных случаев устраните ошибки, прежде чем перейти к следующему шагу. Дополнительные сведения об ошибках, полученных во время копирования данных в Data Box через протоколы SMB или NFS см. в статье [Устранение Data Box и поле данных в большой проблемы](data-box-troubleshoot.md). Сведения об ошибках, полученных во время копирования данных в Data Box с помощью REST см. в статье [проблем с хранилищем Устранение поле большой двоичный объект данных](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Проверьте Спецификации во время подготовки к отправке

Во время подготовки к отправке, список файлов, известных как при создании спецификации (Спецификация) или файл манифеста.

- Этот файл используется для проверки фактические имена и количество файлов, которые были скопированы в Data Box.
- Этот файл используется для проверки фактических размерах файлов.
- Убедитесь, что *crc64* соответствующий строке, ненулевое значение. <!--A null value for crc64 indicates that there was a reparse point error)-->

Дополнительные сведения об ошибках, полученные в процессе подготовки отгрузки, перейдите к [Устранение Data Box и поле данных в большой проблемы](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>Метки порядка БАЙТОВ или манифест файла

Отметка BOM или файл манифеста содержит список всех файлов, которые копируются на устройство Data Box. Файл Спецификации имеет имена файлов и соответствующих размеров, а также контрольную сумму. Для блочных, страничных BLOB-объектов, файлов Azure, для копирования с помощью REST API, а также для копирования на управляемые диски на Data Box создается отдельный файл Спецификации. Спецификации файлы можно загрузить из локального пользовательского веб-Интерфейсе устройства во время подготовки к отправке.

Эти файлы также находиться на устройство Data Box и передаются в связанную учетную запись хранения в центре обработки данных Azure.

### <a name="bom-file-format"></a>Спецификации формата файла

Метки порядка БАЙТОВ или манифест файла имеет следующий общий формат:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Ниже приведен пример манифеста создается, когда данные скопированы в блок больших двоичных объектов общий ресурс на Data Box.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

Спецификации или файлы также копируются в учетную запись хранения Azure. Вы можете использовать метки порядка БАЙТОВ или файлов, чтобы убедиться, что файлы, отправленные в Azure соответствуют данным, который был скопирован в поле манифеста.

## <a name="review-copy-log-during-upload-to-azure"></a>Просмотрите журнал копирования во время отправки в Azure

Во время отправки данных в Azure *copylog* создается.

### <a name="copylog"></a>Copylog

Для каждого обрабатываемого заказа Data Box служба создает *copylog* в соответствующей учетной записи. *Copylog* имеет общее число файлов, отправленных и количество файлов, то, завершившиеся сбоем во время их копирования из поля данных в учетную запись хранилища Azure.

Вычисление циклической проверки избыточности (CRC) выполняется при отправке в Azure. CRC из копии данных, и после передачи данных сравниваются. Несовпадение контрольной СУММЫ указывает, что соответствующие файлы не удалось отправить.

По умолчанию журналы записываются в контейнер с именем `copylog`. Журналы хранятся с следующее соглашение об именовании:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Путь copylog отображается также на **Обзор** колонке портала.

![Путь к copylog в колонке "Обзор" после завершения](media/data-box-logs/copy-log-path-1.png)

Приведенный ниже описан общий формат файла copylog для передачи в поле данных, которая завершилась успешно:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Отправить в Azure также может завершиться с ошибками.

![Путь к copylog в колонке "Обзор" при выполнена с ошибками.](media/data-box-logs/copy-log-path-2.png)

Ниже приведен пример copylog место, где отправки завершена с ошибками:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
Ниже приведен пример `copylog` где контейнеры, которые не соответствует соглашениям об именовании Azure были переименованы во время отправки данных в Azure.

Новые уникальные имена для контейнеров, в формате `DataBox-GUID` и данные для контейнера, помещаются в новый файл переименован контейнер. `copylog` Указывает старое и новое имя контейнера для контейнера.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Ниже приведен пример `copylog` где большие двоичные объекты или файлы, которые не соответствует соглашениям об именовании Azure, были переименованы во время отправки данных в Azure. Новый большой двоичный объект или имена файлов преобразуются в хэш-кода SHA256 относительного пути к контейнеру и передаются в путь в зависимости от типа назначения. Назначения может быть блочных, страничных BLOB-объектов или файлов Azure.

`copylog` Указывает старое и новое имя большого двоичного объекта или файла и путь в Azure.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Получить цепочки обеспечения сохранности журналов после стирания данных

После удаления данных данные с дисков Data Box согласно рекомендациям Revision 1 NIST SP 800-88, цепочки обеспечения сохранности журналов доступны. В этих журналах содержатся журналы аудита и История заказа. Отметка BOM или файлы манифеста, также копируются с журналами аудита.

### <a name="audit-logs"></a>Журналы аудита

Журналы аудита, содержат сведения о включении и общий доступ на Data Box или большой поле данных в том случае, когда он находится за пределами центра обработки данных Azure. Эти журналы расположены в каталоге: `storage-account/azuredatabox-chainofcustodylogs`

Ниже приведен пример журнала аудита из поля данных.

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Скачивание журнала заказов

История заказа доступна на портале Azure. Если заказ будет обработан и завершении очистки устройства (стирания данных на основе дисков), а затем перейдите на ваш заказ устройства и перейдите к **Order details**. ** Загрузить историю заказов** доступен. Дополнительные сведения см. в разделе [загрузить историю заказов](data-box-portal-admin.md#download-order-history).

Если прокрутить содержимое История заказа, вы увидите:

- Оператора, данные отслеживания изменений для устройства.
- События с *SecureErase* действия. Эти события соответствуют стирания данных на диске.
- Ссылки журнала поле данных. Пути для *журналы аудита*, *copylogs*, и *Спецификации* файлы будут представлены.

Ниже приведен пример журнала заказа с портала Azure.

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [Устранение неполадок на Data Box и поле данных в большой](data-box-troubleshoot.md).
