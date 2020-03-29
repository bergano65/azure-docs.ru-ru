---
title: Запуск контейнера компьютерного зрения в экземплярах контейнеров Azure
titleSuffix: Azure Cognitive Services
description: Развернуть контейнер Computer Vision в azure Container Instance и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 1c858432a3382e6dbc8e479aab11b18dc5eebfe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499084"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Развертывание контейнера компьютерного зрения в экземпляры контейнеров Azure

Узнайте, как развернуть контейнер Cognitive Services [Computer Vision](computer-vision-how-to-install-containers.md) в [экземплярах контейнеров](https://docs.microsoft.com/azure/container-instances/)Azure. Эта процедура демонстрирует создание ресурса Computer Vision. Затем мы обсуждаем потянув связанных изображения контейнера. Наконец, мы подчеркиваем возможность осуществлять оркестровку двух из браузера. Использование контейнеров может сместить внимание разработчиков от управления инфраструктурой к сосредоточению внимания на разработке приложений.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]