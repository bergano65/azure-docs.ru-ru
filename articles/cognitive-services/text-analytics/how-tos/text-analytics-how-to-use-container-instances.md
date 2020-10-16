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
ms.openlocfilehash: f7dbb79e2d9009b00b21d4c12663d8e6c855a4c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88243757"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Развертывание контейнера Анализ текста в службе "экземпляры контейнеров Azure"

Узнайте, как развернуть контейнер [Анализ текста][install-and-run-containers] Cognitive Services в службе " [экземпляры контейнеров][container-instances]Azure". Эта процедура обладает создание ресурса Анализ текста, создание связанного анализ тональности образа и возможность выполнить это согласование двух из браузера. Использование контейнеров может повлечь за собой внимание разработчиков от управления инфраструктурой до того, чтобы сосредоточиться на разработке приложений.

## <a name="prerequisites"></a>Предварительные требования

* Используйте подписку Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services), прежде чем начинать работу.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[извлечение ключевых фраз](#tab/keyphrase);

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[распознавание языка](#tab/language);

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Анализ тональности](#tab/sentiment).

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[Анализ текста для работоспособности](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>Дальнейшие шаги 

* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../../cognitive-services-container-support.md)
* Использование [подключенной службы анализ текста](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances