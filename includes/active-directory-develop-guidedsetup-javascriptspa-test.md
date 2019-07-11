---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 494f5fb6f2bfdec86cad01a37e57c3ec219a77f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133177"
---
## <a name="test-your-code"></a>Тестирование кода

Протестируйте код с помощью следующих сред.

### <a name="test-with-nodejs"></a>Тестирование кода с Node.js

Если вы не используете Visual Studio, убедитесь, что веб-сервер запущен.

1. Настройте для сервера ожидание передачи данных через TCP-порт на основе расположения файла *index.html*. Чтобы запустить веб-сервер для ожидания передачи данных через порт в Node.js, выполните следующие команды в командной строке из папки приложения:

    ```bash
    npm install
    node server.js
    ```
1. В браузере введите **http://\<span>\<localhost:30662** или **http://\<span>\</span>localhost:{port}** , где *port* соответствует порту, с которого веб-сервер ожидает передачи данных. Появится содержимое файла *index.html* с кнопкой **Sign In** (Войти).

### <a name="test-with-visual-studio"></a>Тестирование кода в Visual Studio

Если вы используете Visual Studio, выберите решение проекта и нажмите клавишу F5, чтобы запустить проект. В браузере откроется адрес http://<span></span>localhost:{port} и отобразится кнопка **Sign In** (Войти).

## <a name="test-your-application"></a>Тестирование приложения

После загрузки файла *index.html* в браузер нажмите кнопку **Sign In** (Войти). Вам будет предложено войти с помощью конечной точки платформы удостоверений Майкрософт.

![Окно входа учетной записи JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Предоставление разрешения на доступ к приложению

При первом входе в приложение вам будет предложено предоставить ему доступ к профилю, а также выполнить вход:

![Окно "Запрошенные разрешения"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Просмотр результатов приложения

После входа сведения о профиле пользователя будут возвращены в ответе API Microsoft Graph, отображаемом на странице.

![Результаты после вызова Microsoft API Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область *user.read*. По умолчанию эта область автоматически добавляется в каждое приложение, зарегистрированное на портале регистрации. Для других API Microsoft Graph, а также для пользовательских API вашего внутреннего сервера, могут потребоваться дополнительные области. Например, для отображения списка календарей пользователя API Microsoft Graph требуется область *Calendars.Read*.

Чтобы из контекста приложения получить доступ к календарям пользователя, добавьте делегированное разрешение *Calendars.Read* в сведения о регистрации приложения. Затем добавьте область *Calendars.Read* в вызов `acquireTokenSilent`.

>[!NOTE]
>При увеличении количества областей от пользователя могут потребоваться дополнительные согласия.

Если серверному API не требуется область (не рекомендуется), вы можете использовать *clientId* в качестве области в вызовах для получения маркеров.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
