---
title: Копирование данных из нескольких контейнеров с помощью фабрики данных Azure | Документация Майкрософт
description: Сведения об использовании шаблона решения для копирования файлов из нескольких контейнеров с помощью фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543429"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Копирование файлов из нескольких контейнеров с помощью Фабрики данных Azure

В этой статье описывается шаблона решения, которые можно использовать для копирования файлов из нескольких контейнеров между хранилищами файлов. Например его можно использовать для переноса вашей Озера данных из AWS S3 в Azure Data Lake Store. Или можно использовать шаблон для репликации все, что из одной учетной записи хранилища BLOB-объектов Azure в другую.

> [!NOTE]
> Если вы хотите скопировать файлы из одного контейнера, это более эффективно использовать [в инструменте копирования данных](copy-data-tool.md) для создания конвейера с помощью отдельного действия копирования. Шаблон в этой статье больше, чем требуется для этого простой сценарий.

## <a name="about-this-solution-template"></a>Информация о шаблоне решения

Этот шаблон перечисляет контейнеры из исходного хранилища хранилища. Затем оно копирует эти контейнеры в целевое хранилище.

Шаблон состоит из трех действий.
- **GetMetadata** просматривает хранилище исходного хранилища и возвращает список контейнеров.
- **По каждому элементу** возвращает список контейнеров из **GetMetadata** действие проходит по списку и передает каждый контейнер в действии копирования.
- **Копировать** копирует каждый контейнер из исходного хранилища хранилища в целевое хранилище.

Шаблон определяет два параметра.
- *SourceFilePath* — путь к хранилище источника данных, где можно получить список контейнеров. В большинстве случаев путь указывает на корневой каталог с несколькими папками контейнеров. По умолчанию этот параметр имеет значение `/`.
- *DestinationFilePath* — это путь, где файлы будут копироваться в хранилище данных назначения. По умолчанию этот параметр имеет значение `/`.

## <a name="how-to-use-this-solution-template"></a>Использование шаблона решения

1. Перейдите к **скопировать несколько файлов контейнеров между хранилищами файлов** шаблона. Создание **New** подключения к хранилищу хранилища источника. Исходное хранилище хранилища — это место для копирования файлов из нескольких контейнеров из.

    ![Создание нового подключения к источнику](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Создание **New** подключения хранилища в целевое хранилище.

    ![Создание нового подключения к целевому хранилищу](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Выберите **этот шаблон используется**.

    ![Использовать этот шаблон](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Вы увидите конвейера, как показано в следующем примере:

    ![Отображение конвейера](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Выберите **Отладка**, введите **параметры**, а затем выберите **Готово**.

    ![Запуск конвейера](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Просмотрите результаты.

    ![Просмотр результатов](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Массовое копирование из базы данных с помощью элемента управления таблицы с помощью фабрики данных Azure](solution-template-bulk-copy-with-control-table.md)

- [Копирование данных из нескольких контейнеров с помощью фабрики данных Azure](solution-template-copy-files-multiple-containers.md)