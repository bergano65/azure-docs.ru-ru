---
title: Выполнить сервис Azure Kubernetes - Текстовая аналитика
titleSuffix: Azure Cognitive Services
description: Развернуть изображение контейнера Text Analytics в службу Azure Kubernetes и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 3264ec5a83277e6bb4befad46cd1337175e911c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383507"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Развертывание контейнера текстовых аналитиков в службе Azure Kubernetes

Узнайте, как развернуть изображение контейнера [для текстовых аналитиков](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) Azure Cognitive Services в службе Azure Kubernetes (AKS). Эта процедура показывает, как создать ресурс Text Analytics, как создать связанное изображение анализа настроений, и как осуществить эту оркестровку двух из браузера. Использование контейнеров может сместить ваше внимание с управления инфраструктурой на вместо этого сосредоточиться на разработке приложений.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этой процедуры необходимо установить и запустить несколько средств локально. Не используйте облачную оболочку Azure. Кроме этого, вам потребуются:

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
* Текстовый редактор, например, [Visual Studio Code](https://code.visualstudio.com/download).
* Установлен [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) установлен.
* Ресурс Azure с правильной ценовой категорией. Не все ценовые категории поддерживают этот контейнер.
    * Ресурс **Azure Text Analytics** с только уровнями ценообразования F0 или стандартными.
    * Ресурс **Azure Cognitive Services** с уровнем ценообразования S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Извлечение ключевых фраз](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Распознавание языка](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Анализ настроений](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Дальнейшие действия

* Используйте больше [контейнеров Cognitive Services](../../cognitive-services-container-support.md)
* Использование [службы связи с текстовыми аналитиками](../vs-text-connected-service.md)
