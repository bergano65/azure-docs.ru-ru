---
title: Пользовательская домашняя страница для опубликованных приложений - Azure AD Application Proxy
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
ms.openlocfilehash: 1621b273f617955a374ed46d9c215ba99e5b2913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275599"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Настройка пользовательской домашней страницы для опубликованных приложений с помощью прокси приложения Azure AD

В этой статье обсуждается, как настроить приложение, чтобы направить пользователя на пользовательскую домашнюю страницу. Когда вы публикуете приложение с помощью приложения Proxy, вы устанавливаете внутренний URL-адрес, но иногда это не та страница, которую пользователь должен увидеть в первую очередь. Установите пользовательскую домашнюю страницу таким образом, чтобы пользователь получил правильную страницу при доступе к приложению. Пользователь увидит установленную на специальной странице пользовательскую домашнюю страницу, независимо от того, получает ли он доступ к приложению из панели доступа active Directory Или с приложением Office 365.

Когда пользователь запускает приложение, он по умолчанию направляется на URL-адрес корневого домена для опубликованного приложения. Обычно целевой страницей считается URL-адрес домашней страницы приложения. Используйте модуль Azure AD PowerShell для определения пользовательского URL-адреса домашней страницы, если вы хотите, чтобы пользователь приложения приземлился на определенной странице приложения.

Вот один сценарий, который объясняет, почему ваша компания будет устанавливать пользовательские домашнюю страницу:

- Внутри корпоративной сети пользователь `https://ExpenseApp/login/login.aspx` переходит к пользователю, чтобы войти в систему и получить доступ к вашему приложению.
- Поскольку у вас есть другие ресурсы (например, изображения), к которым необходимо получить `https://ExpenseApp` доступ к приложению на верхнем уровне структуры папки, вы публикуете приложение в качестве внутреннего URL-.
- Внешний URL `https://ExpenseApp-contoso.msappproxy.net`по умолчанию— это URL-адрес, который не забирает внешнего пользователя на страницу ввоза.
- Вместо этого `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` необходимо установить URL-адрес главной страницы, чтобы внешний пользователь сначала увидел страницу ввне.

> [!NOTE]
> Когда вы предоставляете пользователям доступ к опубликованным приложениям, эти приложения отображаются на [панели доступа Azure AD](../user-help/my-apps-portal-end-user-access.md) и в [средстве запуска приложений Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Перед началом работы

Прежде чем указывать URL-адрес домашней страницы, учтите следующие требования:

- Указанный путь должен быть поддоменным путем URL-адреса корневого домена.

  Например, если URL-адрес `https://apps.contoso.com/app1/`корневого домена — URL-адрес `https://apps.contoso.com/app1/`главной страницы, который вы настраиваете, должен начинаться с:

- Когда вы вносите изменения в опубликованное приложение, URL-адрес домашней страницы может вернуться к значению по умолчанию. Поэтому при дальнейшем обновлении приложения следует повторно проверить URL-адрес домашней страницы и изменить его при необходимости.

URL-адрес главной страницы можно установить либо через портал Azure, либо с помощью PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Изменение домашней страницы на портале Azure

Чтобы изменить URL-адрес домашней страницы приложения через портал Azure AD, выполните следующие действия:

1. Вопиюсь на [портал Azure](https://portal.azure.com/) в качестве администратора.
1. Выберите **Активный каталог Azure,** а затем **регистрацию приложений.** Отображается список зарегистрированных приложений.
1. Выберите приложение из списка. Появляется страница с указанием деталей зарегистрированного приложения.
1. Под **управлением**, выберите **Брендинг**.
1. Обновление **URL-адреса главной страницы** с новым путем.

   ![Страница брендинга для зарегистрированного приложения, показывающего поле URL-адреса главной страницы](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Нажмите кнопку **Сохранить**.

## <a name="change-the-home-page-with-powershell"></a>Изменение домашней страницы с помощью PowerShell

Чтобы настроить домашнюю страницу приложения с помощью PowerShell, необходимо:

1. Установите модуль Azure AD PowerShell.
1. Найдите значение приложения ObjectId.
1. Обновление URL-адреса домашней страницы приложения с помощью команд PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Установка модуля Azure AD PowerShell

Прежде чем настраивать URL-адрес пользовательской домашней страницы с помощью PowerShell, установите модуль Azure AD PowerShell.Этот пакет можно скачать из [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), в которой используется конечная точка API Graph.

Для установки пакета выполните следующие действия.

1. Откройте стандартное окно PowerShell и выполните следующую команду.

   ```powershell
   Install-Module -Name AzureAD
   ```

    Если вы запускаете команду не от имени администратора, используйте параметр `-scope currentuser`.

1. Во время установки выберите **Y** для установки двух пакетов из Nuget.org. Оба пакета обязательны.

### <a name="find-the-objectid-of-the-app"></a>Найти ObjectId приложения

Вы получаете ObjectId приложения, ища приложение по имени дисплея или домашней странице.

1. В том же окне PowerShell импортируйте модуль Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Войдите в модуль Azure AD в качестве администратора клиента.

   ```powershell
   Connect-AzureAD
   ```

1. Найдите приложение. Этот пример использует PowerShell для поиска ObjectId, ища `SharePoint`приложение с именем отображения.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Вы должны получить результат, похожий на приведенный ниже. Копирование UseD ObjectId для использования в следующем разделе.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Кроме того, вы можете просто вытащить список всех приложений, искать список для приложения с определенным именем дисплея или домашней странице, и скопировать ObjectId приложения, как только приложение найдено.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Изменение URL-адреса домашней страницы

Создайте URL-адрес главной страницы и обновите приложение с этим значением. Продолжить использование того же окна PowerShell, или если вы используете новое окно PowerShell, `Connect-AzureAD`вопием в модуль Azure AD снова с помощью . Затем выполните следующие действия.

1. Создайте переменную для удержания значения ObjectId, скопированный в предыдущем разделе. (Заменить значение ObjectId, используемое в этом примере SharePoint, значением ObjectId вашего приложения.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Подтвердите, что у вас есть правильное приложение, запустив следующую команду. Выход должен быть идентичен выходу, который вы видели в предыдущем разделе[(Найти ObjectId приложения](#find-the-objectid-of-the-app)).

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

1. Сделать обновление домашней страницы.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Чтобы подтвердить, что изменение было успешным, запустите следующую команду со ступени 2 снова.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Например, вывод должен теперь отображаться следующим образом:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Перезапустите приложение, чтобы подтвердить, что главная страница отображается как первый экран, как и ожидалось.

> [!NOTE]
> Все изменения, которые вы внесли в приложение, могут сбросить URL-адрес домашней страницы. В случае сброса URL-адреса домашней страницы повторите шаги в этом разделе, чтобы снова задать его.

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка удаленного доступа к SharePoint с помощью прокси приложения Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Учебник: Добавьте приложение для удаленного доступа через приложение Proxy в Active Directory Azure](application-proxy-add-on-premises-application.md)