---
title: Создание арендатора Azure для мультитенантного приложения
description: Руководство для независимых поставщиков программного обеспечения по интеграции с Active Directory Azure
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812611"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Создание арендатора Azure для мультитенантного приложения  

Для обеспечения доступа к мультитенантным приложениям необходимо создать арендатора Active Directory Azure для регистрации приложения и включения в реестр идентификационных данных клиента. Смотрите [Выбор правильного протокола федерации для приложения с несколькими арендаторами.](isv-choose-multi-tenant-federation.md) Этот арендатор позволит вам протестировать приложение и федерацию в среде, аналогичной средам Azure AD.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Затраты на размещение мультитенантного приложения

Активный каталог Azure доступен в нескольких изданиях. [Смотрите подробное сравнение функций](https://azure.microsoft.com/pricing/details/active-directory/).

Вы можете создавать подписку Azure и активный каталог Azure бесплатно и использовать основные функции.

## <a name="create-your-tenant"></a>Создайте арендатора

1. Создайте своего Арендатора. Смотрите [Настройка среды разработчиков.](../develop/quickstart-create-new-tenant.md)

2. Включить и протестировать один очный доступ к вашему приложению,

   а. **Для приложений OIDC или Oath** [зарегистрируйте свое заявление](../develop/quickstart-register-app.md) в качестве приложения для нескольких арендаторов. Выберите учетные записи в любом каталоге организаций и варианте личных учетных записей Майкрософт в типах поддерживаемых учетных записей

   b. **Для приложений, основанных на SAML- и WS-Fed,** вы [настраиваете приложения на основе одиночек SAML](configure-single-sign-on-non-gallery-applications.md) с использованием универсального шаблона SAML в Azure AD.

При необходимости можно также [преобразовать приложение с одним арендатором в мультитенант.](../develop/howto-convert-app-to-be-multi-tenant.md)

## <a name="next-steps"></a>Next Steps

[Интеграция SSO в приложение](isv-sso-content.md)
