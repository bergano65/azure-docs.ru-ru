---
title: Создание арендатора Azure AD для Azure Red Hat OpenShift
description: Вот как создать для размещения кластера Microsoft Azure Red Hat OpenShift — это вопрос создания для размещения кластера Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243696"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Создание арендатора Azure AD для Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift требуется для создания кластера для создания кластера для создания элемента [Azure Active Directory (Azure AD).](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) *Арендатор* — это специализированный экземпляр Azure AD, который получает разработчик организации или приложения при создании отношений с корпорацией Майкрософт, подписавшись на Azure, Microsoft Intune или Microsoft 365. Каждый арендатор Azure AD отличается от других арендаторов Azure AD и имеет свои собственные идентификаторы работы и школы и регистрации приложений.

Если у вас еще нет арендатора Azure AD, следуйте этим инструкциям для его создания.

## <a name="create-a-new-azure-ad-tenant"></a>Создание нового клиента Azure AD

Для создания арендатора:

1. Вопийте на [порталaz,](https://portal.azure.com/) используя учетную запись, с помощью учетной записи, с помощью чего вы хотите связать сярприцию с кластером Azure Red Hat OpenShift.
2. Откройте [лезвие Active Directory Azure](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) для создания нового арендатора (также известного как новый *активный каталог Azure).*
3. Укажите **название организации**.
4. Предоставьте **исходное доменное имя.** Это будет *иметь onmicrosoft.com* придавили к нему. Значение *для названия Организации* можно использовать повторно.
5. Выберите страну или регион, где будет создан арендатор.
6. Нажмите кнопку **Создать**.
7. После создания ad-арендатора Azure AD выберите **нажмите здесь, чтобы управлять новой ссылкой каталога.** Ваше новое имя клиента должно отображаться в правом верхнем углу портала Azure:  

    ![Скриншот портала с указанием имени арендатора в правом верхнем углу][tenantcallout]  

8. Обратите внимание на *идентификатор клиента,* чтобы позже указать, где создать кластер Azure Red Hat OpenShift. Теперь на портале следует просмотреть лезвие обзора Active Directory Azure для нового арендатора. Выберите **Свойства** и скопируйте значение для **идентификатора каталога.** Мы будем ссылаться `TENANT` на это значение как в учебнике [кластера Azure Red Hat OpenShift.](tutorial-create-cluster.md)

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Ресурсы

Для получения дополнительной информации о [клиентах Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)ознакомьтесь с [документацией Active Directory.](https://docs.microsoft.com/azure/active-directory/)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создать основной сервис, создать URL-адрес обратного вызова клиента и проверку подлинности, а также создать нового пользователя Active Directory для тестирования приложений в кластере Azure Red Hat OpenShift.

[Создание объекта приложения и пользователя Azure AD](howto-aad-app-configuration.md)
