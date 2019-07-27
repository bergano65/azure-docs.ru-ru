---
title: Устранение неполадок в пакете SDK для речевых устройств — служба речи
titleSuffix: Azure Cognitive Services
description: Эта статья содержит сведения, которые помогут решить проблемы, которые могут возникнуть при использовании пакета SDK для речевых устройств.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: 9a757402360b4b69e760315a809482be9b4e04d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558902"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Устранение неполадок пакета SDK для устройств, подключаемых к службе "Речь"

Эта статья содержит сведения, которые помогут решить проблемы, которые могут возникнуть при использовании пакета SDK для речевых устройств.

## <a name="certificate-failures"></a>Ошибки сертификата

Если при использовании речевых служб выдается ошибка сертификата, убедитесь, что устройство имеет правильные дату и время:

1. Перейдите в меню **Параметры**. В разделе **Система** выберите **Дата и время**.

    ![В разделе "Параметры" выберите "Дата и время".](media/speech-devices-sdk/qsg-12.png)

1. Оставьте параметр **Automatic date & time** (Автоматическая установка даты и времени) выбранным. В разделе **Выбор часового пояса** выберите нужный часовой пояс.

    ![Выбор параметров даты и часового пояса](media/speech-devices-sdk/qsg-13.png)

    Когда время, установленное в комплекте разработки, соответствует времени ПК, это означает, что комплект разработки подключен к Интернету.

## <a name="next-steps"></a>Следующие шаги

* [Просмотр заметки о выпуске](devices-sdk-release-notes.md)
