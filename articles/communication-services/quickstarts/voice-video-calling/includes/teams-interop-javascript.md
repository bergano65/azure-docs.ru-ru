---
title: Краткое руководство о том, как присоединиться к собранию Teams
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0559a9b763d273579373bb57922606f14ff6e6db
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94574071"
---
## <a name="prerequisites"></a>Обязательные условия

- Рабочее приложение, [вызывающее Службы коммуникации Azure](../getting-started-with-calling.md).
- [Развертывание Teams](https://docs.microsoft.com/deployoffice/teams-install).

## <a name="enable-teams-interoperability"></a>Включение взаимодействия с Teams

Функция взаимодействия с Teams в настоящее время находится на стадии закрытой предварительной версии. Чтобы включить эту функцию для ресурса Служб коммуникации Azure, отправьте сообщение электронной почты [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) с помощью:

1. Идентификатор подписки Azure, которая содержит ресурс Служб коммуникации.
2. Ваш идентификатор клиента Teams. Самый простой способ получить его — [получить ссылку на команду и поделиться ею](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team).

Для использования этой функции пользователь должен быть членом организации-владельца обеих сущностей.

## <a name="add-the-teams-ui-controls"></a>Добавление элементов управления пользовательского интерфейса Teams

Добавьте новое текстовое поле и кнопку в HTML. Текстовое поле будет использоваться для ввода контекста собрания Teams, а кнопка — для присоединения к указанному собранию:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Включение элементов управления пользовательского интерфейса Teams

Теперь кнопку **Присоединиться к собранию Teams** можно привязать к коду, который выполняет присоединение к предоставленному собранию Teams:

```javascript
meetingButton.addEventListener("click", () => {
    
    // set display name in the meeting
    callAgent.updateDisplayName('YOUR_NAME');
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>Получение контекста собрания

Контекст Teams можно получить с помощью API Graph. Это действие подробно описано в [документации по Graph](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?view=graph-rest-beta&tabs=http).

Вы также можете получить необходимую информацию о собрании по URL-адресу **Присоединиться к собранию** в самом приглашении на собрание.

## <a name="run-the-code"></a>Выполнение кода

Пользователи Webpack могут использовать `webpack-dev-server` для сборки и запуска приложения. Выполните следующую команду, чтобы создать пакет узла приложения на локальном веб-сервере:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Откройте веб-браузер и перейдите по адресу http://localhost:8080/. Вы увидите следующее:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Снимок экрана: готовое приложение JavaScript.":::

Вставьте контекст Teams в текстовое поле и нажмите *Присоединиться к собранию Teams*, чтобы присоединиться к собранию Teams из приложения Служб коммуникации Azure.

