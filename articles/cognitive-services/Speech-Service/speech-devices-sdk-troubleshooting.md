---
title: Troubleshoot речевых устройств SDK - Речевая служба
titleSuffix: Azure Cognitive Services
description: В этой статье содержится информация, которая поможет вам решить проблемы, с которыми вы можете столкнуться при использовании SDK-устройств речи.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815560"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Устранение неполадок пакета SDK для устройств, подключаемых к службе "Речь"

В этой статье содержится информация, которая поможет вам решить проблемы, с которыми вы можете столкнуться при использовании SDK-устройств речи.

## <a name="certificate-failures"></a>Ошибки сертификата

Если при использовании службы speech вы получаете сбои в сертификате, убедитесь, что устройство имеет правильную дату и время:

1. Перейдите в **Параметры**. В разделе **Система** выберите **Дата и время**.

    ![В разделе "Параметры" выберите "Дата и время".](media/speech-devices-sdk/qsg-12.png)

1. Оставьте параметр **Automatic date & time** (Автоматическая установка даты и времени) выбранным. В разделе **Выбор часового пояса** выберите нужный часовой пояс.

    ![Выбор параметров даты и часового пояса](media/speech-devices-sdk/qsg-13.png)

    Когда время, установленное в комплекте разработки, соответствует времени ПК, это означает, что комплект разработки подключен к Интернету.

## <a name="next-steps"></a>Дальнейшие действия

* [Просмотр заметки о выпуске](devices-sdk-release-notes.md)
