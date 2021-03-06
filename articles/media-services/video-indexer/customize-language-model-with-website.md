---
title: Настройка языковой модели с помощью веб-сайта Индексатора видео в Azure
titleSuffix: Azure Media Services
description: В этой статье описана настройка языковой модели с помощью веб-сайта Индексатора видео.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 5d92cb02a0ac52b317cf9d4b6c8e0278f9291910
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838457"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Настройка языковой модели с помощью веб-сайта Индексатора видео

Индексатор видео позволяет создавать пользовательские языковые модели для настройки распознавания речи путем передачи текста адаптации, а именно текста из предметной области, к словарю которой вы хотите адаптировать подсистему. Как только вы обучите свою модель, новые слова, появляющиеся в тексте адаптации, будут распознаваться. 

Подробные сведения и рекомендации для пользовательских языковых моделей см. в [этой статье](customize-language-model-overview.md).

Вы можете использовать веб-сайт Индексатора видео для создания и изменения пользовательских языковых моделей в своей учетной записи, как описано в этом разделе. Кроме того, вы можете применять API, как описано в [этой статье](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Создание языковой модели

1. Откройте веб-сайт [Индексатора видео](https://www.videoindexer.ai/) и выполните вход.
2. Чтобы настроить модель в учетной записи, в правом верхнем углу страницы нажмите кнопку **Content model customization** (Настройка модели содержимого).

   ![Настройка модели содержимого](./media/content-model-customization/content-model-customization.png)

3. Выберите вкладку **Язык**.

    Откроется список поддерживаемых языков. 

    ![Настройка языковой модели](./media/customize-language-model/customize-language-model.png)

4. Под нужным языком щелкните **Добавить модель**.
5. Введите имя языковой модели и нажмите клавишу ВВОД.

    Будет создана модель, и у вас появится возможность передавать текстовые файлы в модель.
6. Чтобы добавить текстовый файл, щелкните **Добавить файл**. Откроется проводник.

7. Перейдите к текстовому файлу и выберите его. Вы можете добавить несколько текстовых файлов в языковую модель.

    Вы также можете добавить текстовый файл, нажав кнопку **...** справа от языковой модели и выбрав **Добавить файл**.
8. После передачи текстовых файлов щелкните кнопку **Обучить** зеленого цвета.

    ![Обучение языковой модели](./media/customize-language-model/train-model.png)

Процесс обучения может занять несколько минут. По завершении рядом с моделью появится значение состояния **Trained** (Обученная). Вы можете просматривать, скачивать и удалять файл из модели.

![Обученная языковая модель](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Использование языковой модели в новом видео

Чтобы применить языковую модель к новому видео, выполните одно из следующих действий:

* Нажмите кнопку **Передать** в верхней части страницы. 

    ![Отправить](./media/customize-language-model/upload.png)
* Поместите аудио- или видеофайл в круг или же перейдите к своему файлу.

    ![Отправить](./media/customize-language-model/upload2.png)

Это даст вам возможность выбрать **исходный язык видео**. Щелкните раскрывающийся список и выберите созданную языковую модель. В ней должны быть указаны язык вашей языковой модели и заданное имя в скобках.

Щелкните параметр **Передать** в нижней части страницы, и ваше новое видео будет проиндексировано с использованием вашей языковой модели.

### <a name="using-a-language-model-to-reindex"></a>Повторная индексация с помощью языковой модели

Чтобы использовать языковую модель для повторной индексации видео в вашей коллекции, перейдите в раздел **Account videos** (Видеозаписи в учетной записи) на домашней странице [Индексатора видео](https://www.videoindexer.ai/) и наведите указатель мыши на название нужного видео.

Вы увидите параметры для изменения, удаления и повторной индексации вашего видео. Щелкните соответствующий параметр, чтобы выполнить повторную индексацию видеозаписи.

![Повторная индексация](./media/customize-language-model/reindex1.png)

Вам нужно будет выбрать **исходный язык видео**, который будет использоваться для повторной индексации. Щелкните раскрывающийся список и выберите созданную языковую модель. В ней должны быть указаны язык вашей языковой модели и заданное имя в скобках.

![Повторная индексация](./media/customize-language-model/reindex.png)

Нажмите кнопку **Re-index** (Повторная индексация), и видео будет повторно индексировано с использованием вашей языковой модели.

## <a name="edit-a-language-model"></a>Изменение языковой модели

Вы можете отредактировать языковую модель, изменив ее имя, добавив в нее файлы и удалив их из нее.

Если вы добавляете или удаляете файлы из языковой модели, вам нужно будет снова обучить модель, нажав зеленую кнопку **Обучить**.

### <a name="rename-the-language-model"></a>Переименование языковой модели

Вы можете изменить имя языковой модели, нажав кнопку **...** справа от модели и выбрав **Переименовать**. 

Введите новое имя и нажмите клавишу ВВОД.

### <a name="add-files"></a>Добавление файлов

Чтобы добавить текстовый файл, щелкните **Добавить файл**. Откроется проводник.

Перейдите к текстовому файлу и выберите его. Вы можете добавить несколько текстовых файлов в языковую модель.

Вы также можете добавить текстовый файл, нажав кнопку **...** справа от языковой модели и выбрав **Добавить файл**.

### <a name="delete-files"></a>Удаление файлов

Чтобы удалить файл из языковой модели, нажмите кнопку **...** справа от текстового файла и выберите **Удалить**. Откроется новое окно, информирующее о том, что удаление невозможно будет отменить. Щелкните **Удалить** в новом окне.

Это действие полностью удалит файл из языковой модели.

## <a name="delete-a-language-model"></a>Удаление языковой модели

Чтобы удалить языковую модель из учетной записи, нажмите кнопку **...** справа от модели и выберите **Удалить**.

Откроется новое окно, информирующее о том, что удаление невозможно будет отменить. Щелкните **Удалить** в новом окне.

Это действие полностью удалит языковую модель из вашей учетной записи. Любое видео, в котором использовалась удаленная языковая модель, будет хранить тот же индекс, пока вы не выполните повторную индексацию. При повторной индексации вы можете назначить новую языковую модель для видео. В противном случае для повторной индексации видео Индексатор видео будет использовать модель по умолчанию. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Настройка языковых моделей путем исправления записей

Индексатор видео поддерживает автоматическую настройку языковых моделей на основе реальных исправлений, внесенных пользователями в транскрипции.

1. Чтобы внести исправления в запись, откройте видео, которое требуется изменить, из видео своей учетной записи. Перейдите на вкладку **временная шкала** .

    ![Настройка языковой модели](./media/customize-language-model/timeline.png)
1. Щелкните значок карандаша, чтобы изменить запись транскрипции. 

    ![Настройка языковой модели](./media/customize-language-model/edits.png)

    Индексатор видео захватывает все строки, исправленные вами, в транскрипции видео и автоматически добавляет их в текстовый файл с именем "из изменений в записи". Эти изменения используются для повторного обучения конкретной языковой модели, которая использовалась для индексирования этого видео. 
    
    Если вы не указали языковую модель при индексировании этого видео, все изменения для этого видео будут храниться в языковой модели по умолчанию с именем адаптация учетных записей на обнаруженном языке видео. 
    
    Если в одну и ту же строку внесено несколько изменений, для обновления языковой модели будет использоваться только последняя версия исправленной строки.  
    
    > [!NOTE]
    > Для настройки используются только текстовые исправления. Это означает, что исправления, не затрагивающие фактических слов (например, знаки пунктуации и пробелы), не включаются. 
    
1. Исправления будут отображаться на вкладке язык страницы Настройка модели содержимого.

    ![Настройка языковой модели](./media/customize-language-model/customize.png)

   Чтобы просмотреть файл "от изменения записи в журнал" для каждой модели языка, щелкните его, чтобы открыть. 

    ![От изменений в транскрипции](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Дальнейшие действия

[Customize a Language model with the Video Indexer APIs](customize-language-model-with-api.md) (Настройка языковой модели с помощью API Индексатора видео)
