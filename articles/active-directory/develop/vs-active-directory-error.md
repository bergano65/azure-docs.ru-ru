---
title: Диагностика ошибок с помощью подключенной службы Azure Active Directory
description: Подключенная служба Active Directory обнаружила несовместимый тип аутентификации
author: ghogen
manager: jillfra
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6df50db564dac09785b460d158d4276ea8a215e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271246"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Диагностика ошибок с помощью подключенной службы Azure Active Directory

При обнаружении предыдущего кода проверки подлинности подключенная служба Azure Active Directory обнаружила несовместимый тип проверки подлинности.

Чтобы правильно обнаружить предыдущий код проверки подлинности в проекте, необходимо выполнить перестроение проекта. Если вы видите эту ошибку и у вас нет предыдущего кода проверки подлинности в проекте, выполните повторную сборку и повторите попытку.

## <a name="project-types"></a>Типы проектов

Подключенная служба проверяет тип проекта, над которым вы работаете, чтобы внедрить в него правильную логику аутентификации. Если в проекте имеется контроллер, производный от `ApiController`, проект считается проектом WebAPI. Если в проекте есть какие-либо контроллеры, являющиеся производными от `MVC.Controller`, то этот проект рассматривается как проект MVC. Подключенная служба не поддерживает другие типы проектов.

## <a name="compatible-authentication-code"></a>Совместимый код аутентификации

Также подключенная служба проверяет параметры аутентификации, настроенные ранее или совместимые с этой службой. Если все параметры заданы, он считается повторно используемым регистром, а подключенная служба открывает отображение параметров.  Если имеются только некоторые параметры, это считается ошибкой.

В проекте MVC подключенная служба проверяет все перечисленные ниже параметры, которые могли быть созданы при предыдущем применении этой службы.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Кроме того, подключенная служба проверяет наличие в проекте веб-API любого из следующих параметров, что приводит к предыдущему использованию службы:

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
