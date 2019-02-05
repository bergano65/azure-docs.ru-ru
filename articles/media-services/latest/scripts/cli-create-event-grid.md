---
title: Пример скрипта Azure CLI для создания подписки на службу "Сетка событий Azure" | Документация Майкрософт
description: В скрипте Azure CLI в этой статье показано, как создать подписку на службу "Сетка событий" уровня учетной записи для изменений состояния задания.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 2fcacd559db0ab143ffa7ed49bbd546785765154
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098911"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>Пример использования CLI. Создание подписки на службу Сетка событий Azure 

В скрипте Azure CLI в этой статье показано, как создать подписку на службу "Сетка событий" уровня учетной записи для изменений состояния задания.

## <a name="prerequisites"></a>Предварительные требования 

[Создание учетной записи Служб мультимедиа](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Дополнительная информация

См. [другие примеры Azure CLI](../cli-samples.md).
