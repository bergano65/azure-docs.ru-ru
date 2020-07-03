---
title: Массовое копирование из файлов в базу данных
description: Узнайте, как использовать шаблон решения для копирования данных из Azure Data Lake Storage 2-го поколения в Azure синапсе Analytics или базу данных SQL Azure.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 468bd838237e076aacb9dee0ccacfdcc1ea940af
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629122"
---
# <a name="bulk-copy-from-files-to-database"></a>Массовое копирование из файлов в базу данных

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

В этой статье описывается шаблон решения, который можно использовать для копирования данных из Azure Data Lake Storage 2-го поколения в Azure синапсе Analytics или в базу данных SQL Azure.

## <a name="about-this-solution-template"></a>Информация о шаблоне решения

Этот шаблон извлекает файлы из источника Azure Data Lake Storage 2-го поколения. Затем он выполняет итерацию по каждому файлу в источнике и копирует файл в целевое хранилище данных. 

Сейчас этот шаблон поддерживает копирование данных только в формате **DelimitedText** . Файлы в других форматах данных также могут быть получены из исходного хранилища данных, но не могут быть скопированы в целевое хранилище данных.  

Шаблон состоит из трех действий.
- Действие **получения метаданных** извлекает файлы из Azure Data Lake Storage 2-го поколения и передает их в последующее действие *foreach* .
- Действие **foreach** получает файлы из действия *получить метаданные* и выполняет перебор каждого файла с действием *копирования* .
- Действие **копирования** находится в действии *foreach* , чтобы скопировать каждый файл из исходного хранилища данных в целевое хранилище данных.

Шаблон определяет следующие два параметра:
- *Саурцеконтаинер* — это корневой путь к контейнеру, из которого копируются данные в Azure Data Lake Storage 2-го поколения. 
- *SourceDirectory* — это путь к каталогу в корневом контейнере, из которого копируются данные в Azure Data Lake Storage 2-го поколения.

## <a name="how-to-use-this-solution-template"></a>Использование шаблона решения

1. Перейдите к шаблону " **групповое копирование из файлов в базу данных** ". Создайте **новое** подключение к исходному хранилищу Gen2. Имейте в виду, что "Жетметадатадатасет" и "SourceDataset" являются ссылками на одно и то же подключение к исходному хранилищу файлов.

    ![Создать новое соединение с исходным хранилищем данных](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Создайте **новое** подключение к хранилищу данных приемника, куда вы копируете данные.

    ![Создание нового подключения к хранилищу данных приемника](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Выберите **Использовать этот шаблон**.

    ![Использовать этот шаблон](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Вы увидите конвейер, созданный, как показано в следующем примере:

    ![Проверка конвейера](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Если вы выбрали **Azure синапсе Analytics (ранее SQL DW)** в качестве назначения данных на **шаге 2** , упомянутого выше, необходимо ввести подключение к хранилищу BLOB-объектов Azure для промежуточного хранения в соответствии с требованиями polybase хранилища данных SQL. Как показано на следующем снимке экрана, шаблон автоматически создаст *путь к хранилищу* BLOB-объектов. Проверьте, создан ли контейнер после выполнения конвейера.
        
    ![Настройка Polybase](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Выберите **Отладка**, введите **Параметры**и нажмите кнопку **Готово**.

    ![Щелкните * * Отладка * *](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. После успешного выполнения конвейера отобразятся результаты, аналогичные приведенным в следующем примере:

    ![Просмотр результатов](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Дальнейшие действия

- [Знакомство с Фабрикой данных Azure](introduction.md)