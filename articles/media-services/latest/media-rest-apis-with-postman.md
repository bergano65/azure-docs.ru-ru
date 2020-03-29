---
title: Наконфигурите Почтальон для службы мультимедиа Azure v3 REST API
description: В этой статье описано, как настроить Postman для вызова REST API Служб мультимедиа Azure (AMS).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: juliako
ms.openlocfilehash: 872dad95fc5b536c51e251612f40439da020a059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779643"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Настройка Почтальон для медиа-услуг v3 REST API звонки

В этой статье описано, как настроить **Postman** для вызова REST API Служб мультимедиа Azure (AMS). Здесь также описывается, как импортировать файлы среды и коллекции в **Postman**. Коллекция содержит сгруппированные определения HTTP-запросов, которые вызывают REST API служб мультимедиа Azure (AMS). Файл среды содержит переменные, которые используются коллекцией.

Прежде чем приступить к разработке, просмотрите [разработку с помощью AA- иАТ Media Services v3.](media-services-apis-overview.md)

## <a name="prerequisites"></a>Предварительные требования

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Обязательно запомните имя группы ресурсов и имя учетной записи Служб мультимедиа. 
- Получите информацию, необходимую для [доступа к API-интерфейсам](access-api-cli-how-to.md).
- Установите клиент REST [Postman](https://www.getpostman.com/) для выполнения REST API, как показано в некоторых руководствах по REST AMS. 

    Мы используем **Postman**, но подойдет любое средство REST. Другие альтернативы: **Visual Studio Code** с плагином REST или **Telerik Fiddler**. 

> [!IMPORTANT]
> Просмотр [именования конвенций](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Скачивание файлов Postman

Клонируйте репозиторий GitHub, который содержит файлы коллекции и среды Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Настройка Postman

### <a name="configure-the-environment"></a>Настройка среды 

1. Откройте приложение**Postman**.
2. В правой части экрана выберите параметр **Управление средой**.

    ![Управление средой](./media/develop-with-postman/postman-import-env.png)
4. В диалоговом окне **Управление средой** нажмите кнопку **Импорт**.
2. Перейдите к файлу `Azure Media Service v3 Environment.postman_environment.json`, который вы скачали при клонировании `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Будет добавлена среда **Azure Media Service v3 Environment**.

    > [!Note]
    > Задайте переменным доступа новые значения, полученные в разделе **Доступ к API Служб мультимедиа** выше.

7. Дважды щелкните выбранный файл и введите значения, которые вы получили, получив доступ к API.
8. Закройте диалоговое окно.
9. Выберите среду **Azure Media Service v3 Environment** в раскрывающемся списке.

    ![Выбор среды](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Настройка коллекции

1. Нажмите кнопку **импорта**, чтобы импортировать файл коллекции.
1. Перейдите к файлу `Media Services v3.postman_collection.json`, который вы скачали при клонировании `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
3. Выберите файл **Media Services v3.postman_collection.json**.

    ![Импорт файла](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Получение маркера Azure AD 

Прежде чем вы сможете работать с ресурсами AMS версии 3, вам нужно получить и задать маркер Azure AD для аутентификации субъекта-службы.

1. В левом окне приложения Почтальон выберите "Шаг 1: Получить токен AAD Auth".
2. Затем выберите Get Azure AD Token for Service Principal Authentication (Получение маркера безопасности Azure AD для аутентификации субъекта-службы).
3. Нажмите кнопку **Отправить**.

    Это действие отправляет следующую операцию **POST**.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Ответ на этот запрос возвращает маркер и присваивает его значение переменной среды AccessToken.  

    ![Получение маркера AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Устранение неполадок 

* Если приложение не справляется с "HTTP 504: Gateway Timeout", убедитесь, что переменная местоположения не была явно установлена на значение, кроме ожидаемого местоположения учетной записи Media Services. 
* Если вы получили ошибку "учетная запись не найдена", также проверьте, чтобы убедиться, что свойство местоположения в сообщении Body JSON настроено на место, в которое находится учетная запись Media Services. 

## <a name="see-also"></a>См. также

- [Передача файлов в учетную запись Служб мультимедиа с помощью REST](upload-files-rest-how-to.md)
- [Создание фильтров с помощью REST API Служб мультимедиа](filters-dynamic-manifest-rest-howto.md)
- [REST API на основе Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Дальнейшие действия

- [Потоковая передача файлов с помощью REST](stream-files-tutorial-with-rest.md)  
- [Учебник: Кодирование удаленного файла на основе URL и потоковое видео - REST](stream-files-tutorial-with-rest.md)
