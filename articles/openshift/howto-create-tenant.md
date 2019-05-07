---
title: Создание клиента Azure AD для Azure Red Hat OpenShift | Документация Майкрософт
description: Вот как можно создать клиент Azure Active Directory (Azure AD) для размещения кластера Microsoft Azure Red Hat OpenShift.
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 3c7f0b263cc775b7f9fec62e4d5388250e5124fb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079125"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Создание клиента Azure AD для Azure Red Hat OpenShift

Требуется Microsoft Azure Red Hat OpenShift [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) клиента, в котором для создания кластера. Объект *клиента* — это выделенный экземпляр Azure AD, который разработчик организации или приложение получает при создании связи с корпорацией Майкрософт, оформите подписку Azure, Microsoft Intune или Microsoft 365. Каждый клиент Azure AD отличается, и отдельно от других Azure AD версии клиента и имеет свои собственные рабочие и удостоверения учебного заведения и регистрации приложений.

Если у вас нет клиента Azure AD, выполните следующие действия для ее создания.

## <a name="create-a-new-azure-ad-tenant"></a>Создание нового клиента Azure AD

Чтобы создать клиент:

1. Войдите в [портала Azure](https://portal.azure.com/) с помощью учетной записи, вы хотите связать с кластером Azure Red Hat OpenShift.
2. Откройте [колонки Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) для создания нового клиента (также известный как новый *Azure Active Directory*).
3. Укажите **имя_организации**.
4. Укажите **первоначальное доменное имя**. Это свойство будет иметь *onmicrosoft.com* добавленным к нему. Вы можете повторно использовать значение *имя_организации* здесь.
5. Выберите страну или регион, где будет создан клиент.
6. Нажмите кнопку **Создать**.
7. После создания клиента Azure AD, выберите **щелкните здесь, чтобы управлять новым каталогом** ссылку. Новое имя клиента должно отображаться в правом верхнем углу портала Azure:  

    ![Снимок экрана портала, на которой отображается имя клиента в правом верхнем углу][tenantcallout]  

8. Запишите *идентификатор_клиента* , позже можно указать место для создания кластера Azure Red Hat OpenShift. На портале вы увидите колонку обзора Azure Active Directory для нового клиента. Выберите **свойства** и скопируйте значение для вашей **идентификатор каталога**. Мы будем называть это значение как `tenant id` в [создать кластер Azure Red Hat OpenShift](tutorial-create-cluster.md) руководства.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Ресурсы

Ознакомьтесь с [документации Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) Дополнительные сведения о [клиентов Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создать субъект-службу, создать секрет и проверки подлинности обратного вызова URL-адрес клиента и создание нового пользователя Active Directory для тестирования приложений в кластере Azure Red Hat OpenShift.

[Создание объекта приложения Azure AD и пользователя](howto-aad-app-configuration.md)