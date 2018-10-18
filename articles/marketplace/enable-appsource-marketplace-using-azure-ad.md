---
title: Включение описания в Microsoft AppSource и Azure Marketplace с помощью Azure Active Directory | Azure
description: Включение типа описания с помощью Azure Active Directory в Azure Marketplace и AppSource для издателей приложений и служб.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: d7fd09928c0a687755d216e7f10f7eac23677c63
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987345"
---
# <a name="enable-a-microsoft-appsource-and-azure-marketplace-listing-by-using-azure-active-directory"></a>Включение описания в Microsoft AppSource и Azure Marketplace с помощью Azure Active Directory

Microsoft Azure Active Directory (Azure AD) — это облачная служба идентификации, которая позволяет выполнять проверку подлинности с помощью учетной записи Майкрософт с использованием стандартных платформ.  Дополнительные сведения об Azure AD см. в [этой статье](https://azure.microsoft.com/services/active-directory).

## <a name="benefits-of-using-azure-active-directory"></a>Преимущества использования Azure Active Directory

Клиенты Microsoft AppSource и Azure Marketplace используют имеющиеся в продуктах возможности для поиска по каталогам описаний, для чего им требуется выполнить вход в продукт.  Интегрировав приложение с Azure AD, вы можете повысить вовлеченность и удобство работы клиентов. Azure AD:

- Обеспечивает единый вход для миллионов корпоративных пользователей.
- Обеспечивает согласованные возможности входа для приложений, опубликованных различными партнерами.
- Предоставляет масштабируемую кроссплатформенную проверку входа для мобильных и облачных приложений.

Как описано в разделе ниже, для реализации Azure AD при публикации в Marketplace требуются некоторые предложения.

## <a name="azure-active-directory-requirements"></a>Требования для Azure Active Directory

Для Microsoft AppSource и Azure Marketplace имеются различные [варианты описаний и типы предложений](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).  Ниже представлены требования Azure AD для этих вариантов и типов.

| **Тип предложения**    | **Требуется единый вход AAD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Связь со мной | Пробная версия | Тестовый выпуск | "Транзакция" |
| Виртуальная машина | Недоступно | Нет  | Нет  | Нет  |
| Приложения Azure (шаблон решения)  | Недоступно | Недоступно | Недоступно | Недоступно |
| Управляемые приложения  | Недоступно | Недоступно | Недоступно | Нет  |
| SaaS  | Нет  | Yes | Да | Yes |
| Контейнеры  | Недоступно | Недоступно | Недоступно | Нет  |
| Консультационные услуги  | Нет  | Недоступно | Недоступно | Недоступно |

Дополнительные сведения о технических требованиях к SaaS см. в статье [Руководство по публикации SaaS-приложений](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="integration-with-azure-active-directory"></a>Интеграция с Azure Active Directory

Сведения об интеграции с Azure AD для включения единого входа см. в на странице https://aka.ms/aaddev.

Дополнительные сведения о едином входе Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Включение типа приложения пробной версии с помощью Azure Active Directory

Когда клиент выбирает бесплатную пробную версию в Marketplace, он перенаправляется в пробную среду. В пробной среде вы можете выполнять настройку для клиента напрямую, не требуя дополнительных шагов входа. Приложение или предложение получает маркер из Azure AD во время выполнения аутентификации. Маркер содержит ценные сведения о пользователе, используемые для создания учетной записи пользователя в приложении или предложении. Вы можете автоматизировать настройку клиента и увеличить вероятность преобразования.

Дополнительные сведения о маркере, полученном от Azure AD во время выполнения аутентификации, см. в разделе [Примеры токенов](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Преимущества использования Azure AD для включения аутентификации одним щелчком в приложении или пробной версии: Azure AD предоставляет следующие преимущества: 
*   Упрощается взаимодействие клиентов с пробной версией в Marketplace.
*   Сохраняется ощущение использования продукта, даже когда пользователь перенаправляется с Marketplace на ваш домен или в пробную среду.
*   Сокращается вероятность отказа от перенаправления, так как отсутствует дополнительный шаг выполнения входа.
*   Уменьшаются ограничения развертывания для большого числа пользователей Azure AD.

### <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Проверка интеграции Azure AD с Marketplace для мультитенантных приложений
Azure AD позволяет использовать следующие возможности для вашего решения:
*   Регистрация приложения в магазинах в Marketplace.
*   Включите функцию поддержки мультитенантности в Azure AD, чтобы получить пробную версию одним щелчком.

Дополнительные сведения о регистрации приложений см. в статье [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

Если вы не использовали федеративный единый вход (SSO) в Azure AD, выполните следующие действия:
1.  Зарегистрируйте свое приложение в Marketplace. 
2.  Настройте единый вход в Azure AD с помощью OpenID Connect или OAuth 2.0.
    *   Дополнительные сведения об OAuth 2.0 см. в статье [Авторизация доступа к веб-приложениям Azure Active Directory с помощью потока предоставления кода OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
    *   Дополнительные сведения об Open ID Connect см. в статье [Предоставление доступа к веб-приложениям с помощью OpenID Connect и Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
3.  Включите функцию поддержки мультитенантности в Azure AD, чтобы предоставить пробную версию одним щелчком.
    
    Дополнительные сведения о сертификации AppSource см. в статье [Как получить сертификат AppSource для Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

### <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Проверка интеграции с Azure AD в Marketplace для однотенантных приложений
Azure AD позволяет использовать один из следующих вариантов для однотенантного решения: 
*   Добавьте пользователей в свой каталог в качестве гостевых пользователей, используя Azure Active Directory B2B (Azure AD B2B). Дополнительные сведения об Azure AD B2B см. в статье [Что такое служба совместной работы Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Вручную настройте бесплатную пробную версию для клиентов, используя параметр публикации "Свяжитесь со мной".
*   Разработайте тестовый выпуск для одного клиента.
*   Создайте демонстрационное мультитенантное приложение, использующее единый вход.

## <a name="next-steps"></a>Дополнительная информация

Если это еще не сделано, 
- [зарегистрируйтесь](https://azuremarketplace.microsoft.com/sell) в Marketplace.

Если вы зарегистрированы и создаете предложение или работаете над существующим,
- [войдите на Портал Cloud Partner](https://cloudpartner.azure.com/), чтобы создать или завершить предложение.

