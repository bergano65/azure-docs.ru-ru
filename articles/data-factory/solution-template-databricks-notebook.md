---
title: Преобразование с Azure Databricks
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
ms.date: 12/10/2018
ms.openlocfilehash: 322f5306949b266958ded908e981ed530e8245c8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227747"
---
# <a name="transformation-with-azure-databricks"></a>Преобразование с Azure Databricks

В этом руководстве вы создадите сквозной конвейер, содержащий действия **проверки**, **копирования**и **записных книжек** в фабрике данных.

-   Действие **проверки** используется для обеспечения готовности исходного набора данных к нисходящему потреблению перед запуском задания копирования и аналитики.

-   Действие **копирования** копирует исходный файл или набор данных в хранилище приемников. Хранилище-приемник подключается как DBFS в записной книжке Databricks таким образом, чтобы набор данных можно было непосредственно использовать с помощью Spark.

-   Действие **записной книжки** "подобласти данных" запускает записную книжку "кирпичные", которая преобразует DataSet, и добавляет ее в обработанную папку или хранилище данных SQL.

Чтобы не усложнять этот шаблон, он не создает запланированный триггер. При необходимости вы можете его добавить.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Предварительные требования

1. Создайте **учетную запись хранения больших двоичных объектов** и контейнер с именем `sinkdata` для использования в качестве **приемника**. Сохраните **имя учетной записи хранения**, **имя контейнера** и **ключ доступа**, так как они понадобятся позже в шаблоне.

2. Убедитесь в наличии **рабочей области Azure Databricks** или создайте новую.

3. **Импортируйте записную книжку для преобразования**. 
    1. В Azure Databricks сослаться на снимки экрана для импорта записной книжки **преобразования** в рабочую область "кирпичи". Он не должен находиться в том же расположении, что и приведенный ниже, но следует запомнить путь, выбранный для дальнейшего использования.
   
       ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)    
    
    1. Выберите "Импорт из: **URL-адрес**" и введите в текстовом поле следующий URL-адрес:
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)    

4. Теперь изменим в записную книжку **Преобразование** сведения о подключении к хранилищу. Перейдите к **команде 5** (как показано в приведенном ниже фрагменте кода) в импортированной записной книжке, а затем замените `<storage name>`и `<access key>` сведениями о подключении к хранилищу. Убедитесь, что это та же учетная запись, которую вы создали ранее. Она должна содержать контейнер `sinkdata`.

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

5.  Создайте **маркер доступа Databricks**, чтобы Фабрика данных могла получать доступ к Databricks. **Сохраните маркер доступа** для последующего использования при создании связанной службы "кирпичные данные", которая выглядит примерно так: "dapi32db32cbb4w6eee18b7d87e45exxxxxx".

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="how-to-use-this-template"></a>Как использовать этот шаблон

1.  Переход к **преобразованию с помощью шаблона Azure Databricks** . Создайте новые связанные службы для следующих подключений. 
    
    ![Параметры подключений](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **Подключение** к исходному BLOB-объекту — для доступа к исходным данным. 
        
        Вы можете использовать для этого примера общедоступное хранилище BLOB-объектов, содержащее исходные файлы. Ниже приведена ссылка на снимок экрана для настройки. Используйте ниже **URL-адрес SAS** для подключения к исходному хранилищу (доступ только для чтения): 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Подключение к целевому BLOB-объекту** — для копирования данных в. 
        
        В связанной службе приемника выберите хранилище, созданное в **предварительном компоненте** 1.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Azure Databricks** — для подключения к кластеру "кирпичы".

        Создайте связанную службу "кирпичы" с помощью ключа доступа, созданного в **предварительном компоненте** 2. c. Если у вас есть *интерактивный кластер*, его можно выбрать. (В этом примере используется параметр *New job cluster* (Новый кластер заданий).)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

1. Выберите **использовать этот шаблон**, и вы увидите конвейер, созданный, как показано ниже:
    
    ![Создание конвейера](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>Введение и Настройка конвейера

В созданном новом конвейере большинство параметров настроены автоматически со значениями по умолчанию. Ознакомьтесь с конфигурациями и обновлениями, где это необходимо, чтобы соответствовать собственным параметрам. Дополнительные сведения см. в приведенных ниже инструкциях и снимках экрана для справки.

1.  Для выполнения проверки доступности источника создается **флаг доступности** действия проверки. *Саурцеаваилабилитидатасет* , созданный на предыдущем шаге, выбран в качестве набора данных.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Для копирования набора данных из источника в приемник создается файл действия копирования **в BLOB-объект** . Настройки источника и приемника в действии копирования см. на снимках экрана ниже.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Будет создано **Преобразование** действия записной книжки и выбрана связанная служба, созданная на предыдущем шаге.
    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

     1. Выберите вкладку **Параметры** . Для *пути к записной книжке*шаблон определяет путь по умолчанию. Может потребоваться просмотреть и выбрать правильный путь к записной книжке, отправленный в **предварительном компоненте** 2. 

         ![17](media/solution-template-Databricks-notebook/databricks-tutorial-image17.png)
    
     1. Ознакомьтесь с *базовыми параметрами* , созданными, как показано на снимке экрана. Они передаются в записную книжку "кирпичы" из фабрики данных. 

         ![Базовые параметры](media/solution-template-Databricks-notebook/base-parameters.png)

1.  **Параметры конвейера** определяются следующим образом.

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1. Настройка наборов данных.
    1.  **Саурцеаваилабилитидатасет** создается для проверки доступности исходных данных.

        ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Саурцефилесдатасет** — для копирования исходных данных.

        ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Дестинатионфилесдатасет** — для копирования в приемник или место назначения.

        1.  Связанная служба — *sinkBlob_LS* , созданная на предыдущем шаге.

        2.  Путь к файлу — *синкдата/staged_sink*.

            ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)


1.  Выберите **Отладка** , чтобы запустить конвейер. Чтобы просмотреть более подробные журналы Spark, можно перейти по ссылке для журналов Databricks.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Вы также можете проверить файл данных с помощью обозревателя хранилища. (Для корреляции с запусками конвейера Фабрики данных этот пример добавляет идентификатор запуска конвейера из фабрики данных в выходную папку. Таким образом, вы сможете отслеживать файлы, созданные во время каждого запуска.)

    ![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке](introduction.md)
