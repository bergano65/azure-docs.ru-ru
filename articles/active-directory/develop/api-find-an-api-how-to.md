---
title: Найдите API для специально разработанного приложения Azure
description: Как настроить разрешения, необходимые для доступа к определенному интерфейсу API, в пользовательском приложении Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: cd3b21050c6a442284647212fdf7c5707943ffc1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885622"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Как найти интерфейс API, необходимый для пользовательского приложения

Для доступа к интерфейсам API необходимо настроить области доступа и роли. Если вы хотите предоставить веб-API приложения-ресурса клиентским приложениям, необходимо настроить области доступа и роли для интерфейсов API. Если вы хотите, чтобы клиентское приложение получило доступ к веб-API, необходимо настроить разрешения на доступ к API-интерфейсу при регистрации приложения.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Настройка приложения ресурсов для предоставления веб-API

При предоставлении веб-API этот интерфейс API будет отображаться в списке **Выберите API** при добавлении разрешений для регистрации приложения. Чтобы добавить области доступа, выполните шаги, изложенные в [настройке приложения для разоблачения веб-AIS.](quickstart-configure-app-expose-web-apis.md)

## <a name="configuring-a-client-application-to-access-web-apis"></a>Настройка клиентского приложения для доступа к веб-API

При добавлении разрешений во время регистрации вашего приложения можно **добавить доступ к API** к предоставляемым веб-API. Чтобы получить доступ к веб-AA, выполните шаги, изложенные в [настройке клиентского приложения для доступа к web-AA.](quickstart-configure-app-access-web-apis.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Основные сведения о манифесте приложения Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
