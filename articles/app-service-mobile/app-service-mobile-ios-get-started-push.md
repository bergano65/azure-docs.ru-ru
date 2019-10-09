---
title: Добавление push-уведомлений в приложение iOS с помощью мобильных приложений Azure
description: Узнайте, как использовать мобильные приложения Azure для отправки push-уведомлений в приложение iOS.
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: ''
author: elamalani
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 7a037cfb411eb3c15f60bb6a8763374d9102bc4e
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027321"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Добавление push-уведомлений в приложение iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Центр приложений Visual Studio поддерживает комплексные и интегрированные службы для разработки мобильных приложений. Разработчики могут использовать службы **сборки**, **тестирования** и **распространения** для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения разработчики могут отслеживать состояние и использование своих приложений с помощью служб **аналитики** и **диагностики** , а также привлекать пользователей с помощью службы **push-уведомлений** . Разработчики также **могут использовать проверку подлинности** для аутентификации пользователей и службы **данных** , чтобы сохранять и синхронизировать данные приложений в облаке.
> Если вы хотите интегрировать облачные службы в мобильное приложение, зарегистрируйтесь в центре приложений Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) уже сегодня.

## <a name="overview"></a>Обзор

В этом руководстве мы добавим push-уведомления в проект по [Быстрый запуск iOS], чтобы при каждом добавлении новой записи на устройство отправлялось push-уведомление.

Если вы не используете скачанный проект сервера, необходимо добавить пакет расширений для push-уведомлений. Дополнительные сведения см. в руководстве [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

[Симулятор iOS не поддерживает push-уведомления](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Требуется физическое устройство iOS и [участие в программе для разработчиков на платформе Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Настройка центра уведомлений

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Регистрация приложения для работы с push-уведомлениями

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Настройка Azure для отправки push-уведомлений

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Обновление серверной части для отправки push-уведомлений

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Добавление push-уведомлений в приложение

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Тестирование push-уведомлений

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Дополнительные сведения

* Шаблоны обеспечивают гибкость при отправке push-уведомлений локально и между различными платформами. [Использование клиентской библиотеки iOS для мобильных приложений Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) описано, как регистрировать шаблоны.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Быстрый запуск iOS]: app-service-mobile-ios-get-started.md
