---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 15db2192703971a8056df34343c427db11c8411a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988525"
---
## <a name="register-your-application"></a>Регистрация приложения

Зарегистрировать приложение и добавить сведения о его регистрации в решение можно двумя способами:

### <a name="option-1-express-mode"></a>Вариант 1. Экспресс-режим

Чтобы быстро зарегистрировать приложение, сделайте следующее:

1. Зарегистрируйте свое приложение на [портале регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2. Введите название приложения и адрес электронной почты.
3. Выберите параметр Guided Setup (Пошаговая настройка).
4. Следуйте дальнейшим инструкциям, чтобы добавить URL-адрес перенаправления в приложение.

### <a name="option-2-advanced-mode"></a>Вариант 2. Расширенный режим

Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение, сделайте следующее:

1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app) для регистрации приложения.
2. Введите название приложения и адрес электронной почты.
3. Убедитесь, что параметр Guided Setup (Пошаговая настройка) не выбран.
4. Выберите `Add Platform`, а затем `Web`.
5. Вернитесь в Visual Studio и в обозревателе решений выберите проект и просмотрите окно свойств (если окно свойств не отображается, нажмите клавишу F4).
6. Для параметра "SSL включен" измените значение на `True`.
7. Щелкните правой кнопкой мыши проект в Visual Studio, а затем выберите **Свойства** и вкладку **Интернет**. В разделе *Серверы* измените *URL-адрес проекта* на URL-адрес SSL.
8. Скопируйте URL-адрес SSL и добавьте его в список URL-адресов перенаправления в списке на портале регистрации:<br/><br/>![Свойства проекта](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Добавьте следующий код в файл `web.config`, расположенный в корневой папке в разделе `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Замените `ClientId` зарегистрированным идентификатором приложения.
11. Замените `redirectUri` URL-адресом SSL проекта.
