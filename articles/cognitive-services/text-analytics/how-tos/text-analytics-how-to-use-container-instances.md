---
title: Запуск службы "экземпляры контейнеров Azure" — Анализ текста
titleSuffix: Azure Cognitive Services
description: Разверните контейнеры Text Analytics в экземпляре контейнера Azure и протестируйте его в веб-браузере.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: be43d04672dcefe368eb4052b4d1a929e25327ab
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009949"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Развертывание контейнера Анализ текста в службе "экземпляры контейнеров Azure"

Узнайте, как развернуть контейнер [Анализ текста][install-and-run-containers] Cognitive Services в службе " [экземпляры контейнеров][container-instances]Azure". Эта процедура обладает создание ресурса Анализ текста, создание связанного анализ тональности образа и возможность выполнить это согласование двух из браузера. Использование контейнеров может повлечь за собой внимание разработчиков от управления инфраструктурой до того, чтобы сосредоточиться на разработке приложений.

## <a name="prerequisites"></a>Предварительные требования

* Используйте подписку Azure. Если у вас нет подписки Azure, перед началом работы [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[извлечение ключевых фраз](#tab/keyphrase);

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[распознавание языка](#tab/language);

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[анализ тональности](#tab/sentiment);

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[Анализ текста для сферы здравоохранения](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>Следующие шаги 

* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../../cognitive-services-container-support.md)
* Использование [подключенной службы анализ текста](../index.yml)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances