---
title: Настройка Postman для Azure Digital Twins | Документация Майкрософт
description: Настройка Postman для Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: v-adgera
ms.openlocfilehash: 66dbfd09ef07740c07ddb010b73e33e783340e5a
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873653"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Настройка Postman для Azure Digital Twins

В этой статье описана настройка клиента REST Postman для взаимодействия и тестирования API управления Azure Digital Twins. В частности, рассматриваются следующие операции:

* Настройка приложения Azure Active Directory для использования потока неявного предоставления разрешений OAuth 2.0;
* Использование клиента REST Postman для выполнения HTTP-запросов, содержащих токены, к вашим API управления.
* Использование Postman для составления составных запросов POST в API управлении.

## <a name="postman-summary"></a>Сведения о Postman

Начните работу в Azure Digital Twins с помощью клиентского средства REST, например [Postman](https://www.getpostman.com/), чтобы подготовить локальную среду тестирования. Клиент Postman помогает быстро создавать сложные HTTP-запросы. Скачайте версию клиента Postman для рабочего стола, перейдя по адресу [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) — это средство тестирования REST, которое размещает ключевые возможности HTTP-запроса в полезном графическом пользовательском интерфейсе на основе плагина для настольных систем.

С помощью клиента Postman разработчики решений могут указать тип HTTP-запроса (*POST*, *GET*, *UPDATE*, *PATCH* и *DELETE*), конечную точку API, которую нужно вызвать, и использование SSL. Postman также поддерживает добавление заголовков, параметров, данных форм и текста HTTP-запросов.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Настройка Azure Active Directory для использования потока неявного предоставления разрешений OAuth 2.0

Настройте приложение Azure AD на использование потока неявного предоставления разрешений OAuth 2.0.

1. Чтобы создать собственное приложение Azure AD, воспользуйтесь сведениями из статьи [Краткое руководство. Регистрация приложения в конечной точке Azure Active Directory версии 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad). Кроме того, можно повторно использовать имеющуюся регистрацию собственного приложения.

1. В разделе **Необходимые разрешения** выберите **Добавить** и введите **Azure Digital Twins** в разделе **Добавить доступ через API**. Если найти API не удается, выполните поиск по запросу **Azure Smart Spaces**. Затем выберите **Предоставление разрешений > Делегированные разрешения** и нажмите **Готово**.

    [![Добавление API Azure Active Directory регистрации приложений](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Выберите **Манифест** , чтобы открыть манифест приложения для приложения. Для *oauth2AllowImplicitFlow* задайте значение `true`.

    [![Azure Active Directory неявный поток](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. В качестве **URL-адреса ответа** укажите `https://www.getpostman.com/oauth2/callback`.

    [![URL-адрес ответа Azure Active Directory](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

1. Скопируйте и сохраните **идентификатор приложения** Azure AD. Он используется в последующих шагах.

## <a name="obtain-an-oauth-20-token"></a>Получение маркера OAuth 2.0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Настройте и настройте POST, чтобы получить маркер Azure Active Directory. После этого выполните HTTP-запрос с проверкой подлинности к Azure Digital Twins, используя полученный токен:

1. Скачайте приложение, перейдя по адресу [www.getpostman.com](https://www.getpostman.com/).
1. Убедитесь, что ваш **URL-адрес авторизации** указан правильно. Его формат должен быть следующим:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Название  | Заменить на | Пример |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Имя вашего клиента или название организации | `microsoft` |

1. Последовательно выберите вкладку **Авторизация**, затем **OAuth 2.0** и **Get New Access Token** (Получить новый маркер доступа).

    | Поле  | Значение |
    |---------|---------|
    | Тип предоставления разрешения | `Implicit` |
    | URL-адрес обратного вызова | `https://www.getpostman.com/oauth2/callback` |
    | URL-адрес аутентификации | Использование **URL-адреса авторизации** из шага 2 |
    | Идентификатор клиента | Используйте **идентификатор приложения** для приложения Azure AD, созданного или перепрофилированного на предыдущем шаге |
    | `Scope` | Не указывайте |
    | Область | Не указывайте |
    | Проверка подлинности клиента | `Send as Basic Auth header` |

1. Теперь клиент должен выглядеть следующим образом.

    [![Пример клиента POST](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Выберите **Request Token** (Токен запроса).

    >[!TIP]
    >Если вы получили сообщение об ошибке OAuth 2 couldn’t be completed (OAuth 2 не удалось завершить), попробуйте сделать следующее:
    > * Чтобы повторить попытку, закройте Postman и снова откройте его.
  
1. Прокрутите вниз и выберите **Use Token** (Использовать токен).

<div id="multi"></div>

## <a name="make-a-multipart-post-request"></a>Создание составного запроса POST

После выполнения предыдущих шагов настройте Postman на выполнение аутентифицированного HTTP-запроса POST, состоящего из нескольких частей.

1. На вкладке **Верхний колонтитул** добавьте ключ заголовка HTTP-запроса **Content-Type** со значением `multipart/mixed`.

   [![Тип содержимого multipart/Mixed](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Сериализируйте текстовые данные в файлы. Данные JSON будут сохранены в качестве JSON-файла.
1. На вкладке **Основной текст** добавьте каждый файл с присвоенным именем **key**, выбрав `file` или `text`.
1. Далее выберите каждый файл, используя кнопку **Выберите файл**.

   [![Пример клиента POST](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * Клиент Postman не требует, чтобы составные части имели назначенный вручную **Content-Type** или **Content-Disposition**.
   > * Необходимо указать эти заголовки для каждой части.
   > * Вы должны выбрать `multipart/mixed` или другой подходящий **Content-Type** для всего запроса.

1. Наконец, выберите **Отправить** , чтобы отправить многокомпонентный запрос HTTP POST.

## <a name="next-steps"></a>Следующие шаги

- Сведения об API управления Digital Twins и их использовании см. в статье [How to use Azure Digital Twins management APIs](how-to-navigate-apis.md) (Как использовать API управления Azure Digital Twins).

- Дополнительные сведения о составных запросах см. в статье [Добавление больших двоичных объектов к объектам в Azure Digital Twins](./how-to-add-blobs.md).

- Сведения о проверке подлинности в API управления см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).
