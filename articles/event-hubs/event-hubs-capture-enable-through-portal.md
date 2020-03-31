---
title: Концентраторы событий - Захват потоковых событий с помощью портала Azure
description: В этой статье объясняется, как на портале Azure включить сбор событий потоковой передачи из Центров событий Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 8a6d9456b00e5520e6f4fbb9ccb77b0260731ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187424"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Включение сбора событий, передающихся потоком из Центров событий Azure

Захват [концентратов событий][capture-overview] Azure позволяет автоматически передавать потоковые данные в концентрах событий в [хранилище Azure Blob](https://azure.microsoft.com/services/storage/blobs/) или учетную запись [Azure Data Lake Storage Gen1 или Gen 2](https://azure.microsoft.com/services/data-lake-store/) по вашему выбору.

Запись можно настроить при создании концентратора событий с помощью [портала Azure](https://portal.azure.com). Можно зафиксировать данные в хранилище Azure [Blob,](https://azure.microsoft.com/services/storage/blobs/) либо в учетную запись [Azure Data Lake Storage Gen 1 или Gen 2.](https://azure.microsoft.com/services/data-lake-store/)

Подробнее см. в статье [Общие сведения о функции "Сбор" в Центрах событий Azure][capture-overview].

## <a name="capture-data-to-azure-storage"></a>Захват данных в хранилище Azure

Запись можно включить при создании концентратора событий. Для этого на экране **Создание концентратора событий** на портале нажмите кнопку **Вкл**. Затем укажите учетную запись хранения и контейнер, выбрав **Служба хранилища Azure** в поле **поставщика записи**. Так как для функции "Сбор" в Центрах событий и хранилища используется проверка подлинности со взаимодействием между службами, указывать строку подключения к хранилищу не нужно. Средство выбора ресурсов автоматически выбирает универсальный код ресурса (URI) для вашей учетной записи хранения. При использовании Azure Resource Manager этот универсальный код ресурса необходимо явным образом указать как строку.

Продолжительность времени окна по умолчанию составляет 5 минут. Минимальное значение равно 1, а максимальное — 15. Диапазон **окна размера** составляет 10–500 МБ.

![Временное окно для сбора][1]

> [!NOTE]
> Вы можете включить или отключить создание пустых файлов за время окна сбора данных, когда события отсутствуют. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Захват данных в Azure Data Lake Storage Gen 2 

1. Следуйте [за статьей «Создайте учетную запись хранения»](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) для создания учетной записи хранилища Azure. Установите **иерархическое пространство имен** для **включения** на вкладке **Advanced,** чтобы сделать его учетной записью Azure Data Lake Storage Gen 2.
2. При создании концентратора событий сделайте следующие действия: 

    1. Выберите **для** **захвата**. 
    2. Выберите **хранилище Azure** в качестве поставщика захвата. Опция **Azure Data Lake Store,** которая просматривается для **поставщика захвата,** предназначена для Gen 1 хранилища озер лазурных данных. Для использования Gen 2 хранилища озер данных Azure вы можете выбрать **хранилище Azure.**
    2. Выберите кнопку **«Выберите контейнер».** 

        ![Включить захват данных Хранения озера Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Выберите учетную запись **Azure Data Lake Storage Gen 2** из списка. 

    ![Выберите хранилище озер данных Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Выберите **контейнер** (файловая система в Data Lake Storage Gen 2).

    ![Выберите файловую систему в хранилище](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. На странице **«Создать концентратор событий»** выберите **«Создать».** 

    ![Выберите кнопку «Создание»](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Контейнер, создаваемый в Azure Data Lake Storage Gen 2 с помощью этого пользовательского интерфейса (UI), отображается в **системах файлов** в **Storage Explorer.** Аналогичным образом, файловая система, создаваемый в учетной записи Data Lake Storage Gen 2, отображается в качестве контейнера в этом файле связи. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Захват данных в Azure Data Lake Storage Gen 1 

Для сбора данных в Azure Data Lake Storage Gen 1 создается учетная запись Data Lake Storage Gen 1 и концентратор событий:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Создание учетной записи и папок Azure Data Lake Storage Gen 1

1. Создайте учетную запись хранения озер данных, следуя инструкциям в [Get started с Azure Data Lake Storage Gen 1 с помощью портала Azure.](../data-lake-store/data-lake-store-get-started-portal.md)
2. Следуйте инструкциям в разделе [Присваивают разрешения](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) в раздел Event Hubs для создания папки в учетной записи Data Lake Storage Gen 1, в которой требуется захватить данные из концентраторов событий, и назначить разрешения к концентрам событий, чтобы можно было записывать данные в учетную запись Data Lake Storage Gen 1.  


### <a name="create-an-event-hub"></a>Создание концентратора событий

1. Концентратор событий должен находиться в той же подписке Azure, что и созданная вами учетная запись Azure Data Lake Storage Gen 1. Создайте концентратор событий, нажав кнопку **Вкл.** в разделе **Запись** на странице портала **Создание концентратора событий**. 
2. На странице портала **Создание концентратора событий** в поле **поставщика записи** выберите **Azure Data Lake Store**.
3. В **Select Store** рядом со списком выпадающих **данных Магазина озер** укажите созданную ранее учетную запись Data Lake Storage Gen 1, а в поле **«Путь озера данных»** введите путь к созданной папке данных.

    ![Выбор учетной записи Data Lake Storage][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Добавление или настройка функции записи для существующего концентратора событий

Функцию "Сбор" можно настроить в существующих концентраторах событий, расположенных в пространствах имен Центров событий. Чтобы включить запись в существующем концентраторе событий или изменить параметры записи, щелкните пространство имен. Появится экран обзора. Затем щелкните концентратор событий, для которого необходимо включить запись или изменить ее параметры. Наконец, на открывшейся странице слева щелкните параметр **записи** и измените параметры записи, как показано на следующих снимках экрана.

### <a name="azure-blob-storage"></a>хранилище BLOB-объектов Azure

![Настройка хранилища BLOB-объектов Azure][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage 2-го поколения

![Настройка Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Лазурное хранилище озер данных Gen 1 

![Настройка Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о функции "Сбор" в Центрах событий см. в [этой статье][capture-overview].
- Функцию "Сбор" в Центрах событий можно также настроить с помощью шаблонов Azure Resource Manager. См. дополнительные сведения [о включении записи с помощью шаблона Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Узнайте, как создать подписку на Сетку событий Azure с пространством имен Центров событий в качестве источника](store-captured-data-data-warehouse.md)
- [Начало работы с Azure Data Lake Store с помощью портала Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
