---
title: 'Руководство по фабрике данных: Первый конвейер данных | Документация Майкрософт'
description: В этом руководстве по фабрике данных Azure объясняется, как создать и запланировать фабрику данных, которая обрабатывает данные с помощью сценария Hive в кластере Hadoop.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 2dd2edfabff51c749890fe20d47a29c1ec39947c
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140380"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Учебник. Создание первого конвейера для преобразования данных с помощью кластера Hadoop.
> [!div class="op_single_selector"]
> * [Обзор и предварительные требования](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Шаблон Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> В этой статье рассматривается служба "Фабрика данных Azure" версии 1. Если вы используете текущую версию службы "Фабрика данных", ознакомьтесь с [кратким руководством по Создание фабрики данных и конвейера с помощью пакета SDK .NET](../quickstart-create-data-factory-dot-net.md).

Из этого учебника вы узнаете, как создать свою первую фабрику данных Azure, используя конвейер данных. Конвейер преобразует входные данные, запуская сценарий Hive в кластере Azure HDInsight (Hadoop) для создания выходных данных.  

Эта статья содержит общие сведения и описание необходимых компонентов для работы с учебником. Обеспечив наличие всех необходимых компонентов, вы можете выполнить инструкции из руководства с помощью следующих средств или пакетов: Visual Studio, PowerShell, диспетчер ресурсов шаблон, REST API. Выберите один из вариантов в раскрывающемся списке в начале (или) ссылки в конце этой статьи, чтобы пройти учебник, используя один из этих вариантов.    

## <a name="tutorial-overview"></a>Обзор учебника
Вот какие шаги выполняются в этом учебнике:

1. Создание **фабрики данных**. Фабрика данных может содержать один или несколько конвейеров данных для перемещения и преобразования данных. 

    В этом учебнике рассказывается о том, как создать один конвейер в фабрике данных. 
2. Создание **конвейера**. Конвейер может содержать одно или несколько действий. (Например, действие копирования или действие Hive HDInsight). В этом примере используется действие HDInsight Hive, которое запускает скрипт Hive в кластере HDInsight Hadoop. Этот скрипт сначала создает таблицу, которая ссылается на необработанные данные веб-журнала в хранилище BLOB-объектов Azure, а затем разбивает необработанные данные по годам и месяцам.

    Описанный в данном учебнике конвейер использует действие Hive для преобразования данных с помощью запроса Hive в кластере Hadoop под управлением службы Azure HDInsight. 
3. Создание **связанных служб**. С помощью связанной службы можно связать хранилище данных или службу вычислений с фабрикой данных. Хранилище данных, например хранилище Azure, содержит входные и выходные данные действий в конвейере. Служба вычислений, такая как кластер HDInsight Hadoop, обрабатывает и преобразует данные.

    Работая с этим руководством, вы создали две связанные службы: **Службу хранилища Azure** и **Azure HDInsight**. Связанная служба хранилища Azure используется, чтобы связать учетную запись хранения Azure, которая содержит входные и выходные данные, с фабрикой данных. Связанная служба Azure HDInsight используется, чтобы связать кластер Azure HDInsight, который применяется для преобразования данных, с фабрикой данных. 
3. Создание входных и выходных **наборов данных**. Входной набор данных представляет входные данные для действия в конвейере, а выходной набор данных — выходные данные для действия.

    В этом учебнике наборы входных и выходных данных задают расположение входных и выходных данных в хранилище BLOB-объектов Azure. Связанная служба хранилища Azure указывает, какая учетная запись хранения Azure используется. Входной набор данных определяет, где расположены входные файлы, а выходной набор данных — где будут размещаться выходные файлы. 


Подробный обзор фабрики данных Azure см. в статье [Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке](data-factory-introduction.md).
  
Ниже приведено **представление схемы** примера фабрики данных, создаваемого в этом руководстве. У **MyFirstPipeline** есть одно действие типа Hive, которое использует набор данных **AzureBlobInput** в качестве входных данных и выдает набор данных **AzureBlobOutput** в качестве выходных данных. 

![Схема в руководстве по фабрике данных](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


В этом руководстве папка **inputdata** контейнера больших двоичных объектов Azure **adfgetstarted** содержит один файл с именем input.log. Этот файл журнала содержит записи за три месяца: январь, февраль и март 2016 г. Вот пример строк за каждый месяц во входном файле. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Когда файл обрабатывается в конвейере с помощью действия Hive HDInsight, действие запускает в кластере HDInsight сценарий Hive, который разбивает входные данные по годам и месяцам. Этот сценарий создает три выходные папки, содержащие файл с записями за каждый месяц.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

В примерах строк выше первая из них (содержащая дату 2016-01-01) записывается в файл 000000_0 в папке month=1. Аналогичным образом вторая строка записывается в файл в папке month=2, а третья — в файл в папке month=3.  

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим учебником необходимо следующее:

1. **Подписка Azure**. Если ее нет, можно за пару минут создать бесплатную пробную учетную запись. Сведения о том, как получить такую учетную запись, см. на странице [бесплатного ознакомительного периода](https://azure.microsoft.com/pricing/free-trial/).
2. **Служба хранилища Azure**. В данном учебнике предполагается, что для хранения данных используется учетная запись хранения Azure. Если у вас ее нет, см. раздел [Создание учетной записи хранения](../../storage/common/storage-quickstart-create-account.md). После создания учетной записи хранения запишите **ее имя** и **ключ доступа**. См. разделы о [просмотре, копировании и повторном создании ключей доступа к хранилищу](../../storage/common/storage-account-manage.md#access-keys).
3. Скачайте и просмотрите файл запроса Hive (**HQL**) по адресу: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Этот запрос преобразовывает входные данные в выходные. 
4. Скачайте и просмотрите файл примера входных данных (**input.log**) по адресу: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log).
5. Создайте контейнер больших двоичных объектов с именем **adfgetstarted** в хранилище BLOB-объектов Azure. 
6. Передайте файл **partitionweblogs.hql** в папку **script** в контейнере **adfgetstarted**. Воспользуйтесь таким средством, как [Обозреватель службы хранилища Microsoft Azure](https://storageexplorer.com/). 
7. Передайте файл **input.log** в папку **inputdata** в контейнере **adfgetstarted**. 

Обеспечив наличие всех необходимых компонентов, выберите одно из следующих средств или пакетов SDK для прохождения этого учебника: 

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Шаблон Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio предоставляет графический интерфейс для создания фабрик данных. PowerShell, шаблон Resource Manager и интерфейс REST API, в свою очередь, дают возможность сделать это с помощью средств создания сценариев и программирования.

> [!NOTE]
> Описанный в этом руководстве конвейер данных преобразовывает входные данные в выходные. Он не копирует данные из исходного хранилища данных в целевое. Инструкции по копированию данных с помощью Фабрики данных Azure см. в [руководстве по Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Можно объединить в цепочку два действия (выполнить одно действие вслед за другим), настроив выходной набор данных одного действия как входной набор данных другого действия. Подробные сведения см. в статье [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md). 





  
