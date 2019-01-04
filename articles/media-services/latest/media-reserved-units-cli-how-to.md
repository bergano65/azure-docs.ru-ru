---
title: Использование CLI для масштабирования зарезервированных единиц кодирования — Azure | Документация Майкрософт
description: В этом разделе показано, как использовать CLI для масштабирования обработки мультимедиа с использованием служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2b10de83e00b3668f70461f76634c560bcbea1a4
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133797"
---
# <a name="scaling-media-processing"></a>Масштабирование при обработке мультимедиа

Службы мультимедиа Azure позволяют масштабировать обработку мультимедиа в учетной записи, управляя зарезервированными единицами мультимедиа (MRU). Подробный обзор см. в разделе [Обработка масштабирования мультимедиа](../previous/media-services-scale-media-processing-overview.md). 

В этой статье показано, как с помощью [интерфейса командной строки Служб мультимедиа версии 3](https://aka.ms/ams-v3-cli-ref) масштабировать MRU.

> [!NOTE]
> Для заданий анализа аудио и видео, которые активируют Службы мультимедиа версии 3 или Индексатор видео, настоятельно рекомендуется подготовить к работе вашу учетную запись с помощью 10 единиц MRU S3. <br/>Если вам требуется более 10 единиц MRU S3, отправьте запрос в службу поддержки с помощью [портала Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Предварительные требования 

- Установите и используйте CLI на локальном компьютере. Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

    Сейчас в Azure Cloud Shell работают не все команды [интерфейса командной строки Служб мультимедиа версии 3](https://aka.ms/ams-v3-cli-ref). Рекомендуется использовать интерфейс командной строки локально.

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Масштабирование зарезервированных единиц кодирования с помощью интерфейса командной строки

Следующая команда [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) настраивает зарезервированные единицы мультимедиа в учетной записи amsaccount с использованием параметров **count** и **type**.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Выставление счетов

Плата взимается за число MRU, их тип и период времени, в течение которого MRU инициируются в вашей учетной записи. Плата взимается независимо от запуска любого задания. Подробное описание см. в разделе часто задаваемых вопросов на странице [цен на службы мультимедиа](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Дальнейшие действия

[Анализ видео](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>См. также

[Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
