---
title: Преобразование с помощью Azure Databricks
description: Сведения об использовании шаблона решения для преобразования данных с помощью записной книжки Databricks в Фабрике данных Azure.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 9a05b09f958d741fa56c586fbc7f5c5908dbbce6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384387"
---
# <a name="transformation-with-azure-databricks"></a>Преобразование с помощью Azure Databricks

В этом учебнике создается сквозной конвейер, содержащий данные **проверки,** **копирование**и **действия блокнота** на фабрике данных Azure.

- **Проверка** гарантирует, что набор исходных данных будет готов к потреблению ниже по течению, прежде чем вы срабатывать работу по копированию и аналитике.

- **Копирование данных** дублирует набор исходных данных в хранилище раковины, которое устанавливается как DBFS в блокноте Azure Databricks. Таким образом, набор данных может быть непосредственно использован Spark.

- **Ноутбук** запускает блокнот Databricks, который преобразует набор данных. Он также добавляет набор данных в обработанную папку или хранилище данных Azure S'L.

Для простоты шаблон в этом учебнике не создает запланированный триггер. Вы можете добавить один, если это необходимо.

![Диаграмма трубопровода](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись хранения Azure Blob с контейнером, призванная `sinkdata` использоваться в качестве раковины.

  Обратите внимание на имя учетной записи хранилища, имя контейнера и ключ доступа. Эти значения понадобятся позже в шаблоне.

- Рабочее пространство Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Импорт ноутбука для трансформации

Импорт ноутбука **Transformation** в рабочее пространство Databricks:

1. Вопийте в рабочей области Azure Databricks, а затем выберите **импорт.**
       ![Команда меню для импорта](media/solution-template-Databricks-notebook/import-notebook.png) рабочего пространства Ваш рабочий путь может отличаться от показанной, но запомните его на потом.
1. Выберите **Импорт из: URL**. В текстовом `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`поле введите .

   ![Выбор для импорта ноутбука](media/solution-template-Databricks-notebook/import-from-url.png)

1. Теперь давайте обновим блокнот **Transformation** с информацией о подключении к хранению.

   В импортируемом блокноте перейдите в **команду 5,** как показано в следующем фрагменте кода.

   - `<storage name>`Замените `<access key>` и с вашей собственной информации подключения хранения.
   - Используйте учетную `sinkdata` запись хранения с контейнером.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Создайте **маркер доступа Databricks**, чтобы Фабрика данных могла получать доступ к Databricks.
   1. В рабочем пространстве Databricks выберите значок профиля пользователя в правом верхнем углу.
   1. Выберите **настройки пользователя.**
    ![Команда меню для настроек пользователя](media/solution-template-Databricks-notebook/user-setting.png)
   1. Выберите **generate New Token** под вкладкой **«Токены доступа».**
   1. Выберите **Создать**.

    ![Кнопка "Генерировать"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Сохраните маркер доступа* для последующего использования при создании службы, связанной с Databricks. Токен доступа выглядит `dapi32db32cbb4w6eee18b7d87e45exxxxxx`примерно как .

## <a name="how-to-use-this-template"></a>Как использовать этот шаблон

1. Перейдите к **шаблону «Трансформация» с** помощью шаблона Azure Databricks и создайте новые связанные службы для следующих подключений.

   ![Настройка подключения](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Источник Blob Connection** - для доступа к исходным данным.

       Для этого упражнения можно использовать общедоступное хранилище, содержащее исходные файлы. Справка о следующем скриншоте для конфигурации. Используйте следующий **URL SAS** для подключения к исходной памяти (только для чтения доступа):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Выборы для метода проверки подлинности и URL SAS](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Пункт назначения Blob Connection** - для хранения скопированных данных.

       В **новом подключенном** окне службы выберите каплю для хранения раковины.

       ![Раковина хранения капли как новая связанная услуга](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** - подключение к кластеру Databricks.

        Создайте службу, связанную с Databricks, используя ключ доступа, созданный ранее. Вы можете выбрать *интерактивный кластер,* если он у вас есть. В этом примере используется опция **нового кластера задания.**

        ![Выбор ы для подключения к кластеру](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Выберите **Использовать этот шаблон**. Вы увидите созданный конвейер.

    ![Создание конвейера](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Внедрение и конфигурация трубопровода

В новом конвейере большинство параметров настраиваются автоматически с значениями по умолчанию. Просмотрите конфигурации конвейера и внедьте необходимые изменения.

1. В **флаге доступности** **действия проверки** убедитесь, что `SourceAvailabilityDataset` значение набора **исходных данных** настроено на то, что было создано ранее.

   ![Значение набора исходных данных](media/solution-template-Databricks-notebook/validation-settings.png)

1. В файле действия **копирования данных** — файле к **blob**проверьте вкладки **«Источник»** и **«Sink».** При необходимости изменяйте настройки.

   - **Вкладка Исходная** вкладка ![Источник](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **Вкладка раковины** ![вкладка раковина](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. В **преображении**активности **ноутбука** просмотрите и обновите пути и настройки по мере необходимости.

   **Служба, связанная с Databricks,** должна быть предварительно заселена ![значением предыдущего шага, как показано на рисунке: Заполняемые значения для службы, связанной с Databricks](media/solution-template-Databricks-notebook/notebook-activity.png)

   Чтобы проверить настройки **ноутбука:**
  
    1. Выберите вкладку **«Настройки».** Для **пути записной книжки**убедитесь, что путь по умолчанию является правильным. Возможно, вам придется просматривать и выбирать правильный путь ноутбука.

       ![Путь записной книжки](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Расширьте селектор **базовых параметров** и убедитесь, что параметры соответствуют тому, что показано на следующем скриншоте. Эти параметры передаются в блокнот Databricks от Data Factory.

       ![Базовые параметры](media/solution-template-Databricks-notebook/base-parameters.png)

1. Убедитесь, что **параметры трубопровода** соответствуют тому, ![что показано на следующем скриншоте: Параметры трубопровода](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Подключите к наборам данных.

   - **SourceAvailabilityDataset** - чтобы проверить, что исходные данные доступны.

     ![Выборы для связанных служб и файлов для SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** - для доступа к исходным данным.

       ![Выборы для связанных служб и файлов для SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** - для копирования данных в место назначения раковины. Используйте следующие значения:

     - **Связанный сервис,** - `sinkBlob_LS`созданный на предыдущем этапе.

     - **Путь файла** - `sinkdata/staged_sink`

       ![Выборы для связанных служб и файлов для DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Выберите **Debug** для запуска конвейера. Вы можете найти ссылку на журналы Databricks для более подробных журналов Spark.

    ![Ссылка на журналы Databricks из вывода](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Вы также можете проверить файл данных с помощью Azure Storage Explorer.

    > [!NOTE]
    > Для сопоставления с запусками конвейера Data Factory этот пример прикладает идентификатор запуска конвейера с фабрики данных в папку вывода. Это помогает отслеживать файлы, генерируемые каждым запуском.
    > ![Приложено идентификатор запуска конвейера](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Знакомство с Фабрикой данных Azure](introduction.md)
