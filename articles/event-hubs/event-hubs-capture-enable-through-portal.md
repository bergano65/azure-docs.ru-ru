---
title: Концентраторы событий — запись потоковых событий с помощью портал Azure
description: В этой статье объясняется, как на портале Azure включить сбор событий потоковой передачи из Центров событий Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 2381bfa627d00a78ed91af0ba81579588ee016ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613583"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Включение сбора событий, передающихся потоком из Центров событий Azure

[Запись концентраторов событий][capture-overview] Azure позволяет автоматически доставлять потоковые данные из концентраторов событий в [хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/) или [Azure Data Lake Storage 1-го поколения или Gen 2](https://azure.microsoft.com/services/data-lake-store/) по своему усмотрению.

Запись можно настроить при создании концентратора событий с помощью [портала Azure](https://portal.azure.com). Можно либо записать данные в контейнер [хранилища BLOB-объектов](https://azure.microsoft.com/services/storage/blobs/) Azure, либо в учетную запись [Azure Data Lake Storage Gen 1 или Gen 2](https://azure.microsoft.com/services/data-lake-store/) .

Подробнее см. в статье [Общие сведения о функции "Сбор" в Центрах событий Azure][capture-overview].

> [!IMPORTANT]
> Целевая учетная запись хранилища (служба хранилища Azure или Azure Data Lake Storage) должна находиться в той же подписке, что и концентратор событий.

## <a name="capture-data-to-azure-storage"></a>Запись данных в службу хранилища Azure

Запись можно включить при создании концентратора событий. Для этого на экране **Создание концентратора событий** на портале нажмите кнопку **Вкл**. Затем укажите учетную запись хранения и контейнер, выбрав **Служба хранилища Azure** в поле **поставщика записи**. Так как для функции "Сбор" в Центрах событий и хранилища используется проверка подлинности со взаимодействием между службами, указывать строку подключения к хранилищу не нужно. Средство выбора ресурсов автоматически выбирает универсальный код ресурса (URI) для вашей учетной записи хранения. При использовании Azure Resource Manager этот универсальный код ресурса необходимо явным образом указать как строку.

Продолжительность времени окна по умолчанию составляет 5 минут. Минимальное значение равно 1, а максимальное — 15. Диапазон **окна размера** составляет 10–500 МБ.

![Временное окно для сбора][1]

> [!NOTE]
> Вы можете включить или отключить создание пустых файлов за время окна сбора данных, когда события отсутствуют. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Запись данных в Azure Data Lake Storage Gen 2 

1. Чтобы создать учетную запись хранения Azure, следуйте указаниям в статье [Создание учетной записи хранения](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) . Установите для параметра **Иерархическое пространство имен** значение **включено** на вкладке **Дополнительно** , чтобы сделать его учетной записью Azure Data Lake Storage Gen 2.
2. При создании концентратора событий выполните следующие действия. 

    1. Выберите **включить** для **записи**. 
    2. Выберите **хранилище Azure** в качестве поставщика записи. Параметр **Azure Data Lake Store** , отображаемый для **поставщика записи** , предназначен для поколения 1 Azure Data Lake Storage. Чтобы использовать Gen 2 Azure Data Lake Storage, выберите **хранилище Azure**.
    2. Нажмите кнопку **выбрать контейнер** . 

        ![Включение захвата для Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Выберите учетную запись **Azure Data Lake Storage Gen 2** из списка. 

    ![Выбор Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Выберите **контейнер** (файловая система в Data Lake Storage Gen 2).

    ![Выбор файловой системы в хранилище](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. На странице **Создание концентратора событий** выберите **создать**. 

    ![Нажмите кнопку Создать.](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Контейнер, созданный в Azure Data Lake Storage Gen 2 с помощью этого пользовательского интерфейса, отображается в разделе **файловые системы** в **Обозреватель службы хранилища**. Аналогичным образом, файловая система, созданная в учетной записи Data Lake Storage Gen 2, отображается как контейнер в этом пользовательском интерфейсе. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Запись данных в Azure Data Lake Storage Gen 1 

Чтобы записать данные в Azure Data Lake Storage Gen 1, создайте учетную запись Data Lake Storage Gen 1 и концентратор событий.

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Создание учетной записи и папок Azure Data Lake Storage поколения 1

1. Создайте учетную запись Data Lake Storage, следуя инструкциям в статье [Приступая к работе с Azure Data Lake Storage Gen 1 с помощью портал Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Следуйте инструкциям в разделе [Назначение разрешений в концентраторы событий](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) , чтобы создать папку в учетной записи Data Lake Storage Gen 1, в которой необходимо записать данные из концентраторов событий, а также назначить разрешения концентраторам событий, чтобы они могли записывать данные в учетную запись Data Lake Storage Gen 1.  


### <a name="create-an-event-hub"></a>Создание концентратора событий

1. Концентратор событий должен находиться в той же подписке Azure, что и созданная учетная запись Azure Data Lake Storage Gen 1. Создайте концентратор событий, нажав кнопку **Вкл.** в разделе **Запись** на странице портала **Создание концентратора событий**. 
2. На странице портала **Создание концентратора событий** в поле **поставщика записи** выберите **Azure Data Lake Store**.
3. В окне **Выбор хранилища** рядом с раскрывающимся списком **Data Lake Store** Укажите созданную ранее учетную запись Data Lake Storage Gen 1, а в поле **Data Lake путь** введите путь к созданной папке данных.

    ![Выбор учетной записи Data Lake Storage][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Добавление или настройка функции записи для существующего концентратора событий

Функцию "Сбор" можно настроить в существующих концентраторах событий, расположенных в пространствах имен Центров событий. Чтобы включить запись в существующем концентраторе событий или изменить параметры записи, щелкните пространство имен. Появится экран обзора. Затем щелкните концентратор событий, для которого необходимо включить запись или изменить ее параметры. Наконец, на открывшейся странице слева щелкните параметр **записи** и измените параметры записи, как показано на следующих снимках экрана.

### <a name="azure-blob-storage"></a>хранилище BLOB-объектов Azure

![Настройка хранилища BLOB-объектов Azure][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage 2-го поколения

![Настройка Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage 1-го поколения 

![Настройка Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о функции "Сбор" в Центрах событий см. в [этой статье][capture-overview].
- Функцию "Сбор" в Центрах событий можно также настроить с помощью шаблонов Azure Resource Manager. См. дополнительные сведения [о включении записи с помощью шаблона Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Узнайте, как создать подписку на Сетку событий Azure с пространством имен Центров событий в качестве источника](store-captured-data-data-warehouse.md)
- [Приступая к работе с Azure Data Lake Store с помощью портал Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
