---
title: Как настроить пост-Azure Digital двойников | Документация Майкрософт
description: Узнайте, как настроить и использовать POST для тестирования интерфейсов API цифровых двойников Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 377639d7a88478308709743ab842db71028686ed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023316"
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

1. Выполните действия, описанные в [кратком руководстве](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) , чтобы создать и настроить приложение Azure Active Directory. Кроме того, можно повторно использовать существующую регистрацию приложения.

    [![настроить новый URI перенаправления POST](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Теперь добавьте **URI перенаправления** для `https://www.getpostman.com/oauth2/callback`.

1. Установите флажок **неявное предоставление** > **маркеров доступа** , чтобы разрешить использование потока неявного предоставления OAuth 2,0. Выберите **Настройка**, а затем **сохранить**.

1. Скопируйте **идентификатор клиента** приложения Azure Active Directory.

## <a name="obtain-an-oauth-20-token"></a>Получение маркера OAuth 2.0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Настройте и настройте POST, чтобы получить маркер Azure Active Directory. После этого выполните HTTP-запрос с проверкой подлинности к Azure Digital Twins, используя полученный токен:

1. Убедитесь, что ваш **URL-адрес авторизации** указан правильно. Его формат должен быть следующим:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Имя  | Заменить на | Пример |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Имя клиента или организации. Используйте понятное имя вместо буквенно-цифрового **идентификатора клиента** регистрации Azure Active Directory приложения. | `microsoft` |

1. Скачайте приложение, перейдя по адресу [www.getpostman.com](https://www.getpostman.com/).

1. Мы хотим сделать запрос GET. Перейдите на вкладку **авторизация** , выберите OAuth 2,0 и щелкните **получить новый маркер доступа**.

    | Поле  | Значение |
    |---------|---------|
    | Тип предоставления разрешения | `Implicit` |
    | URL-адрес обратного вызова | `https://www.getpostman.com/oauth2/callback` |
    | URL-адрес аутентификации | Используйте **URL-адрес авторизации** из **шага 1** . |
    | Идентификатор клиента | Используйте **идентификатор приложения** для Azure Active Directoryного приложения, созданного или повторно используемого из предыдущего раздела. |
    | Область действия | Не указывайте |
    | Состояние | Не указывайте |
    | Аутентификация клиента | `Send as Basic Auth header` |

1. Теперь клиент должен выглядеть следующим образом.

    [Пример маркера клиента ![POST](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Выберите **Request Token** (Токен запроса).
  
1. Прокрутите вниз и выберите **Use Token** (Использовать токен).

## <a name="make-a-multipart-post-request"></a>Создание составного запроса POST

После выполнения предыдущих шагов настройте Postman на выполнение аутентифицированного HTTP-запроса POST, состоящего из нескольких частей.

1. На вкладке **заголовки** добавьте **тип содержимого** "ключ заголовка HTTP-запроса" со значением `multipart/mixed`.

   [![указать тип содержимого multipart/Mixed](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Сериализируйте текстовые данные в файлы. Данные JSON будут сохранены в качестве JSON-файла.
1. На вкладке **текст** выберите `form-data`. 
1. Добавьте каждый файл, назначив имя **ключа** , выбрав `File`.
1. Далее выберите каждый файл, используя кнопку **Выберите файл**.

   [Пример тела формы клиента ![POST](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Клиент Postman не требует, чтобы составные части имели назначенный вручную **Content-Type** или **Content-Disposition**.
   > * Необходимо указать эти заголовки для каждой части.
   > * Вы должны выбрать `multipart/mixed` или другой подходящий **Content-Type** для всего запроса.

1. Наконец, выберите **Отправить** , чтобы отправить многокомпонентный запрос HTTP POST. Код состояния `200` или `201` указывает на Успешный запрос. В интерфейсе клиента появится соответствующее ответное сообщение.

1. Проверьте данные запроса HTTP POST, вызвав конечную точку API: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об API управления Digital Twins и их использовании см. в статье [How to use Azure Digital Twins management APIs](how-to-navigate-apis.md) (Как использовать API управления Azure Digital Twins).

- Дополнительные сведения о составных запросах см. в статье [Добавление больших двоичных объектов к объектам в Azure Digital Twins](./how-to-add-blobs.md).

- Сведения о проверке подлинности в API управления см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).
