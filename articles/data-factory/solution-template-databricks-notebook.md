---
title: Преобразование данных с помощью Databricks в Фабрике данных Azure
description: Сведения об использовании шаблона решения для преобразования данных с помощью записной книжки Databricks в Фабрике данных Azure.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 471c1d9610fa346b0eac44c73fe02f4510bd2889
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684250"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Преобразование данных с помощью Databricks в Фабрике данных Azure

В этом руководстве вы создадите комплексный конвейер, содержащий действия **поиска**, **копирования** и **записной книжки Databricks** в Фабрике данных.

-   Действие **поиска** или GetMetadata позволяет обеспечить готовность исходного набора данных для дальнейшего использования, прежде чем запускать задания копирования и аналитики.

-   **Действие копирования** копирует исходный файл или набор данных в хранилище-приемник. Хранилище-приемник подключается как DBFS в записной книжке Databricks таким образом, чтобы набор данных можно было непосредственно использовать с помощью Spark.

-   **Действие записной книжки Databricks** запускает записную книжку Databricks, которая преобразует набор данных и добавляет его в обработанную папку или Хранилище данных SQL.

Чтобы не усложнять этот шаблон, он не создает запланированный триггер. При необходимости вы можете его добавить.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Предварительные требования

1.  Создайте **учетную запись хранения больших двоичных объектов** и контейнер с именем `sinkdata` для использования в качестве **приемника**. Сохраните **имя учетной записи хранения**, **имя контейнера** и **ключ доступа**, так как они понадобятся позже в шаблоне.

2.  Убедитесь в наличии **рабочей области Azure Databricks** или создайте новую.

1.  **Импортируйте записную книжку для извлечения, преобразования и загрузки**. Импортируйте приведенную ниже записную книжку преобразования в рабочую область Databricks. (Он не должен находиться в том же расположении, что и ниже, но помните путь, выбранный для дальнейшего использования.) Импортируйте записную книжку со следующего URL-адреса, введя его в поле URL-адрес: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Выберите **Импортировать**.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Теперь давайте обновим записную книжку **преобразования**, указав **сведения о подключении к хранилищу** (имя и ключ доступа). Перейдите к **команде 5** в импортированной записной книжке выше и замените ее приведенным ниже фрагментом кода, предварительно заменив выделенные значения. Убедитесь, что это та же учетная запись, которую вы создали ранее. Она должна содержать контейнер `sinkdata`.

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

1.  Создайте **маркер доступа Databricks**, чтобы Фабрика данных могла получать доступ к Databricks. **Сохраните маркер доступа** для дальнейшего использования при создании связанной службы Databricks. Он выглядит примерно так: "dapi32db32cbb4w6eee18b7d87e45exxxxxx".

    ![4\.](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Создание связанных служб и наборов данных

1.  Создайте **связанные службы** в пользовательском интерфейсе Фабрики данных, выбрав *Connections Linked services (Связанные службы подключений) + Создать*.

    1.  **Источник данных** — для доступа к исходным данным. Вы можете использовать для этого примера общедоступное хранилище BLOB-объектов, содержащее исходные файлы.

        Выберите **Хранилище BLOB-объектов** и используйте приведенный ниже **универсальный код ресурса SAS** для подключения к хранилищу источника (доступ только для чтения).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Приемник** — для копирования данных.

        Выберите хранилище, созданное в пункте 1 раздела о предварительных требованиях, в связанной службе приемника.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** — для подключения к кластеру Databricks.

        Создайте связанную службу Databricks с использованием ключа доступа, созданного в пункте 2.c. Если у вас есть *интерактивный кластер*, его можно выбрать. (В этом примере используется параметр *New job cluster* (Новый кластер заданий).)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Создайте **наборы данных**.

    1.  Создайте **sourceAvailability_Dataset** для проверки доступности исходных данных.

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Исходный набор данных** — для копирования исходных данных (с помощью двоичного копирования).

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Целевой набор данных** — для копирования в приемник или целевое расположение.

        1.  Связанная служба — выберите "sinkBlob_LS", созданную при работе с пунктом в 1.b.

        2.  Путь к файлу — "sinkdata/staged_sink".

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Создание действий

1.  Создайте действие поиска **Availability flag** (Флаг доступности) для выполнения проверки доступности источника (можно использовать действие поиска или GetMetadata). Выберите набор sourceAvailability_Dataset, созданный при работе с пунктом 2.a.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Создайте действие копирования **file-to-blob** (файл в большой двоичный объект) для копирования набора данных из источника в приемник. В этом случае данные — это двоичный файл. Настройки источника и приемника в действии копирования см. на снимках экрана ниже.

    ![13.](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14.](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Определите **параметры конвейера**.

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Создайте **действие Databricks**.

    Выберите связанную службу, созданную на предыдущем шаге.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Настройте **параметры**. Создайте **базовые параметры**, как показано на снимке экрана, и параметры для передачи в записную книжку Databricks из Фабрики данных. Просмотрите и **выберите** **правильный путь к записной книжке**, переданный в **пункте 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Запустите конвейер**. Чтобы просмотреть более подробные журналы Spark, можно перейти по ссылке для журналов Databricks.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Вы также можете проверить файл данных с помощью обозревателя хранилища. (Для корреляции с запусками конвейера Фабрики данных этот пример добавляет идентификатор запуска конвейера из фабрики данных в выходную папку. Таким образом, вы сможете отслеживать файлы, созданные во время каждого запуска.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке](introduction.md)
