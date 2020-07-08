---
title: Отключить проверку электронной почты во время регистрации клиента
titleSuffix: Azure AD B2C
description: Узнайте, как отключить проверку электронной почты во время регистрации клиента в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10613bd2d6219272248f882e45ae1c33d2cc9d61
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85384215"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Отключить проверку электронной почты во время регистрации клиента в Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Чтобы отключить проверку по электронной почте, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com)
1. Используйте фильтр **каталог и подписка** в верхнем меню, чтобы выбрать каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Либо щелкните **Все службы**, а затем найдите и выберите **Azure AD B2C**
1. Выберите **Потоки пользователей**.
1. Выберите поток пользователя, для которого требуется отключить проверку по электронной почте. Например, *B2C_1_signinsignup*.
1. Выберите **макеты страниц**.
1. Выберите **страницу регистрации локальной учетной записи**.
1. В разделе **атрибуты пользователя**выберите **адрес электронной почты**.
1. В раскрывающемся списке **требуется подтверждение** выберите **нет**.
1. Нажмите кнопку **Сохранить**. Проверка электронной почты теперь отключена для этого потока пользователя.

## <a name="next-steps"></a>Дальнейшие шаги

- Узнайте, как [настроить пользовательский интерфейс в Azure Active Directory B2C](customize-ui-overview.md)

