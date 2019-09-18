---
title: Определение настраиваемых атрибутов в Azure Active Directory B2C | Документация Майкрософт
description: Определите настраиваемые атрибуты в Azure Active Directory B2C для сбора данных о клиентах.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e6b0eac0b8cf7f61d76f90a4f769ba11abab6999
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065676"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Определение настраиваемых атрибутов в Azure Active Directory B2C

 Каждое приложение, взаимодействующее с клиентами, имеет свои уникальные требования к сбору информации. Клиент Azure Active Directory B2C (Azure AD B2C) поставляется со встроенным набором данных, хранящихся в атрибутах, таких как имя, фамилия, город и почтовый индекс. С помощью Azure AD B2C можно расширить набор атрибутов, хранящихся в каждой учетной записи клиента.

 Вы можете создать настраиваемые атрибуты на [портале Azure](https://portal.azure.com/) и использовать их в потоках пользователей регистрации, регистрации или входа в систему, а также редактирования профиля. Можно также читать и записывать эти атрибуты с помощью [API Graph Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md). Настраиваемые атрибуты в Active Directory B2C используют [расширения схемы каталога API Graph Azure AD](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> Поддержка более новых [Microsoft Graph API](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) для запросов Azure AD B2C клиента все еще находится в разработке.
>

## <a name="create-a-custom-attribute"></a>Создание настраиваемого атрибута

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что используется каталог с вашим клиентом Azure AD B2C, переключившись на него в правом верхнем углу окна портала Azure. Выберите сведения о подписке, а затем выберите **Переключение каталога**.

    ![Переключение на клиент Azure AD B2C](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Выберите каталог, содержащий ваш клиент.

    ![Клиент B2C, выделенный в каталоге и фильтре подписок](./media/active-directory-b2c-reference-custom-attr/select-directory.PNG)

3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Выберите **Атрибуты пользователя**, а затем щелкните **Добавить**.
5. Введите **Имя** настраиваемого атрибута (например, ShoeSize).
6. Выберите **Тип данных**. Здесь доступны только варианты **String**, **Boolean** и **Int**.
7. При желании введите **Описание**, которое используется только в информационных целях.
8. Нажмите кнопку **Создать**.

Теперь настраиваемый атрибут доступен в списке **Атрибуты пользователя** и может использоваться в ваших потоках пользователей. Пользовательский атрибут создается только при первом использовании в любом потоке пользователя, но не при добавлении в список **Атрибуты пользователя**.


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Использование настраиваемого атрибута в потоке пользователя

1. В клиенте Azure AD B2C выберите **Потоки пользователей**.
2. Откройте политику (например B2C_1_SignupSignin), щелкнув ее.
4. Выберите **Атрибуты пользователя**, а затем — настраиваемый атрибут (например, ShoeSize). Нажмите кнопку **Сохранить**.
5. Щелкните **Утверждения приложения** и выберите настраиваемый атрибут.
6. Нажмите кнопку **Сохранить**.

После создания нового пользователя с помощью пользовательского потока, который использует только что созданный настраиваемый атрибут, этот объект можно запросить в [Azure AD Graph Explorer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart). Кроме того, можно использовать функцию « [**Запуск потока пользователя**](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) » в потоке пользователя, чтобы проверить работу клиента. Теперь вы увидите **ShoeSize** в списке атрибутов, собираемых в процессе регистрации, а также в токене, возвращаемом в приложение.

