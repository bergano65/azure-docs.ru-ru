---
title: Краткое руководство. Настройка платформы для пакета SDK службы "Речь" для C# (Xamarin) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить платформу для использования C# (Xamarin) с пакетом SDK службы "Речь",
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c941f0c96c127a46bb0b888d0500223ebcd9be2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551309"
---
а также как установить [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для [Xamarin](/xamarin/get-started/what-is-xamarin), платформы с открытым кодом, позволяющей создавать современные производительные приложения для iOS, Android и Windows с .NET. Если вам нужно только имя пакета, чтобы приступить к работе самостоятельно, выполните `Install-Package Microsoft.CognitiveServices.Speech` в консоли NuGet.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* В Windows для вашей платформы необходим [распространяемый компонент Microsoft Visual C++ для Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0). При первой установке может потребоваться перезагрузка.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Создание проекта Visual Studio и установка пакета SDK службы "Речь"

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

Теперь пакет SDK для службы "Речь" установлен. Вы можете удалить или повторно использовать проект helloworld, созданный на предыдущих шагах.

Теперь можно перейти к разделу [Дополнительная информация](#next-steps) ниже.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [windows](../quickstart-list.md)]