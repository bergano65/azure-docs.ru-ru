---
title: Добавление push-уведомлений в iOS
description: Узнайте, как использовать мобильные приложения Azure для отправки push-уведомлений в приложение iOS.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ad58ee158a2e1842c1b41db281bdd9f04b9b2ca4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461510"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Добавление push-уведомлений в приложение iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

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
