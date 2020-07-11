---
title: Руководство по экспорту данных из Azure Data Box | Документация Майкрософт
description: Ознакомьтесь с предварительными требованиями к развертыванию и экспортом данных из Azure Data Box
services: databox
author: priestlg
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 07/10/2020
ms.author: v-grpr
ms.openlocfilehash: 5b17488ebab8dc29221723fabf8efe0c00467509
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209712"
---
# <a name="tutorial-create-export-order-for-azure-data-box-preview"></a>Учебник. создание порядка экспорта для Azure Data Box (Предварительная версия)

Azure Data Box — это гибридное решение, которое позволяет перемещать данные из Azure в свое расположение. В этом учебнике описано, как создать порядок экспорта для Azure Data Box. Основная причина создания порядка экспорта — для аварийного восстановления, в случае компрометации локального хранилища и необходимости восстановления резервной копии.

В этом руководстве вы рассмотрите следующее:

> [!div class="checklist"]
>
> * Необходимые условия для экспорта
> * Упорядочивание Data Box для экспорта
> * Отслеживание порядка экспорта
> * Отменить порядок экспорта

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Обязательные условия

Перед тем как заказать устройство, выполните следующие необходимые условия для настройки службы Data Box и устройства.

### <a name="for-service"></a>Для службы

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Убедитесь, что у вас есть группа ресурсов, которую можно использовать с Azure Data Box.

