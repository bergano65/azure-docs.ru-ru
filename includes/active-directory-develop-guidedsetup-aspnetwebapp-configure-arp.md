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
ms.date: 09/17/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f3048daa7d597d92041733cdf5c6f299cebc2f73
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142922"
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Настройка веб-приложения ASP.NET с помощью сведений о регистрации приложения

На этом шаге вам предстоит настроить проект для использования SSL, а затем использовать URL-адрес SSL для настройки сведений о регистрации приложения. После этого вы добавите сведения о регистрации приложения в свое решение, используя файл *web.config*.

1. В обозревателе решений выберите проект и просмотрите окно `Properties` (если окно свойств не отображается, нажмите клавишу F4).
2. Измените `SSL Enabled` на `True`.
3. Скопируйте значение указанного выше `SSL URL` и вставьте его в поле `Redirect URL` в верхней части этой страницы, а затем щелкните *Обновление*:<br/><br/>![Свойства проекта](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4. Добавьте следующий код в файл `web.config`, расположенный в корневой папке в разделе `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
