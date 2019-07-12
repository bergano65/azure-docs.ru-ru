---
title: Создание клиента Azure для мультитенантного приложения
description: Рекомендации для независимых поставщиков программного обеспечения на интеграцию с Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659584"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Создание клиента Azure для мультитенантного приложения  

Для предоставления доступа к мультитенантного приложения необходимо создать клиент Azure Active Directory для регистрации приложения и включить федерацию удостоверений клиента. См. в разделе [Выбор протокола правой федерации для мультитенантного приложения](isv-choose-multi-tenant-federation.md). Этот клиент позволит тестировать приложения и федерации в среду, аналогичную среды клиентов Azure AD.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Затраты на размещение мультитенантного приложения

Azure Active Directory доступна в трех SKU, Free, Basic и Premium. [Подробное сравнение функций см. в разделе](https://azure.microsoft.com/pricing/details/active-directory/).

Можно создать подписку Azure и Azure active directory бесплатно и использовать основные функции.

## <a name="create-your-tenant"></a>Создание клиента

1. Создание вашего клиента. См. в разделе [Настройка среды разработки](../develop/quickstart-create-new-tenant.md).

2. Включить и проверить единый вход на доступ к вашему приложению,

   1\. **Для приложений OIDC или Oath**, [зарегистрировать приложение](../develop/quickstart-register-app.md) как мультитенантное приложение. Выберите учетные записи в любой организации каталоге и параметр личное учетные записи Майкрософт в учетной записи, поддерживаемые типы

   2\. **SAML и WS-Fed-приложений на основе**, вы [на основе SAML, настроить единый вход](configure-single-sign-on-non-gallery-applications.md) приложения при помощи универсального шаблона SAML в Azure AD.

Вы также можете [преобразование приложения одним клиентом для нескольких клиентов](../develop/howto-convert-app-to-be-multi-tenant.md) при необходимости.

## <a name="next-steps"></a>Следующие шаги

[Интеграция единого входа в приложение](isv-sso-content.md)
