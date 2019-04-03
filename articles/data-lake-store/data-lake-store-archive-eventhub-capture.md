---
title: Сбор данных из Центров событий в Azure Data Lake Storage 1-го поколения | Документация Майкрософт
description: Сбор данных из Центров событий с помощью Azure Data Lake Storage 1-го поколения
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bb67c1769510710b368bef4dc0b501f939b3427e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884518"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Сбор данных из Центров событий с помощью Azure Data Lake Storage 1-го поколения

В этой статье приведены сведения о сборе данных, полученных Центрами событий Azure, с помощью Azure Data Lake Storage 1-го поколения.

## <a name="prerequisites"></a>Технические условия

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Учетная запись Azure Data Lake Storage 1-го поколения**. За инструкциями по созданию учетной записи обращайтесь к статье [Начало работы с Azure Data Lake Storage 1-го поколения](data-lake-store-get-started-portal.md).

*  **Пространство имен Центров событий.** Дополнительные сведения см. в разделе [Создание пространства имен Центров событий](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Убедитесь, что учетная запись Data Lake Storage 1-го поколения и пространство имен Центров событий находятся в одной подписке Azure.


## <a name="assign-permissions-to-event-hubs"></a>Назначение разрешений Центрам событий

В этом разделе вы создадите папку в учетной записи, в которой необходимо собрать данные из Центров событий. Вы также можете назначить разрешения Центрам событий. Это позволит им записывать данные в учетную запись Data Lake Storage 1-го поколения. 

1. Откройте учетную запись Data Lake Storage 1-го поколения, в которую необходимо собрать данные из Центров событий, и щелкните **Обозреватель данных**.

    ![Обозреватель данных Data Lake Storage 1-го поколения](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 data explorer")

1.  Выберите **Создать папку** и введите имя папки, в которую необходимо сохранять данные.

    ![Создание папки в Azure Data Lake Storage 1-го поколения](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Create a new folder in Data Lake Storage Gen1")

1. Назначьте разрешения в корневой папке Data Lake Storage 1-го поколения. 

    a. Щелкните **Обозреватель данных**, выберите корневую папку учетной записи Data Lake Storage 1-го поколения, а затем — **Доступ**.

    ![Назначение разрешений в корневой папке Data Lake Storage 1-го поколения](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Assign permissions for the Data Lake Storage Gen1 root")

    2. В разделе **Доступ** выберите **Добавить**, щелкните **Выберите пользователя или группу**, а затем найдите `Microsoft.EventHubs`. 

    ![Назначение разрешений в корневой папке Data Lake Storage 1-го поколения](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Assign permissions for the Data Lake Storage Gen1 root")
    
    Нажмите кнопку **Выбрать**.

    c. В разделе **Назначение разрешений** выберите **Выбор разрешений**. Задайте для параметра **Разрешения** значение **Выполнить**. Задайте для параметра **Добавить к** значение **К этой папке и всем вложенным элементам**. Задайте для параметра **Add as** (Добавить как) значение **Запись разрешений доступа и запись разрешений по умолчанию**.

    > [!IMPORTANT]
    > Это простой способ обеспечения доступа к целевой папке при создании новой иерархии папок для записи данных, поступающих в Центры событий Azure.  Однако добавление разрешений для всех дочерних элементов папки верхнего уровня с многочисленными дочерними файлами и папками может занять много времени.  Если корневая папка содержит большое число файлов и папок, возможно, быстрее будет добавить разрешения на **выполнение** для `Microsoft.EventHubs` по отдельности в каждую папку в пути к конечной целевой папке. 

    ![Назначение разрешений в корневой папке Data Lake Storage 1-го поколения](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Assign permissions for the Data Lake Storage Gen1 root")

    Последовательно выберите **ОК**.

1. Назначьте разрешения папке в учетной записи Data Lake Storage 1-го поколения, в которой необходимо сохранить данные.

    a. Щелкните **Обозреватель данных**, выберите папку в учетной записи Data Lake Storage 1-го поколения, а затем — **Доступ**.

    ![Назначение разрешений в папке Data Lake Storage 1-го поколения](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Assign permissions for the Data Lake Storage Gen1 folder")

    2. В разделе **Доступ** выберите **Добавить**, щелкните **Выберите пользователя или группу**, а затем найдите `Microsoft.EventHubs`. 

    ![Назначение разрешений в папке Data Lake Storage 1-го поколения](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Assign permissions for the Data Lake Storage Gen1 folder")
    
    Нажмите кнопку **Выбрать**.

    c. В разделе **Назначение разрешений** выберите **Выбор разрешений**. Для параметра **Разрешения** установите флажки **Чтение, Запись** и **Выполнить**. Задайте для параметра **Добавить к** значение **К этой папке и всем вложенным элементам**. Наконец, задайте для параметра **Add as** (Добавить как) значение **Запись разрешений доступа и запись разрешений по умолчанию**.

    ![Назначение разрешений в папке Data Lake Storage 1-го поколения](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Assign permissions for the Data Lake Storage Gen1 folder")
    
    Последовательно выберите **ОК**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Настройка Центров событий для сбора данных в Data Lake Storage 1-го поколения

В этом разделе вы создаете концентратор событий в пространстве имен Центров событий. Кроме того, вы также настроите концентратор событий для записи данных в учетную запись Azure Data Lake Storage 1-го поколения. В этом разделе предполагается, что вы уже создали пространство имен Центров событий.

1. В области **Обзор** пространства имен Центров событий выберите **+Концентратор событий**.

    ![Создание концентратора событий](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Create Event Hub")

1. Чтобы настроить Центры событий для сбора данных в Data Lake Storage 1-го поколения, укажите приведенные ниже значения.

    ![Создание концентратора событий](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Create Event Hub")

    a. Укажите имя концентратора событий.
    
    2. В этом руководстве задайте для параметров **Количество разделов** и **Хранение сообщений** значения по умолчанию.
    
    c. Установите для параметра **Запись** значение **Включено**. Задайте **окно времени** (частота выполнения записи) и **окно размера** (размер данных для записи). 
    
    d. Для параметра **Capture Provider** (Поставщик сбора) задайте значение **Azure Data Lake Store** и выберите учетную запись Data Lake Storage 1-го поколения, созданную ранее. В качестве значения параметра **Data Lake Path** (Путь к Data Lake) введите имя папки, созданной в учетной записи Data Lake Storage 1-го поколения. Необходимо только указать относительный путь к папке.

    д. Оставьте стандартное значение параметра **Воспользуйтесь поиском, чтобы отфильтровать репликации**. Этот параметр определяет структуру папки, созданной в папке записи.

    Е. Нажмите кнопку **Создать**.

## <a name="test-the-setup"></a>Тестирование настройки

Теперь вы можете протестировать решение, отправив данные в концентратор событий Azure. Инструкции см. в статье [Отправка событий в Центры событий Azure с помощью платформы .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Отправляемые данные отобразятся в Data Lake Storage 1-го поколения с использованием указанной структуры папок. Например, на снимке экрана ниже приведена структура папок, в которой отобразятся данные в учетной записи Data Lake Storage 1-го поколения.

![Пример данных концентратора событий в Data Lake Storage 1-го поколения](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Sample EventHub data in Data Lake Storage Gen1")

> [!NOTE]
> Даже если в Центры событий не поступают сообщения, они записывают пустые файлы лишь с заголовками в учетную запись Data Lake Storage 1-го поколения. Файлы записываются с интервалом времени, указанным при создании концентраторов событий.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Анализ данных в Data Lake Storage 1-го поколения

Когда данные появятся в Data Lake Storage 1-го поколения, вы можете выполнить задания аналитики, чтобы обработать их. Сведения об использовании Azure Data Lake Analytics для выполнения этих действий см. в [примере USQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).
  

## <a name="see-also"></a>См. также
* [Защита данных в поколение 1 хранилища Озера данных](data-lake-store-secure-data.md)
* [Копирование данных из больших двоичных объектов хранилища Azure в поколение 1 хранилища Озера данных](data-lake-store-copy-data-azure-storage-blob.md)
