---
title: Запуск контейнера лиц в службе "экземпляры контейнеров Azure"
titleSuffix: Azure Cognitive Services
description: Разверните контейнер Face в экземпляре контейнера Azure и протестируйте его в веб-браузере.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 5fbe316580cfa2ca280a2587536df037146e8d02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86538128"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Развертывание контейнера "лицо" в службе "экземпляры контейнеров Azure"

> [!IMPORTANT]
> Достигнут предел для пользователей контейнера Распознавания лиц. Сейчас мы не принимаем новые приложения для контейнера Распознавания лиц.

Узнайте, как развернуть контейнер Cognitive Services [Face](../face-how-to-install-containers.md) в службе " [экземпляры контейнеров](https://docs.microsoft.com/azure/container-instances/)Azure". Эта процедура демонстрирует создание ресурса лица Azure. Затем мы обсудим извлечение связанного образа контейнера. Наконец, мы выделим возможность выполнить согласование двух из браузера. Использование контейнеров может повлечь за собой внимание разработчиков от управления инфраструктурой до того, чтобы сосредоточиться на разработке приложений.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]