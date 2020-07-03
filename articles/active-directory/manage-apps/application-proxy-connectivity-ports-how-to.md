---
title: Как открыть порты брандмауэра, необходимые для приложения прокси приложения
description: Узнайте, какие порты необходимо открыть для правильной работы прокси приложения Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b676508c73ff0233526f19b865bb9e4f7b80f12
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74275548"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Как открыть порты брандмауэра, необходимые для приложения прокси приложения

Чтобы просмотреть полный список необходимых портов и функции каждого порта, обратитесь к разделу "Необходимые компоненты" в [документации по прокси приложения](application-proxy-add-on-premises-application.md). Обратите внимание, что прокси приложения использует только исходящие порты.

Вы также можете проверить, открыты ли все необходимые порты, открыв [средство проверки портов соединителей](https://aadap-portcheck.connectorporttest.msappproxy.net/) из локальной сети. Большее число зеленых флажков означает большую устойчивость. 

## <a name="app-proxy-regions"></a>Регионы прокси приложения

Мы работаем над тем, чтобы зеленым цветом выделялись только необходимые для вас регионы. Пока убедитесь, что все регионы выделены зеленым цветом. Регион "Центральная часть США" должен быть выделен зеленым цветом независимо от того, в каком регионе вы находитесь.

Чтобы получить правильные результаты, необходимо:

-   Откройте средство в браузере на сервере, на котором установлен соединитель.

-   Убедитесь, что все прокси-серверы или брандмауэры, которые применяются к соединителю, также применяются к этой странице. Это можно сделать в Internet Explorer, выбрав **Параметры**  - &gt; **Internet свойства**  - &gt; **подключения Подключение**  - &gt; **Параметры локальной сети**. На этой странице появится флажок "Использовать прокси-сервер для локальной сети". Установите этот флажок и укажите адрес прокси-сервера в поле "Адрес".

## <a name="next-steps"></a>Дальнейшие действия
[Общие сведения о соединителях Azure AD Application Proxy](application-proxy-connectors.md)
