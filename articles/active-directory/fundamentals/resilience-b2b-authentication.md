---
title: Отказоустойчивость сборок при аутентификации внешних пользователей с помощью Azure Active Directory
description: Рекомендации для ИТ-администраторов и архитекторов по созданию устойчивой проверки подлинности для внешних пользователей
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae8e24341c321fbfffb84d9b072abc4cf925aff3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920067"
---
# <a name="build-resilience-in-external-user-authentication"></a>Устойчивость к сборке при аутентификации внешних пользователей

[Azure Active Directory службы совместной работы B2B](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b) (Azure AD B2B) — это функция [внешних удостоверений](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations) , которая обеспечивает совместную работу с другими организациями и отдельными пользователями. Она обеспечивает безопасное подключение гостевых пользователей в клиенте Azure AD без необходимости управлять учетными данными. Внешние пользователи получают свои удостоверения и учетные данные с помощью внешнего поставщика удостоверений (IdP), поэтому им не нужно запоминать новые учетные данные. 

## <a name="ways-to-authenticate-external-users"></a>Способы проверки подлинности внешних пользователей

Вы можете выбрать методы проверки подлинности внешних пользователей в каталоге. Вы можете использовать Microsoft поставщиков удостоверений или другие поставщиков удостоверений.

С каждым внешним IdP зависит от доступности этого IdP. С помощью некоторых методов подключения к поставщиков удостоверений существуют вещи, которые можно выполнить для повышения устойчивости.

> [!NOTE] 
> В Azure AD B2B есть встроенная возможность проверки подлинности любого пользователя из любого [Azure Active Directory](https://docs.microsoft.com/azure/active-directory) клиента или личной [учетной записи Майкрософт](https://account.microsoft.com/account). Нет необходимости настраивать эти встроенные параметры.

### <a name="considerations-for-resilience-with-other-idps"></a>Рекомендации по устойчивости с помощью других поставщиков удостоверений

При использовании внешних поставщиков удостоверений для аутентификации гостевых пользователей существуют определенные конфигурации, которые необходимо обеспечить для предотвращения нарушений.

| Метод проверки подлинности| Рекомендации по устойчивости |
| - | - |
| Федерация с социальным поставщиков удостоверенийм, например [Facebook](https://docs.microsoft.com/azure/active-directory/external-identities/facebook-federation) или [Google](https://docs.microsoft.com/azure/active-directory/external-identities/google-federation).| Необходимо сохранить учетную запись с IdP и настроить идентификатор клиента и секрет клиента. |
| [Прямая Федерация с SAML и WS-Federation поставщиками удостоверений](https://docs.microsoft.com/azure/active-directory/external-identities/direct-federation)| Для доступа к своим конечным точкам необходимо совместно работать с владельцем IdP, на основе которого вы зависите. <br>Необходимо сохранить метаданные, содержащие сертификаты и конечные точки. |
| [Одноразовый секретный код электронной почты](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode)| При использовании этого метода вы зависят от почтовой системы корпорации Майкрософт, системы электронной почты пользователя и почтового клиента пользователя. |


 

## <a name="self-service-sign-up-preview"></a>Самостоятельная регистрация (Предварительная версия)

В качестве альтернативы отправке приглашений или ссылок можно включить [самостоятельную регистрацию](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-overview).  Это позволяет внешним пользователям запрашивать доступ к приложению. Необходимо создать [соединитель API](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) и связать его с потоком пользователя. Вы связываете потоки пользователей, которые определяют взаимодействие с пользователем, с помощью одного или нескольких приложений. 

[Соединители API](https://docs.microsoft.com/azure/active-directory/external-identities/api-connectors-overview) можно использовать для интеграции самостоятельной регистрации пользователей с интерфейсами API внешних систем. Эту интеграцию API можно использовать для [пользовательских рабочих процессов утверждения](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-approvals), [выполнения проверки личности](https://docs.microsoft.com/azure/active-directory/external-identities/code-samples-self-service-sign-up)и других задач, таких как перезапись атрибутов пользователя. Для использования интерфейсов API необходимо управлять следующими зависимостями.

* **Проверка подлинности соединителя API**. для настройки соединителя требуется URL-адрес конечной точки, имя пользователя и пароль. Настройте процесс, с помощью которого сохраняются эти учетные данные, и обратитесь к владельцу API, чтобы убедиться, что расписание срока действия известно.

* **Ответ соединителя API**. Если API недоступен, соединители API-интерфейсов в потоке регистрации должны быть корректно выполнены. Изучите и предоставьте разработчикам API такие [примеры ответов API](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) и рекомендации [по устранению неполадок](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector). Обратитесь к группе разработки API, чтобы протестировать все возможные сценарии реагирования, включая продолжение, проверку-ошибку и блокирующие ответы. 

## <a name="next-steps"></a>Дальнейшие действия
Ресурсы по устойчивости для администраторов и архитекторов
 
* [Устойчивость к сборке с помощью управления учетными данными](resilience-in-credentials.md)

* [Устойчивость сборки к состояниям устройств](resilience-with-device-states.md)

* [Устойчивость сборки с помощью оценки непрерывного доступа (автоматизированного конструирования)](resilience-with-continuous-access-evaluation.md)

* [Устойчивость к сборке в гибридной проверке подлинности](resilience-in-hybrid.md)

* [Устойчивость к сборке в доступе к приложениям с помощью прокси приложения](resilience-on-premises-access.md)

Обеспечение устойчивости ресурсов для разработчиков

* [Создание устойчивости IAM в приложениях](resilience-app-development-overview.md)

* [Устойчивость сборок в CIAM Systems](resilience-b2c.md)
 
