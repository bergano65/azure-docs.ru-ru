---
title: Location condition in Azure Active Directory Conditional Access
description: Узнайте, как использовать условие расположения для управления доступом к облачным приложениям на основе расположения пользователя в сети.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380295"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>What is the location condition in Azure Active Directory Conditional Access? 

With [Azure Active Directory (Azure AD) Conditional Access](../active-directory-conditional-access-azure-portal.md), you can control how authorized users can access your cloud apps. The location condition of a Conditional Access policy enables you to tie access controls settings to the network locations of your users.

В этой статье приведены сведения, необходимые для настройки условия расположения.

## <a name="locations"></a>Расположения

Azure AD enables single sign-on to devices, apps, and services from anywhere on the public internet. Условие расположения позволяет управлять доступом к облачным приложениям на основе расположения пользователя в сети. Ниже приведены наиболее распространенные варианты использования условия расположения.

- Требование многофакторной проверки подлинности для пользователей, которые пытаются получить доступ к службе, но пока отключены от корпоративной сети.
- Блокировка доступа для пользователей, которые пытаются получить доступ к службе из определенных стран или регионов.

A location is a label for a network location that either represents a named location or multi-factor authentication Trusted IPs.

## <a name="named-locations"></a>Именованные расположения

With named locations, you can create logical groupings of IP address ranges or countries and regions.

You can access your named locations in the **Manage** section of the Conditional Access page.

![Named locations in Conditional Access](./media/location-condition/02.png)

Именованное расположение состоит из следующих компонентов.

![Create a new named location](./media/location-condition/42.png)

- **Имя** — отображаемое имя именованного расположения.
- **Диапазоны IP-адресов** — один или не сколько диапазонов IPv4-адресов в формате CIDR. Specifying an IPv6 address range is not supported.

   > [!NOTE]
   > IPv6 address ranges cannot currently be included in a named location. This means IPv6 ranges cannot be excluded from a Conditional Access policy.

