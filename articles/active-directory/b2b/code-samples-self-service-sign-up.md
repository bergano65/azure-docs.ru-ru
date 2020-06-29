---
title: Примеры кода соединителя API для потоков пользователей — Azure AD
description: Примеры кода соединителей API в потоках самостоятельной регистрации для внешних удостоверений Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0814312bb12582dd9e9ebfafc60fba470f6a9a9
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905163"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>Примеры самостоятельной регистрации для внешних удостоверений

В приведенных ниже таблицах содержатся ссылки на примеры кода для использования веб-API в потоках самостоятельной регистрации пользователей с применением [соединителей API](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Краткие руководства по соединителям API Функций Azure

| Образец                                                                                                                          | Описание                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | В этом примере функции Azure для .NET Core показано, как ограничить число регистраций для определенных доменов клиентов и проверить предоставляемые пользователями сведения. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | В этом примере функции Azure для Node.js показано, как ограничить число регистраций для определенных доменов клиентов и проверить предоставляемые пользователями сведения.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | В этом примере функции Azure для Python показано, как ограничить число регистраций для определенных доменов клиентов и проверить предоставляемые пользователями сведения.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>Настраиваемые рабочие процессы утверждения

| Образец | Описание |
|--------| ----------- |
| [Рабочий процесс утверждения вручную](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | В этом примере демонстрируется полный рабочий процесс утверждения для управления созданием учетной записи пользователя-гостя при самостоятельной регистрации |

## <a name="identity-verification"></a>Проверка личности

| Образец                                                                                                            | Описание                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | В этом примере показано, как проверить удостоверение пользователя при самостоятельной регистрации с помощью соединителя API для интеграции с IDology. |

<!-- | [Experian](https://github.com/Azure-Samples/) | This sample shows how add identity verification to your self-service sign-up user flow by using an API connector to integrate with Experian. | -->
