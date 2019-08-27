---
title: Запуск службы "экземпляры контейнеров Azure" — Анализ текста
titleSuffix: Azure Cognitive Services
description: Разверните контейнеры Text Analytics в экземпляре контейнера Azure и протестируйте его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 35812ec4585e3189282c5acf0aa09d309c33c7ed
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051934"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Развертывание контейнера Анализ текста в службе "экземпляры контейнеров Azure"

Узнайте, как развернуть контейнер [Анализ текста][install-and-run-containers] Cognitive Services в службе " [экземпляры контейнеров][container-instances]Azure". Эта процедура обладает создание ресурса Анализ текста, создание связанного анализ тональности образа и возможность выполнить это согласование двух из браузера. Использование контейнеров может повлечь за собой внимание разработчиков от управления инфраструктурой до того, чтобы сосредоточиться на разработке приложений.

## <a name="prerequisites"></a>Предварительные требования

* Используйте подписку Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Извлечение ключевых фраз](#tab/keyphrase).

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[Распознавание языка](#tab/language).

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Анализ тональности](#tab/sentiment).

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Следующие шаги 

* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../../cognitive-services-container-support.md)
* Использование [подключенной службы анализ текста](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances