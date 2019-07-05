---
title: Заметки о выпуске индексатор видео служб мультимедиа Azure | Документация Майкрософт
description: Чтобы оставаться в курсе последних разработок, статья содержит последние обновления на индексатор видео служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: f1c5f43316292f17547b84d970a0f03a1a2c366f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454026"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Заметки о выпуске индексатор видео служб мультимедиа Azure

Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

* Последние выпуски.
* Известные проблемы
* Исправления ошибок
* Нерекомендуемые функции.

## <a name="june-2019"></a>Июнь 2019 г.

### <a name="video-indexer-deployed-to-japan-east"></a>Индексатор видео, развернутых в Восточной Японии

Теперь можно создать индексатор видео, в платной учетной записи в Восточной Японии.

### <a name="create-and-repair-account-api-preview"></a>Создать и восстановить учетную запись API (Предварительная версия)

Добавлен новый API, который позволяет [обновить конечную точку подключения службы мультимедиа Azure или ключ](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Улучшения обработки при передаче ошибок 

Описательное сообщение возвращается в случае неправильной настройки базовой учетной записи служб мультимедиа Azure.

### <a name="player-timeline-keyframes-preview"></a>Просмотр опорные кадры на временной шкале проигрывателя 

Теперь вы видите предварительную версию образа для каждого времени на временной шкале игрока.

### <a name="editor-semi-select"></a>Редактор полуизбирательного выбора

Теперь вы видите предварительную версию все сведения, которые выбираются в результате выбора определенной понимание интервал времени в редакторе.

## <a name="may-2019"></a>Май 2019 г.

### <a name="update-custom-language-model-from-closed-caption-file"></a>Обновление пользовательского языка модели из файла субтитров

[Создание пользовательской языковой модели](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) и [обновление пользовательских языковых моделей](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API-интерфейсы теперь поддерживают VTT SRT, и форматы файлов TTML в качестве входных данных для моделей языков.

При вызове [transcript видео обновления API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), автоматически добавляется запись. Обучение модели, связанной с видео автоматически также обновляется. Сведения о том, как настроить и обучение модели языка, см. в разделе [настроить языковую модель с помощью индексатора видео](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Новые загрузки transcript форматы — txt- и CSV

В дополнение к закрытым субтитров форматом уже поддерживается (SRT VTT и TTML) индексатор видео теперь поддерживает загрузку transcript в формате TXT и CSV.

## <a name="next-steps"></a>Дальнейшие действия

[Обзор набора средств Visual Studio для Unity](video-indexer-overview.md)
