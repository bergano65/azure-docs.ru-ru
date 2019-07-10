---
title: Запустите экземпляры контейнеров Azure
titleSuffix: Azure Cognitive Services
description: Развертывание контейнеров analytics текста с учетом образа анализа тональности в экземпляр контейнера Azure и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9f174d54fcc74eed613eb69412bc0e515f15897b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711728"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>Развертывание контейнера анализ тональности в экземпляры контейнеров Azure

Дополнительные сведения о развертывании Cognitive Services [текстовая аналитика](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) контейнера с помощью образа анализ тональности в Azure [экземпляры контейнеров](https://docs.microsoft.com/azure/container-instances/). Эта процедура служит отличным примером создания ресурса анализа текста, создания связанное изображение анализ тональности, а также для выполнения этой оркестрацией двух из браузера. С помощью контейнеров можно сместить внимание разработчиков от управления инфраструктурой, чтобы вместо этого сосредоточиться на разработке приложений.

## <a name="prerequisites"></a>Технические условия

* Используйте подписку Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Дальнейшие действия 

* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../../cognitive-services-container-support.md)
* Используйте [текстовая аналитика подключенной службы](../vs-text-connected-service.md)
