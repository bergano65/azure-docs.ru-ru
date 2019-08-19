---
title: Интеграция с платформой идентификации Майкрософт | Службы
description: Ознакомьтесь с рекомендациями и общими сведениями при интеграции с платформой идентификации Майкрософт (v 2.0).
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 33128cbece3b217778182b3831b02e2f3f654f3b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853212"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Контрольный список интеграции платформы идентификации Майкрософт

Контрольный список интеграции платформы идентификации Майкрософт предназначен для обеспечения высокого качества и безопасной интеграции. Здесь приводятся рекомендации и общие сведения об интеграции с платформой идентификации Майкрософт, поэтому регулярно Изучите список, чтобы обеспечить качество и безопасность интеграции приложения с платформой идентификации. Контрольный список не предназначен для просмотра всего приложения. Содержимое контрольного списка может быть изменено по мере внесения улучшений в платформу.

Если вы только начинаете работу, ознакомьтесь с [документацией](index.yml) , чтобы узнать об основах проверки подлинности, сценариях приложений на платформе Microsoft Identity и многом другое.

## <a name="testing-your-integration"></a>Тестирование интеграции

Используйте следующий контрольный список, чтобы убедиться, что приложение эффективно интегрировано с [платформой Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/).

### <a name="basics"></a>Основные сведения

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Прочтите и изучите [политики платформы Майкрософт](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Убедитесь, что ваше приложение соответствует условиям, которые были разработаны для защиты пользователей и платформы. |

### <a name="ownership"></a>Владения

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Убедитесь, что сведения, связанные с учетной записью, используемой для регистрации и управления приложениями, обновлены. |

### <a name="branding"></a>Фирменная символика

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Придерживайтесь [рекомендаций по фирменной символике для приложений](howto-add-branding-in-azure-ad-apps.md). |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Укажите понятное имя и логотип для своего приложения. Эти сведения отображаются в запросе согласия вашего приложения. Убедитесь, что ваше имя и логотип являются репрезентативными для вашей компании или продукта, чтобы пользователи могли принимать взвешенные решения. Убедитесь, что вы не нарушаете ни один товарный знак. |

### <a name="privacy"></a>Конфиденциальность

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Укажите ссылки на условия предоставления услуг и заявление о конфиденциальности приложения. |

### <a name="security"></a>Безопасность

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Обеспечьте владение всеми URI перенаправления и обеспечьте актуальность записей DNS. Не используйте подстановочные знаки (*) в URI. Для веб-приложений убедитесь, что все URI безопасны и зашифрованы (например, с помощью схем HTTPS). Для общедоступных клиентов используйте зависящие от платформы URI перенаправления, если это применимо (в основном для iOS и Android). В противном случае используйте URI перенаправления с большим количеством случайных значений, чтобы избежать конфликтов при обратном вызове приложения. Если приложение используется изолированным веб-агентом, вы можете использовать https://login.microsoftonline.com/nativeclient. Регулярно просматривайте и обрежьте все неиспользуемые или ненужные URI перенаправления. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Если приложение зарегистрировано в каталоге, сократите и вручную проведите наблюдение за списком владельцев регистрации приложений. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Не включайте поддержку [потока неявного предоставления OAuth2](v2-oauth2-implicit-grant-flow.md) , если это не требуется явным образом. Сведения о допустимом сценарии см. [здесь](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Не используйте [поток учетных данных пароля владельца ресурса (ропк)](v2-oauth-ropc.md), который напрямую обрабатывает пароли пользователей. Этот поток требует высокого уровня доверия и раскрытия пользователей, и его следует использовать, только если другие, более безопасные, потоки использовать нельзя. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Защитите свои учетные данные приложения и управляйте ими. Используйте [учетные данные сертификата](active-directory-certificate-credentials.md), а не пароль (секреты клиента). Если необходимо использовать учетные данные пароля, не устанавливайте их вручную. Не храните учетные данные в коде или конфигурации и никогда не разрешите им обрабатывать их. По возможности используйте [управляемые удостоверения для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) или [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) для хранения и регулярного смены учетных данных. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Убедитесь, что приложение запрашивает разрешения с минимальными правами доступа. Запрашивать только разрешения, необходимые приложению, и только тогда, когда это необходимо. Изучите различные [типы разрешений](v1-permissions-and-consent.md#types-of-permissions). При необходимости используйте только разрешения приложения. по возможности используйте делегированные разрешения. Полный список разрешений Microsoft Graph см. в этом [справочнике по разрешениям](https://docs.microsoft.com/graph/permissions-reference). |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Если вы защищаете API с помощью платформы Microsoft Identity, тщательно обдумайте предоставляемые им разрешения. Подумайте, какова правильная степень гранулярности для вашего решения и какие разрешения требуются для предоставления согласия администратора. Проверьте наличие ожидаемых разрешений во входящих маркерах перед принятием решений об авторизации. |

### <a name="implementation"></a>Реализация

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Используйте современные решения проверки подлинности (OAuth 2,0, [OpenID Connect Connect](v2-protocols-oidc.md)) для безопасного входа пользователей. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Не реализуйте сами протоколы — используйте [поддерживаемые корпорацией Майкрософт библиотеки проверки](reference-v2-libraries.md) подлинности (MSAL, по промежуточного слоя сервера). Убедитесь, что вы используете последнюю версию библиотеки проверки подлинности, интегрированную с. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Если данные, необходимые для приложения, доступны через [Microsoft Graph](https://developer.microsoft.com/graph), запросите разрешения для этих данных, используя конечную точку Microsoft Graph, а не отдельный API. |

### <a name="end-user-experience"></a>Возможности для пользователей

|   |   |
|---|---|
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | [Ознакомьтесь с согласия](application-consent-experience.md) пользователя и настройте части запроса согласия вашего приложения таким образом, чтобы конечные пользователи и администраторы имели достаточную информацию для определения того, доверяете ли они вашему приложению. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Сократите число попыток ввода учетных данных входа пользователя при использовании приложения, попытайтесь выполнить автоматическую проверку подлинности (получение токена без уведомления) перед интерактивными потоками. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Не используйте параметр "prompt = согласие" для каждого входа. Используйте предложение Prompt = согласие, если вы определили, что вам нужно предоставить согласие на предоставление дополнительных разрешений (например, если вы изменили необходимые разрешения приложения). |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Там, где это применимо, следует расширить возможности приложения с помощью пользовательских данных. Для этого удобно использовать [Microsoft Graph API](https://developer.microsoft.com/graph) . Средство [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) , которое поможет приступить к работе. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Зарегистрируйте полный набор разрешений, необходимых вашему приложению, чтобы администраторы могли легко предоставить согласие клиенту. Используйте [добавочное согласие](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) во время выполнения, чтобы помочь пользователям понять, почему приложение запрашивает разрешения, которые могут потребовать или запутать пользователей при запросе при первом запуске. |
| ![Установка](./media/active-directory-integration-checklist/checkbox-two.svg) | Реализуйте [чистый интерфейс единого](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)выхода. Это требование к конфиденциальности и безопасности, что обеспечивает хорошую работу пользователей. |

### <a name="testing"></a>Тестирование

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
