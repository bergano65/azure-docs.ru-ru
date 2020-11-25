---
title: Выполнение скриптов Python с помощью Фабрики данных
description: Учебник по выполнению скриптов Python в составе конвейера с помощью Фабрики данных Azure в пакетной службе Azure.
author: mammask
ms.devlang: python
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: komammas
ms.custom: mvc, devx-track-python
ms.openlocfilehash: c66c14d42c3d14fc4171f6fdfaf2e7f75a531507
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886912"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Руководство по Выполнение скриптов Python с помощью Фабрики данных Azure в пакетной службе Azure

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Проверка подлинности с помощью учетных записей хранения и пакетной службы.
> * Разработка и запуск скрипта на языке Python
> * Создание пула вычислительных узлов для запуска приложения.
> * Планирование рабочих нагрузок Python
> * Мониторинг конвейера аналитики
> * Доступ к файлам журналов

Приведенный ниже пример выполняет скрипт Python, который получает входные данные в формате CSV из контейнера хранилища BLOB-объектов, выполняет процесс обработки данных и записывает результат в отдельный контейнер хранилища BLOB-объектов.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Установленный дистрибутив [Python](https://www.python.org/downloads/) для локального тестирования.
* Пакет `pip` [azure-storage-blob](https://pypi.org/project/azure-storage-blob/).
* [Набор данных iris.csv](https://www.kaggle.com/uciml/iris/version/2#Iris.csv)
* учетная запись пакетной службы Azure и связанная учетная запись службы хранилища Azure. Дополнительные сведения о том, как создать учетные записи пакетной службы и связать их с учетными записями хранения, см. в статье [Создание учетной записи Пакетной службы](quick-create-portal.md#create-a-batch-account).
* Учетная запись Фабрики данных Azure. В статье [Создание фабрики данных](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) представлены дополнительные сведения о том, как создать фабрику данных на портале Azure.
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Создание пула пакетной службы с помощью Batch Explorer

В этом разделе описано, как с помощью Batch Explorer создать пул пакетной службы, который будет использоваться в конвейере Фабрики данных Azure. 

1. Войдите в Batch Explorer, используя свои учетные данные Azure.
1. Выберите учетную запись пакетной службы
1. Создайте пул, выбрав **Пулы** на левой боковой панели, а затем нажав кнопку **Добавить** над формой поиска. 
    1. Выберите идентификатор и отображаемое имя. В этом примере мы используем имя `custom-activity-pool`.
    1. Установите тип масштаба **Фиксированный размер** и задайте количество выделенных узлов равное 2.
    1. В разделе **Обработка и анализ данных** выберите операционную систему **Dsvm Windows**.
    1. Выберите размер виртуальной машины `Standard_f2s_v2`.
    1. Включите задачу запуска и добавьте команду `cmd /c "pip install azure-storage-blob pandas"`. Вы можете сохранить указанное по умолчанию удостоверение пользователя **Пользователь пула**.
    1. Щелкните **ОК**.

## <a name="create-blob-containers"></a>Создание контейнеров больших двоичных объектов

Здесь вы создадите контейнеры больших двоичных объектов, в которых будут храниться ваши входные и выходные файлы для пакетного задания распознавания текста.

1. Войдите в Обозреватель службы хранилища с помощью своих учетных данных Azure.
1. Используя учетную запись хранения, связанную с вашей учетной записью Пакетной службы, создайте два контейнера больших двоичных объектов (один для входных файлов, а другой для выходных), выполнив шаги, описанные в разделе о [создании контейнера больших двоичных объектов](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * В этом примере мы будем вызывать наш входной (`input`) и выходной (`output`) контейнеры.
1. Передайте [`iris.csv`](https://www.kaggle.com/uciml/iris/version/2#Iris.csv) во входной контейнер `input` с помощью Обозревателя службы хранилища, выполнив инструкции из раздела [Управление большими двоичными объектами в контейнере](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container).

## <a name="develop-a-script-in-python"></a>Разработка скрипта на языке Python

Следующий скрипт Python загружает набор данных `iris.csv` из контейнера `input`, выполняет процесс обработки данных и записывает результат обратно в контейнер `output`.

``` python
# Load libraries
from azure.storage.blob import BlobServiceClient
import pandas as pd

# Define parameters
storageAccountURL = "<storage-account-url>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blob_service_client = BlockBlobService(account_url=storageAccountURL,
                               credential=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
container_client = blob_service_client.get_container_client(containerName)
with open("iris_setosa.csv", "rb") as data:
    blob_client = container_client.upload_blob(name="iris_setosa.csv", data=data)
```

Сохраните этот скрипт с именем `main.py` и отправьте его в контейнер **службы хранилища Azure** `input`. Не забудьте проверить его функциональность локально, прежде чем передавать скрипт в контейнер больших двоичных объектов.

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Настройка конвейера Фабрики данных Azure

В этом разделе вы создадите и проверите конвейер с помощью скрипта Python.

1. Выполните процедуру "Создание фабрики данных" из [этой статьи](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).
1. На панели **Ресурсы фабрики** нажмите кнопку + (плюс) и выберите **Конвейер**.
1. На вкладке **Общие** задайте конвейеру имя "Выполнение Python".

    ![На вкладке "Общие" задайте конвейеру имя "Выполнение Python".](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. На панели **Действия** разверните узел **Пакетная служба**. Перетащите пользовательское действие с панели элементов **Действия** в область конструктора конвейера.
1. На вкладке **Общие** укажите значение **testPipeline** в поле "Имя".

    ![На вкладке "Общие" укажите значение testPipeline в поле "Имя".](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. На вкладке **Пакетная служба Azure** добавьте **учетную запись пакетной службы**, которую вы создали на предыдущих шагах и выполните **тестирование подключения**.

    ![На вкладке "Пакетная служба Azure" добавьте учетную запись пакетной службы, которую вы создали на предыдущих шагах, и протестируйте подключение.](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. На вкладке **Параметры** введите команду `python main.py`.
1. В поле **Resource Linked Service** (Служба, связанная с ресурсом) выберите учетную запись хранения, которую вы создали на предыдущих шагах. Проверьте успешность создания подключения к серверу.
1. В поле **Путь к папке** выберите имя контейнера **хранилища BLOB-объектов Azure**, который содержит нужный скрипт Python и входные данные для него. Это действие приводит к скачиванию выбранных файлов из контейнера в экземпляры узлов пула перед выполнением скрипта Python.

    ![В поле "Путь к папке" задайте имя контейнера Хранилища BLOB-объектов Azure](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. На панели инструментов конвейера над холстом щелкните **Проверка**, чтобы проверить параметры конвейера. Убедитесь, что проверка конвейера прошла успешно. Чтобы закрыть результаты проверки, нажмите кнопку &gt;&gt; (стрелка вправо).
1. Щелкните **Отладка**, чтобы проверить конвейер и убедиться в правильности его работы.
1. Щелкните **Опубликовать**, чтобы опубликовать этот конвейер.
1. Щелкните **Активировать**, чтобы выполнить скрипт Python в рамках пакетного процесса.

    ![Щелкните "Активировать", чтобы выполнить скрипт Python в рамках пакетного процесса](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Мониторинг файлов журналов

Если при выполнении скрипта создаются предупреждения или ошибки, вы можете получить дополнительные сведения о выходных данных из `stdout.txt` и `stderr.txt`.

1. Выберите раздел **Задания** слева в окне Batch Explorer.
1. Выберите задание, созданное фабрикой данных. Если вы присвоили пулу имя `custom-activity-pool`, выберите `adfv2-custom-activity-pool`.
1. Щелкните задачу, для которой указан код завершения с ошибкой.
1. Просмотрите `stdout.txt` и `stderr.txt`, чтобы изучить и диагностировать проблему.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы на конкретном примере изучили выполнение скриптов Python в составе конвейера с помощью Фабрики данных Azure в пакетной службе Azure.

Подробное описание Фабрики данных Azure см. здесь:

> [!div class="nextstepaction"]
> [Фабрика данных Azure](../data-factory/introduction.md)
> [Конвейеры и действия](../data-factory/concepts-pipelines-activities.md)
> [Пользовательские действия](../data-factory/transform-data-using-dotnet-custom-activity.md).
