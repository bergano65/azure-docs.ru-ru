---
title: Запуск контейнера детекторов аномалий в службе "экземпляры контейнеров Azure"
titleSuffix: Azure Cognitive Services
description: Разверните контейнер детектора аномалий в экземпляре контейнера Azure и протестируйте его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 52d2e3ed59a7ad71177f34909e37ce885d603297
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515271"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Развертывание контейнера детекторов аномалий в службе "экземпляры контейнеров Azure"

Узнайте, как развернуть контейнер [детектора Cognitive Services аномалии](../anomaly-detector-container-howto.md) в службе " [экземпляры контейнеров](https://docs.microsoft.com/azure/container-instances/)Azure". Эта процедура демонстрирует создание ресурса детектора аномалий. Затем мы обсудим извлечение связанного образа контейнера. Наконец, мы выделим возможность выполнить согласование двух из браузера. Использование контейнеров может повлечь за собой внимание разработчиков от управления инфраструктурой до того, чтобы сосредоточиться на разработке приложений.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

Сначала необходимо завершить и отправить [форму запроса контейнера детектора аномалий](https://aka.ms/adcontainer) , чтобы запросить доступ к контейнеру.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Дальнейшие действия

* Проверьте раздел [Установка и запуск контейнеров](../anomaly-detector-container-configuration.md) , чтобы получить образ контейнера и запустить контейнер.
* Ознакомьтесь со статьей о [конфигурации контейнеров](../anomaly-detector-container-configuration.md).
* [Дополнительные сведения о службе API детектора аномалий](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
