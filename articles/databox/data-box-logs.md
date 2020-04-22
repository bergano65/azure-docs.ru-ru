---
title: Отслеживайте и регистрировать поле данных Azure, тяжелые события Azure Data Box. Документы Майкрософт
description: Описывает, как отслеживать и регистрировать события на различных этапах вашего заказа Azure Data Box и Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 74d38af4a64a184b26bd6ba1105db0d2530d8ba6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676411"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Отслеживание и регистрация событий для вашего ящика данных Azure и ящика данных Azure Heavy

Заказ Box или Data Box Heavy проходит следующие действия: заказ, настройка, копия данных, возврат, загрузка в Azure и проверка, а также стирание данных. В соответствии с каждым шагом в заказе, вы можете предпринять несколько действий, чтобы контролировать доступ к заказу, проверять события, отслеживать заказ и интерпретировать различные журналы, которые генерируются.

В следующей таблице приводится сводка шагов заказа Data Box или Data Box Heavy и инструменты, доступные для отслеживания и аудита заказа на каждом этапе.

| Этап заказа коробки данных       | Инструмент для отслеживания и аудита                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Создание заказа               | [Настройка управления доступом в заказе через RBAC](#set-up-access-control-on-the-order)                                                    |
| Заказ обработан            | [Отслеживайте порядок](#track-the-order) через <ul><li> Портал Azure </li><li> Сайт перевозчика груза </li><li>Уведомления по электронной почте</ul> |
| Настройка устройства              | Доступ к учетным данным устройств, зарегистрированный в [журналах activity](#query-activity-logs-during-setup)                                              |
| Копия данных на устройство        | [Просмотр файлов *error.xml* ](#view-error-log-during-data-copy) для копирования данных                                                             |
| Подготовка к отправке            | [Проверка файлов BOM](#inspect-bom-during-prepare-to-ship) или файлов манифеста на устройстве                                      |
| Загрузка данных в Azure       | [Просмотр журналов копий](#review-copy-log-during-upload-to-azure) на данные при загрузке данных в центре обработки данных Azure                         |
| Стирание данных с устройства   | [Просмотр цепочки журналов хранения,](#get-chain-of-custody-logs-after-data-erasure) включая журналы аудита и историю заказов                |

В этой статье подробно описаны различные механизмы или инструменты, доступные для отслеживания и аудита заказа Data Box или Data Box Heavy. Информация в этой статье относится как к Box Data Box, так и к Box Data Heavy. В последующих разделах любые ссылки на Box данных также относятся к Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Настройка управления доступом в заказе

Вы можете контролировать, кто может получить доступ к вашему заказу при первом создании заказа. Настройка ролей управления доступом на основе ролей (RBAC) в различных областях для управления доступом к порядку Data Box. Роль RBAC определяет тип доступа – запись чтения, чтения, чтения, чтения-записи в подмножество операций.

Две роли, которые могут быть определены для службы Azure Data Box:

- **Data Box Reader** - имеют доступ только для чтения к заказу (ы), как это определено областью. Они могут просматривать только детали заказа. Они не могут получить доступ к другим данным, связанным с учетными записями хранения, или отсеять данные заказа, такие как адрес и так далее.
- **Data Box Contributor** - может создать заказ на передачу данных на данный счет хранения, *если у них уже есть доступ к учетной записи хранилища.* Если у них нет доступа к учетной записи хранилища, они даже не могут создать заказ Box для копирования данных в учетную запись. Эта роль не определяет никаких разрешений, связанных с учетной записью хранения, и не предоставляет доступ к учетным записям хранения.  

Чтобы ограничить доступ к заказу, можно:

- Назначать роль на уровне заказа. Пользователь имеет только те разрешения, как это определено ролями для взаимодействия только с этим конкретным заказом Box data Box и больше ничего.
- Назначить роль на уровне группы ресурсов, пользователь имеет доступ ко всем заказам Box данных в группе ресурсов.

Для получения дополнительной информации о [Best practices for Azure RBAC](../role-based-access-control/best-practices.md)предлагаемом использовании RBAC см.

## <a name="track-the-order"></a>Отслеживание заказа

Отслеживание заказа можно через портал Azure и через веб-сайт перевозчика. Для отслеживания заказа Box в любое время существуют следующие механизмы:

- Чтобы отследить порядок, когда устройство находится в центре обработки данных Azure или в вашем помещении, перейдите на **ваш заказ Box > Обзор** на портале Azure.

    ![Просмотр состояния заказа и отслеживания нет](media/data-box-logs/overview-view-status-1.png)

- Чтобы отследить заказ во время транзита устройства, перейдите на региональный сайт перевозчика, например, на веб-сайте UPS в США. Укажите номер отслеживания, связанный с вашим заказом.
- Data Box также отправляет уведомления по электронной почте в любое время изменения статуса заказа на основе электронных писем, предоставленных при создании заказа. Для списка всех статусов заказа Box, [см.](data-box-portal-admin.md#view-order-status) Чтобы изменить параметры уведомлений, связанные с заказом, [см.](data-box-portal-admin.md#edit-notification-details)

## <a name="query-activity-logs-during-setup"></a>Журналы активности запроса во время настройки

- Ваш ящик данных прибывает в ваше помещение в заблокированном состоянии. Для заказа можно использовать учетные данные устройства, доступные на портале Azure.  

    При настройке коробки данных может потребоваться знать, кто получил доступ к учетным данным устройства. Чтобы выяснить, кто получил доступ к **лезвию учетных данных устройства,** можно задать запрос в журналы активности.  Любое действие, вовлекающее к доступу к деталям устройства `ListCredentials` > лезвие **учетных данных,** ввожет в журналы активности как действие.

    ![Запрос журналов действий](media/data-box-logs/query-activity-log-1.png)

- Каждый знак в поле данных регистрируется в режиме реального времени. Однако эта информация доступна только в [журналах аудита](#audit-logs) после успешного завершения заказа.

## <a name="view-error-log-during-data-copy"></a>Просмотр журнала ошибок во время копирования данных

Во время копирования данных в Data Box или Data Box Heavy, файл ошибки генерируется, если есть какие-либо проблемы с копируемыми данными.

### <a name="errorxml-file"></a>Файл Error.xml

Убедитесь, что задания копирования закончились без ошибок. Если в процессе копирования возникают ошибки, скачайте журналы со страницы **Подключение и копирование**.

- Если вы скопировали файл, который не является 512 байтами, выровненных с папкой управляемого диска в коробке данных, файл не загружается в качестве капли страницы в вашу учетную запись хранения. Вы увидите сообщение об ошибке в журналах. Удалите файл и скопируйте файл,который выровнен по размеру 512 байт.
- Если вы скопировали VHDX, или динамический VHD, или различные VHD (эти файлы не поддерживаются), вы увидите ошибку в журналах.

Вот пример *ошибки.xml* для различных ошибок при копировании управляемых дисков.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Вот пример *error.xml* для различных ошибок при копировании на странице капли.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Вот пример *error.xml* для различных ошибок при копировании, чтобы заблокировать капли.

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

Вот пример *ошибки.xml* для различных ошибок при копировании в Файлы Azure.

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

В каждом из вышеперечисленных случаев устраните ошибки, прежде чем перейти к следующему шагу. Для получения дополнительной информации об ошибках, полученных во время копирования данных в Data Box через протоколы SMB или NFS, перейдите на [Troubleshoot Data Box и Data Box Heavy issues.](data-box-troubleshoot.md) Для получения информации об ошибках, полученных во время копирования данных в Data Box через REST, перейдите на [проблемы с хранением DataBox Blob.](data-box-troubleshoot-rest.md)

## <a name="inspect-bom-during-prepare-to-ship"></a>Осмотрите БОМ во время подготовки к отправке

Во время подготовки к отправке создается список файлов, известных как Билль материалов (BOM) или файл манифеста.

- Используйте этот файл для проверки фактических имен и количества файлов, которые были скопированы в box data.
- Используйте этот файл для проверки фактических размеров файлов.
- Убедитесь, что *crc64* соответствует ненулевой строке. <!--A null value for crc64 indicates that there was a reparse point error)-->

Для получения дополнительной информации об ошибках, полученных во время подготовки к отправке, перейдите на [Troubleshoot Data Box и Data Box Heavy вопросов](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>BOM или файл манифеста

Файл BOM или манифест содержит список всех файлов, которые копируются на устройство Data Box. Файл BOM имеет имена файлов и соответствующие размеры, а также чекум. Отдельный файл BOM создается для блоковых капли, капли страниц, файлов Azure, для копирования через APIs REST и для копирования управляемых дисков на коробке данных. Вы можете скачать файлы BOM из локального веб-файла устройства во время подготовки к отправке.

Эти файлы также находятся на устройстве Box данных и загружаются в связанную учетную запись хранения в центре обработки данных Azure.

### <a name="bom-file-format"></a>Формат файла BOM

BOM или манифест файл имеет следующий общий формат:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Вот пример манифеста, генерируемого при копировании данных на долю блока капли на коробке данных.

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

Файлы BOM или манифеста также копируются в учетную запись хранения Azure. Можно использовать файлы BOM или манифестдляного проявления для проверки того, что файлы, загруженные в Azure, соответствуют данным, скопированным в box data.

## <a name="review-copy-log-during-upload-to-azure"></a>Просмотр журнала копий во время загрузки в Azure

Во время загрузки данных в Azure создается журнал копий.

### <a name="copy-log"></a>Копирование журнала

Для каждого обработанного заказа служба Data Box создает журнал копий в связанной учетной записи хранилища. В журнале копий имеется общее количество загруженных файлов и количество файлов, которые были поошибке в ходе копии данных из коробки данных в учетную запись хранения Azure.

Вычисление Cyclic Redundancy Check (CRC) выполняется во время загрузки в Azure. CrCs из копии данных и после загрузки данных сравниваются. Несоответствие CRC указывает на то, что соответствующие файлы не были загружены.

По умолчанию журналы записываются в контейнер `copylog`. Бревна хранятся со следующей конвенцией именования:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Путь журнала копирования также отображается на лезвии **Обзора** для портала.

![Путь к копированию входа в лезвие обзора после завершения](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Загрузка завершена успешно 

Следующий пример описывает общий формат журнала копий для загрузки коробки данных, которая успешно завершена:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Загрузка завершена с ошибками 

Загрузка в Azure также может быть завершена ошибками.

![Путь к копированию входа в лезвие Обзора при заполнении с ошибками](media/data-box-logs/copy-log-path-2.png)

Вот пример журнала копий, где загрузка завершена с ошибками:

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
### <a name="upload-completed-with-warnings"></a>Загрузка завершена с предупреждениями

Загрузка в Azure завершается предупреждениями, если в ваших данных были имена контейнеров/blob/file, которые не соответствовали соглашениям о присвоении имен Azure, а имена были изменены для загрузки данных в Azure.

![Путь к копированию входа в лезвие Обзора при заполнении с предупреждениями](media/data-box-logs/copy-log-path-3.png)

Вот пример журнала копий, где контейнеры, не соответствующие конвенциям о присвоении имен azure, были переименованы во время загрузки данных в Azure.

Новые уникальные названия контейнеров `DataBox-GUID` находятся в формате, а данные для контейнера помещаются в новый переименованный контейнер. В журнале копий указывается старое и новое название контейнера для контейнера.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Вот пример журнала копий, где капли или файлы, не соответствующие конвенциям о присвоении имен azure, были переименованы во время загрузки данных в Azure. Новые имена капли или файла преобразуются в дайджест SHA256 относительного пути к контейнеру и загружаются в путь в зависимости от типа назначения. Пунктом назначения могут быть блокировки капли, капли страниц или файлы Azure.

В `copylog` спецификации указаны старые и новые капли или имя файла и путь в Azure.

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

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Получить цепочку журналов опеки после стирания данных

После того, как данные удалены из дисков Data Box в рамках руководящих принципов NIST SP 800-88 Revision 1, доступна цепочка журналов хранения данных. Эти журналы включают журналы аудита и историю заказов. Файлы BOM или манифеста также копируются с журналами аудита.

### <a name="audit-logs"></a>Журналы аудита

В журналах аудита содержится информация о том, как ввести питание и получить доступ к акциям в Box или Data Box Heavy, когда он находится за пределами центра обработки данных Azure. Эти журналы расположены по адресу:`storage-account/azuredatabox-chainofcustodylogs`

Вот пример журнала аудита из коробки данных:

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

История заказов доступна на портале Azure. Если заказ завершен и очистка устройства (стирание данных с дисков) завершена, перейдите к заказу устройства и перейдите к **деталям заказа.** Доступен параметр **Скачать журнал заказов**. Для получения дополнительной информации [см.](data-box-portal-admin.md#download-order-history)

Если вы прокрутите историю заказов, вы увидите:

- Информация о отслеживании носителя для вашего устройства.
- События с активностью *SecureErase.* Эти события соответствуют стиранию данных на диске.
- Ссылки на журнал Data Box. Представлены пути для *журналов аудита,* *журналов копий*и файлов *BOM.*

Вот пример журнала истории заказов с портала Azure:

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

## <a name="next-steps"></a>Следующие шаги

- Узнайте, как [проблемы с устранением неполадок в коробке данных и коробке данных Heavy.](data-box-troubleshoot.md)
