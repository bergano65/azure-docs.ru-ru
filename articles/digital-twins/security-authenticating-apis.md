---
title: Общие сведения об аутентификации API с помощью Azure Digital Twins | Документация Майкрософт
description: Сведения о подключении к API и выполнении аутентификации с помощью Azure Digital Twins
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016222"
---
# <a name="connect-and-authenticate-to-apis"></a>Подключение к API и аутентификация

Azure Digital Twins использует Azure Active Directory (Azure AD) для аутентификации пользователей и защиты приложений. Служба Azure AD поддерживает проверку подлинности для различных современных архитектур. Все они основаны на таких стандартных отраслевых протоколах, как OAuth 2.0 или OpenID Connect. Кроме того, разработчики могут использовать Azure AD для создания приложения с одним клиентом, а также бизнес-приложений. Разработчики также могут использовать Azure AD для разработки мультиканальных приложений.

Общие сведения об Azure AD, пошаговые руководства и понятия см. в статье [Базовая документация по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/index).

Чтобы интегрировать приложение или службу с Azure AD, разработчику сначала необходимо зарегистрировать приложение в Azure AD. Дополнительные сведения см. в статье [Краткое руководство. Регистрация приложения в конечной точке Azure Active Directory версии 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Пять основных сценариев приложений](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types), которые поддерживает система Azure AD, приведены далее.

* Одностраничное приложение (SPA). Пользователю нужно войти в одностраничное приложение, защиту которого обеспечивает Azure AD.
* Веб-браузер — веб-приложение. Пользователю нужно войти в веб-приложение, защиту которого обеспечивает Azure AD.
* Собственное приложение — веб-API. Собственному приложению, которое выполняется на телефоне, планшете или компьютере, необходимо выполнить аутентификацию пользователя для получения ресурсов из веб-API, защиту которого обеспечивает Azure AD.
* Веб-приложение — веб-API. Веб-приложению требуется получать ресурсы из веб-API, защиту которого обеспечивает Azure AD.
* Управляющая программа или серверное приложение — веб-API. Управляющей программе или серверному приложению, в котором нет веб-интерфейса пользователя, требуется получить ресурсы из веб-API, защиту которого обеспечивает Azure AD.

Библиотека аутентификации Windows Azure предлагает множество способов получения токенов Active Directory. Дополнительные сведения о библиотеках и примерах кода см. [здесь](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Вызов Digital Twins из веб-API среднего уровня

Когда разработчики создают решения Digital Twins, обычно они также создают приложение среднего уровня или API. Затем приложение или API вызывает нижний уровень API Digital Twins. Сначала пользователи проходят аутентификацию для приложения среднего уровня, а затем при вызове нижнего уровня используется поток токенов On-Behalf-Of. Подробные инструкции по организации потока On-Behalf-Of см. в статье [Azure Active Directory версии 2.0 и поток On-Behalf-Of в OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Также примеры кодов можно найти в статье [Calling a downstream web API from a web API using Azure AD](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/) (Вызов веб-API нижнего уровня из веб-API с помощью Azure AD).


## <a name="test-with-the-postman-client"></a>Тестирование с использованием клиента Postman

Чтобы приступить к работе с API Digital Twins, вы можете использовать клиент Postman в качестве среды API. Postman позволяет быстро создавать сложные HTTP-запросы. Ниже приведены действия для получения маркера Azure AD, который можно использовать для вызова Digital Twins в пользовательском интерфейсе Postman.


1. Чтобы загрузить приложение, перейдите на сайт https://www.getpostman.com/.
1. Чтобы создать приложение Azure AD, воспользуйтесь сведениями из статьи [Краткое руководство. Регистрация приложения в конечной точке Azure Active Directory версии 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad). Или можно повторно использовать существующую регистрацию. 
1. В разделе **Требуемые разрешения** введите Azure Digital Twins и выберите **Делегированные разрешения**. Затем выберите **Предоставить разрешения**.
1. Откройте манифест приложения и установите для **oauth2AllowImplicitFlow** значение true.
1. В качестве URL-адреса ответа укажите [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback).
1. Последовательно выберите вкладку **Авторизация**, затем **OAuth 2.0** и **Get New Access Token** (Получить новый маркер доступа).

    |**Поле**  |**Значение** |
    |---------|---------|
    | Тип предоставления разрешения | Неявный |
    | URL-адрес обратного вызова | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | URL-адрес аутентификации | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | Идентификатор клиента | Используйте идентификатор приложения для приложения Azure AD, созданного или перепрофилированного на шаге 2. |
    | Область | Не указывайте. |
    | Состояние | Не указывайте. |
    | Аутентификация клиента | Отправьте в качестве заголовка обычной аутентификации. |

1. Выберите **Request Token** (Токен запроса).

    >[!NOTE]
    >Если вы получили сообщение об ошибке OAuth 2 couldn’t be completed (OAuth 2 не удалось завершить), попробуйте сделать следующее:
    > * Чтобы повторить попытку, закройте Postman и снова откройте его.
   
1. Прокрутите вниз и выберите **Use Token** (Использовать токен).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о безопасности Azure Digital Twins см. в статье [Создание назначений ролей и управление ими](./security-create-manage-role-assignments.md).
