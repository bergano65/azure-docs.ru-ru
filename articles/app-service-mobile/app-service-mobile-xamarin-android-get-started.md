---
title: Приступая к работе с мобильными приложениями Azure для приложений Xamarin.Android | Microsoft Azure
description: Этот учебник поможет приступить к использованию мобильных приложений Azure для разработки приложений Xamarin Android
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: eed900ee54f62056eceeb35a43a4ba6526b049ca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447091"
---
# <a name="create-a-xamarinandroid-app"></a>Создание приложения Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Центр приложений Visual Studio вкладывает средства в новые и интегрированной службы, необходимые для разработки мобильных приложений. Разработчики могут использовать **построения**, **теста** и **распределить** служб для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения, разработчики могут отслеживать состояние и использования их приложений с помощью **Analytics** и **диагностики** служб и общайтесь с пользователями, с помощью **Push** Служба. Разработчики также могут использовать **Auth** подлинность пользователей и **данных** службы для сохранения и синхронизировать данные приложения в облаке. Ознакомьтесь с [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-android-get-started) уже сегодня.
>

## <a name="overview"></a>Обзор
В этом руководстве показано, как добавить облачную серверную службу в приложение Xamarin.Android. Дополнительные сведения см. в статье [Что такое мобильные приложения?](app-service-mobile-value-prop.md).

Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными мобильным приложениям для приложений Xamarin.Android.

## <a name="prerequisites"></a>Технические условия
Для работы с данным руководством вам потребуется:

* Активная учетная запись Azure. Если у вас еще нет учетной записи, зарегистрируйтесь для использования пробной версии Azure и получите до 10 бесплатных мобильных приложений. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio с Xamarin. Инструкции см. в статье об [установке и настройке Visual Studio и Xamarin](/visualstudio/cross-platform/setup-and-install).

## <a name="create-an-azure-mobile-app-backend"></a>Создание серверной части мобильного приложения Azure
Чтобы создать серверную часть мобильного приложения, выполните указанные ниже действия.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Итак, вы подготовили серверную часть мобильного Azure, которая может использоваться мобильными клиентскими приложениями. Теперь скачайте серверный проект со списком простых задач и опубликуйте его в Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Создание подключения к базе данных и настройка проекта клиента и сервера
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Запуск приложения Xamarin.Android
1. Откройте проект Xamarin.Android.

2. Перейдите к [портала Azure](https://portal.azure.com/) и перейдите к мобильное приложение, которое вы создали. На `Overview` колонке найдите URL-адрес, который является общедоступной конечной точки для мобильного приложения. Пример - sitename для моей имя приложения «test123» будет https://test123.azurewebsites.net.

3. Откройте файл `ToDoActivity.cs` в этой папке - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs. Имя приложения — `ZUMOAPPNAME`.

4. В `ToDoActivity` класса, замените `ZUMOAPPURL` переменной с общедоступной конечной точки выше.

    `const string applicationURL = @"ZUMOAPPURL";`

    становится
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Нажмите клавишу F5, чтобы развернуть и запустить приложение.

6. В приложении введите содержательный текст, например *Работа с руководством*, и нажмите кнопку **Добавить**.

    ![][10]

    Данные из запроса вставляются в таблицу TodoItem. Элементы, сохраненные в таблице, возвращаются серверной частью мобильного приложения, а данные отображаются в списке.

   > [!NOTE]
   > Вы можете просмотреть код, который получает доступ к внутреннему серверу мобильного приложения для запроса и вставки данных, которые находятся в файле C# ToDoActivity.cs.
   
## <a name="troubleshooting"></a>Устранение неполадок
При проблемах со сборкой проекта запустите диспетчер пакетов NuGet и выполните обновление пакетов поддержки `Xamarin.Android`. Проекты быстрого запуска не всегда включают самые свежие версии.

Обратите внимание, что все пакеты поддержки, на которые содержатся ссылки в проекте Android, должны быть одной версии. [Пакет NuGet для мобильных приложений Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) имеет зависимость `Xamarin.Android.Support.CustomTabs` для платформы Android, поэтому если в проекте используются пакеты поддержки более новой версии, во избежание конфликтов необходимо установить требуемую версию пакета.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
