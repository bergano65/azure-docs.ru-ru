---
title: Создание приложения UWP
description: Следуйте указаниям этого руководства, чтобы начать работу с серверной частью мобильных приложений Azure для разработки приложений универсальной платформы Windows (UWP) на C#, Visual Basic или JavaScript.
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9188db19adab9bd46d65fc97f1c62b39141cee90
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461391"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Создание приложения Windows с серверной частью Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Обзор

В этом руководстве показано, как добавить облачную серверную службу в универсальную платформу Windows (UWP). Дополнительные сведения см. в статье [Что такое мобильные приложения?](app-service-mobile-value-prop.md). Ниже приведены снимки экрана готового приложения.

![Готовое классическое приложение](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Изучение этого руководства является необходимым условием для работы со всеми остальными руководствами, посвященными мобильным приложениям UWP.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10;
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

    преобразуется в
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Нажмите клавишу F5, чтобы развернуть и запустить приложение.

6. В приложении в поле *Insert a TodoItem* (Вставить TodoItem) введите содержательный текст, например **Работа с руководством**, и нажмите кнопку **Сохранить**.

    ![Полный быстрый запуск Windows — классическая версия](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    В результате будет отправлен запрос POST к серверной части нового мобильного приложения, размещенного в Azure.
