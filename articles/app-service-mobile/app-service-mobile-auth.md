---
title: Проверка подлинности и авторизация в службе приложений Azure для мобильных приложений | Документация Майкрософт
description: Принципы использования и обзор функции проверки подлинности и авторизации в службе приложений Azure, в частности для мобильных приложений
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 87bdfcc827155e5dd0a02ffb1640bf7e9cd4e479
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859130"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Проверка подлинности и авторизация в службе приложений Azure для мобильных приложений

В этой статье описано, как работает проверка подлинности и авторизация при разработке собственных мобильных приложений для серверной части службы приложений. Служба приложений предлагает встроенную проверку подлинности и авторизацию, что позволяет организовать вход пользователей в мобильные приложения, не изменяя код службы приложений. Она является простым способом обеспечения защиты приложения и работы с данными пользователей. 

Эта статья посвящена разработке мобильных приложений. Если вы хотите быстро приступить к использованию проверки подлинности и авторизации службы приложений для мобильного приложения, ознакомьтесь с одним из руководств, посвященных [проверке подлинности в приложении iOS][iOS], [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] или [Cordova]. 

Дополнительные сведения о работе проверки подлинности и авторизации в службе приложений см. в [этой статье](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Проверка подлинности с пакетом SDK поставщика

Выполнив все настройки в службе приложений, подготовьте мобильные клиенты для входа в службу приложений. Существует два подхода:

* использование пакета SDK, опубликованного указанным поставщиком удостоверений, для определения удостоверения и последующего получения доступа к службе приложений;
* использование одной строки кода для входа пользователей с помощью пакета SDK клиента для мобильных приложений.

> [!TIP]
> Для большинства приложений следует использовать пакет SDK поставщика, чтобы обеспечить согласованное взаимодействие с пользователями во время входа, поддерживать обновление маркеров и ряд других преимуществ, обеспечиваемых поставщиком.
> 
> 

При использовании пакета SDK поставщика пользователи могут входить в интерфейс, который теснее интегрируется с операционной системой, в которой запущено приложение. Этот метод предоставит вам маркер поставщика и некоторые сведения о пользователях на стороне клиента, что упрощает использование API Graph и настройку пользовательского интерфейса. Иногда в блогах и на форумах этот подход называют "клиентской последовательностью" или "управляемой клиентом последовательностью", так как клиентский код управляет процессом входа и имеет доступ к маркеру поставщика.

Полученный маркер поставщика необходимо отправить в службу приложений для проверки. После того как служба приложений проверит маркер, она создает новый маркер службы приложений, который возвращается клиенту. Пакет SDK для клиента мобильных приложений содержит вспомогательные методы, которые управляют этим обменом и автоматически присоединяют маркеры ко всем запросам к серверной части приложения. Разработчики также могут сохранить ссылку на маркер поставщика.

Дополнительные сведения см. в разделе [о последовательности проверки подлинности службы приложений](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Аутентификация без пакета SDK поставщика

Вместо настройки пакета SDK можно настроить выполнение входа с помощью функции мобильных приложений службы приложений Azure. Клиентский пакет SDK мобильных приложений будет открывать выбранному вами поставщику веб-представление и осуществлять процедуру входа. Иногда в блогах и на форумах этот подход называется "серверной последовательностью" или "управляемой сервером последовательностью", так как процедурой входа управляет сервер, а пакет SDK клиента никогда не получает маркер поставщика.

Код, необходимый для запуска этого потока, рассматривается в руководстве по проверке подлинности для каждой платформы. В конце потока у клиентского пакета SDK есть маркер службы приложений, и маркер автоматически присоединяется ко всем запросам, отправляемым к серверной части приложения.

Дополнительные сведения см. в разделе [о последовательности проверки подлинности службы приложений](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Дополнительные ресурсы

В следующих руководствах показано, как добавить проверку подлинности в мобильные клиенты с использованием [управляемого сервером потока](../app-service/overview-authentication-authorization.md#authentication-flow).

* [Добавление проверки подлинности в приложение iOS][iOS]
* [Добавление проверки подлинности в приложение Android][Android]
* [Добавление проверки подлинности в приложение Windows][Windows]
* [Добавление проверки подлинности в приложение Xamarin.Android][Xamarin.iOS]
* [Добавление проверки подлинности в приложение Xamarin.Android][Xamarin.Android]
* [Добавление проверки подлинности в приложение Xamarin.iOS][Xamarin.Forms]
* [Добавление проверки подлинности в приложение Cordova][Cordova]

Если нужно использовать [управляемый клиентом поток](../app-service/overview-authentication-authorization.md#authentication-flow) для Azure Active Directory, см. следующие ресурсы:

* [Использование клиентской библиотеки iOS для мобильных приложений Azure][ADAL-iOS]
* [Использование клиентской библиотеки Android для мобильных приложений][ADAL-Android]
* [Использование управляемого клиента для мобильных приложений Azure][ADAL-dotnet]

Если нужно использовать [управляемый клиентом поток](../app-service/overview-authentication-authorization.md#authentication-flow) для Facebook, см. следующий раздел.

* [Практическое руководство: проверка подлинности пользователей с помощью пакета SDK Facebook для iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Если нужно использовать [управляемый клиентом поток](../app-service/overview-authentication-authorization.md#authentication-flow) для Twitter, см. следующий раздел.

* [Практическое руководство: проверка подлинности пользователей с помощью структуры Twitter для iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Если нужно использовать [управляемый клиентом поток](../app-service/overview-authentication-authorization.md#authentication-flow) для Google, см. следующий раздел.

* [How to: Authenticate users with the Google Sign-In SDK for iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
