---
title: Создание клиента Azure AD для Azure Red Hat OpenShift
description: Вот как можно создать клиент Azure Active Directory (Azure AD) для размещения кластера Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79243696"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Создание клиента Azure AD для Azure Red Hat OpenShift

Для Microsoft Azure Red Hat OpenShift требуется клиент [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) , в котором будет создан кластер. *Клиент* — это выделенный экземпляр Azure AD, который разработчик организации или приложения получает при создании связи с Майкрософт путем регистрации в Azure, Microsoft Intune или Microsoft 365. Каждый клиент Azure AD отличается от других клиентов Azure AD и имеет собственные рабочие и учебные удостоверения и регистрации приложений.

Если у вас еще нет клиента Azure AD, следуйте этим инструкциям, чтобы создать его.

## <a name="create-a-new-azure-ad-tenant"></a>Создание нового клиента Azure AD

Чтобы создать клиент, выполните следующие действия.

1. Войдите в [портал Azure](https://portal.azure.com/) с помощью учетной записи, которую вы хотите связать с кластером Azure Red Hat OpenShift.
2. Откройте [колонку Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) , чтобы создать новый клиент (также известный как новый *Azure Active Directory*).
3. Укажите **название организации**.
4. Укажите **исходное доменное имя**. К нему будет добавлен *onmicrosoft.com* . Вы можете повторно использовать значение *имени Организации* здесь.
5. Выберите страну или регион, в котором будет создан клиент.
6. Нажмите кнопку **Создать**.
7. После создания клиента Azure AD выберите ссылку **щелкните здесь для управления ссылкой на новый каталог** . Новое имя клиента должно отображаться в правом верхнем углу портал Azure:  

    ![Снимок экрана портала, отображающий имя клиента в верхнем правом углу][tenantcallout]  

8. Запишите *идентификатор клиента* , чтобы позднее указать, где можно создать кластер Azure Red Hat OpenShift. На портале отобразится колонка обзор Azure Active Directory для нового клиента. Выберите **Свойства** и скопируйте значение для **идентификатора каталога**. Мы будем называть это значение, как `TENANT` описано в учебнике [Создание кластера Azure Red Hat OpenShift](tutorial-create-cluster.md) .

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Ресурсы

Дополнительные сведения о [клиентах Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)см. в [Azure Active Directory документации](https://docs.microsoft.com/azure/active-directory/) .

## <a name="next-steps"></a>Дальнейшие шаги

Узнайте, как создать субъект-службу, сгенерировать секрет клиента и URL обратного вызова проверки подлинности, а затем создать нового Active Directory пользователя для тестирования приложений в кластере Azure Red Hat OpenShift.

[Создание объекта приложения и пользователя Azure AD](howto-aad-app-configuration.md)
