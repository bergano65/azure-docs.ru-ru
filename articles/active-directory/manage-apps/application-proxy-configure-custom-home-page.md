---
title: Настройка пользовательской домашней страницы для опубликованных приложений с помощью прокси приложения Azure AD | Документация Майкрософт
description: Основные сведения о соединителях прокси приложения Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51596e4db8999de5089748e40f9b24bd46c84e56
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807836"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Настройка пользовательской домашней страницы для опубликованных приложений с помощью прокси приложения Azure AD

В этой статье описывается настройка приложения для перенаправления пользователя на пользовательскую домашнюю страницу. При публикации приложения с помощью прокси приложения, задайте внутренний URL-адрес, но иногда это не страницы, на которую пользователь должен видеть сначала. Задайте пользовательскую домашнюю страницу, чтобы пользователь получает соответствующую страницу, при доступе к приложению. Пользователь видит на пользовательскую домашнюю страницу установить, независимо от того, является ли они доступ к приложению из панели доступа Azure Active Directory или средства запуска приложений Office 365.

Когда пользователь запускает приложение, им будут предоставлены по умолчанию URL-адрес корневого домена для опубликованного приложения. Обычно целевой страницей считается URL-адрес домашней страницы приложения. Используйте модуль Azure AD PowerShell для определения URL-адрес пользовательской домашней страницы, когда требуется пользователя приложения должны переходить на определенную страницу в приложении.

Ниже приведен один из сценариев, объясняющее, почему ваша компания устанавливал пользовательской домашней страницы.

- В корпоративной сети, пользователь переходит к `https://ExpenseApp/login/login.aspx` на вход и доступ к вашему приложению.
- Так как у вас есть другие активы (например, изображения), прокси приложения требуется доступ к на верхнем уровне структуры папок, публикации приложения с помощью `https://ExpenseApp` как внутренний URL-адрес.
- Внешний URL-адрес по умолчанию `https://ExpenseApp-contoso.msappproxy.net`, который не принимает внешнего пользователя на страницу входа в систему.
- Вы хотите установить `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` как URL-адрес домашней страницы, таким образом внешний пользователь увидит страницу входа сначала.

> [!NOTE]
> Когда вы предоставляете пользователям доступ к опубликованным приложениям, эти приложения отображаются на [панели доступа Azure AD](../user-help/my-apps-portal-end-user-access.md) и в [средстве запуска приложений Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Перед началом работы

Прежде чем указывать URL-адрес домашней страницы, учтите следующие требования:

- Указываемый путь должен являться путем поддомен URL-адрес корневого домена.

  Например, если URL-адрес корневого домена `https://apps.contoso.com/app1/`, настроенному в URL-адрес домашней страницы должен начинаться с `https://apps.contoso.com/app1/`.

- Когда вы вносите изменения в опубликованное приложение, URL-адрес домашней страницы может вернуться к значению по умолчанию. Поэтому при дальнейшем обновлении приложения следует повторно проверить URL-адрес домашней страницы и изменить его при необходимости.

Можно задать URL-адрес домашней страницы на портале Azure или с помощью PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Изменение домашней страницы на портале Azure

Чтобы изменить URL-адрес домашней страницы приложения с помощью портала Azure AD, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com/) с использованием учетной записи администратора.
1. Выберите **Azure Active Directory**, а затем **регистрация приложений**. Появится список зарегистрированных приложений.
1. Выберите приложение из списка. Откроется страница со сведениями об зарегистрированного приложения.
1. В разделе **управление**выберите **фирменной символики**.
1. Обновление **URL-адрес домашней страницы** новый путь.

   ![Фирменная символика страницы для зарегистрированного приложения, показывающая поле URL-адрес домашней страницы](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Щелкните **Сохранить**.

## <a name="change-the-home-page-with-powershell"></a>Изменение домашней страницы с помощью PowerShell

Чтобы настроить домашнюю страницу приложения с помощью PowerShell, вам потребуется:

1. Установите модуль Azure AD PowerShell.
1. Найдите значение ObjectId приложения.
1. Обновите URL-адрес домашней страницы приложения, с помощью команд PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Установка модуля Azure AD PowerShell

Прежде чем настраивать URL-адрес пользовательской домашней страницы с помощью PowerShell, установите модуль Azure AD PowerShell. Этот пакет можно скачать из [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), в которой используется конечная точка API Graph.

Для установки пакета выполните следующие действия.

1. Откройте стандартное окно PowerShell и выполните следующую команду.

   ```powershell
   Install-Module -Name AzureAD
   ```

    Если вы запускаете команду не от имени администратора, используйте параметр `-scope currentuser`.

1. Во время установки выберите ответ **Y** (Да) на предложение установить два пакета с сайта Nuget.org. Требуются оба пакета.

### <a name="find-the-objectid-of-the-app"></a>Поиск идентификатора ObjectId приложения

Получение идентификатора ObjectId для приложения, выполнив поиск в приложение по его отображаемое имя или домашней странице.

1. В том же окне PowerShell импортируйте модуль Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Войдите в модуль Azure AD в качестве администратора клиента.

   ```powershell
   Connect-AzureAD
   ```

1. Найдите приложение. В этом примере PowerShell, чтобы найти ObjectId для приложения с отображаемым именем из `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Вы должны получить результат, похожий на приведенный ниже. Скопируйте GUID ObjectId для использования в следующем разделе.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Кроме того можно просто извлечь список всех приложений, найдите в списке приложений с именем экрана или домашнюю страницу и скопировать ObjectId приложения после обнаружения приложения.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Изменение URL-адреса домашней страницы

Создайте URL-адрес домашней страницы и обновить приложение с этим значением. Продолжать использовать том же окне PowerShell, или если вы используете новое окно PowerShell, войдите снова с помощью модуля Azure AD `Connect-AzureAD`. Затем выполните следующие действия.

1. Создайте переменную для хранения значения ObjectId, который вы скопировали в предыдущем разделе. (Замените значение ObjectId, используемое в этом примере SharePoint со значением ObjectId приложения).

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Убедитесь, что вы нашли правильное приложение, выполнив следующую команду. Выходные данные должны быть идентичны в выходные данные, вы видели в предыдущем разделе ([поиск идентификатора ObjectId приложения](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Создайте пустой объект приложения, в котором будут храниться ваши изменения.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Задайте нужное значение для URL-адреса домашней страницы. Это значение должно быть путем к поддомену опубликованного приложения. Например, если изменить URL-адрес домашней страницы с `https://sharepoint-iddemo.msappproxy.net/` на `https://sharepoint-iddemo.msappproxy.net/hybrid/`, пользователи приложения перейдут непосредственно на пользовательскую домашнюю страницу.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Установите обновление, домашней страницы.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Чтобы убедиться, что изменения внесены успешно, выполните следующую команду из шага 2 еще раз.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Для нашего примера результат должен теперь выглядеть следующим образом:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Перезапустите приложение, чтобы убедиться, что на домашней странице отображается как первый экран, должным образом.

> [!NOTE]
> Все изменения, которые вы внесли в приложение, могут сбросить URL-адрес домашней страницы. В случае сброса URL-адреса домашней страницы повторите шаги в этом разделе, чтобы снова задать его.

## <a name="next-steps"></a>Следующие шаги

- [Настройка удаленного доступа к SharePoint с помощью прокси приложения Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Учебник. Добавление локального приложения для удаленного доступа через прокси приложения в Azure Active Directory](application-proxy-add-on-premises-application.md)