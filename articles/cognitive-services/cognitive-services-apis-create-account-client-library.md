---
title: Создание ресурса Cognitive Services с помощью клиентской библиотеки управления Azure
titleSuffix: Azure Cognitive Services
description: Сведения о том, как создать ресурсы Cognitive Services и управлять ими с помощью клиентской библиотеки управления Azure.
services: cognitive-services
keywords: Cognitive Services, когнитивня аналитика, когнитивные решения, службы ИИ
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 1fd748a0184c1718ac9450aaca3e2db1a185051a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368956"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Краткое руководство. Создание ресурса Cognitive Services с помощью клиентской библиотеки управления Azure

В этом кратком руководстве описано, как создать ресурсы Cognitive Services и управлять ими с помощью клиентской библиотеки управления Azure.

Azure Cognitive Services — это облачные службы с REST API и пакетами SDK клиентских библиотек, которые помогают разработчикам без опыта работы со средствами искусственного интеллекта (ИИ) и обработки и анализа данных создавать когнитивные интеллектуальные приложения. С помощью Azure Cognitive Services разработчики могут без усилий добавлять в свои приложения когнитивные функции, создавая когнитивные решения, которые могут видеть, слышать, говорить, понимать и даже в некоторой степени размышлять.

Отдельные службы ИИ представлены [ресурсами](../azure-resource-manager/management/manage-resources-portal.md) Azure, которые вы создаете в своей подписке Azure. После создания ресурса вы можете использовать созданные ключи и конечную точку для аутентификации приложений.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end