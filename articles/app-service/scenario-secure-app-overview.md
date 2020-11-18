---
title: Руководство. Создание безопасного веб-приложения в Службе приложений Azure | Azure
description: В этом руководстве показано, как создать веб-приложение с помощью Службы приложений Azure, включить проверку подлинности, вызвать службу хранилища Azure и вызвать Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428433"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Руководство. Включение проверки подлинности в Службе приложений и получение доступа к хранилищу и Microsoft Graph

В этом руководстве описан типичный сценарий работы с приложением:

- [(A) Настройка проверки подлинности для веб-приложения](scenario-secure-app-authentication-app-service.md) и ограничение доступа для пользователей в организации.
- [(Б) Безопасный доступ к службе хранилища Azure](scenario-secure-app-access-storage.md) от имени веб-приложения с помощью управляемых удостоверений.
- (C) Доступ к данным в Microsoft Graph от имени [выполнившего вход пользователя](scenario-secure-app-access-microsoft-graph-as-user.md) или [веб-приложения](scenario-secure-app-access-microsoft-graph-as-app.md) с помощью управляемых удостоверений.
- [Очистка ресурсов](scenario-secure-app-clean-up-resources.md), созданных для работы с этим руководством.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Сценарии использования приложений с платформой удостоверений Майкрософт" border="false":::

Чтобы начать работу, узнайте, как включить проверку подлинности для веб-приложения.

> [!div class="nextstepaction"]
> [Настройка проверки подлинности для веб-приложения](scenario-secure-app-authentication-app-service.md)
