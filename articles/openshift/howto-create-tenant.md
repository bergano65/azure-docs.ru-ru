---
title: Создание клиента Azure AD для Azure Red Hat OpenShift
description: Вот как можно создать клиент Azure Active Directory (Azure AD) для размещения кластера Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: e949f1ac5259ba35772ce98c2ee88e5ea66c2d84
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221168"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Создание клиента Azure AD для Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 будет прекращена 30 июня 2022. Поддержка для создания новых кластеров Azure Red Hat OpenShift 3,11 продолжится до 30 ноября 2020. После выхода из эксплуатации оставшиеся кластеры Azure Red Hat OpenShift 3,11 будут закрыты для предотвращения уязвимости системы безопасности.
> 
> Следуйте указаниям этого руководством, чтобы [создать кластер Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Если у вас есть определенные вопросы, [свяжитесь с нами](mailto:arofeedback@microsoft.com).

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

8. Запишите *идентификатор клиента* , чтобы позднее указать, где можно создать кластер Azure Red Hat OpenShift. На портале отобразится колонка обзор Azure Active Directory для нового клиента. Выберите **Свойства** и скопируйте значение для **идентификатора каталога**. Мы будем называть это значение, как описано `TENANT` в учебнике [Создание кластера Azure Red Hat OpenShift](tutorial-create-cluster.md) .

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Ресурсы

Дополнительные сведения о [клиентах Azure AD](../active-directory/develop/quickstart-create-new-tenant.md)см. в [Azure Active Directory документации](../active-directory/index.yml) .

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создать субъект-службу, сгенерировать секрет клиента и URL обратного вызова проверки подлинности, а затем создать нового Active Directory пользователя для тестирования приложений в кластере Azure Red Hat OpenShift.

[Создание объекта приложения и пользователя Azure AD](howto-aad-app-configuration.md)