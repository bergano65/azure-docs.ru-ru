---
title: Краткое руководство. Настройка платформы для пакета SDK службы "Речь" для C# (Unity) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить платформу для C# (Unity) с пакетом SDK для службы "Речь".
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 6fa1024b68bae6a8353cc71f2749b221ab7e3bc4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504406"
---
Здесь также описано, как установить [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для [Unity](https://unity3d.com/).

> [!NOTE]
> Пакет SDK службы "Речь" для Unity поддерживает Windows Desktop (x86 и x64) или универсальную платформу Windows (x86, x64, ARM и ARM64), Android (x86, ARM32/64) и iOS (симулятор x64, ARM32 и ARM64).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

- [Unity 2018.3 или последующей версии](https://store.unity.com/). В [Unity 2019.1 добавлена поддержка UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Можно также использовать Visual Studio 2017 версии 15.9 или более поздней.
- Для поддержки Windows ARM64 установите [дополнительные инструменты сборки для ARM64 и пакет SDK Windows 10 для ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Чтобы установить пакет SDK службы "Речь" для Unity, выполните следующие действия:

1. Скачайте и откройте [пакет SDK службы "Речь" для Unity](https://aka.ms/csspeech/unitypackage), который упакован как пакет ресурсов Unity (.unitypackage) и должен быть уже связан с Unity. При открытии пакета ресурса появляется диалоговое окно **импорта пакета Unity**. Чтобы этот шаг получился, может потребоваться создать и открыть пустой проект.

   [![Диалоговое окно импорта пакета Unity в Unity Editor](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Убедитесь, что выбраны все файлы, и нажмите кнопку **Import** (Импортировать). Через некоторое время пакет ресурса Unity будет импортирован в проект.

Дополнительные сведения об импорте пакетов ресурсов в Unity см. в [документации по Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

Теперь можно перейти к разделу [Дополнительная информация](#next-steps) ниже.

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [windows](../quickstart-list.md)]
