---
title: Примеры кода — Content Moderator, .NET
description: Использование Content Moderator в приложениях .NET с помощью пакета SDK
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 43aa1ca624bce78fa113c34fa19da9ccfea69bbc
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222105"
---
# <a name="content-moderator-net-sdk-samples"></a>Примеры использования пакета SDK для .NET с Content Moderator

Ниже приведен список ссылок на примеры кода, созданные с помощью пакета SDK Azure Content Moderator для .NET.

- **Вспомогательная библиотека**. [Создание клиента Content Moderator для использования в других примерах](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). См. [краткое руководство](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Модерация

- **Модерация изображений**. [Оценка изображения на наличие содержимого для взрослых, неприличного содержимого, текста и лиц](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). См. [краткое руководство](image-moderation-quickstart-dotnet.md).
- **Пользовательские изображения**. [Модерация с использованием пользовательских списков изображений](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). См. [краткое руководство](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Существует максимальное ограничение в **5 списков изображений**, каждый из которых может содержать **не более 10 000 изображений**.
>

- **Модерация текста**. [Проверка текста на наличие ненормативной лексики и персональных данных (PII)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). См. [краткое руководство](text-moderation-quickstart-dotnet.md).
- **Настраиваемые термины**. [Модерация с помощью настраиваемых списков терминов](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). См. [краткое руководство](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Существует максимальное ограничение в **5 списков терминов**, каждый из которых может содержать **не более 10 000 терминов**.
>

- **Модерация видео**. [Сканирование видео на наличие содержимого для взрослых и непристойного содержимого и получение результатов](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). См. [краткое руководство](video-moderation-api.md).

## <a name="review"></a>Проверка

- **Задания модерации изображений**. [Запуск задания модерации, проверяющего содержимое и создающего проверки](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). См. [краткое руководство](moderation-jobs-quickstart-dotnet.md).
- **Проверки изображений**. [Создание проверок для рассмотрения человеком](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). См. [краткое руководство](moderation-reviews-quickstart-dotnet.md).
- **Проверки видео**. [Создание проверок видео для рассмотрения человеком](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). См. [краткое руководство](video-reviews-quickstart-dotnet.md).
- **Проверки расшифровок видео**. [Создание проверок расшифровок видео для рассмотрения человеком](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). См. [краткое руководство](video-reviews-quickstart-dotnet.md).

Все примеры для .NET можно просмотреть на странице [примеров Content Moderator для .NET на GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
