---
title: Настройка токенов — Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как настроить время жизни маркера и параметры совместимости в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2b9cc0c69f0c559c9561a02de4192974ee7f521
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926193"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Настройка маркеров в Azure Active Directory B2C

Из этой статьи вы узнаете, как настроить [время существования и совместимость маркера](tokens-overview.md) в Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Предварительные условия

[Создайте поток пользователя](tutorial-create-user-flows.md), чтобы пользователи могли зарегистрироваться и войти в ваше приложение.

## <a name="configure-jwt-token-lifetime"></a>Настройка времени существования токена JWT

Время существования маркера можно настроить в любом потоке пользователя.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. В верхнем меню выберите фильтр **каталог и подписка** и выберите каталог, содержащий клиент Azure AD B2C.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. Выберите **потоки пользователя (политики)**.
5. Откройте созданный ранее поток пользователя.
6. Выберите **Свойства**.
7. В разделе **время существования маркера**настройте следующие свойства в соответствии с потребностями приложения:

    ![Параметры свойства "время существования токена" в портал Azure](./media/configure-tokens/token-lifetime.png)

8. Нажмите кнопку **Сохранить**.

## <a name="configure-jwt-token-compatibility"></a>Настройка совместимости токенов JWT

1. Выберите **потоки пользователя (политики)**.
2. Откройте созданный ранее поток пользователя.
3. Выберите **Свойства**.
4. В разделе **параметры совместимости токенов**настройте следующие свойства в соответствии с потребностями приложения:

    ![Параметры свойств совместимости маркеров в портал Azure](./media/configure-tokens/token-compatibility.png)

5. Нажмите кнопку **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, как [использовать маркеры доступа](access-tokens.md).



