---
title: Включение описания в Microsoft AppSource и Azure Marketplace с помощью Azure Active Directory | Azure
description: Включение типа описания с помощью Azure Active Directory в Azure Marketplace и AppSource для издателей приложений и служб.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 324f8def5ddafb15156a31fe5addabadcee6f115
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160619"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Включение описания в AppSource и Marketplace с помощью Azure Active Directory

 Azure Active Directory (Azure AD) — это облачная служба идентификации, которая позволяет выполнять проверку подлинности с помощью учетной записи Майкрософт. В Azure AD используются стандартные отраслевые платформы. Дополнительные [сведения о Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Преимущества Azure AD

Клиенты Microsoft AppSource и Azure Marketplace используют имеющиеся в продуктах возможности для поиска по каталогам описаний. Для этого им требуется выполнить вход в продукт. Интеграция с Azure AD обеспечивает следующие преимущества:

- Более быстрое взаимодействие и улучшение качества.
- Единый вход для миллионов корпоративных пользователей.
- Согласованные возможности входа для приложений, опубликованных различными партнерами.
- Масштабируемую кроссплатформенную проверку входа для мобильных и облачных приложений.

## <a name="offers-that-require-azure-ad"></a>Предложения, для которых может понадобиться Azure AD

При внедрении службы Azure AD требования для AppSource и Azure Marketplace отличаются. Это происходит из-за того, что существую различные [варианты описаний и типы предложений](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type). Дополнительные сведения см. в таблице, приведенной ниже.

| **Тип предложения**    | **Требования к единому входу в Azure AD**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Связь со мной | Пробная версия | Тестовый выпуск | "Транзакция" |
| Виртуальная машина | Н/Д | Нет | Нет | Нет |
| Приложения Azure (шаблон решения)  | Н/Д | Н/Д | Н/Д | Н/Д |
| Управляемые приложения  | Н/Д | Н/Д | Н/Д | Нет |
| SaaS  | Нет | Да | Да | Да |
| Контейнеры  | Н/Д | Н/Д | Н/Д | Нет |
| Консультационные услуги  | Нет | Н/Д | Н/Д | Н/Д |

Дополнительные сведения о технических требованиях к SaaS см. в статье [Руководство по публикации SaaS-приложений](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Интеграция с Azure AD

- Дополнительны сведения о включении единого входа с помощью интеграции Azure AD со списком см. в статье [Платформа удостоверений Майкрософт (Azure Active Directory для разработчиков)]( https://docs.microsoft.com/azure/active-directory/develop/).
- Дополнительные сведения о едином входе в Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Включение типа приложения пробной версии

Автоматизация настройки клиента может увеличить вероятность преобразования. Когда клиент выбирает тип приложения пробную версию и перенаправляется в пробную среду, вы можете выполнять настройку для клиента напрямую, не требуя дополнительных шагов входа.

Во время процесса проверки подлинности Azure AD отправляет маркер приложению или предложению. Сведения о пользователе, предоставляемые маркером, используются для создания учетной записи пользователя в приложении или предложении. Дополнительные сведения см. в статье [Маркеры идентификации](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Использование Azure AD для включения аутентификации одним щелчком в приложении или в типе приложения пробной версии дает следующе преимущества.

- Упрощается взаимодействие клиентов с типом приложения пробной версии в Marketplace.
- Сохраняется ощущение использования продукта, даже когда пользователь перенаправляется с Marketplace на ваш домен или в пробную среду.
- Сокращается вероятность отказа при перенаправлении пользователей, так как отсутствует дополнительный шаг выполнения входа.
- Уменьшаются ограничения развертывания для большого числа пользователей Azure AD.

## <a name="verify-azure-ad-integration"></a>Проверка интеграции с Azure AD

### <a name="multitenant-solutions"></a>Мультитенантные решения

Используйте Azure AD для поддержки следующих действий.

- Зарегистрируйте свое приложение в одном из онлайн-магазинов Marketplace. Чтобы получить дополнительные сведения, см. статью [Краткое руководство. Регистрация приложения в конечной точке Azure Active Directory версии 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) или [Как получить сертификат AppSource для Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).
- Включите функцию поддержки мультитенантности в Azure AD, чтобы получить пробную версию одним щелчком.

Если вы не использовали федеративный единый вход в Azure AD, выполните следующие действия:

1. Зарегистрируйте свое приложение в Marketplace.
1. Разрабатывайте единый вход с помощью Azure AD, используя [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) или [OpenID Connect Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Включите функцию поддержки мультитенантности в Azure AD, чтобы предоставить пробную версию одним щелчком.

### <a name="single-tenant-solutions"></a>Решения для приложений с одним клиентом

Используйте Azure AD для поддержки одного из следующих действий.

- Добавление гостевых пользователей с помощью [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Вручную настройте пробные версии для клиентов, используя вариант публикации " **свяжитесь со мной** ".
- Разработайте тестовый выпуск для одного клиента.
- Создайте демонстрационное мультитенантное приложение, использующее единый вход.

## <a name="next-steps"></a>Следующие шаги

Если это еще не сделано, 

- [Сведения](https://azuremarketplace.microsoft.com/sell) о Marketplace.

Чтобы зарегистрироваться в центре партнеров, начните создавать новое предложение или работать над существующим:

- [Войдите в центр партнеров](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , чтобы создать или завершить свое предложение.
