---
title: Добавление проверки подлинности в мобильные приложения с помощью центра приложений Visual Studio и служб Azure
description: Узнайте о таких службах, как центр приложений Visual Studio, которые помогают настроить проверку подлинности пользователей и позволяют мобильным приложениям проходить проверку подлинности с помощью учетных записей социальных сетей, Azure Active Directory и пользовательской проверки подлинности.
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: bc7d8d4aaec6ebe27a0f8d2ecc11ca408266f7ef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453247"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Добавление проверки подлинности и управление удостоверениями пользователей в мобильных приложениях

Наличие представления пользователя и их поведения в приложении позволяет разработчикам лучше привлекать пользователей, создавая для них специализированное взаимодействие. Независимо от того, являетесь ли вы разработчиком приложения, создающим приложение для совместной работы для пользователей внутри организации, или вы создаете следующую платформу социальных сетей, вам потребуется способ проверки подлинности пользователей и управления удостоверениями пользователей. Служба управления удостоверениями является одной из наиболее важных функций мобильной серверной службы.

Используйте следующие службы, чтобы включить проверку подлинности пользователей в мобильных приложениях.

## <a name="visual-studio-app-center"></a>Центр приложений Visual Studio.
[Проверка подлинности центра приложений](/appcenter/auth/) — это облачная служба управления удостоверениями, которую разработчики могут использовать для проверки подлинности пользователей и управления удостоверениями пользователей. Проверка подлинности в центре приложений также интегрируется с другими частями центра приложений Visual Studio. Разработчики могут использовать удостоверение пользователя для [просмотра данных пользователей](/appcenter/data/index) в других службах и даже [отправлять push-уведомления пользователям, а не отдельным устройствам](/appcenter/push/push-to-user#app-center-auth-set-identity). 

**Основные возможности**
- На платформе Azure Active Directory B2C (Azure AD B2C). 
    - Корпоративный уровень.
    - Высокая доступность.
    - Безопасная и Глобальная служба.
- Используйте собственные удостоверения и возможность использования других популярных поставщиков удостоверений и управления доступом, таких как Auth0 и Firebase.
- Поддержка Azure Active Directory.
    - Подключите существующие клиенты Azure AD. 
    - Включите проверку подлинности в корпоративном домене.
    - Управление доступом к конфиденциальным данным.
- Простое взаимодействие с пользователем и пакет SDK для Magical. для этого используется оболочка библиотеки проверки подлинности Майкрософт с пакетом SDK для центра приложений Visual Studio.
- Поддержка платформ для iOS, Android, Xamarin и реагирования на машинный код.

**Справочные материалы**
- [Регистрация в центре приложений Visual Studio](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Приступая к работе с проверкой подлинности App Center](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) — это служба управления удостоверениями "бизнес-потребитель" (B2C), которую разработчики могут использовать для проверки подлинности своих клиентов. Эта служба "белые метки" позволяет разработчикам настраивать и контролировать взаимодействие пользователей с веб-приложениями, настольными, мобильными и одностраничными. С помощью Azure AD B2C пользователи могут регистрировать, изменять свои профили, а также входить в них и сбрасывать пароли. Azure AD B2C реализует формы протоколов OpenID Connect и OAuth 2.0. 

**Основные возможности**
- Безопасная проверка подлинности клиентов с помощью предпочтительного поставщика удостоверений.
- Управление удостоверениями клиентов и доступом.
- Получите поддержку входа для социальных сетей, таких как Facebook, GitHub, Google, LinkedIn, Twitter, WeChat и Weibo.
- Подключитесь к учетным записям пользователей с помощью стандартных отраслевых протоколов, таких как OpenID Connect Connect или SAML, чтобы обеспечить возможность управления удостоверениями на различных платформах.
- Предоставьте фирменную регистрацию и возможности входа.
- Легко интегрируются с базами данных CRM, инструментами для маркетинговой аналитики и системами проверки учетных записей.
- Сбор данных для входа, предпочтений и преобразований для клиентов.

**Справочные материалы**
- [Портал Azure](https://portal.azure.com/)
- [Документация по Azure AD B2C](/azure/active-directory-b2c/)
- [Краткие руководства](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Примеры](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) — это облачная служба управления удостоверениями и доступом Майкрософт, которая позволяет сотрудникам входить в систему и получать доступ к следующим:
- Внешние ресурсы, такие как Microsoft Office 365, портал Azure и тысячи других приложений SaaS (программное обеспечение как услуга).
- во внутренних ресурсах, таких как приложения в корпоративной сети и интрасети, а также в любых облачных приложениях, разработанных вашей организацией.

**Основные возможности**
- Простой и надежный доступ путем подключения пользователей к необходимым приложениям.
- Комплексная защита идентификации и повышенная безопасность для удостоверений и доступа на основе контекста пользователя, расположения, устройства, данных и приложения.
- Тысячи предварительно интегрированных приложений для коммерческих и пользовательских приложений, таких как Office 365, Salesforce.com и Box.
- Возможность управления доступом в масштабе.

**Справочные материалы**
- [Портал Azure](https://portal.azure.com/)
- [Что такое Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Начало работы с Azure AD](/azure/active-directory/fundamentals/active-directory-whatis)
- [Краткие руководства](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)