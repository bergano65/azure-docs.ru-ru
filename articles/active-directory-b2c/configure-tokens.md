---
title: Настройка маркеров (Azure Active Directory B2C) | Документация Майкрософт
description: Узнайте, как настроить время существования и параметры совместимости маркера в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dca235374ca20ef8a17e685706f952127d5ece2a
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869441"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Настройка маркеров в Azure Active Directory B2C

Из этой статьи вы узнаете, как настроить [время существования и параметры совместимости маркера](tokens-overview.md) в Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Предварительные требования

[Создайте поток пользователя](tutorial-create-user-flows.md), чтобы пользователи могли зарегистрироваться и войти в ваше приложение.

## <a name="configure-jwt-token-lifetime"></a>Настройка времени существования маркеров JWT

Время существования маркера можно настроить в любом потоке пользователя.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите фильтр **Каталог и подписка** в верхнем меню, а затем каталог, содержащий клиент Azure AD B2C.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. Выберите **Потоки пользователей (политики)** .
5. Откройте созданный ранее пользовательский поток.
6. Выберите **Свойства**.
7. В разделе **Срок действия маркера** настройте следующие свойства в соответствии с потребностями приложения.

    ![Параметры времени существования маркера на портале Azure](./media/configure-tokens/token-lifetime.png)

8. Выберите команду **Сохранить**.

## <a name="configure-jwt-token-compatibility"></a>Настройка совместимости маркеров JWT

1. Выберите **Потоки пользователей (политики)** .
2. Откройте созданный ранее пользовательский поток.
3. Выберите **Свойства**.
4. В разделе **Параметры совместимости токенов** настройте следующие свойства в соответствии с потребностями приложения.

    ![Параметры совместимости маркера на портале Azure](./media/configure-tokens/token-compatibility.png)

5. Выберите команду **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия

Изучите информацию о том, как [запросить маркеры доступа](access-tokens.md).



