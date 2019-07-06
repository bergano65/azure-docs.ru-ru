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
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: f55171a177dfcbebb9bc6df5ce125a8f29494946
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606297"
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
