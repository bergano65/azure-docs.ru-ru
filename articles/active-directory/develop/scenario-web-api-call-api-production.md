---
title: Перемещение веб-API вызова веб-API в производство - Microsoft идентификационная платформа (ru) Azure
description: Узнайте, как переместить веб-aPI, который вызывает веб-API в производство.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d557c70c09824f3aead41a654dc92c4ec46e9238
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885061"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Веб-aPI, который вызывает веб-API: Переход к производству

После приобретения маркера для вызова web-аПО можно перевести приложение в рабочее время.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Дополнительные сведения

Теперь, когда вы знаете основы вызова web-aPI с вашего собственного веб-API, вы можете быть заинтересованы в следующем учебнике, который описывает код, который используется для создания защищенного веб-aPI, который вызывает web API.

| Образец | Платформа | Описание |
|--------|----------|-------------|
| [активный каталог-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API, Desktop (WPF) | ASP.NET Core 2.2 Web API называет Microsoft Graph, который вы вызываете из приложения WPF с помощью платформы идентификации Майкрософт (v2.0). |
