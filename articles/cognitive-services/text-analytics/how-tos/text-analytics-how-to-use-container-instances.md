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
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383534"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Развертывание контейнера Анализ текста в службе "экземпляры контейнеров Azure"

Узнайте, как развернуть контейнер [Анализ текста][install-and-run-containers] Cognitive Services в службе " [экземпляры контейнеров][container-instances]Azure". Эта процедура обладает создание ресурса Анализ текста, создание связанного анализ тональности образа и возможность выполнить это согласование двух из браузера. Использование контейнеров может повлечь за собой внимание разработчиков от управления инфраструктурой до того, чтобы сосредоточиться на разработке приложений.

## <a name="prerequisites"></a>предварительным требованиям

* Используйте подписку Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/) , прежде чем начинать работу.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Извлечение ключевых фраз](#tab/keyphrase).

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[Распознавание языка](#tab/language).

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Анализ тональности](#tab/sentiment).

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Дополнительная информация 

* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../../cognitive-services-container-support.md)
* Использование [подключенной службы анализ текста](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances