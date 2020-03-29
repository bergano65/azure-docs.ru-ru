---
title: Выполнить Azure контейнера Instances - Текстовая аналитика
titleSuffix: Azure Cognitive Services
description: Развертывание контейнеров для анализа текста в Azure Container Instance и тестирование в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383534"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Развертывание контейнера текстовых аналитиков в экземплярах контейнеров Azure

Узнайте, как развернуть контейнер [«Текстовая аналитика][install-and-run-containers] когнитивных служб» в [экземплярах контейнеров][container-instances]Azure. Эта процедура является примером создания ресурса Text Analytics, создания связанного изображения анализа настроений и способности осуществлять эту оркестровку двух из браузера. Использование контейнеров может сместить внимание разработчиков от управления инфраструктурой к сосредоточению внимания на разработке приложений.

## <a name="prerequisites"></a>Предварительные требования

* Используйте подписку Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Извлечение ключевых фраз](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Распознавание языка](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Анализ настроений](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Дальнейшие действия 

* Используйте больше [контейнеров когнитивных услуг](../../cognitive-services-container-support.md)
* Использование [службы связи с текстовыми аналитиками](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances