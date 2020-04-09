---
title: Диагностика ошибок с помощью сервиса подключения Azure AD (Visual Studio)
description: Подключенная служба Active Directory обнаружила несовместимый тип аутентификации
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 4b39aa77ea3895a606ad34a3bc9b70dba924a23f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886098"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Диагностика ошибок с помощью подключенной службы Azure Active Directory

При обнаружении предыдущего кода проверки подлинности служба связана с Azure Active Directory, обнаружив несовместимый тип проверки подлинности.

Чтобы правильно обнаружить предыдущий код проверки подлинности в проекте, проект должен быть перестроен. Если вы видите эту ошибку и у вас нет предыдущего кода проверки подлинности в проекте, перестроить и повторить попытку.

## <a name="project-types"></a>Типы проектов

Подключенная служба проверяет тип проекта, над которым вы работаете, чтобы внедрить в него правильную логику аутентификации. Если в проекте есть какой-либо контроллер, `ApiController` проект считается проектом WebAPI. Если в проекте есть какие-либо контроллеры, являющиеся производными от `MVC.Controller`, то этот проект рассматривается как проект MVC. Подключенная служба не поддерживает другие типы проектов.

## <a name="compatible-authentication-code"></a>Совместимый код аутентификации

Также подключенная служба проверяет параметры аутентификации, настроенные ранее или совместимые с этой службой. Если все настройки присутствуют, это считается случаем повторного участника, и подключенная служба открывает отображать настройки.  Если присутствуют только некоторые настройки, это считается случаем ошибки.

В проекте MVC подключенная служба проверяет все перечисленные ниже параметры, которые могли быть созданы при предыдущем применении этой службы.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Кроме того, подключенные службы проверяют на наличие любых следующих параметров в проекте Web API, которые являются результатом предыдущего использования службы:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Несовместимый код аутентификации

И в завершение подключенная служба пытается обнаружить версии кода аутентификации, настроенные для предыдущих версий Visual Studio. Эта ошибка означает, что проект содержит несовместимый тип проверки подлинности. Подключенная служба обнаруживает следующие типы аутентификации от предыдущих версий Visual Studio:

* Проверка подлинности Windows
* Учетные записи индивидуальных пользователей
* Учетные записи организации

Чтобы обнаружить аутентификацию Windows в проекте MVC, подключенная служба ищет элемент `authentication` в файле `web.config`.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Чтобы обнаружить аутентификацию Windows в проекте Web API, подключенная служба ищет элемент `IISExpressWindowsAuthentication` в файле `.csproj`.

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Чтобы обнаружить аутентификацию по учетным записям отдельных пользователей, подключенная служба ищет элемент пакета в файле `packages.config`.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Чтобы обнаружить старый тип аутентификации по корпоративной учетной записи, подключенная служба ищет в файле `web.config` следующий элемент:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Чтобы изменить тип аутентификации, удалите несовместимый тип аутентификации и повторно добавьте подключенную службу.

Дополнительные сведения см. в статье [Сценарии аутентификации в Azure Active Directory](authentication-scenarios.md).
