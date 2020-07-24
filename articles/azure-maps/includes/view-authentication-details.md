---
title: Просмотр сведений о проверке подлинности Azure Maps
description: Используйте портал Azure, чтобы просмотреть сведения о проверке подлинности Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6ffcbf5a8c36958dd3ea74de4d826fe25a1139c
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126467"
---
Сведения о проверке подлинности учетной записи Azure Maps можно просмотреть в портал Azure. В учетной записи в меню **Параметры** выберите **Проверка подлинности**.

![Подробные сведения о проверке подлинности](../media/how-to-manage-authentication/how-to-view-auth.png)

После создания учетной записи Azure Maps на `x-ms-client-id` странице сведений о проверке подлинности портал Azure отображается значение Azure Maps. Это значение представляет учетную запись, которая будет использоваться для запросов REST API. Это значение должно храниться в конфигурации приложения и извлекаться перед выполнением HTTP-запросов при использовании проверки подлинности Azure AD с Azure Maps.
