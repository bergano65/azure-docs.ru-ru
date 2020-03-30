---
title: Настройка токенов - Активный каталог Azure B2C Документы Майкрософт
description: Узнайте, как настроить настройки срока службы и совместимости маркеров в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189623"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Настройка токенов в Azure Active Directory B2C

В этой статье вы узнаете, как настроить [срок службы и совместимость токена](tokens-overview.md) в Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Предварительные требования

[Создайте поток пользователя](tutorial-create-user-flows.md), чтобы пользователи могли зарегистрироваться и войти в ваше приложение.

## <a name="configure-token-lifetime"></a>Настройка срока службы маркера

Вы можете настроить срок службы маркера на любом потоке пользователя.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Убедитесь, что вы используете каталог, содержащий ваш арендатор Azure AD B2C. Выберите фильтр **подписки каталога** в верхнем меню и выберите каталог, содержащий ваш клиент Azure AD B2C.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. Выберите **потоки пользователей (политики)**.
5. Откройте пользовательский поток, созданный ранее.
6. Выберите **Свойства**.
7. В **течение срока службы Token**отрегулируйте следующие свойства в соответствии с потребностями приложения:

    ![Настройки свойств пожизненных токенов на портале Azure](./media/configure-tokens/token-lifetime.png)

8. Нажмите **Сохранить**.

## <a name="configure-token-compatibility"></a>Настройка совместимости токенов

1. Выберите **потоки пользователей (политики)**.
2. Откройте пользовательский поток, созданный ранее.
3. Выберите **Свойства**.
4. В **настройках совместимости токенов**отрегулируйте следующие свойства в соответствии с потребностями приложения:

    ![Настройки свойств совместимости токенов на портале Azure](./media/configure-tokens/token-compatibility.png)

5. Нажмите **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, как [использовать маркеры доступа](access-tokens.md).



