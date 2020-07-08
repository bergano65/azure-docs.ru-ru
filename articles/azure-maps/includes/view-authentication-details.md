---
title: Просмотр сведений о проверке подлинности Azure Maps
description: Используйте портал Azure, чтобы просмотреть сведения о проверке подлинности Azure Maps.
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9146844a6b83f78ad99ef7cd1aec4b028daf3ff6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988432"
---
Сведения о проверке подлинности учетной записи Azure Maps можно просмотреть в портал Azure. В учетной записи в меню **Параметры** выберите **Проверка подлинности**.

![Подробные сведения о проверке подлинности](../media/how-to-manage-authentication/how-to-view-auth.png)

После создания учетной записи Azure Maps на `x-ms-client-id` странице сведений о проверке подлинности портал Azure отображается значение Azure Maps. Это значение представляет учетную запись, которая будет использоваться для запросов REST API. Это значение должно храниться в конфигурации приложения и извлекаться перед выполнением HTTP-запросов при использовании проверки подлинности Azure AD с Azure Maps.
