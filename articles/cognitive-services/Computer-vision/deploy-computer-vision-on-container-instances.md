---
title: Запуск Компьютерное зрение контейнера в службе "экземпляры контейнеров Azure"
titleSuffix: Azure Cognitive Services
description: Разверните контейнер Компьютерное зрение в экземпляре контейнера Azure и протестируйте его в веб-браузере.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b734f54f39b0f442f398a60ad25a846b0f9a6322
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2020
ms.locfileid: "96021815"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Развертывание контейнера Компьютерное зрение в службе "экземпляры контейнеров Azure"

Узнайте, как развернуть контейнер [Компьютерное зрение](computer-vision-how-to-install-containers.md) Cognitive Services в службе " [экземпляры контейнеров](../../container-instances/index.yml)Azure". Эта процедура демонстрирует создание Компьютерное зрение ресурса. Затем мы обсудим извлечение связанного образа контейнера. Наконец, мы выделим возможность выполнить согласование двух из браузера. Использование контейнеров может повлечь за собой внимание разработчиков от управления инфраструктурой до того, чтобы сосредоточиться на разработке приложений.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]