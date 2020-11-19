---
title: Пример сценария Azure CLI. Создание преобразования | Документация Майкрософт
description: Преобразования описывают простой рабочий процесс задач для обработки видео- и аудиофайлов (его часто называют "рецептом"). В этой статье показано, как создать преобразование с помощью сценария Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ffab4eaf63844057a3872730f91634cb2df5669
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918220"
---
# <a name="create-a-transform"></a>Создание преобразования

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

В этой статье показано, как создать преобразование с помощью сценария Azure CLI. Преобразования описывают простой рабочий процесс задач для обработки видео- и аудиофайлов (его часто называют "рецептом"). Всегда проверяйте, существует ли уже "рецепт" и преобразование с нужным именем. Если да, используйте их.

## <a name="prerequisites"></a>Предварительные требования

[Создание учетной записи Служб мультимедиа](./create-account-howto.md).

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Вы можете указать путь к пользовательскому JSON-файлу предустановок кодировщика ценовой категории "Стандартный" в параметре [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) (см. пример [кодирования с помощью пользовательского преобразования](custom-preset-cli-howto.md)).
>
> При использовании [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) нельзя передать имя файла.

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-transform-rest.md)]

---

## <a name="next-steps"></a>Следующие шаги

[Дополнительные сведения о преобразованиях и заданиях](transforms-jobs-concept.md)
