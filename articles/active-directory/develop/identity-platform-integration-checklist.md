---
title: Интеграция с платформой Microsoft identity | Azure
description: Дополнительные сведения о рекомендациях и общих упущения при интеграции с платформой Microsoft identity (версии 2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev
ms.openlocfilehash: e9070127780659142ab8f956a8016622ecfea144
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540151"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Контрольный список для интеграции платформы удостоверений Microsoft

Контрольный список для интеграции платформы Microsoft identity предназначен помогут вам в высококачественный и безопасный интеграции. Выделяются рекомендации и распространенные упущения при интеграции с платформой Microsoft identity, таким образом, просмотрите список на регулярной основе, чтобы убедиться, что вы сохраняете качество и безопасность приложения интеграции с платформой удостоверений. Контрольный список не предназначена для просмотра всего приложения. Содержимое контрольного списка могут измениться, как улучшения платформы.

Если вы только начинаете работу, ознакомьтесь с [документации](index.yml) Дополнительные сведения о основы проверки подлинности, сценарии приложений в платформе Microsoft identity и многое другое.

## <a name="testing-your-integration"></a>Тестирование интеграции

Используйте следующий контрольный список, чтобы убедиться, что приложение эффективно интегрирован с [платформы удостоверений Microsoft](https://docs.microsoft.com/legal/mdsa).

### <a name="basics"></a>Основные сведения

|   |   |
|---|---|
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Чтение и понимание [политики платформы Microsoft](https://docs.microsoft.com/legal/mdsa). Убедитесь, что приложение соответствует условия, описано, как они предназначены для защиты пользователей и платформы. |

### <a name="ownership"></a>Владение

|   |   |
|---|---|
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Убедитесь, что сведения, связанные с учетной записью, вы использовали для регистрации и управления приложениями, актуальна. |

### <a name="branding"></a>Фирменная символика

|   |   |
|---|---|
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Придерживаться [фирменной символики, касающиеся приложений](howto-add-branding-in-azure-ad-apps.md). |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Укажите понятное имя и логотип для вашего приложения. Эта информация отображается на согласие для приложения. Убедитесь, что ваше имя и логотип, типична для вашего продукта/компании, таким образом, чтобы пользователи принимать обоснованные решения. Убедитесь, что вы не нарушают любые товарные знаки. |

### <a name="privacy"></a>Конфиденциальность

|   |   |
|---|---|
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Укажите ссылки на условия вашего приложения, службы и заявление о конфиденциальности. |

### <a name="security"></a>Безопасность

|   |   |
|---|---|
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Ведение владение все коды URI перенаправления и обновлять записи DNS для них. Не используйте подстановочные знаки (*) в вашей URI. Для веб-приложений убедитесь, что все URI являются защищенным и зашифрованным каналам (например, с помощью схемы https). Для общедоступных клиентов URI перенаправления использования платформы, если это возможно (главным образом для iOS и Android). В противном случае используйте URI перенаправления с большим количеством случайность во избежание конфликтов во время обратного вызова в приложение. Если приложение используется из изолированного веб-агент, вы можете использовать https://login.microsoftonline.com/nativeclient. Просмотрите и обрезать все неиспользуемые или ненужные URI перенаправления на регулярной основе. |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Если ваше приложение будет зарегистрировано в каталоге, свести к минимуму и вручную в список владельцев регистрации приложения. |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Не включайте поддержку [потоке предоставления OAuth2 неявное](v2-oauth2-implicit-grant-flow.md) Если это не требуется явным образом. Дополнительные сведения о допустимых сценарий [здесь](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Не используйте [поток учетных данных пароля владельца ресурса (ROPC)](v2-oauth-ropc.md), который напрямую обрабатывает пароли пользователей. Этот поток необходим высокий уровень доверия и пользователем раскрытия и должен использоваться только при других, более безопасные, потоки, нельзя использовать. |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Защита и управление ими учетные данные приложения. Используйте [учетные данные сертификатов](active-directory-certificate-credentials.md), не пароль учетных данных (секреты клиента). Если необходимо использовать пароль учетных данных, не устанавливайте его вручную. Не хранить учетные данные в коде или конфигурации и никогда не дают возможность обрабатывать пользователю. По возможности используйте [управляемые удостоверения для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) или [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) для хранения и регулярно изменять учетные данные. |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Убедитесь, что приложение запрашивает разрешения с минимальными привилегиями. Запросить у только те разрешения, которые абсолютно необходимы приложению и только тогда, когда они нужны. Назначение различных [типов разрешений](v1-permissions-and-consent.md#types-of-permissions). Использовать только разрешений приложения, если это необходимо; Используйте делегированные разрешения, где это возможно. Полный список разрешений Microsoft Graph, см. в этом [Справочник по разрешениям](https://docs.microsoft.com/graph/permissions-reference). |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Если вы безопасность API с помощью платформы удостоверений, тщательно продумайте разрешения, которые он должен предоставлять. Рассмотрим, что является необходимым уровнем точности для вашего решения и какие разрешения требуют согласия администратора. Проверьте наличие ожидаемого разрешений во входящих токенах для принятия решения авторизации. |

### <a name="implementation"></a>Реализация

|   |   |
|---|---|
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Используйте решения современную проверку подлинности (OAuth 2.0, [OpenID Connect](v2-protocols-oidc.md)) для безопасного входа пользователей. |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Не самостоятельно реализовать протоколы — использовать [библиотеки проверки подлинности, поддерживаемые корпорацией Майкрософт](reference-v2-libraries.md) (MSAL, по промежуточного слоя сервера). Убедитесь, что вы используете последнюю версию библиотеки проверки подлинности, интегрированных с. |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Если данные, необходимые для приложения доступны через [Microsoft Graph](https://developer.microsoft.com/graph), запросить разрешения для этих данных с использованием конечной точки Microsoft Graph, а не отдельных API. |

### <a name="end-user-experience"></a>Возможности для пользователей

|   |   |
|---|---|
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | [Понять взаимодействие согласия](application-consent-experience.md) и настроить части вашего приложения на согласие, таким образом, чтобы конечные пользователи и Администраторы имеют достаточно информации для определения того, если они доверяют приложения. |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Свести к минимуму количество раз, пользователю нужно ввести учетные данные для входа при использовании вашего приложения, попытавшись автоматической проверки подлинности (автоматическом получения маркера) перед интерактивные потоки. |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Не используйте «prompt = согласия» для каждого входа в систему. Использовать только строки = согласия, если вы определили, что вам нужно запросить согласие для дополнительных разрешений (например, если вы изменили необходимые разрешения для вашего приложения). |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Когда это возможно, расширить приложение с данными пользователя. Используйте [Microsoft Graph API](https://developer.microsoft.com/graph) — простой способ это сделать. [Graph explorer](https://developer.microsoft.com/graph/graph-explorer) средство, которое может помочь вам приступить к работе. |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Зарегистрируйте полный набор разрешений, которые необходимы приложению, поэтому администраторы могут предоставлять согласие легко их клиенту. Используйте [добавочное согласие](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) во время выполнения, чтобы помочь пользователям понять, почему приложение запрашивает разрешения, которые могут обращаться или запутывания пользователей при запросе при первом запуске. |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Реализуйте [очистки выхода централизованному](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Она заявления о конфиденциальности и требование к безопасности и делает для удобства работы пользователей. |

### <a name="testing"></a>Тестирование

|   |   |
|---|---|
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Для проверки [политики условного доступа](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , которые могут повлиять на возможность пользователей использовать приложения. |
| ![флажок](./media/active-directory-integration-checklist/checkbox-two.svg) | Тестирование приложения с помощью всех возможных учетных записей, которые вы планируете поддерживать (для примера, рабочих или учебных учетных записей, личных учетных записей Майкрософт, учетные записи детей и независимых учетных записей). |

## <a name="additional-resources"></a>Дополнительные ресурсы

Ниже приведены ресурсы с подробными сведениями о версии 2.0:

* [Платформы удостоверений Microsoft (Общие сведения о версии 2.0)](v2-overview.md)
* [Справочник по протоколам платформы удостоверений Microsoft](active-directory-v2-protocols.md)
* [Справочник по маркерам доступа](access-tokens.md)
* [Справочник по токенам идентификаторов](id-tokens.md)
* [Справочник по библиотекам проверки подлинности](reference-v2-libraries.md)
* [Разрешения и согласие в платформе Microsoft identity](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)