* Убедитесь, что учетная запись хранения Azure, из которой вы хотите экспортировать данные, является одним из поддерживаемых типов учетных записей хранения, как описано в разделе [Поддерживаемые учетные записи хранения для Data Box](data-box-system-requirements.md#supported-storage-accounts).

### <a name="for-device"></a>Для устройств

Перед тем как начать, убедитесь в следующем.

* Главный компьютер должен быть присоединен к сети центра обработки данных. Данные из Azure Data Box будут скопированы на этот компьютер. Главный компьютер должен запускать поддерживаемую операционную систему, как описано в разделе [Требования к системе Azure Data Box](data-box-system-requirements.md).

* Центр обработки данных должен иметь высокоскоростную сеть. Настоятельно рекомендуем использовать хотя бы одно соединение Ethernet со скоростью передачи данных 10 Гбит/с. Если 10-гигабитное подключение недоступно, можно использовать канал данных 1-GbE, однако скорость копирования затронута.

## <a name="order-data-box-for-export"></a>Data Box заказа для экспорта

Чтобы заказать устройство на портале Azure, выполните следующие шаги.

1. Используйте учетные данные Microsoft Azure для входа по этому URL-адресу: [https://portal.azure.com](https://portal.azure.com).

2. Выберите **+Создать ресурс** и выполните поиск по фразе *Azure Data Box*. Выберите **Azure Data Box**.

   ![Создайте ресурс](media/data-box-deploy-export-ordered/azure-data-box-export-00b.png)

3. Нажмите кнопку **Создать**.

   ![Создание Azure Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-00c.png)

4. Проверьте, доступна ли служба Azure Data Box в вашем регионе. Введите или выберите следующие сведения и нажмите кнопку **Применить**.

    |Параметр  |Значение  |
    |---------|---------|
    |Тип передачи     | Выберите **Экспорт в Azure**.        |
    |Подписка     | Выберите для использования службы Data Box подписку EA, CSP или "Спонсорское предложение Azure". <br> Подписка привязана к учетной записи для выставления счетов.       |
    |Группа ресурсов     |    Выберите существующую группу ресурсов. <br> Группа ресурсов — это логический контейнер для ресурсов, которые могут управляться или развертываться вместе.         |
    |Исходный регион Azure    |    Выберите регион Azure, в котором находятся ваши данные.         |
    |Страна назначения     |     Выберите страну, в которой вы хотите поставлять устройство.        |

   ![Выберите параметры Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-01.png)

5. Выберите **Data Box**. Максимальная полезная емкость для одного заказа составляет 80 ТБ. Для больших размеров данных можно создать несколько заказов.

   ![Выбор емкости Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-02b.png)

6. По **порядку**укажите **основные** сведения о заказе. Введите или выберите следующие сведения и нажмите кнопку **Далее**.

    |Параметр  |Значение  |
    |---------|---------|
    |Подписка     | Подписка автоматически заполняется в соответствии с предыдущим выбором.|
    |Группа ресурсов | Выбранная ранее группа ресурсов. |
    |Имя заказа на экспорт     |  Введите понятное имя для отслеживания заказа. <br> Имя может содержать от 3 до 24 знаков, среди которых могут быть буквы, цифры и дефисы. <br> Имя должно начинаться и заканчиваться буквой или цифрой.      |

    ![Основы порядка экспорта](media/data-box-deploy-export-ordered/azure-data-box-export-03.png)

    Выберите **Далее: выбор данных** для продолжения.

7. В **области Выбор данных**выберите **Добавить учетную запись хранения и тип экспорта**.

    ![Добавление учетной записи хранения и типа экспорта](media/data-box-deploy-export-ordered/azure-data-box-export-03b.png)

8. В окне **Выбор параметра экспорта**укажите сведения о параметре экспорта. Введите или выберите следующие сведения и нажмите кнопку **Добавить**.

    |Параметр  |Значение  |
    |---------|---------|
    |Учетная запись хранения     | Учетная запись хранения Azure, из которой необходимо экспортировать данные. |
    |Тип экспорта     | Указывает тип данных для экспорта из **всех объектов** и **использования XML-файла**.<ul><li> **Все объекты** — указывает, что задание экспортирует все данные в зависимости от выбранных **параметров перемещения**.</li><li> **Использовать XML-файл** — указывает XML-файл, содержащий набор путей и префиксов для больших двоичных объектов и файлов, экспортируемых из учетной записи хранения. XML-файл должен находиться в контейнере выбранной учетной записи хранения, и выбор из общих файловых ресурсов в настоящее время не поддерживается. Файл должен быть непустым XML-файлом.</li></ul>        |
    |Параметры перемещения     |  Задает параметры перемещения данных из области **выбрать все**, **все большие двоичные объекты**и **все файлы**. <ul><li> **Выбрать все** — указывает, что экспортируются все большие двоичные объекты и файлы Azure. Если вы используете учетную запись хранения, которая поддерживает только большие двоичные объекты (учетная запись хранилища BLOB-объектов), параметр " **все файлы** " не будет выбран.</li><li> **Все большие двоичные объекты** — указывает, что экспортируются только блочные и страничные BLOB-объекты.</li><li> **Все файлы** — указывает, что экспортируются все файлы, кроме больших двоичных объектов. Тип данных, которые можно экспортировать, определяется типом учетной записи хранения (GPv1 и GPv2, хранилище класса Premium или хранилище BLOB-объектов). Дополнительные сведения см. в разделе [Поддерживаемые учетные записи хранения для экспорта](../storage/common/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Включить подробный журнал     | Указывает, нужен ли подробный файл журнала, содержащий список всех файлов, которые были успешно экспортированы.        |

    > [!NOTE]
    >
    > Если для параметра **тип экспорта** выбрать параметр **использовать XML-файл** , необходимо убедиться в том, что XML содержит допустимые пути и (или) префиксы. Необходимо создать и указать XML-файл.  Если файл является недопустимым или не совпадает с указанными путями, порядок завершается частичными данными или данные не экспортируются.

    Чтобы узнать, как добавить XML-файл в контейнер, см. статью [Экспорт порядка с помощью XML-файла](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Выбор параметра экспорта](media/data-box-deploy-export-ordered/azure-data-box-export-04b.png)

   Пример входных данных XML см. в разделе [Пример входных данных XML](data-box-deploy-export-ordered.md#sample-xml-file) .

9. В **области Выбор данных**проверьте параметры и нажмите кнопку **Далее: контактная информация>**.

   ![Контактные сведения](media/data-box-deploy-export-ordered/azure-data-box-export-05.png)

10. В **сведениях о контакте**выберите **+ Добавить адрес доставки** , чтобы ввести сведения о доставке.

    ![Добавить адрес доставки](media/data-box-deploy-export-ordered/azure-data-box-export-06.png)

11. В поле **Добавить адрес доставки**укажите имя и фамилию, имя и почтовый адрес компании, а также допустимый номер телефона. Выберите **Проверить**. Данная служба проверяет адрес доставки на доступность услуг. Если служба доступна для указанного адреса доставки, вы получите соответствующее уведомление.

    ![Проверка адреса доставки](media/data-box-deploy-export-ordered/azure-data-box-export-07.png)

    При упорядочении в регионе, где доступна самостоятельно управляемая доставка, можно выбрать этот параметр. Дополнительные сведения см. в статье [об использовании самостоятельной доставки](data-box-portal-customer-managed-shipping.md).

12. Выберите **Добавить адрес доставки** после того, как сведения о доставке успешно проверены.

13. В окне **контактные данные**проверьте адрес доставки и адрес электронной почты. Служба отправляет уведомления по электронной почте об обновлениях состояния заказа на указанные адреса электронной почты.

    Рекомендуется использовать групповой адрес электронной почты, чтобы продолжить получать уведомления после ухода администратора группы.

    ![Сведения о заказах](media/data-box-deploy-export-ordered/azure-data-box-export-09.png)

14. Выберите **Далее: Проверка + порядок>**. Чтобы продолжить создание заказа, необходимо принять условия.

15. Выберите **Заказ**. Создание заказа может занять несколько минут.

    ![Порядок фиксации](media/data-box-deploy-export-ordered/azure-data-box-export-10.png)

## <a name="export-order-using-xml-file"></a>Порядок экспорта с использованием XML-файла

Если выбран параметр **использовать XML-файл**, можно указать конкретные контейнеры и большие двоичные объекты (страницу и блок), которые требуется экспортировать. Для форматирования XML необходимо следовать спецификациям [таблицы XML-файлов](#sample-xml-file) . Ниже описано, как использовать XML-файл для экспорта данных.

1. Для **типа экспорта**выберите **использовать XML-файл**. Это ваш XML-файл, указывающий определенные большие двоичные объекты и файлы Azure, которые вы хотите экспортировать. Чтобы добавить XML-файл, выберите **пункт Щелкните здесь, чтобы выбрать XML-файл**.
     ![XML-файл](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-01.png)

2. Выберите **+ контейнер** , чтобы создать контейнер.
    ![XML-файл](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-02.png)

3. На вкладке **новый контейнер** , которая выводится с правой стороны портал Azure, добавьте имя для контейнера. Имя должно быть в нижнем регистре и содержать цифры и дефисы "-". Затем в раскрывающемся списке выберите **уровень "открытый доступ** ". Рекомендуется выбрать **частный (не анонимный) доступ** , чтобы запретить другим пользователям доступ к данным. Дополнительные сведения об уровнях доступа к контейнеру см. в разделе [разрешения на доступ к контейнеру](../storage/blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

   ![XML-файл](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-04.png)

4. Нажмите кнопку **Создать**.

   ![XML-файл](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-07.png)

   Если контейнер создан успешно, вы получите следующее сообщение:

   ![XML-файл](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-09.png)

5. Выберите созданный контейнер и дважды щелкните его.

   ![XML-файл](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-08.png)

6. Если дважды щелкнуть контейнер, откроется представление свойств контейнера. Теперь нужно присоединить (или перейти к) XML-файл, содержащий список больших двоичных объектов и (или) файлов Azure, которые вы хотите экспортировать. Щелкните **Отправить**.

   ![XML-файл](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-10c.png)

7. Вы успешно добавили XML-файл в контейнер. Будут экспортированы только большие двоичные объекты и файлы Azure, указанные в этом XML.

   ![XML-файл](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-12.png)

## <a name="track-the-order"></a>Отслеживание заказа

После размещения заказа можно отслеживать его состояние на портале Azure. Перейдите к заказу Data Box, а затем щелкните **Обзор** для просмотра состояния. Заказ на портале отображается в состоянии **Заказано**.

После завершения подготовки устройства копирование данных начнется из выбранных учетных записей хранения. На портале отображается порядковый номер **копирования данных в состоянии выполняется** .

![Порядок экспорта Data Box обработан](media/data-box-deploy-export-ordered/azure-data-box-export-15b.png)

Data Box копирует данные из исходных учетных записей хранения. После завершения копирования данных Data Box блокируется, и на портале отобразится заказ **Копирование завершено** .

![Копирование Data Box экспорт данных завершено](media/data-box-deploy-export-ordered/azure-data-box-export-15c.png)

Если устройство недоступно, вы получите уведомление. Если устройство доступно, корпорация Майкрософт определяет устройство для поставки и подготавливает отгрузку. Во время подготовки устройства выполняются следующие действия.

* Для каждой учетной записи хранилища создаются общие ресурсы SMB, связанные с устройством.
* Для каждого общего ресурса создаются учетные данные доступа, такие как имя пользователя и пароль.
* Устройство заблокировано, доступ к нему можно получить только с помощью пароля разблокировки устройства. Чтобы получить пароль, необходимо войти в учетную запись портал Azure и выбрать **сведения об устройстве**.

Затем корпорация Майкрософт подготавливает и отправляет устройства с помощью регионального перевозчика. Когда устройство будет отправлено, вы получите номер для отслеживания. На портале отобразится состояние заказа — **Отправлено**.

![Data Box заказ на экспорт отправлен](media/data-box-deploy-export-ordered/azure-data-box-export-16.png)

Если выбрано самостоятельное отгрузка, вы получите уведомление по электронной почте о дальнейших действиях, когда устройство будет готово к отправке из центра обработки данных. Дополнительные сведения о самостоятельной отгрузке см. в разделе [Самостоятельное](data-box-portal-customer-managed-shipping.md)Управление доставкой.

![Самостоятельно управляемая доставка, готовая к отправке](media/data-box-deploy-export-ordered/azure-data-box-export-17.png)

## <a name="cancel-the-order"></a>Отмена заказа

Чтобы отменить этот порядок, в портал Azure перейдите в раздел **Обзор** и нажмите **кнопку Отмена** на панели команд.

После размещения заказа его можно отменить в любой момент до начала обработки заказа.

Чтобы удалить отмененный заказ, перейдите к разделу **Обзор** и выберите пункт **Удалить** на панели команд.

## <a name="sample-xml-file"></a>Пример XML-файла

В следующем коде XML показан пример имен больших двоичных объектов, префиксов BLOB-объектов и файлов Azure, содержащихся в формате XML, который используется при выборе параметра **использовать XML-файл** :

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Некоторые важные моменты в отношении XML-файлов:

* XML-теги чувствительны к регистру и должны точно совпадать, как указано в приведенном выше примере.
* Открывающие и закрывающие теги должны совпадать.
* Неверные XML-теги или форматирование могут привести к сбою экспорта данных.
* При недопустимых префиксах BLOB-объектов и (или) файлов данные не будут экспортированы.

### <a name="examples-of-valid-blob-paths"></a>Примеры допустимых путей к большим двоичным объектам

В следующей таблице приведены примеры допустимых путей к большим двоичным объектам.

   | Выбор | Путь к BLOB-объекту | Описание |
   | --- | --- | --- |
   | Начинается с |/ |Экспорт всех blob-объектов в учетной записи хранения |
   | Начинается с |/$root/ |Экспорт всех blob-объектов в корневом контейнере |
   | Начинается с |/контаинерс |Экспортирует все большие двоичные объекты в контейнере, который начинается с **контейнеров** префиксов. |
   | Начинается с |/контаинер-наме/ |Экспортирует все большие двоичные объекты в **контейнере контейнера — имя** |
   | Начинается с |/контаинер-наме/префикс |Экспортирует все большие двоичные объекты в **контейнере контейнера — имя** , начинающееся **с префикса префикса** |
   | Равно |$root/logo.bmp |Экспортирует blob-объект **logo.bmp** в корневом контейнере |
   | Равно |8tbpageblob/mydata.txt |Экспортирует **mydata.txt** больших двоичных объектов в контейнере **8tbpageblob** |

## <a name="sample-log-files"></a>Примеры файлов журналов

В этом разделе приводятся примеры файлов журналов, создаваемых во время экспорта. Журналы ошибок создаются автоматически. Чтобы создать файл подробного журнала, необходимо выбрать параметр **включить подробный журнал** в портал Azure при настройке порядка экспорта.
Дополнительные сведения о журналах копирования и подробных сведений см. в статье [копирование журналов](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве были освещены следующие темы относительно Azure Data Box.

> [!div class="checklist"]
>
> * Необходимые условия для экспорта
> * Упорядочивание Data Box для экспорта
> * Отслеживание порядка экспорта
> * Отменить порядок экспорта

Перейдите к следующему руководству, чтобы узнать, как настроить Data Box.

> [!div class="nextstepaction"]
> [Руководство. Распаковка, подключение и разблокировка диска Azure Data Box](./data-box-deploy-set-up.md)
