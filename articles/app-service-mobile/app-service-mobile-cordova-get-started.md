---
title: Создание мобильного приложения Cordova в службе мобильных приложений Azure | Документация Майкрософт
description: Изучите этот учебник, чтобы начать работу с серверными частями мобильных приложений Azure для разработки программ Apache Cordova.
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
tags: ''
keywords: cordova, javascript, мобильный, клиент
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a1baa26fee46a4bba5db9652cf1e65e9d473284a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388825"
---
# <a name="create-an-apache-cordova-app"></a>Создание приложения Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Центр приложений Visual Studio поддерживает комплексные и интегрированные службы, необходимые для разработки мобильных приложений. Разработчики могут использовать службы **Build**, **Test** и **Distribute** для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения разработчики могут отслеживать его состояние и использование с помощью служб **Analytics** и **Diagnostics**, а также взаимодействовать с пользователями с помощью службы **Push**. Разработчики также могут использовать **Auth** для реализации проверки подлинности пользователей и службу **Data** для хранения и синхронизации данных приложений в облаке.
>
> Если вы хотите интегрировать облачные службы с мобильным приложением, зарегистрируйтесь в [Центре приложений](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) сегодня.

## <a name="overview"></a>Краткое описание
В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение для платформы Apache Cordova с помощью серверной части мобильного приложения Azure.  Мы создадим серверную часть мобильного приложения и простое приложение *списка задач* (на платформе Apache Cordova), которое хранит свои данные в Azure.

Выполнение инструкций из этого учебника необходимо для работы с другими учебниками по Apache Cordova, посвященными использованию функции мобильных приложений в службе приложений Azure.

## <a name="prerequisites"></a>Технические условия
Для работы с данным руководством вам потребуется:

* компьютер с [Visual Studio Community 2017] или более поздней версии;
* [Инструменты Visual Studio для Apache Cordova];
* [Активная учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/).

Можно также обойтись без Visual Studio и использовать командную строку Apache Cordova напрямую.  Эта командная строка пригодится, если вы работаете с данным руководством на компьютере Mac.  Компиляция клиентских приложений Apache Cordova с помощью командной строки в этом учебнике не рассматривается.

## <a name="create-an-azure-mobile-app-backend"></a>Создание серверной части мобильного приложения Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Создание подключения к базе данных и настройка клиентского и серверного проекта
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Загрузка и запуск приложения Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Инструменты Visual Studio для Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
