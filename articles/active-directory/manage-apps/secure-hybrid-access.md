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
ms.openlocfilehash: 5a936552bd7d3b97e2c027f7b0d23abd7c21b6e2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94652201"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Безопасный гибридный доступ: защищенные устаревшие приложения с Azure Active Directory

Теперь вы можете защитить локальные и облачные приложения проверки подлинности, подключив их к Azure Active Directory (AD) с помощью:

- [Прокси приложения Azure AD.](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Существующие контроллеры и сети доставки приложений](#sha-through-networking-and-delivery-controllers)

- [Приложения виртуальной частной сети (VPN) и Software-Defined периметра (SDP)](#sha-through-vpn-and-sdp-applications)

Вы можете преодолеть разрыв и усилить уровень безопасности для всех приложений с помощью таких возможностей Azure AD, как [Условный доступ](../conditional-access/overview.md) Azure AD и [Защита идентификации](../identity-protection/overview-identity-protection.md)Azure AD.

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Безопасный гибридный доступ (SHA) через Azure AD Application Proxy
  
С помощью [прокси приложения](./what-is-application-proxy.md) можно обеспечить [безопасный удаленный доступ](./application-proxy.md) к локальным веб-приложениям. Пользователи не должны использовать VPN. Пользователи получают возможность легко подключаться к своим приложениям с любого устройства после [единого входа](./add-application-portal-setup-sso.md). Прокси приложения предоставляет удаленный доступ как службу и позволяет [легко публиковать локальные приложения](./application-proxy-add-on-premises-application.md) для пользователей за пределами корпоративной сети. Она помогает масштабировать управление облачным доступом без необходимости вносить изменения в локальные приложения. [Планирование развертывания AD application proxy Azure](./application-proxy-deployment-plan.md) в качестве следующего этапа.

## <a name="azure-ad-partner-integrations"></a>Интеграция с партнерами Azure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA через сети и контроллеры доставки

В дополнение к [Azure AD application proxy](./what-is-application-proxy.md), чтобы использовать [инфраструктуру с нулевым доверием](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), партнеры Майкрософт со сторонними поставщиками. Вы можете использовать существующие сетевые устройства и контроллеры доставки, а также легко защищать устаревшие приложения, которые важны для бизнес-процессов, но не смогли обеспечить защиту перед использованием Azure AD. Скорее всего, у вас уже есть все необходимое для защиты этих приложений.

![На рисунке показан безопасный гибридный доступ с помощью сетевых партнеров и прокси приложения](./media/secure-hybrid-access/secure-hybrid-access.png)

Следующие поставщики сетей предлагают готовые решения и подробные рекомендации по интеграции с Azure AD.

- [Доступ к корпоративному приложению Akamai (EAA)](../saas-apps/akamai-tutorial.md)

- [Контроллер доставки приложений Citrix (ADC)](../saas-apps/citrix-netscaler-tutorial.md)

- [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)

- [Kemp](../saas-apps/kemp-tutorial.md)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA через VPN и SDP-приложения

Используя решения VPN и SDP, вы можете в любое время обеспечить безопасный доступ к корпоративной сети с любого устройства в любой момент, одновременно защищая данные Организации. Используя Azure AD в качестве поставщика удостоверений (IDP), вы можете использовать современные методы проверки подлинности и авторизации, такие как [единый вход](./what-is-single-sign-on.md) Azure AD и [многофакторная идентификация](../authentication/concept-mfa-howitworks.md) , для защиты локальных приложений прежних версий.  

![На рисунке показан безопасный гибридный доступ с партнерами VPN и прокси приложения ](./media/secure-hybrid-access/app-proxy-vpn.png)

Следующие поставщики услуг VPN и SDP предлагают готовые решения и подробные рекомендации по интеграции с Azure AD.

• [Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)

• [Fortinet](../saas-apps/fortigate-ssl-vpn-tutorial.md)

• [F5 BIG-IP APM](../saas-apps/headerf5-tutorial.md)

• [Palo Alto Networks Global Protect](../saas-apps/paloaltoadmin-tutorial.md)

• [Zscaler закрытый доступ (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)