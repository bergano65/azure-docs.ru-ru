---
title: Защищенный гибридный доступ Azure AD | Документация Майкрософт
description: В этой статье описаны партнерские решения для интеграции устаревших локальных, общедоступных облачных или частных облачных приложений с Azure AD. Вы можете защитить свои устаревшие приложения, подключив к Azure AD сети или контроллеры доставки приложений.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94a118b6d526d538015b7aa076b2715ed68af338
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032076"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Безопасный гибридный доступ: защищенные устаревшие приложения с Azure Active Directory

Теперь вы можете защитить локальные и облачные приложения проверки подлинности, подключив их к Azure Active Directory (AD) с помощью:

- [AD Application Proxy Azure](#secure-hybrid-access-through-azure-ad-application-proxy)

- [Существующие контроллеры и сети доставки приложений](#secure-hybrid-access-through-networking-and-delivery-controllers)

- [Приложения виртуальной частной сети (VPN)](#secure-hybrid-access-through-vpn-applications)

Вы можете преодолеть разрыв и усилить уровень безопасности для всех приложений с помощью таких возможностей Azure AD, как [Условный доступ](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) Azure AD и [Защита идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)Azure AD.

## <a name="secure-hybrid-access-through-azure-ad-application-proxy"></a>Безопасный гибридный доступ с помощью Azure AD Application Proxy
  
С помощью [прокси приложения](https://aka.ms/whyappproxy) можно обеспечить [безопасный удаленный доступ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) к локальным веб-приложениям. Пользователи не должны использовать VPN. Пользователи получают возможность легко подключаться к своим приложениям с любого устройства после [единого входа](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-sso). Прокси приложения предоставляет удаленный доступ как службу и позволяет [легко публиковать локальные приложения](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) для пользователей за пределами корпоративной сети. Она помогает масштабировать управление облачным доступом без необходимости вносить изменения в локальные приложения. [Планирование развертывания AD application proxy Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) в качестве следующего этапа.

## <a name="azure-ad-partner-integrations"></a>Интеграция с партнерами Azure AD

### <a name="secure-hybrid-access-through-networking-and-delivery-controllers"></a>Безопасный гибридный доступ через сети и контроллеры доставки

В дополнение к [Azure AD application proxy](https://aka.ms/whyappproxy), чтобы использовать [инфраструктуру с нулевым доверием](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), партнеры Майкрософт со сторонними поставщиками. Вы можете использовать существующие сетевые устройства и контроллеры доставки, а также легко защищать устаревшие приложения, которые важны для бизнес-процессов, но не смогли обеспечить защиту перед использованием Azure AD. Скорее всего, у вас уже есть все необходимое для защиты этих приложений.

![На рисунке показан безопасный гибридный доступ с помощью сетевых партнеров и прокси приложения](media/secure-hybrid-access/secure-hybrid-access.png)

Следующие поставщики сетей предлагают готовые решения и подробные рекомендации по интеграции с Azure AD.

- [Доступ к корпоративному приложению Akamai (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Контроллер доставки приложений Citrix (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

### <a name="secure-hybrid-access-through-vpn-applications"></a>Безопасный гибридный доступ через VPN-приложения

Используя решения VPN, вы можете в любое время обеспечить безопасный доступ к корпоративной сети с любого устройства в любой момент, одновременно защищая данные Организации. Используя Azure AD в качестве поставщика удостоверений (IDP), вы можете использовать современные методы проверки подлинности и авторизации, такие как [единый вход](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) Azure AD и [многофакторная идентификация](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) , для защиты локальных приложений прежних версий.  

![На рисунке показан безопасный гибридный доступ с партнерами VPN и прокси приложения ](media/secure-hybrid-access/app-proxy-vpn.png)

Следующие поставщики VPN предлагают предварительно созданные решения и подробные рекомендации по интеграции с Azure AD.

• [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

• [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

• [F5 BIG-IP APM](https://aka.ms/f5-hybridaccessguide)

• [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

• [Zscaler закрытый доступ (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
