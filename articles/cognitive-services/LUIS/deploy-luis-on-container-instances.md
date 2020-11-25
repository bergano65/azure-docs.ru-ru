---
title: Развертывание контейнера LUIS в службе "экземпляры контейнеров Azure"
titleSuffix: Azure Cognitive Services
description: Разверните контейнер LUIS в экземпляре контейнера Azure и протестируйте его в веб-браузере.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: edc2ad0f895b8a1bb6448ce1cdf79b1b2ce83951
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95997207"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Развертывание контейнера Language Understanding (LUIS) в службе "экземпляры контейнеров Azure"

Узнайте, как развернуть контейнер Cognitive Services [Luis](luis-container-howto.md) в службе " [экземпляры контейнеров](../../container-instances/index.yml)Azure". Эта процедура демонстрирует создание ресурса детектора аномалий. Затем мы обсудим извлечение связанного образа контейнера. Наконец, мы выделим возможность выполнить согласование двух из браузера. Использование контейнеров может повлечь за собой внимание разработчиков от управления инфраструктурой до того, чтобы сосредоточиться на разработке приложений.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>создать файловый ресурс Azure;

Для контейнера LUIS требуется `.gz` файл модели, который извлекается во время выполнения. Контейнер должен иметь возможность доступа к этому файлу модели через подключение тома из экземпляра контейнера. Сведения о создании файлового ресурса Azure см. в разделе [Создание общей папки](../../storage/files/storage-how-to-create-file-share.md). Запишите имя учетной записи хранения Azure, ключ и имя общей папки, так как они понадобятся вам позже.

### <a name="export-and-upload-packaged-luis-app"></a>Экспорт и передача упакованного приложения LUIS

Чтобы передать модель LUIS (упакованное приложение) в общую папку Azure, <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">сначала <span class="docon docon-navigate-external x-hidden-focus"></span> необходимо экспортировать ее с портала Luis </a>. На портал Azure перейдите на страницу **Обзор** ресурса учетной записи хранения и выберите **файловые ресурсы**. Выберите имя общей папки, которое вы недавно создали, а затем нажмите кнопку **Отправить** .

> [!div class="mx-imgBorder"]
> ![Отправить в общую папку](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Отправьте файл модели LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]