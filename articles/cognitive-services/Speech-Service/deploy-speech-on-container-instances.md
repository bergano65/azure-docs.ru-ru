---
title: Запустите экземпляры контейнеров Azure
titleSuffix: Azure Cognitive Services
description: Развертывание контейнера службы распознавания речи в экземпляре контейнера Azure и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 062765be22135b12abb29ff6f7ce8a772c67adae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711519"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Развертывание контейнера службы распознавания речи в экземпляры контейнеров Azure

Дополнительные сведения о развертывании Cognitive Services [служба распознавания речи](speech-container-howto.md) контейнера в Azure [экземпляры контейнеров](https://docs.microsoft.com/azure/container-instances/). Эта процедура демонстрирует создание ресурса Azure служба распознавания речи. Затем мы обсудим, получение образа связанного контейнера. Наконец рассматривается возможность выполнять оркестрацию двух из браузера. С помощью контейнеров можно сместить внимание разработчиков от управления инфраструктурой, чтобы вместо этого сосредоточиться на разработке приложений.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Сначала выполните и отправить [форму запроса контейнеры речи Cognitive Services](https://aka.ms/speechcontainerspreview/) чтобы запросить доступ к контейнеру. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech Service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create an Speech Service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
