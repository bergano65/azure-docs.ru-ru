---
title: Сбор событий потоковой передачи с помощью портала Azure — Центры событий Azure | Документация Майкрософт
description: В этой статье объясняется, как на портале Azure включить сбор событий потоковой передачи из Центров событий Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 18dea8e8e4cfcabec63e35dd302f6a5259721a8a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320313"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Включение сбора событий, передающихся потоком из Центров событий Azure

[Запись концентраторов событий][capture-overview] Azure позволяет автоматически доставлять потоковые данные в концентраторах событий в [хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/) или [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) выбранной учетной записи.

Запись можно настроить при создании концентратора событий с помощью [портала Azure](https://portal.azure.com). Данные можно записывать в контейнер [хранилища BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/) или в учетную запись [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Дополнительные сведения см. в разделе [Общие сведения о записи концентраторов событий][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Запись данных в учетную запись службы хранилища Azure  

Запись можно включить при создании концентратора событий. Для этого на экране **Создание концентратора событий** на портале нажмите кнопку **Вкл**. Затем укажите учетную запись хранения и контейнер, выбрав **Служба хранилища Azure** в поле **поставщика записи**. Так как для функции "Сбор" в Центрах событий и хранилища используется проверка подлинности со взаимодействием между службами, указывать строку подключения к хранилищу не нужно. Средство выбора ресурсов автоматически выбирает универсальный код ресурса (URI) для вашей учетной записи хранения. При использовании Azure Resource Manager этот универсальный код ресурса необходимо явным образом указать как строку.

Продолжительность времени окна по умолчанию составляет 5 минут. Минимальное значение равно 1, а максимальное — 15. Диапазон **окна размера** составляет 10–500 МБ.

![Временное окно для сбора][1]

> [!NOTE]
> Вы можете включить или отключить создание пустых файлов за время окна сбора данных, когда события отсутствуют. 

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Запись данных в учетную запись Azure Data Lake Store

Для записи данных в Azure Data Lake Store создайте учетную запись Data Lake Store и концентратор событий.

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Создание учетной записи и папок Azure Data Lake Store


1. Создайте учетную запись Data Lake Store, следуя инструкциям в статье [Начало работы с Azure Data Lake Store с помощью портала Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Следуйте инструкциям по [назначению разрешений для Центров событий](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs), чтобы создать папку в рамках учетной записи Data Lake Store, в которой нужно собирать данные из концентраторов событий. Затем назначьте разрешения для Центров событий, чтобы разрешить запись данных в вашу учетную запись Data Lake Store.  


### <a name="create-an-event-hub"></a>Создание концентратора событий

1. Обратите внимание, что концентратор событий должен находиться в той же подписке Azure, что и только что созданная учетная запись Azure Data Lake Store. Создайте концентратор событий, нажав кнопку **Вкл.** в разделе **Запись** на странице портала **Создание концентратора событий**. 
2. На странице портала **Создание концентратора событий** в поле **поставщика записи** выберите **Azure Data Lake Store**.
3. В поле **Выберите Data Lake Store** укажите учетную запись Data Lake Store, созданную ранее, а в поле **Data Lake Path** (Путь к Data Lake) введите путь к созданной папке данных.

    ![Выбор учетной записи Data Lake Storage][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Добавление или настройка функции записи для существующего концентратора событий

Функцию "Сбор" можно настроить в существующих концентраторах событий, расположенных в пространствах имен Центров событий. Чтобы включить запись в существующем концентраторе событий или изменить параметры записи, щелкните пространство имен. Появится экран обзора. Затем щелкните концентратор событий, для которого необходимо включить запись или изменить ее параметры. Наконец, на открывшейся странице слева щелкните параметр **записи** и измените параметры записи, как показано на следующих снимках экрана.

### <a name="azure-blob-storage"></a>Хранилище BLOB-объектов Azure

![Настройка хранилища BLOB-объектов Azure][2]

### <a name="azure-data-lake-store"></a>Хранилище озера данных Azure

![Настройка Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о записи концентраторов событий см. в [обзоре записи концентраторов событий][capture-overview].
- Функцию "Сбор" в Центрах событий можно также настроить с помощью шаблонов Azure Resource Manager. См. дополнительные сведения [о включении записи с помощью шаблона Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Узнайте, как создать подписку на Сетку событий Azure с пространством имен Центров событий в качестве источника](store-captured-data-data-warehouse.md)
- [Начало работы с Azure Data Lake Store с помощью портала Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
