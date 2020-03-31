---
title: Отключить проверку электронной почты во время регистрации клиента
titleSuffix: Azure AD B2C
description: Узнайте, как отключить проверку электронной почты во время регистрации клиента в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126723"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Отработать проверку электронной почты во время регистрации клиента в Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Выполните следующие действия, чтобы отключить проверку электронной почты:

1. Войти на [портал Azure](https://portal.azure.com)
1. Используйте фильтр **подписки каталога в** верхнем меню, чтобы выбрать каталог, содержащий ваш арендатор Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Либо щелкните **Все службы**, а затем найдите и выберите **Azure AD B2C**
1. Выберите **Потоки пользователей**.
1. Выберите поток пользователей, для которого вы хотите отключить проверку электронной почты. Например, *B2C_1_signinsignup*.
1. Выберите **макеты Страницы**.
1. Выберите **страницу регистрации локальной учетной записи.**
1. Под атрибутами пользователя выберите **адрес электронной** **почты.**
1. В **REQUIRES VERIFICATION** выпадение вниз, выберите **Нет**.
1. Нажмите кнопку **Сохранить**. Проверка электронной почты теперь отключена для этого потока пользователей.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [настроить пользовательский интерфейс в Azure Active Directory B2C](customize-ui-overview.md)

