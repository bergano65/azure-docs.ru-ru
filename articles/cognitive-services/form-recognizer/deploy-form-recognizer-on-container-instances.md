---
title: Запустите экземпляры контейнеров Azure
titleSuffix: Azure Cognitive Services
description: Развертывание контейнера распознаватель формы в экземпляре контейнера Azure и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c424465678a9989940d92910c5d288fa2fb1cab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711398"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Развертывание контейнера распознаватель формы в экземпляры контейнеров Azure

Дополнительные сведения о развертывании Cognitive Services [распознаватель формы](form-recognizer-container-howto.md) контейнера в Azure [экземпляры контейнеров](https://docs.microsoft.com/azure/container-instances/). Эта процедура демонстрирует создание ресурса Azure формы распознаватель. Затем мы обсудим, получение образа связанного контейнера. Наконец рассматривается возможность выполнять оркестрацию двух из браузера. С помощью контейнеров можно сместить внимание разработчиков от управления инфраструктурой, чтобы вместо этого сосредоточиться на разработке приложений.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Прежде всего необходимо заполнить и отправить [форму запроса доступа к контейнерам Распознавателя документов Cognitive Services](https://aka.ms/FormRecognizerRequestAccess), чтобы запросить доступ к контейнеру. При этом вы зарегистрируетесь для использования API Компьютерного зрения. Вам не нужно специально выполнять вход в систему, чтобы заполнить форму запроса на API Компьютерного зрения. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
