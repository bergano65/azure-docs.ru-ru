---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199423"
---
## <a name="register-your-application"></a>Регистрация приложения

Приложение можно зарегистрировать одним из двух способов.

### <a name="option-1-express-mode"></a>Вариант 1. Экспресс-режим

Чтобы быстро зарегистрировать приложение, сделайте следующее:
1. Перейдите на [портал Azure > Регистрация приложения](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Введите имя приложения и нажмите кнопку **Зарегистрировать**.
1. Следуйте инструкциям, чтобы быстро скачать и автоматически настроить новое приложение.

### <a name="option-2-advanced-mode"></a>Вариант 2. Расширенный режим

Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение, сделайте следующее:
1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
1. Перейдите на страницу [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) Платформы удостоверений Майкрософт для разработчиков.
1. Выберите **Новая регистрация**.
   - В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `Win-App-calling-MsGraph`.
   - В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts (for example, Skype, Xbox, Outlook.com)** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт (например, Skype, Xbox, Outlook.com)).
   - Выберите **Зарегистрировать**, чтобы создать приложение.
1. В списке страниц приложения выберите **Проверка подлинности**.
   1. В разделе **URI перенаправления** в списке URI перенаправления сделайте следующее:
   1. В столбце **Тип** выберите **Общедоступный клиент (мобильный и классический)**.
   1. Введите `urn:ietf:wg:oauth:2.0:oob` в столбец **URI перенаправления**.
1. Щелкните **Сохранить**.
1. Перейдите в Visual Studio, откройте файл *App.xaml.cs*, а затем замените `Enter_the_Application_Id_here` только что зарегистрированным ы скопированным идентификатором приложения.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
