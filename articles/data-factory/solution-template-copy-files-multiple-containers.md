---
title: Копирование файлов из нескольких контейнеров
description: Узнайте, как использовать шаблон решения для копирования файлов из нескольких контейнеров с помощью фабрики данных Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 35eff70c12e6f98fa74a4180bf82a369c1ecfaa4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927706"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Копирование файлов из нескольких контейнеров с помощью Фабрики данных Azure

В этой статье описывается шаблон решения, который можно использовать для копирования файлов из нескольких контейнеров между хранилищами файлов. Например, с его помощью можно перенести Data Lake из AWS S3 в Azure Data Lake Store. Кроме того, можно использовать шаблон для репликации всех данных из одной учетной записи хранения BLOB-объектов Azure в другую.

> [!NOTE]
> Если вы хотите скопировать файлы из одного контейнера, более эффективно использовать [средство копирование данных](copy-data-tool.md) для создания конвейера с одним действием копирования. Шаблон в этой статье больше, чем требуется для этого простого сценария.

## <a name="about-this-solution-template"></a>Информация о шаблоне решения

Этот шаблон перечисляет контейнеры из исходного хранилища хранилища. Затем он копирует эти контейнеры в целевое хранилище.

Шаблон содержит три действия.
- **Метаданные** проверяют хранилище исходного хранилища и получают список контейнеров.
- **Foreach** получает список контейнеров из действия "операции с **метаданными** ", а затем выполняет итерацию по списку и передает каждый контейнер в действие копирования.
- **Copy** копирует каждый контейнер из исходного хранилища в целевое хранилище.

Шаблон определяет два параметра.
- *SourceFilePath* — это путь к хранилищу источника данных, в котором можно получить список контейнеров. В большинстве случаев путь указывает на корневой каталог с несколькими папками контейнеров. По умолчанию этот параметр имеет значение `/`.
- *Дестинатионфилепас* — это путь, по которому файлы будут скопированы в целевое хранилище. По умолчанию этот параметр имеет значение `/`.

## <a name="how-to-use-this-solution-template"></a>Использование шаблона решения

1. Перейдите к шаблону **копирование нескольких файлов между хранилищами файлов** . Создайте **новое** подключение к исходному хранилищу. В исходном хранилище хранилища необходимо скопировать файлы из нескольких контейнеров из.

    ![Создание нового подключения к источнику](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Создайте **новое** подключение к целевому хранилищу хранилища.

    ![Создание нового подключения к целевому хранилищу](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Выберите **Использовать этот шаблон**.

    ![Использование шаблона](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Вы увидите конвейер, как показано в следующем примере:

    ![Отображение конвейера](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Выберите **Отладка**, введите **Параметры**и нажмите кнопку **Готово**.

    ![Запуск конвейера](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Просмотрите результаты.

    ![Просмотр результатов](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Полное копирование из базы данных с помощью таблицы элементов управления и фабрики данных Azure](solution-template-bulk-copy-with-control-table.md)

- [Копирование файлов из нескольких контейнеров с помощью фабрики данных Azure](solution-template-copy-files-multiple-containers.md)