---
title: Пример сценария Azure CLI. Создание преобразования | Документация Майкрософт
description: Используйте сценарий Azure CLI для создания преобразования.
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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 7fea8d997d25d6075e30620e7fd3527003c6a8bb
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128791"
---
# <a name="cli-example-create-a-transform"></a>Пример использования CLI. Создайте преобразование.

В этой статье показано, как создать преобразование с помощью сценария Azure CLI. Преобразования описывают простой рабочий процесс задач для обработки видео- и аудиофайлов (часто называются "рецептом"). Всегда проверяйте, существует ли уже "рецепт" и преобразование с нужным именем. Если да, используйте их.

## <a name="prerequisites"></a>Предварительные требования 

[Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Вы можете указать путь к пользовательскому JSON-файлу предустановок кодировщика ценовой категории "Стандартный" в параметре [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) (см. пример [кодирования с помощью пользовательского преобразования](custom-preset-cli-howto.md)).
>
> При использовании [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) нельзя передать имя файла.

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Дополнительная информация

[az ams transform (CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
