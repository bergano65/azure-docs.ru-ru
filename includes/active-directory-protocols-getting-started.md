---
title: Обзор протокола .NET Azure AD | Документация Майкрософт
description: Использование HTTP-сообщений для авторизации доступа к веб-приложениям и веб-API в клиенте с использованием Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523677"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Регистрация приложения в клиенте AD
Сначала зарегистрируйте приложение в клиенте Azure Active Directory (Azure AD). После этого вашему приложению будет присвоен идентификатор, и оно сможет получать маркеры.

1. Войдите на [портал Azure](https://portal.azure.com).
   
1. Выберите свой клиент Azure AD, выбрав свою учетную запись в правом верхнем углу страницы, а затем выбрав параметр Навигация по **каталогу** и выбрав нужный клиент. 
   - Пропустите этот шаг, если у вас есть только один клиент Azure AD в вашей учетной записи или если вы уже выбрали соответствующий клиент Azure AD.
   
1. В портал Azure найдите и выберите **Azure Active Directory**.
   
1. В меню **Azure Active Directory** слева выберите **Регистрация приложений**, а затем нажмите кнопку **создать регистрацию**.
   
1. Следуйте инструкциям на экране, а затем создайте новое приложение. Не имеет значения, является ли это веб-приложением или общедоступным клиентским приложением (мобильным & Desktop) для этого руководства, но если вы хотите получить конкретные примеры для веб-приложений или общедоступных клиентских приложений, ознакомьтесь с нашими [краткими руководствами](../articles/active-directory/develop/v1-overview.md).
   
   - **Имя** — это имя приложения, которое описывает его для конечных пользователей.
   - В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт).
   - Укажите **URI перенаправления**. Для веб-приложений это базовый URL-адрес приложения, в котором пользователи могут входить в систему.  Пример: `http://localhost:12345`. Для общедоступного клиента (Mobile & Desktop) Azure AD использует его для возврата ответов маркеров. Укажите значение, специфичное для вашего приложения.  Пример: `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. После завершения регистрации Azure AD присвоит приложению уникальный идентификатор клиента ( **идентификатор приложения**). Это значение вам понадобится в следующих разделах, поэтому не забудьте скопировать его на странице приложения.
   
1. Чтобы найти приложение в портал Azure, выберите **Регистрация приложений**, а затем выберите **Просмотреть все приложения**.
