---
title: Создание приложения UWP
description: Следуйте указаниям этого руководства, чтобы начать работу с серверной частью мобильных приложений Azure для разработки приложений универсальной платформы Windows (UWP) на C#, Visual Basic или JavaScript.
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: a0f78239406567513c1eb94b48bf1090165d9185
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668689"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Создание приложения Windows с серверной частью Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Центр приложений Visual Studio поддерживает комплексные и интегрированные службы, необходимые для разработки мобильных приложений. Разработчики могут использовать службы **Build**, **Test** и **Distribute** для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения разработчики могут отслеживать его состояние и использование с помощью служб **Analytics** и **Diagnostics**, а также взаимодействовать с пользователями с помощью службы **Push**. Разработчики также могут использовать **Auth** для реализации проверки подлинности пользователей и службу **Data** для хранения и синхронизации данных приложений в облаке.
>
> Если вы хотите интегрировать облачные службы с мобильным приложением, зарегистрируйтесь в [Центре приложений](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) сегодня.

## <a name="overview"></a>Краткое описание

В этом руководстве показано, как добавить облачную серверную службу в универсальную платформу Windows (UWP). Дополнительные сведения см. в статье [Что такое мобильные приложения?](app-service-mobile-value-prop.md). Ниже приведены снимки экрана готового приложения.

![Готовое классическое приложение](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Изучение этого руководства является необходимым условием для работы со всеми остальными руководствами, посвященными мобильным приложениям UWP.

## <a name="prerequisites"></a>Технические условия

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/);
* Windows 10.
* Visual Studio Community 2017.
* Знакомство с разработкой приложений UWP. Ознакомьтесь с [документацией по UWP](https://docs.microsoft.com/windows/uwp/), чтобы получить инструкции по [настройке](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) для создания приложений UWP.

## <a name="create-a-new-azure-mobile-app-backend"></a>Создание серверной части мобильного приложения Azure

Чтобы создать серверную часть мобильного приложения, выполните указанные ниже действия.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Создание подключения к базе данных и настройка клиентского и серверного проекта
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Запуск клиентского проекта

1. Откройте проект UWP.

2. Перейдите к [портал Azure](https://portal.azure.com/) и перейдите к созданному вами мобильному приложению. В колонке `Overview` найдите URL-адрес, который является общедоступной конечной точкой для мобильного приложения. Пример. имя сайта для имени приложения "test123" будет https://test123.azurewebsites.net.

3. Откройте файл `App.xaml.cs` в этой папке — Windows-UWP-CS/ЗУМОАППНАМЕ/. Имя приложения — `ZUMOAPPNAME`.

4. В классе `App` замените параметр `ZUMOAPPURL` общедоступной конечной точкой выше.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    обретает
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Нажмите клавишу F5, чтобы развернуть и запустить приложение.

6. В приложении в поле **Insert a TodoItem** (Вставить TodoItem) введите содержательный текст, например *Работа с руководством*, и нажмите кнопку **Сохранить**.

    ![Полный быстрый запуск Windows — классическая версия](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    В результате будет отправлен запрос POST к серверной части нового мобильного приложения, размещенного в Azure.
