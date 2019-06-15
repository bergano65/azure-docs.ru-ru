---
title: Устранение проблем с устройствами Speech SDK — службы распознавания речи
titleSuffix: Azure Cognitive Services
description: В этой статье сведения по устранению проблем, могут возникнуть при использовании пакета SDK для устройств речи.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: wellsi
ms.openlocfilehash: 87fb35f06dcb1d1e3fb8c3ae3be64c7448162f14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65026160"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Устранение неполадок пакета SDK для устройств, подключаемых к службе "Речь"

В этой статье сведения по устранению проблем, могут возникнуть при использовании пакета SDK для устройств речи.

## <a name="certificate-failures"></a>Ошибки сертификата

Если сбои сертификата при использовании служб речи, убедитесь, что устройство имеет правильные дату и время:

1. Перейдите в меню **Параметры**. В разделе **Система** выберите **Дата и время**.

    ![В разделе "Параметры" выберите "Дата и время".](media/speech-devices-sdk/qsg-12.png)

1. Оставьте параметр **Automatic date & time** (Автоматическая установка даты и времени) выбранным. В разделе **Выбор часового пояса** выберите нужный часовой пояс.

    ![Выбор параметров даты и часового пояса](media/speech-devices-sdk/qsg-13.png)

    Когда время, установленное в комплекте разработки, соответствует времени ПК, это означает, что комплект разработки подключен к Интернету.

## <a name="next-steps"></a>Дальнейшие действия

* [Просмотр заметки о выпуске](devices-sdk-release-notes.md)
