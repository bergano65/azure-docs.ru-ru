---
title: Развертывание контейнера LUIS в экземплярах контейнера Azure
titleSuffix: Azure Cognitive Services
description: Развернуть контейнер LUIS в экземпляр Azure Container и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 30fd19634f6054b8b636dabcb4ef83b118554468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75689453"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Развертывание контейнера для понимания языка (LUIS) в экземплярах контейнеров Azure

Узнайте, как развернуть контейнер Cognitive Services [LUIS](luis-container-howto.md) в [экземплярах Контейнера](https://docs.microsoft.com/azure/container-instances/)Azure. Эта процедура демонстрирует создание ресурса детектора аномалий. Затем мы обсуждаем потянув связанных изображения контейнера. Наконец, мы подчеркиваем возможность осуществлять оркестровку двух из браузера. Использование контейнеров может сместить внимание разработчиков от управления инфраструктурой к сосредоточению внимания на разработке приложений.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-an-azure-file-share"></a>создать файловый ресурс Azure;

Контейнер LUIS требует `.gz` файла модели, который втягивается во время выполнения. Контейнер должен иметь доступ к файлу этой модели через объемное крепление из экземпляра Контейнера. Для получения информации о создании [create a file share](../../storage/files/storage-how-to-create-file-share.md)общего файла Azure см. Обратите внимание на имя учетной записи, ключ и имя файла, которое понадобится позже.

### <a name="export-and-upload-packaged-luis-app"></a>Экспорт и загрузка упакованное приложение LUIS

Для того, чтобы загрузить модель LUIS (упакованное приложение) в раздел файлов Azure, сначала необходимо <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">экспортировать ее с портала <span class="docon docon-navigate-external x-hidden-focus"> </span>LUIS. </a> С портала Azure перейдите на страницу **Обзора** ресурса учетной записи хранилища и выберите **доли файлов.** Выберите имя файла общего доступа, которое вы недавно создали, а затем выберите кнопку **Загрузка.**

> [!div class="mx-imgBorder"]
> ![Загрузка в файл общего обмена](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Загрузите файл модели LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
