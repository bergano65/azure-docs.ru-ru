---
title: Массовое копирование из файлов в базу данных
description: Узнайте, как использовать шаблон решения для копирования данных оптом из Azure Data Lake Storage Gen2 в Azure Synapse Analytics / Базу данных Azure S'L.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 070b708f204006bc1ba90c4c3676696291fde902
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414869"
---
# <a name="bulk-copy-from-files-to-database"></a>Массовое копирование из файлов в базу данных

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описывается шаблон решения, который можно использовать для копирования данных оптом из Azure Data Lake Storage Gen2 в Azure Synapse Analytics / Базу данных Azure S'L.

## <a name="about-this-solution-template"></a>Информация о шаблоне решения

Этот шаблон извлекает файлы из источника Azure Data Lake Storage Gen2. После этого оно итерирует над каждым архивом в источнике и копирует архив к магазину данных назначения. 

В настоящее время этот шаблон поддерживает только копирование данных в формате **DelimitedText.** Файлы в других форматах данных также могут быть извлечены из хранилища исходных данных, но не могут быть скопированы в хранилище данных назначения.  

Шаблон состоит из трех действий.
- **Получите,** чтобы активность Metadata извлекала файлы из хранилища Azure Data Lake Storage Gen2 и переносит их в последующее действие *ForEach.*
- **Для каждого** действия получает файлы из деятельности *Get Metadata* и итерирует каждый файл в действие *Copy.*
- **Копирование** деятельности находится в деятельности *ForEach* для копирования каждого файла из хранилища исходных данных в хранилище данных назначения.

Шаблон определяет следующие два параметра:
- *SourceContainer* — это путь корневого контейнера, по которому данные копируется в хранилище Azure Data Lake Data. Gen2. 
- *SourceDirectory* — это путь каталога под корневым контейнером, из которого копируется данные в вашем Хранилище Azure Data Lake.

## <a name="how-to-use-this-solution-template"></a>Использование шаблона решения

1. Перейдите к **массовой копии из файлов в шаблон базы данных.** Создайте **новое** соединение с исходным магазином Gen2. Имейте в виду, что "GetMetadataDataset" и "SourceDataset" являются ссылками на одно и то же соединение исходного файла магазина.

    ![Создание нового соединения с хранилищем исходных данных](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Создайте **новое** соединение с хранилищем данных раковины, в которое вы копируете данные.

    ![Создание нового подключения к хранилителю данных раковины](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Выберите **Использовать этот шаблон**.

    ![Использовать этот шаблон](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Вы увидите конвейер, созданный в следующем примере:

    ![Проверка конвейера](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Если вы выбрали **Azure Synapse Analytics (ранее S'L DW)** в качестве пункта назначения данных в **вышеупомянутом шаге 2,** необходимо ввести подключение к хранилищу Azure Blob для постановки, как того требует Polybase хранилища данных. Как показано на следующем скриншоте, шаблон автоматически генерирует *путь хранения* для вашего хранилища Blob. Проверьте, создан ли контейнер после запуска конвейера.
        
    ![Настройка Polybase](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Выберите **отключать,** введите **параметры,** а затем выберите **Finish.**

    ![Нажмите кнопку «Дебуг»](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Когда конвейер завершается успешно, можно увидеть результаты, аналогичные следующему примеру:

    ![Просмотр результатов](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Дальнейшие действия

- [Знакомство с Фабрикой данных Azure](introduction.md)