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
ms.openlocfilehash: dc5570b188bfdc0e1be78aa2bd5c5d92e884f377
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638024"
---
# <a name="connect-and-authenticate-to-apis"></a>Подключение к API и аутентификация

Azure Digital Twins использует Azure Active Directory (Azure AD) для аутентификации пользователей и защиты приложений. Azure AD поддерживает аутентификацию для различных современных архитектур, основанных на стандартных отраслевых протоколах OAuth 2.0 или OpenID Connect. Кроме того, Azure AD могут использовать разработчики, которые создают приложения с одним клиентом и бизнес-приложения, а также разрабатывают мультитенантные приложения.

Общие сведения об Azure AD, пошаговые руководства и понятия см. в статье [Базовая документация по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/index).

Чтобы интегрировать приложение или службу с Azure AD, разработчику сначала необходимо зарегистрировать приложение в Azure AD. Подробные инструкции и снимки экрана см. в статье [Краткое руководство. Регистрация приложения в конечной точке Azure Active Directory версии 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

Ниже приведены [пять основных сценариев приложений](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types), которые поддерживает система Azure AD:

* Одностраничное приложение (SPA). Пользователю нужно войти в одностраничное приложение, защиту которого обеспечивает Azure AD.
* Веб-браузер — веб-приложение. Пользователю нужно войти в веб-приложение, защиту которого обеспечивает Azure AD.
* Собственное приложение — веб-API. Собственному приложению, которое выполняется на телефоне, планшете или компьютере, необходимо выполнить аутентификацию пользователя для получения ресурсов из веб-API, защиту которого обеспечивает Azure AD.
* Веб-приложение — веб-API. Веб-приложению требуется получать ресурсы из веб-API, защиту которого обеспечивает Azure AD.
* Управляющая программа или серверное приложение — веб-API. Управляющей программе или серверному приложению, в котором нет веб-интерфейса пользователя, требуется получить ресурсы из веб-API, защиту которого обеспечивает Azure AD.

Библиотека аутентификации Windows Azure предлагает множество способов получения токенов Active Directory. Подробные сведения о библиотеке, а также примеры кода см. на [сайте GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>Вызов Digital Twins из веб-API среднего уровня

При разработке решений Digital Twins разработчики обычно создают приложения среднего уровня или API, который затем вызывает нижний уровень API Digital Twins. Сначала пользователи должны пройти аутентификацию для приложения среднего уровня, а затем при вызове нижнего уровня будет использоваться поток токенов On-Behalf-Of. Подробные инструкции по организации потока On-Behalf-Of см. в статье [Azure Active Directory версии 2.0 и поток On-Behalf-Of в OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Примеры кода см. в статье [Calling a downstream web API from a web API using Azure AD](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/) (Вызов веб-API нижнего уровня из веб-API с помощью Azure AD).


## <a name="test-with-the-postman-client"></a>Тестирование с использованием клиента Postman

Чтобы приступить к работе с API Digital Twins, вы можете использовать клиент Postman в качестве среды API. Postman позволяет быстро создавать сложные HTTP-запросы. В приведенных ниже инструкциях основное внимание уделяется получению токена Azure AD, необходимого для вызова Digital Twins в пользовательском интерфейсе Postman.


1. Перейдите по ссылке https://www.getpostman.com/, чтобы загрузить приложение.
1. Выполните шаги, приведенные в статье [Краткое руководство. Регистрация приложения в конечной точке Azure Active Directory версии 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad), чтобы создать приложение Azure Active Directory (или же вы можете повторно использовать имеющуюся регистрацию). 
1. В разделе "Требуемые разрешения" добавьте Azure Digital Twins и выберите "Делегированные разрешения". В завершение щелкните "Предоставить разрешения".
1. Откройте манифест приложения и установите для oauth2AllowImplicitFlow значение true.
1. В качестве URL-адреса ответа укажите [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback).
1. Выберите вкладку **Авторизация**, щелкните **OAuth 2.0** и выберите **Get New Access Token** (Получить новый маркер доступа).

    |**Поле**  |**Значение** |
    |---------|---------|
    | Тип предоставления разрешения | Неявный |
    | URL-адрес обратного вызова | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | URL-адрес аутентификации | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | Идентификатор клиента | Используйте идентификатор приложения для приложения Azure AD, созданного или перепрофилированного на шаге 1. |
    | Область | Не указывайте. |
    | Состояние | Не указывайте. |
    | Аутентификация клиента | Отправьте в качестве заголовка обычной аутентификации. |

1. Щелкните **Request Token** (Токен запроса).

    >[!NOTE]
    >Если вы получили сообщение об ошибке OAuth 2 couldn’t be completed (OAuth 2 не удалось завершить), попробуйте сделать следующее:
    > * Закройте Postman и снова откройте, чтобы повторить попытку.
   
1. Прокрутите вниз и щелкните **Use Token** (Использовать токен).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о безопасности Azure Digital Twins см. в статье [Создание назначений ролей и управление ими](./security-create-manage-role-assignments.md).