- **Отметить как надежное расположение** — флажок, который можно установить для именованного расположения, чтобы указать надежное расположение. Как правило, надежные расположения являются областями сети, которые контролируются ИТ-отделом. In addition to Conditional Access, trusted named locations are also used by Azure Identity Protection and Azure AD security reports to reduce [false positives](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Страны и регионы**. Этот параметр позволяет выбрать одну или несколько стран или регионов для определения именованного расположения.
- **Include unknown areas** - Some IP addresses are not mapped to a specific country or region. Этот параметр позволяет указать, нужно ли включать эти IP-адреса в именованное расположение. Используйте данный параметр, когда политика, использующая именованное расположение, должна применяться к неизвестным расположениям.

Количество именованных расположений, которые можно настроить, ограничено размером соответствующего объекта в Azure AD. You can configure locations based on of the following limitations:

- Именованное расположение с диапазонами IP-адресов до 1200.
- Максимум 90 именованных расположений, каждому из которых назначен один диапазон IP-адресов.

Conditional Access policy applies to IPv4 and IPv6 traffic. Currently named locations do not allow IPv6 ranges to be configured. This limitation causes the following situations:

- Conditional Access policy cannot be targeted to specific IPv6 ranges
- Conditional Access policy cannot exclude specific IPV6 ranges

If a policy is configured to apply to “Any location”, it will apply to IPv4 and IPv6 traffic. Named locations configured for specified countries and regions only support IPv4 addresses. IPv6 traffic is only included if the option to “include unknown areas” selected.

## <a name="trusted-ips"></a>Надежные IP-адреса

Вы также можете настроить диапазоны IP-адресов, представляющие локальную интрасеть в [параметрах службы многофакторной проверки подлинности](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Эта функция позволяет настроить до 50 диапазонов IP-адресов. Диапазоны IP-адресов имеют формат CIDR. For more information, see [Trusted IPs](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

If you have Trusted IPs configured, they show up as **MFA Trusted IPS** in the list of locations for the location condition.

### <a name="skipping-multi-factor-authentication"></a>Пропуск многофакторной проверки подлинности

На странице параметров службы многофакторной проверки подлинности можно определить пользователей корпоративной интрасети, установив флажок **Пропустить многофакторную проверку подлинности для запросов от федеративных пользователей из моей интрасети**. Этот параметр указывает, что внутреннее утверждение корпоративной сети, выдаваемое сервером служб федерации Active Directory (AD FS), должно быть надежным и использоваться для идентификации пользователя в корпоративной сети. For more information, see [Enable the Trusted IPs feature by using Conditional Access](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

After checking this option, including the named location **MFA Trusted IPS** will apply to any policies with this option selected.

For mobile and desktop applications, which have long lived session lifetimes, Conditional Access is periodically reevaluated. По умолчанию — один раз в час. Если внутренне утверждение корпоративной сети выпускается только во время первоначальной проверки подлинности, Azure AD может не иметь списка диапазонов надежных IP-адресов. В этом случае трудно определить, находится ли по-прежнему пользователь в корпоративной сети.

1. Проверьте, входит ли IP-адрес пользователя в один из диапазонов надежных IP-адресов.
2. Check whether the first three octets of the user’s IP address match the first three octets of the IP address of the initial authentication. The IP address is compared with the initial authentication when the inside corporate network claim was originally issued and the user location was validated.

Если оба этапа завершатся ошибкой, считается, что пользователь больше не использует надежный IP-адрес.

## <a name="location-condition-configuration"></a>Настройка условия расположения

При настройке условия расположения вы можете выбрать:

- Любое расположение
- Все надежные расположения
- Выбранные расположения

![Настройка условия расположения](./media/location-condition/01.png)

### <a name="any-location"></a>Любое расположение

По умолчанию выбор варианта **Любое местонахождение** приводит к тому, что политика применяется ко всем IP-адресам (то есть к любому адресу в Интернете). Этот параметр не ограничивается IP-адресами, настроенными как именованное расположение. Выбрав вариант **Любое местонахождение**, вы все равно можете исключить определенные расположения из политики. Например, можно применить политику ко всем расположениям (за исключением надежных расположений), чтобы задать для области все расположения (за исключением корпоративной сети).

### <a name="all-trusted-locations"></a>Все надежные расположения

Этот параметр применяется:

- ко всем расположениям, помеченным как надежное расположение;
- **надежным IP-адресам MFA** (если настроены).

### <a name="selected-locations"></a>Выбранные расположения

С помощью этого параметра можно выбрать одно или несколько именованных расположений. Для применения политики с этим параметром пользователю необходимо подключиться из любого из выбранных расположений. Если щелкнуть **Выбрать**, откроется элемент управления со списком именованных сетей. В списке также показано, является ли надежным расположение в сети. Именованное расположение **Надежные IP-адреса MFA** используется для включения параметров IP-адресов, которые можно настроить на странице параметров службы многофакторной проверки подлинности.

## <a name="what-you-should-know"></a>Необходимая информация

### <a name="when-is-a-location-evaluated"></a>Когда выполняется проверка расположения

Conditional Access policies are evaluated when:

- Изначально пользователь входит в веб-приложение, мобильное или классическое приложение.
- Мобильное или классическое приложение, в котором применяется современная аутентификация, использует маркер обновления для получения нового маркера доступа. By default this check is once an hour.

This check means for mobile and desktop applications using modern authentication, a change in location would be detected within an hour of changing the network location. Для мобильных и классических приложений, которые не используют современную аутентификацию, к каждому запросу на маркер применяется политика. Частота выполнения запроса зависит от приложения. Для веб-приложений политика также применяется во время начального входа и действует в течение времени существования сеанса веб-приложения. Так как в разных приложениях время сеанса будет разным, время между оценкам политики также может изменяться. Каждый раз, когда приложение запрашивает новый маркер входа, применяется политика.

По умолчанию Azure AD выдает токен на почасовой основе. После перемещения из корпоративной сети в течение часа политика применяется для приложений, использующих современную проверку подлинности.

### <a name="user-ip-address"></a>IP-адрес пользователя

При оценке политики используется общедоступный IP-адрес пользователя. For devices on a private network, this IP address is not the client IP of the user’s device on the intranet, it is the address used by the network to connect to the public internet.

> [!WARNING]
> If your device has only an IPv6 address, configuring the location condition is not supported.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Массовое обновление и загрузка именованных расположений

При создании или обновлении именованных расположений для массовых обновлений можно передать или скачать CSV-файл с диапазонами IP-адресов. При передаче диапазоны IP-адресов заменяются адресами, указанными в файле. Каждая строка файла содержит один диапазон IP-адресов в формате CIDR.

### <a name="cloud-proxies-and-vpns"></a>Облачные прокси-серверы и виртуальные частные сети (VPN)

Если используется прокси-сервер, размещенный в облаке, или решение VPN, при оценке политики Azure AD использует IP-адрес прокси-сервера. Заголовок X-Forwarded-For (XFF), содержащий общедоступный IP-адрес пользователей, не используется, так как нет подтверждения того, что он поступает из надежного источника, а значит может быть поддельным.

При наличии облачного прокси-сервера можно использовать политику, требующую присоединенного к домену устройства, или внутреннее утверждение корпоративной сети из AD FS.

### <a name="api-support-and-powershell"></a>Поддержка API и PowerShell

API and PowerShell is not yet supported for named locations, or for Conditional Access policies.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать, как настроить политику условного доступа, см. статью [Краткое руководство. Требование Многофакторной идентификации для конкретных приложений с помощью условного доступа Azure Active Directory](app-based-mfa.md).
- Если вы готовы к настройке политик условного доступа для своей среды, см. статью [Best practices for Conditional Access in Azure Active Directory](best-practices.md) (Рекомендации по работе с условным доступом в Azure Active Directory).
