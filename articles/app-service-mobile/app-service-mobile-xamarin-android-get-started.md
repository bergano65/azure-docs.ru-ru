---
title: Приступая к работе с мобильными приложениями Azure для приложений Xamarin.Android | Microsoft Azure
description: Этот учебник поможет приступить к использованию мобильных приложений Azure для разработки приложений Xamarin Android
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: f3e8ca4f9736dffe4928fc8920b0890dff87367b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236024"
---
# <a name="create-a-xamarinandroid-app"></a>Создание приложения Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

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
