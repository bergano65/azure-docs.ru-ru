---
title: Настройка Postman для Azure Digital Twins | Документация Майкрософт
description: Настройка Postman для Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: adgera
ms.openlocfilehash: 824c0caf0d54e8484093304c39c9f5dc05c83298
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117525"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Настройка Postman для Azure Digital Twins

В этой статье описана настройка клиента REST Postman для взаимодействия и тестирования API управления Azure Digital Twins. В частности, рассматриваются следующие операции:

* настройка приложения Azure Active Directory для использования потока неявного предоставления разрешений OAuth 2.0;
* настройка клиента REST Postman для выполнения HTTP-запросов, содержащих токены, к вашим API управления.

## <a name="postman-summary"></a>Сведения о Postman

Начните работу в Azure Digital Twins с помощью клиентского средства REST, например [Postman](https://www.getpostman.com/), чтобы подготовить локальную среду тестирования. Клиент Postman помогает быстро создавать сложные HTTP-запросы. Скачайте версию клиента Postman для рабочего стола, перейдя по адресу [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) — это средство тестирования REST, которое размещает ключевые возможности HTTP-запроса в полезном графическом пользовательском интерфейсе на основе плагина для настольных систем. С помощью клиента Postman разработчики решений могут указать тип HTTP-запроса (*POST*, *GET*, *UPDATE*, *PATCH* и *DELETE*), конечную точку API, которую нужно вызвать, и использование SSL. Postman также поддерживает добавление заголовков, параметров, данных форм и текста HTTP-запросов.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Настройка Azure Active Directory для использования потока неявного предоставления разрешений OAuth 2.0

Настройте приложение Azure AD на использование потока неявного предоставления разрешений OAuth 2.0.

1. Чтобы создать собственное приложение Azure AD, воспользуйтесь сведениями из статьи [Краткое руководство. Регистрация приложения в конечной точке Azure Active Directory версии 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad). Кроме того, можно повторно использовать имеющуюся регистрацию собственного приложения.

1. В разделе **Необходимые разрешения** выберите **Добавить** и введите **Azure Digital Twins** в разделе **Добавить доступ через API**. Если найти API не удается, выполните поиск по запросу **Azure Smart Spaces**. Затем выберите **Предоставление разрешений > Делегированные разрешения** и нажмите **Готово**.

    ![Добавление API при регистрации приложения в Azure AD](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. Щелкните **Манифест**, чтобы открыть манифест вашего приложения. Для *oauth2AllowImplicitFlow* задайте значение `true`.

      ![Неявный поток Azure AD][1]

1. В качестве **URL-адреса ответа** укажите `https://www.getpostman.com/oauth2/callback`.

      ![URL-адрес ответа Azure AD][2]

1. Скопируйте и сохраните **идентификатор приложения** Azure AD. Он понадобится вам позже.

### <a name="configure-the-postman-client"></a>Настройка клиента Postman

Затем установите и настройте Postman для получения токена Azure AD. После этого выполните HTTP-запрос с проверкой подлинности к Azure Digital Twins, используя полученный токен:

1. Скачайте приложение, перейдя по адресу [www.getpostman.com]([https://www.getpostman.com/).
1. Убедитесь, что ваш **URL-адрес авторизации** указан правильно. Его формат должен быть следующим:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | ИМЯ  | Заменить на | Пример |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Имя вашего клиента или название организации | `microsoft` |

1. Последовательно выберите вкладку **Авторизация**, затем **OAuth 2.0** и **Get New Access Token** (Получить новый маркер доступа).

    | Поле  | Значение |
    |---------|---------|
    | Тип предоставления разрешения | `Implicit` |
    | URL-адрес обратного вызова | `https://www.getpostman.com/oauth2/callback` |
    | URL-адрес аутентификации | Используйте **URL-адрес авторизации** из шага 2 |
    | Идентификатор клиента | Используйте **идентификатор приложения** для приложения Azure AD, созданного или перепрофилированного на предыдущем шаге |
    | Область | Не указывайте |
    | Состояние | Не указывайте |
    | Аутентификация клиента | `Send as Basic Auth header` |

1. Теперь клиент должен выглядеть следующим образом:

   ![Пример клиента Postman][3]

1. Выберите **Request Token** (Токен запроса).

    >[!NOTE]
    >Если вы получили сообщение об ошибке OAuth 2 couldn’t be completed (OAuth 2 не удалось завершить), попробуйте сделать следующее:
    > * Чтобы повторить попытку, закройте Postman и снова откройте его.
  
1. Прокрутите вниз и выберите **Use Token** (Использовать токен).

## <a name="next-steps"></a>Дополнительная информация

- Сведения об API управления Digital Twins и их использовании см. в статье [How to use Azure Digital Twins management APIs](how-to-navigate-apis.md) (Как использовать API управления Azure Digital Twins).

- Сведения о проверке подлинности в API управления см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md). 



<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png
