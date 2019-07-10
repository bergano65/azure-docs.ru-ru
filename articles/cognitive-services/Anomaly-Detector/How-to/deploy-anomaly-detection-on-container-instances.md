---
title: Запустите экземпляры контейнеров Azure
titleSuffix: Azure Cognitive Services
description: Развертывание контейнера обнаружения аномалий в экземпляре контейнера Azure и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b5adc3ed9234050d3977e812202717a0ce83e842
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711695"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Развертывание контейнера обнаружения аномалий экземпляры контейнеров Azure

Дополнительные сведения о развертывании Cognitive Services [обнаружения аномалий](../anomaly-detector-container-howto.md) контейнера в Azure [экземпляры контейнеров](https://docs.microsoft.com/azure/container-instances/). Эта процедура демонстрирует создание ресурса обнаружения аномалий. Затем мы обсудим, получение образа связанного контейнера. Наконец рассматривается возможность выполнять оркестрацию двух из браузера. С помощью контейнеров можно сместить внимание разработчиков от управления инфраструктурой, чтобы вместо этого сосредоточиться на разработке приложений.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

Сначала выполните и отправить [формы запроса на контейнер обнаружения аномалий](https://aka.ms/adcontainer) чтобы запросить доступ к контейнеру.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]