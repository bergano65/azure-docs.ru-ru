---
title: Включение многоклиентских приложений в Azure Digital двойников | Документация Майкрософт
description: Как настроить мультитенантные приложения Azure Active Directory для Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 6394d519b93b55358ef9d528f89978d5a3cf3007
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005916"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Включение мультитенантных приложений с помощью Azure Digital Twins

Разработчикам решений, которые выполняют сборку в Azure Digital Twins, может понадобиться реализовать несколько пользователей в рамках одной службы или решения. На самом деле, *мультитенантные* приложения являются наиболее распространенными конфигурациями Azure Digital Twins.

В этом документе описано, как настроить в приложении Azure Digital Twins поддержку нескольких клиентов и пользователей Azure Active Directory.

## <a name="multitenancy"></a>Мультитенантный режим

*Мультитенантный* ресурс — это единый подготовленный экземпляр, который поддерживает нескольких пользователей. У каждого пользователя есть собственные независимые данные и привилегии. Интерфейсы пользователей изолированы друг от друга, поэтому их представление приложения отличается.

Дополнительные сведения о мультитенантности см. в статье [Мультитенантные приложения Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Сценарий проблемы

В этом случае есть **разработчик**, создающий решение Azure Digital Twins, и **пользователь**, который использует это решение:

- У **разработчика** есть подписка Azure с клиентом Azure Active Directory.
- **Разработчик** развертывает экземпляр Azure Digital Twins в свою подписку Azure. Azure Active Directory автоматически создает субъект-службу в клиенте Azure Active Directory **разработчика**.
- Пользователи в клиенте Azure Active Directory **разработчика** затем могут [получить маркеры OAuth 2.0](./security-authenticating-apis.md) от службы Azure Digital Twins.
- **Разработчик** теперь создает мобильное приложение, которое напрямую интегрируется с API управления Azure Digital Twins.
- **Разработчик** позволяет **пользователю** использовать мобильное приложение.
- **Пользователь** должен быть авторизован для использования API управления Azure Digital Twins в приложении **разработчика**.

Проблема:

- Когда **пользователь** входит в приложение **разработчика**, приложение не может получать токены для пользователей **этого пользователя**, чтобы выполнить аутентификацию в API управления Azure Digital Twins.
- В Azure Active Directory выдается исключение, указывающее, что экземпляр Azure Digital Twins не распознан в каталоге **пользователя**.

## <a name="problem-solution"></a>Решение проблемы

Чтобы решить проблему описанного выше сценария, нужно предпринять следующие действия, чтобы создать субъект-службу Azure Digital Twins в клиенте Azure Active Directory **пользователя**.

- Если у **пользователя** еще нет подписки Azure с клиентом Azure Active Directory:

  - Администратор клиента Azure Active Directory **пользователя** должен получить [подписку Azure с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/),
  - после этого администратор клиента Azure AD **пользователя** должен [связать его с новой подпиской](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- На [портале Azure](https://portal.azure.com) администратор клиента Azure AD **пользователя** должен выполнить описанные ниже действия:

  1. Открыть **подписки**.
  1. Выберите подписку с клиентом Azure Active Directory, который будет использоваться в приложении **разработчика**.

     [![подписки Azure Active Directory](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Выбрать **Поставщики ресурсов**.
  1. Найти **Microsoft.IoTSpaces**.
  1. Выберите **Зарегистрировать**.

     [поставщики ресурсов ![Azure Active Directory](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье [Создание определяемых пользователем функций в Azure Digital Twins](./how-to-user-defined-functions.md).

- Дополнительные сведения об использовании управления доступом на основе ролей для дальнейшей защиты приложения см. в статье [Создание назначений ролей и управление ими в Azure Digital Twins](./security-create-manage-role-assignments.md).
