---
title: Запуск контейнера распознавания форм в экземплярах контейнеров Azure
titleSuffix: Azure Cognitive Services
description: Развернуть контейнер распознавания форм в экземпляр контейнера Azure и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 9a3456eb4d30aa8d163488f558b571dd97c73bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605117"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Развертывание контейнера распознавания форм в экземплярах контейнеров Azure

Узнайте, как развернуть контейнер [распознавания когнитивных](form-recognizer-container-howto.md) служб в [контейнерных инстанциях](https://docs.microsoft.com/azure/container-instances/)Azure. Эта процедура демонстрирует создание ресурса распознавания форм Azure. Затем мы обсуждаем потянув связанных изображения контейнера. Наконец, мы подчеркиваем возможность осуществлять оркестровку двух из браузера. Использование контейнеров может сместить внимание разработчиков от управления инфраструктурой к сосредоточению внимания на разработке приложений.

> [!IMPORTANT]
> Контейнеры распознавания форм в настоящее время используют версию 1.0 API распознавания форм. Вы можете получить доступ к последней версии API, используя управляемую службу.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Прежде всего необходимо заполнить и отправить [форму запроса доступа к контейнерам Распознавателя документов Cognitive Services](https://aka.ms/FormRecognizerContainerRequestAccess), чтобы запросить доступ к контейнеру. При этом вы зарегистрируетесь для использования API Компьютерного зрения. Вам не нужно специально выполнять вход в систему, чтобы заполнить форму запроса на API Компьютерного зрения. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
