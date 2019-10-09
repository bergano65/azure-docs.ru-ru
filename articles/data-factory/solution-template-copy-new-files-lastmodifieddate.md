---
title: Копирование новых и измененных файлов с помощью LastModifiedDate с фабрикой данных Azure | Документация Майкрософт
description: Узнайте, как использовать шаблон решения для копирования новых и измененных файлов с помощью LastModifiedDate с фабрикой данных Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: a2a8f0478d1ae4fb19cb911b02572145ff59839b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030076"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Копирование новых и измененных файлов с помощью LastModifiedDate с фабрикой данных Azure

В этой статье описывается шаблон решения, который можно использовать для копирования новых и измененных файлов только с помощью LastModifiedDate из файлового хранилища в целевое хранилище. 

## <a name="about-this-solution-template"></a>Информация о шаблоне решения

Этот шаблон сначала выбирает новые и измененные файлы только по их атрибутам **LastModifiedDate**, а затем копирует выбранные файлы из хранилища источников данных в хранилище назначения данных.

Шаблон содержит одно действие:
- **Копировать** для копирования новых и измененных файлов только по LastModifiedDate из хранилища файлов в целевое хранилище.

Шаблон определяет четыре параметра:
-  *FolderPath_Source* — это путь к папке, в которой можно считывать файлы из исходного хранилища. Необходимо заменить значение по умолчанию на собственный путь к папке.
-  *FolderPath_Destination* — путь к папке, в которую необходимо скопировать файлы в целевое хранилище. Необходимо заменить значение по умолчанию на собственный путь к папке.
-  *LastModified_From* используется для выбора файлов, атрибут LastModifiedDate которых равен значению DateTime или равен ему.  Чтобы выбрать только новые файлы, которые не были скопированы в последний раз, это значение даты и времени может быть временем, когда конвейер был активирован в последний раз. Вы можете заменить значение по умолчанию "2019-02-01T00:00:00Z" ожидаемым LastModifiedDate в часовом поясе UTC. 
-  *LastModified_To* используется для выбора файлов, атрибут LastModifiedDate которых находится перед этим значением DateTime. Чтобы выбрать только новые файлы, которые не были скопированы в последний раз, это значение даты и времени может быть текущим временем.  Вы можете заменить значение по умолчанию "2019-02-01T00:00:00Z" ожидаемым LastModifiedDate в часовом поясе UTC. 

## <a name="how-to-use-this-solution-template"></a>Использование шаблона решения

1. Перейдите к шаблону **Копировать новые файлы только по LastModifiedDate**. Создайте **новое** подключение к исходному хранилищу. В исходном хранилище хранилища необходимо скопировать файлы.

    ![Создание нового подключения к источнику](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Сначала выберите **тип**хранилища. После этого введите **имя учетной записи** хранения и **ключ учетной записи**. Наконец, нажмите кнопку **Готово**.

    ![Ввод строки подключения](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Создайте **новое** подключение к целевому хранилищу. В целевом хранилище необходимо скопировать файлы. Также необходимо ввести сведения о подключении хранилища назначения данных, как показано на шаге 2.

    ![Создание нового подключения к целевому хранилищу](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Выберите **использовать этот шаблон**.

    ![Использовать этот шаблон](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. На панели откроется доступный конвейер, как показано в следующем примере:

    ![Отображение конвейера](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Выберите **Отладка**, запишите значения **параметров** и нажмите кнопку **Готово**.  На рисунке ниже мы устанавливаем параметры следующим образом.
   - **FolderPath_Source** =  **/саурце/**
   - **FolderPath_Destination** =  **/Дестинатион/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     В примере показано, что файлы, которые были изменены в интервале времени между *2019-02-01T00:00:00Z* и *2019-03-01T00:00:00Z* , будут скопированы из папки */саурце/* в папку */Дестинатион/* .  Их можно заменить собственными параметрами.
    
     ![Запуск конвейера](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Просмотрите результаты. Вы увидите, что в целевое хранилище скопированы только те файлы, которые были изменены в течение заданного интервала времени.

    ![Просмотр результатов](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Теперь можно добавить триггер Windows "переворачивающегося" для автоматизации этого конвейера, чтобы конвейер всегда мог периодически копировать новые и измененные файлы только LastModifiedDate.  Выберите **добавить триггер**и щелкните **создать или изменить**.

    ![Просмотр результатов](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. В окне **Add Triggers** (Добавление триггеров) выберите **+ Создать**.

    ![Просмотр результатов](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Выберите **окно "переворачивающегося"** для типа триггера, установите **каждые 15 минут** в качестве периодичности (можно изменить на любое время), а затем нажмите кнопку **Далее**.

    ![Создание триггера](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Запишите значения **параметров запуска триггера** , как показано ниже, и нажмите кнопку **Готово**.
    - **FolderPath_Source** =  **/саурце/** .  Вы можете заменить папку в исходном хранилище данных.
    - **FolderPath_Destination** =  **/Дестинатион/** .  Вы можете заменить папку в целевом хранилище данных.
    - **LastModified_From** =   **\@trigger (). Outputs. windowStartTime**.  Это системная переменная из триггера, определяющая время, когда конвейер был активирован в последний раз.
    - **LastModified_To** =  **\@trigger (). Outputs. windowEndTime**.  Это системная переменная из триггера, определяющая время активации конвейера на данный момент.
    
    ![Входные параметры](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Выберите **Опубликовать все**.
    
    ![Опубликовать все](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Создание новых файлов в исходной папке хранилища источников данных.  Теперь вы ожидаете автоматического запуска конвейера, и в целевое хранилище будут скопированы только новые файлы.

14. Выберите вкладку **наблюдение** на левой навигационной панели и подождите около 15 минут, если частота срабатывания триггера задана каждые 15 минут. 

    ![Выбор мониторинга](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Просмотрите результаты. Вы увидите, что конвейер будет активирован автоматически каждые 15 минут, и только новые или измененные файлы из исходного хранилища будут скопированы в целевое хранилище в каждом выполнении конвейера.

    ![Просмотр результатов](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке](introduction.md)
