---
title: Подготовьте веб-АПЫ для мобильных приложений для производства (ru) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как создать мобильное приложение, которое вызывает web-апоттестаты. (Подготовьте приложения для производства.)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1ea19b8b76f4eb4a2c984f0e39eb0fd373c8b83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132387"
---
# <a name="prepare-mobile-apps-for-production"></a>Подготовка мобильных приложений для производства

В этой статье приводится подробная информация о том, как улучшить качество и надежность вашего мобильного приложения, прежде чем переместить его в производство.

## <a name="handle-errors"></a>Обработка ошибок

При подготовке мобильного приложения к производству может возникнуть несколько ошибок. Основными случаями, с которыми вы будете заниматься, являются молчаливые сбои и откаты к взаимодействию. Другие условия, которые следует учитывать, включают ситуации без сети, перебои в обслуживании, требования к согласию админа и другие случаи, связанные с сценарием.

Для каждого типа Библиотеки аутентификации Майкрософт (MSAL) можно найти пример кода и содержимого вики, описывающие, как обрабатывать условия ошибки:

- [MSAL Android вики](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS вики](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET вики](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Смягчение и изучение проблем

Чтобы лучше диагностировать проблемы в приложении, соберите данные. Для получения информации о видах данных, которые вы можете собрать, [см.](https://docs.microsoft.com/azure/active-directory/develop/msal-logging)

Вот несколько советов по сбору данных:

- Пользователи могут обратиться за помощью, когда у них возникли проблемы. Наилучшей практикой является захват и временное хранение журналов. Предоставьте место, где пользователи могут загружать журналы. MSAL предоставляет расширения для регистрации для сбора подробной информации об аутентификации.

- Если телеметрия доступна, включите ее через MSAL для сбора данных о том, как пользователи вписываются в ваше приложение.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Чтобы опробовать дополнительные образцы, смотрите [настольные и мобильные приложения для публичных клиентов.](sample-v2-code.md#desktop-and-mobile-public-client-apps)
