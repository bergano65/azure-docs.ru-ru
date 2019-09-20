---
title: Рекомендации для платформы Microsoft Identity | Службы
description: Ознакомьтесь с рекомендациями, рекомендациями и общими сведениями при интеграции с платформой идентификации Майкрософт.
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
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1f4afe1c31ae964aab82664de12144185069af5a
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71145661"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Рекомендации и рекомендации по платформе Microsoft Identity

В этой статье описываются рекомендации, рекомендации и общие сведения при интеграции с платформой идентификации Майкрософт.  Этот контрольный список поможет вам обеспечить высокую качество и безопасную интеграцию. Регулярно изучите этот список, чтобы обеспечить качество и безопасность интеграции приложения с платформой идентификации. Контрольный список не предназначен для просмотра всего приложения. Содержимое контрольного списка может быть изменено по мере внесения улучшений в платформу.

Если вы только начинаете работу, ознакомьтесь с [документацией по платформе идентификации Майкрософт](index.yml) , чтобы узнать об основах проверки подлинности, сценариях приложений на платформе Microsoft Identity и многом другое.

Используйте следующий контрольный список, чтобы убедиться, что приложение эффективно интегрировано с [платформой Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Основные сведения

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Прочтите и изучите [политики платформы Майкрософт](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Убедитесь, что ваше приложение соответствует условиям, которые были разработаны для защиты пользователей и платформы. |

## <a name="ownership"></a>Владения

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Убедитесь, что сведения, связанные с учетной записью, используемой для регистрации и управления приложениями, обновлены. |

## <a name="branding"></a>Фирменная символика

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Придерживайтесь [рекомендаций по фирменной символике для приложений](howto-add-branding-in-azure-ad-apps.md). |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Укажите понятное имя и логотип для своего приложения. Эти сведения отображаются в [запросе согласия вашего приложения](application-consent-experience.md). Убедитесь, что ваше имя и логотип являются репрезентативными для вашей компании или продукта, чтобы пользователи могли принимать взвешенные решения. Убедитесь, что вы не нарушаете ни один товарный знак. |

## <a name="privacy"></a>Конфиденциальность

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Укажите ссылки на условия предоставления услуг и заявление о конфиденциальности приложения. |

## <a name="security"></a>Группа безопасности

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Управление URI перенаправления: <ul><li>Обеспечьте владение всеми URI перенаправления и обеспечьте актуальность записей DNS.</li><li>Не используйте подстановочные знаки (*) в URI.</li><li>Для веб-приложений убедитесь, что все URI безопасны и зашифрованы (например, с помощью схем HTTPS).</li><li>Для общедоступных клиентов используйте зависящие от платформы URI перенаправления, если это применимо (в основном для iOS и Android). В противном случае используйте URI перенаправления с большим количеством случайных значений, чтобы избежать конфликтов при обратном вызове приложения.</li><li>Если приложение используется изолированным веб-агентом, вы можете использовать https://login.microsoftonline.com/common/oauth2/nativeclient.</li><li>Регулярно просматривайте и обрежьте все неиспользуемые или ненужные URI перенаправления.</li></ul> |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Если приложение зарегистрировано в каталоге, сократите и вручную проведите наблюдение за списком владельцев регистрации приложений. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Не включайте поддержку [потока неявного предоставления OAuth2](v2-oauth2-implicit-grant-flow.md) , если это не требуется явным образом. Сведения о допустимом сценарии см. [здесь](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Переместитесь за пределы имени пользователя и пароля. Не используйте [поток учетных данных пароля владельца ресурса (ропк)](v2-oauth-ropc.md), который напрямую обрабатывает пароли пользователей. Этот поток требует высокого уровня доверия и раскрытия пользователей, и его следует использовать, только если другие, более безопасные, потоки использовать нельзя. Этот поток по-прежнему необходим в некоторых сценариях (например, DevOps), но следует помнить, что его использование накладывает ограничения на приложение.  Для более современных подходов прочитайте [потоки проверки подлинности и сценарии приложений](authentication-flows-app-scenarios.md).|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Защитите конфиденциальные учетные данные приложений и управляйте ими для веб-приложений, веб-API и приложений управляющей программы. Используйте [учетные данные сертификата](active-directory-certificate-credentials.md), а не пароль (секреты клиента). Если необходимо использовать учетные данные пароля, не устанавливайте их вручную. Не храните учетные данные в коде или конфигурации и никогда не разрешите им обрабатывать их. По возможности используйте [управляемые удостоверения для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) или [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) для хранения и регулярного смены учетных данных. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Убедитесь, что приложение запрашивает разрешения с минимальными правами доступа. Запрашивать только разрешения, необходимые приложению, и только тогда, когда это необходимо. Изучите различные [типы разрешений](v1-permissions-and-consent.md#types-of-permissions). При необходимости используйте только разрешения приложения. по возможности используйте делегированные разрешения. Полный список разрешений Microsoft Graph см. в этом [справочнике по разрешениям](https://docs.microsoft.com/graph/permissions-reference). |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Если вы защищаете API с помощью платформы Microsoft Identity, тщательно обдумайте предоставляемые им разрешения. Подумайте, какова правильная степень гранулярности для вашего решения и какие разрешения требуются для предоставления согласия администратора. Проверьте наличие ожидаемых разрешений во входящих маркерах перед принятием решений об авторизации. |

## <a name="implementation"></a>Реализация

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Используйте современные решения проверки подлинности (OAuth 2,0, [OpenID Connect Connect](v2-protocols-oidc.md)) для безопасного входа пользователей. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) |  Не программировать напрямую с использованием таких протоколов, как OAuth 2,0 и Open ID. Вместо этого используйте [библиотеку проверки подлинности Майкрософт (MSAL)](msal-overview.md). Библиотеки MSAL безопасно заключают протоколы безопасности в удобной библиотеке и получают встроенную поддержку сценариев [условного доступа](/azure/active-directory/conditional-access/overview) , единого [входа на уровне устройства (SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)и встроенной поддержки кэширования маркеров. Дополнительные сведения см. в списке [клиентских библиотек](reference-v2-libraries.md#microsoft-supported-client-libraries) , поддерживаемых корпорацией Майкрософт, и в списке [библиотек по промежуточного слоя](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) и списка [совместимых сторонних клиентских библиотек](reference-v2-libraries.md#compatible-client-libraries).<br/><br/>Если необходимо выполнить код для протоколов проверки подлинности, следует использовать такую методологию, как [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Обратите особое внимание на вопросы безопасности в спецификациях стандартов для каждого протокола.|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) |  Перенос существующих приложений из [библиотеки аутентификации Azure Active Directory (ADAL)](active-directory-authentication-libraries.md) в [библиотеку проверки подлинности Майкрософт](msal-overview.md). MSAL — это новейшее решение для платформы идентификации Майкрософт, которое является предпочтительным для ADAL. Она доступна на .NET и JavaScript и также доступна в общедоступной предварительной версии для Android, iOS, Python и Java. Узнайте больше о миграции [ADAL.NET](msal-net-migration.md), [ADAL. js](msal-compare-msal-js-and-adal-js.md)и [ADAL.NET и приложений брокера iOS](msal-net-migration-ios-broker.md) .|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) |  Для мобильных приложений настройте каждую платформу с помощью процесса регистрации приложения. Чтобы в приложении можно было воспользоваться преимуществами Microsoft Authenticator или Microsoft корпоративный портал для единого входа, приложению необходимо настроить "URI перенаправления брокера". Это позволяет корпорации Майкрософт возвращать управление приложению после проверки подлинности. При настройке каждой платформы процедура регистрации приложения поможет вам выполнить эти действия. Загрузите рабочий пример с помощью краткого руководства. В iOS при возможности используйте брокеры и System WebView.|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) |  В веб-приложениях или веб-API для каждой учетной записи следует использовать один кэш маркеров.  Для веб-приложений кэш маркера должен быть снабжен ключом идентификатора учетной записи.  Для веб-API учетная запись должна быть привязана к хэшу маркера, используемого для вызова API. MSAL.NET обеспечивает сериализацию кэша пользовательской лексемы на платформах .NET Framework и .NET Core. По соображениям безопасности и производительности нашей рекомендацией является сериализация одного кэша на пользователя. Дополнительные сведения см. в статье о [сериализации кэша маркеров](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Если данные, необходимые для приложения, доступны через [Microsoft Graph](https://developer.microsoft.com/graph), запросите разрешения для этих данных, используя конечную точку Microsoft Graph, а не отдельный API. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) |Не Проанализируйте значение маркера доступа или попытайтесь проанализировать его как клиент.  Они могут изменять значения, форматы и даже быть зашифрованными без предупреждения. всегда используйте id_token, если клиенту необходимо узнать о пользователе или вызвать Microsoft Graph.  Только веб-API должны анализировать маркеры доступа (поскольку они определяют формат и устанавливают ключи шифрования). |

## <a name="end-user-experience"></a>Возможности для пользователей

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | [Ознакомьтесь с согласия](application-consent-experience.md) пользователя и настройте части запроса согласия вашего приложения таким образом, чтобы конечные пользователи и администраторы имели достаточную информацию для определения того, доверяете ли они вашему приложению. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Сократите число попыток ввода учетных данных входа пользователя при использовании приложения, попытайтесь выполнить автоматическую проверку подлинности (получение токена без уведомления) перед интерактивными потоками. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Не используйте параметр "prompt = согласие" для каждого входа. Используйте предложение Prompt = согласие, если вы определили, что вам нужно предоставить согласие на предоставление дополнительных разрешений (например, если вы изменили необходимые разрешения приложения). |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Там, где это применимо, следует расширить возможности приложения с помощью пользовательских данных. Для этого удобно использовать [Microsoft Graph API](https://developer.microsoft.com/graph) . Средство [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) , которое поможет приступить к работе. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Зарегистрируйте полный набор разрешений, необходимых вашему приложению, чтобы администраторы могли легко предоставить согласие клиенту. Используйте [добавочное согласие](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) во время выполнения, чтобы помочь пользователям понять, почему приложение запрашивает разрешения, которые могут потребовать или запутать пользователей при запросе при первом запуске. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Реализуйте [чистый интерфейс единого](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)выхода. Это требование к конфиденциальности и безопасности, что обеспечивает хорошую работу пользователей. |

## <a name="testing"></a>Тестирование

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Проверьте [политики условного доступа](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , которые могут повлиять на возможность использования приложения пользователями. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Протестируйте приложение со всеми возможными учетными записями, которые вы планируете поддерживать (например, рабочие или учебные учетные записи, персональные учетные записи Майкрософт, дочерние учетные записи и учетные записи независимых). |

## <a name="additional-resources"></a>Дополнительные ресурсы

Ниже приведены ресурсы с подробными сведениями о версии 2.0:

* [Платформа Microsoft Identity (версия 2.0)](v2-overview.md)
* [Справочник по протоколам платформы удостоверений Майкрософт](active-directory-v2-protocols.md)
* [Справочник по маркерам доступа](access-tokens.md)
* [Справочник по токенам идентификаторов](id-tokens.md)
* [Справочник по библиотекам проверки подлинности](reference-v2-libraries.md)
* [Разрешения и согласие на платформе Microsoft Identity](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)
