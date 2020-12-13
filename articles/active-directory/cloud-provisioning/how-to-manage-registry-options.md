---
title: 'Azure AD Connect агент подготовки облака: Управление параметрами реестра | Документация Майкрософт'
description: В этой статье описывается, как управлять параметрами реестра в Azure AD Connect агент подготовки облака.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3bd938355d138e660958e34d046d7af03e75c7
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371198"
---
# <a name="manage-agent-registry-options"></a>Управление параметрами реестра агента

В этом разделе описываются параметры реестра, которые можно задать для управления поведением обработки среды выполнения Azure AD Connect агент подготовки. 

## <a name="configure-ldap-connection-timeout"></a>Настройка времени ожидания подключения LDAP
При выполнении операций LDAP для настроенных Active Directory контроллеров домена по умолчанию агент подготовки использует значение времени ожидания подключения по умолчанию, равное 30 секундам. Если вашему контроллеру домена требуется больше времени для ответа, в файле журнала агента может появиться следующее сообщение об ошибке: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

Операции поиска LDAP могут занять больше времени, если атрибут поиска не проиндексирован. В качестве первого шага, если вы получаете указанную выше ошибку, сначала проверьте, [проиндексирован](https://docs.microsoft.com/windows/win32/ad/indexed-attributes)ли атрибут поиска и уточняющего запроса. Если атрибуты поиска индексируются и ошибка повторяется, можно увеличить время ожидания подключения LDAP, выполнив следующие действия. 

1. Войдите в систему как администратор на сервере Windows Server, на котором работает агент подготовки Azure AD Connect.
1. Использование элемента меню " *выполнить* " для открытия редактора реестра (regedit.exe) 
1. Откройте папку с ключом **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Щелкните правой кнопкой мыши и выберите "создать-> строковый параметр".
1. Укажите имя: `LdapConnectionTimeoutInMilliseconds`
1. Дважды щелкните **имя значения** и введите значения в `60000` миллисекундах.
    > [!div class="mx-imgBorder"]
    > ![Время ожидания подключения LDAP](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Перезапустите службу подготовки Azure AD Connect из консоли *службы* .
1. Если вы развернули несколько агентов подготовки, примените это изменение реестра ко всем агентам для обеспечения согласованности. 

## <a name="configure-referral-chasing"></a>Настройка прослеживания ссылок
По умолчанию Azure AD Connect агент подготовки не выполняет отслеживание [ссылок](https://docs.microsoft.com/windows/win32/ad/referrals). Вы можете включить отслеживание ссылок для поддержки определенных сценариев подготовки к входящему ПЕРСОНАЛу, например: 
* Проверка уникальности имени участника-пользователя в нескольких доменах
* Разрешение ссылок на междоменные диспетчеры

Чтобы включить отслеживание ссылок, выполните следующие действия.

1. Войдите в систему как администратор на сервере Windows Server, на котором работает агент подготовки Azure AD Connect.
1. Использование элемента меню " *выполнить* " для открытия редактора реестра (regedit.exe) 
1. Откройте папку с ключом **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Щелкните правой кнопкой мыши и выберите "создать-> строковый параметр".
1. Укажите имя: `ReferralChasingOptions`
1. Дважды щелкните **имя значения** и введите значение в поле `96` . Это значение соответствует значению константы для `ReferralChasingOptions.All` и указывает, что за поддеревом и ссылками базового уровня будет следовать агент. 
    > [!div class="mx-imgBorder"]
    > ![Отслеживание ссылок](media/how-to-manage-registry-options/referral-chasing.png)
1. Перезапустите службу подготовки Azure AD Connect из консоли *службы* .
1. Если вы развернули несколько агентов подготовки, примените это изменение реестра ко всем агентам для обеспечения согласованности.

> [!NOTE]
> Чтобы убедиться, что параметры реестра заданы, включите [подробное ведение журнала](how-to-troubleshoot.md#log-files). Журналы, созданные во время запуска агента, будут отображать значения конфигурации, отобранные из реестра. 

## <a name="next-steps"></a>Дальнейшие действия 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [Что такое облачная подготовка Azure AD Connect?](what-is-cloud-provisioning.md)

