---
title: Заметки о выпуске индексатора видео служб мультимедиа Azure | Документация Майкрософт
description: Чтобы оставаться в курсе последних разработок, в этой статье содержатся последние обновления индексатора видео служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: fd67e867b5eac58838551ac29e1fca5e860b15ff
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414215"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Заметки о выпуске индексатора видео служб мультимедиа Azure

Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

* Последние выпуски.
* Известные проблемы
* Исправления ошибок
* Нерекомендуемые функции.

## <a name="june-2019"></a>Июнь 2019 г.

### <a name="video-indexer-deployed-to-japan-east"></a>Индексатор видео, развернутый на японском Востоке

Теперь вы можете создать платную учетную запись индексатора видео в регионе "Восточная Япония".

### <a name="create-and-repair-account-api-preview"></a>Создание и восстановление API учетных записей (Предварительная версия)

Добавлен новый API, позволяющий [обновить конечную точку или ключ подключения к службе мультимедиа Azure](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Улучшение обработки ошибок при передаче 

Описательное сообщение возвращается в случае невыполнения настройки базовой учетной записи служб мультимедиа Azure.

### <a name="player-timeline-keyframes-preview"></a>Предварительный просмотр ключевых кадров временной шкалы проигрывателя 

Теперь можно просмотреть предварительный просмотр образа на временной шкале проигрывателя.

### <a name="editor-semi-select"></a>Частичное выделение в редакторе

Теперь вы можете просмотреть все аналитические сведения, выбранные в результате выбора определенного периода анализа в редакторе.

## <a name="may-2019"></a>Май 2019 г.

### <a name="update-custom-language-model-from-closed-caption-file"></a>Обновление пользовательской языковой модели из файла закрытых субтитров

[Создание пользовательской языковой модели](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) и [Обновление пользовательских языковых моделей](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API теперь поддерживают форматы файлов ВТТ, SRT и TTML в качестве входных для языковых моделей.

При вызове [API обновления видеозаписи запись](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)автоматически добавляется. Модель обучения, связанная с видео, также обновляется автоматически. Сведения о настройке и обучении языковых моделей см. в разделе [Настройка языковой модели с помощью индексатора видео](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Новые форматы записи для скачивания — TXT и CSV

В дополнение к уже поддерживаемому формату субтитров (SRT, ВТТ и TTML), индексатор видео теперь поддерживает загрузку записи в форматах TXT и CSV.

## <a name="next-steps"></a>Следующие шаги

[Обзор](video-indexer-overview.md)
