---
title: Копирование файлов из нескольких контейнеров
description: Узнайте, как использовать шаблон решения для копирования файлов из нескольких контейнеров с помощью Azure Data Factory.
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
ms.openlocfilehash: 0c4c26ba163f83483b3eb48e51d91f9a919a887c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439760"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Копирование файлов из нескольких контейнеров с помощью Фабрики данных Azure

В этой статье описывается шаблон решения, который можно использовать для копирования файлов из нескольких контейнеров между файлами. Например, его можно использовать для переноса озера данных из AWS S3 в Azure Data Lake Store. Или можно использовать шаблон, чтобы воспроизвести все, от одной учетной записи хранения Azure Blob до другой.

> [!NOTE]
> Если вы хотите скопировать файлы из одного контейнера, более эффективно использовать [инструмент копирования данных](copy-data-tool.md) для создания конвейера с одной копией. Шаблон в этой статье больше, чем вам нужно для этого простого сценария.

## <a name="about-this-solution-template"></a>Информация о шаблоне решения

Этот шаблон перечисляет контейнеры из хранилища исходного хранилища. Затем он копирует эти контейнеры в магазин назначения.

Шаблон состоит из трех действий.
- **GetMetadata** сканирует ваш магазин хранения исходного хранилища и получает список контейнеров.
- **ForEach** получает список контейнеров из деятельности **GetMetadata,** а затем итерирует список и передает каждый контейнер в действие Copy.
- **Копирование** копий каждого контейнера из хранилища исходного хранилища в магазин назначения.

Ниже описаны параметры, которые определяет шаблон.
- *SourceFileFolder* — это путь папки в хранилище источников данных, где можно получить список контейнеров. Путь представляет собой корневой каталог, содержащий несколько папок контейнера. Значение параметра по умолчанию — `sourcefolder`.
- *SourceFileDirectory* — это путь субфолдера под корневым каталогом хранилища исходных данных. Значение параметра по умолчанию — `subfolder`.
- *DestinationFileFolder* — это путь папки, по которому файлы будут скопированы в магазине назначения. Значение параметра по умолчанию — `destinationfolder`.
- *DestinationFileDirectory* — это путь субфолдера, по которому файлы будут скопированы в магазине назначения. Значение параметра по умолчанию — `subfolder`.

## <a name="how-to-use-this-solution-template"></a>Использование шаблона решения

1. Перейдите к копии нескольких контейнеров файлов между шаблоном **Хранилиэкземпляр.** Создайте **новое** соединение с хранилищем исходного хранилища. Хранилище исходного хранилища — это место, где требуется копировать файлы из нескольких контейнеров.

    ![Создание нового подключения к источнику](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Создайте **новое** соединение с хранилищем хранилища.

    ![Создание нового подключения к целевому хранилищу](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Выберите **Использовать этот шаблон**.

    ![Использовать этот шаблон](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Вы увидите конвейер, как в следующем примере:

    ![Отображение конвейера](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Выберите **отключать,** введите **параметры,** а затем выберите **Finish.**

    ![Запуск конвейера](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Просмотрите результаты.

    ![Просмотр результатов](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Массовая копия из базы данных с помощью таблицы управления с Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Копирование файлов из нескольких контейнеров с помощью Фабрики данных Azure](solution-template-copy-files-multiple-containers.md)
