---
title: Запустите экземпляры контейнеров Azure
titleSuffix: Text Analytics - Azure Cognitive Services
description: Развертывание контейнеров analytics текста с учетом образа анализа тональности в экземпляр контейнера Azure и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a82b5a1cbed662289d3a406a61fdd19a3ca8861b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454986"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances-aci"></a>Развертывание контейнера анализ тональности для экземпляры контейнеров Azure (ACI)

Дополнительные сведения о развертывании Cognitive Services [текстовая аналитика](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) контейнера с помощью образа анализ тональности в Azure [экземпляры контейнеров](https://docs.microsoft.com/azure/container-instances/) (ACI). Эта процедура служит отличным примером создания ресурса анализа текста, создания связанное изображение анализ тональности, а также для выполнения этой оркестрацией двух из браузера. С помощью контейнеров можно сместить внимание разработчиков от управления инфраструктурой, чтобы вместо этого сосредоточиться на разработке приложений.

## <a name="prerequisites"></a>Технические условия

* Используйте подписку Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](../../containers/includes/create-aci-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Дальнейшие действия 

* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../../cognitive-services-container-support.md)
* Используйте [текстовая аналитика подключенной службы](../vs-text-connected-service.md)
