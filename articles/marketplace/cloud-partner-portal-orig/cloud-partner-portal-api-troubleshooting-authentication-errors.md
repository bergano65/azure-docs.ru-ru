---
title: Устранение распространенных неполадок проверки подлинности | Azure Marketplace
description: Содержит информацию о распространенных ошибках аутентификации при использовании API-интерфейсов портала Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ddf3c9ce26a1538d91f1e6d6bcc04fd0d18e7936
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935818"
---
# <a name="troubleshooting-common-authentication-errors"></a>Устранение распространенных ошибок аутентификации

Эта статья содержит информацию о распространенных ошибках аутентификации при использовании API-интерфейсов портала Cloud Partner.

## <a name="unauthorized-error"></a>Ошибка авторизации

Если постоянно возникают ошибки `401 unauthorized`, убедитесь, что у вас есть допустимый маркер доступа.  Если еще не сделали это, создайте базовое приложение Azure Active Directory (Azure AD) и субъект-службу как описано в [этой статье](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Для проверки доступа используйте приложение или простой запрос HTTP POST.  Чтобы получить маркер доступа необходим идентификатор клиента, идентификатор приложения, идентификатор объекта и секретный ключ, как показано на изображении.

![Устранение ошибки 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Ошибка запрета доступа

Если вы получаете ошибку `403 forbidden`, убедитесь, что на портале Cloud Partner к вашей учетной записи издателя добавлена верная субъект-служба.
Чтобы добавить субъект-службу на портал, выполните действия, описанные на странице [Предварительные требования](./cloud-partner-portal-api-prerequisites.md).

Если субъект-служба была добавлена правильно, проверьте все остальные сведения. Обратите внимание на идентификатор объекта, который вводите на портале. На странице регистрации приложения Azure Active Directory существуют два идентификатора объектов, необходимо использовать локальный. Правильное значение можно найти, перейдя на страницу **Регистрация приложений**, а также если щелкнуть имя приложения в разделе **Управляемое приложение в локальном каталоге**. Это приведет к локальным свойствам приложения, где можно найти правильный идентификатор объекта на странице **Свойства**, как показано на рисунке ниже. Кроме того убедитесь, что при добавлении участника-службы и вызова API используется верный идентификатор издателя.

![Устранение ошибки 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
