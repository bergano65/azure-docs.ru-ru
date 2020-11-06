---
title: Проверка подлинности Azure Active Directory REST API
description: Используйте Azure Active Directory для проверки подлинности в конфигурации приложений Azure с помощью REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424416"
---
# <a name="azure-active-directory-authentication"></a>Проверка подлинности Azure Active Directory

HTTP-запросы могут проходить проверку подлинности с помощью схемы проверки подлинности **носителя** с маркером, полученным от Azure Active Directory (Azure AD). Эти запросы должны передаваться по протоколу TLS.

## <a name="prerequisites"></a>Обязательные условия

Субъект, который будет использоваться для запроса маркера Azure AD, должен быть назначен одной из применимых [ролей конфигурации приложения](./rest-api-authorization-azure-ad.md) .

Предоставьте каждому запросу все заголовки HTTP, необходимые для проверки подлинности. Минимальное число необходимых значений:

|  Заголовок запроса | Описание  |
| --------------- | ------------ |
| **Авторизация** | Сведения о проверке подлинности, необходимые для схемы **носителя** . Ниже описаны формат и сведения. |

**Пример**.

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Получение маркера Azure Active Directory

Перед получением маркера Azure AD необходимо определить, какой пользователь хочет пройти проверку подлинности, какую аудиторию они запрашивают маркер, а также какую конечную точку Azure AD следует использовать.

### <a name="audience"></a>Аудитория

Маркер Azure AD должен быть запрошен соответствующей аудиторией. При запросе маркера для конфигурации приложения Azure необходимо указать одну из следующих аудиторий. Аудитория также может называться "ресурс", для которого запрашивается маркер.

- {Конфигуратионсторенаме}. азконфиг. IO
- *. azconfig.io

> [!IMPORTANT]
> Когда запрошенная аудитория является {Конфигуратионсторенаме}. азконфиг. IO, она должна точно соответствовать заголовку запроса узла (с учетом регистра), используемому для отправки запроса.

### <a name="azure-ad-authority"></a>Центр Azure AD

Центр Azure AD — это конечная точка, используемая для получения маркера Azure AD. Он имеет вид `https://login.microsoftonline.com/{tenantId}` . `{tenantId}`Сегмент относится к идентификатору клиента Azure Active Directory, которому принадлежит пользователь или приложение, которое пытается пройти проверку подлинности.

### <a name="authentication-libraries"></a>Библиотеки проверки подлинности

Azure предоставляет набор библиотек, именуемых Azure Active Directory библиотеками проверки подлинности (ADAL) для упрощения процесса получения маркера Azure AD. Эти библиотеки созданы для нескольких языков. Документацию можно найти [здесь](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).

## <a name="errors"></a>**ошибки**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Причина:** Не указан заголовок запроса авторизации со схемой носителя.
**Решение:** Укажите допустимый ```Authorization``` заголовок HTTP-запроса

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Причина:** Недопустимый маркер Azure AD.
**Решение:** Получите маркер Azure AD из центра Azure AD и убедитесь, что используется соответствующая аудитория.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Причина:** Недопустимый маркер Azure AD.
**Решение:** Получите маркер Azure AD из центра Azure AD и убедитесь, что клиент Azure AD связан с подпиской, к которой принадлежит хранилище конфигураций. Эта ошибка может возникать, если участник принадлежит нескольким клиентам Azure AD.
