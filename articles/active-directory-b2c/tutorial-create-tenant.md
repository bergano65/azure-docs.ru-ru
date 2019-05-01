---
title: Руководство по созданию клиента Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как подготовить приложения к регистрации, создав клиент Azure Active Directory B2C с помощью портала Azure.
services: B2C
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 12ce37579a6f93ba600c39416c7566ac673e9560
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723331"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Руководство по Создание клиента Azure Active Directory B2C

Чтобы приложения могли взаимодействовать с Azure Active Directory (Azure AD) B2C, их нужно зарегистрировать в клиенте, которым вы управляете.

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Создание клиента Azure AD B2C
> * Привязка клиента к подписке

Сведения о регистрации приложения приводятся в следующем руководстве.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-an-azure-ad-b2c-tenant"></a>Создание клиента Azure AD B2C

1. Войдите на [портале Azure](https://portal.azure.com/).
2. Убедитесь, что вы используете каталог, содержащий вашу подписку, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ее. Этот каталог отличается от того, в котором будет содержаться ваш клиент Azure AD B2C.

    ![Переключение на каталог подписки](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Выберите **Создать ресурс** в левом верхнем углу окна портала Azure.
4. Найдите и выберите **Active Directory B2C**, а затем нажмите кнопку **Создать**.
5. Выберите **Создать B2C-клиент Azure AD**, введите название организации и исходное доменное имя, которое будет использоваться в имени клиента, а затем выберите страну (позже ее нельзя будет изменить) и нажмите кнопку **Создать**.

    ![Создание клиента](./media/tutorial-create-tenant/create-tenant.png)

    В этом примере имя клиента — contoso0926Tenant.onmicrosoft.com.

6. На странице **Создать нового клиента B2C или ссылку на существующего клиента** выберите **Связать существующего клиента Azure AD B2C с вашей подпиской Azure**, выберите созданного арендатора, свою подписку, а затем нажмите кнопку **Создать**.
7. Введите имя для группы ресурсов, которая будет содержать клиента, выберите расположение и нажмите кнопку **Создать**.
8. Чтобы начать использовать новый клиент, убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.

    ![Переключение на каталог клиента](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание клиента Azure AD B2C
> * Привязка клиента к подписке

> [!div class="nextstepaction"]
> [Tutorial: Register your applications in Azure Active Directory B2C](tutorial-register-applications.md) (Руководство. Регистрация приложений в Azure Active Directory B2C)
