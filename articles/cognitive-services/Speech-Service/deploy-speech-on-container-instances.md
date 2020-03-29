---
title: Выполнить Azure Контейнер Instances - Речевой сервис
titleSuffix: Azure Cognitive Services
description: Развернуть контейнер службы speech в экземпляр контейнера Azure и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: e5d9143b0c84b579945b283ccae1cce36a7a4291
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717409"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Развертывание контейнера службы речи в экземплярах контейнеров Azure

Узнайте, как развернуть контейнер [службы](speech-container-howto.md) когнитивных служб в [контейнерных инстанциях](https://docs.microsoft.com/azure/container-instances/)Azure. Эта процедура демонстрирует создание ресурса службы Azure Speech. Затем мы обсуждаем потянув связанных изображения контейнера. Наконец, мы подчеркиваем возможность осуществлять оркестровку двух из браузера. Использование контейнеров может сместить внимание разработчиков от управления инфраструктурой к сосредоточению внимания на разработке приложений.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Сначала необходимо заполнить и отправить форму запроса на доступ к контейнеру [Cognitive Services Speech Containers.](https://aka.ms/speechcontainerspreview/) 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
