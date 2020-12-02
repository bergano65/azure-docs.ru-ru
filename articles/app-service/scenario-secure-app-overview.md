---
title: Руководство. Создание безопасного веб-приложения в Службе приложений Azure | Azure
description: Из этого руководства вы узнаете, как создать веб-приложение с помощью Службы приложений Azure, включить проверку подлинности, вызвать службу хранилища Azure и вызвать Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ef7782c68746d4c20df5a9ae5e27ffca3e60efbe
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024132"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Руководство по Включение проверки подлинности в Службе приложений и получение доступа к хранилищу и Microsoft Graph

В этом руководстве описан типичный сценарий работы с приложением:

- [Настройка проверки подлинности для веб-приложения](scenario-secure-app-authentication-app-service.md) и ограничение доступа для пользователей в организации (A на схеме).
- [Безопасный доступ к службе хранилища Azure](scenario-secure-app-access-storage.md) от имени веб-приложения с помощью управляемых удостоверений (B на схеме).
- Доступ к данным в Microsoft Graph от имени [выполнившего вход пользователя](scenario-secure-app-access-microsoft-graph-as-user.md) или [веб-приложения](scenario-secure-app-access-microsoft-graph-as-app.md) с помощью управляемых удостоверений (C на схеме).
- [Очистка ресурсов](scenario-secure-app-clean-up-resources.md), созданных для работы с этим руководством.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Схема: сценарии использования приложений на платформе удостоверений Майкрософт" border="false":::

Чтобы начать работу, узнайте, как включить проверку подлинности для веб-приложения.

> [!div class="nextstepaction"]
> [Настройка проверки подлинности для веб-приложения](scenario-secure-app-authentication-app-service.md)
