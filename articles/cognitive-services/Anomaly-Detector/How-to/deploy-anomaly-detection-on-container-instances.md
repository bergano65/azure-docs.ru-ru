---
title: Запустите контейнер детектор аномалий в экземплярах контейнеров Azure
titleSuffix: Azure Cognitive Services
description: Развернуть контейнер Anomaly Detector в экземпляр контейнера Azure и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716348"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Развертывание контейнера детектора аномалий в экземплярах контейнеров Azure

Узнайте, как развернуть контейнер [детектор аномалий](../anomaly-detector-container-howto.md) когнитивных служб в [контейнерных инстанциях](https://docs.microsoft.com/azure/container-instances/)Azure. Эта процедура демонстрирует создание ресурса детектора аномалий. Затем мы обсуждаем потянув связанных изображения контейнера. Наконец, мы подчеркиваем возможность осуществлять оркестровку двух из браузера. Использование контейнеров может сместить внимание разработчиков от управления инфраструктурой к сосредоточению внимания на разработке приложений.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

Сначала необходимо заполнить и отправить [форму запроса на контейнер Anomaly Detector,](https://aka.ms/adcontainer) чтобы запросить доступ к контейнеру.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Дальнейшие действия

* Просмотрите [установку и запуск контейнеров](../anomaly-detector-container-configuration.md) для вытягивания изображения контейнера и запуска контейнера
* Ознакомьтесь со статьей о [конфигурации контейнеров](../anomaly-detector-container-configuration.md).
* [Подробнее об услуге API Anomaly Detector API](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
