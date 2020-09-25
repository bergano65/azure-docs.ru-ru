---
title: Краткое руководство. Добавление вызова VOIP к приложению с помощью Служб коммуникации Azure
description: Из этого руководства вы узнаете, как использовать клиентскую библиотеку Служб коммуникации Azure для реализации вызовов на JavaScript.
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d58b4d86936c56a08f27bef59edc1d3cc4ce4617
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946001"
---
Из этого краткого руководства вы узнаете, как начать вызов с помощью клиентской библиотеки Служб коммуникации Azure для реализации вызовов на JavaScript.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- [Node.js](https://nodejs.org/), активная версия LTS и версия Maintenance LTS (рекомендуются версии 8.11.1 и 10.14.1).
- Активный ресурс Служб коммуникации. [Создайте ресурс Служб коммуникации.](../../create-communication-resource.md)
- Маркер доступа пользователя для создания экземпляра клиента вызова. Узнайте, как [создать маркер доступа пользователя и обеспечить управление им](../../access-tokens.md).

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

Откройте терминал или командное окно, создайте каталог для своего приложения и перейдите к нему.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Воспользуйтесь командой `npm init -y`, чтобы создать файл **package.json** с параметрами по умолчанию.

```console
npm init -y
```

### <a name="install-the-package"></a>Установка пакета

Используйте команду `npm install`, чтобы установить клиентскую библиотеку Служб коммуникации для реализации вызовов на JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Параметр `--save` указывает библиотеку как зависимость в файле пакета **package.json**.

### <a name="set-up-the-app-framework"></a>Настройка платформы приложения

В этом кратком руководстве для объединения ресурсов приложения используется webpack. Выполните следующую команду, чтобы установить пакеты npm webpack, webpack-cli и webpack-dev-server, а также указать их в качестве зависимостей разработки в файле **package.json**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Создайте файл **index.html** в корневом каталоге проекта. Мы будем использовать этоn файл для настройки базового макета, с помощью которого пользователь сможет выполнить вызов к боту Служб коммуникации Azure.

Ниже приведен код:

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
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Создайте файл в корневом каталоге проекта с именем **client.js**, чтобы включить логику приложения для этого краткого руководства. Добавьте следующий код, чтобы импортировать клиент вызова и получить ссылки на элементы модели DOM, чтобы мы могли присоединить нашу бизнес-логику. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");

// quickstart code goes here
```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы реализуют некоторые основные функции клиентской библиотеки Служб коммуникации Azure для вызовов:

| Имя                             | Описание                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient — это основная точка входа в клиентскую библиотеку для вызовов.                                                                       |
| CallAgent                        | CallAgent используется для инициирования вызовов и управления ими.                                                                                            |
| AzureCommunicationUserCredential | Класс AzureCommunicationUserCredential реализует интерфейс CommunicationUserCredential, который используется для создания экземпляра CallAgent. |


## <a name="authenticate-the-client"></a>Аутентификация клиента

Вам необходимо заменить `<USER_ACCESS_TOKEN>` допустимым маркером доступа пользователя для вашего ресурса. Если у вас еще нет доступного маркера, см. документацию по [маркеру доступа пользователя](../../access-tokens.md). С помощью `CallClient` инициализируйте экземпляр `CallAgent` с `CommunicationUserCredential`, который позволит нам выполнять и принимать вызовы. Добавьте следующий код в файл **client.js**:

```javascript
const callClient = new CallClient();
const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
let callAgent;

callClient.createCallAgent(tokenCredential).then(agent => {
  callAgent = agent;
  callButton.disabled = false;
});
```

## <a name="start-a-call"></a>Инициирование вызова

Добавьте обработчик событий для инициации вызова при нажатии `callButton`:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Завершение вызова

Добавьте прослушиватель событий для завершения текущего вызова при нажатии `hangUpButton`:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

Свойство `forEveryone` позволяет завершить вызов для всех его участников.

## <a name="run-the-code"></a>Выполнение кода

Чтобы создать и запустить приложение, используйте `webpack-dev-server`. Выполните следующую команду, чтобы создать пакет узла приложения на локальном веб-сервере:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Откройте веб-браузер и перейдите по адресу http://localhost:8080/. Вы увидите следующее:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Снимок экрана: готовое приложение JavaScript.":::

Вы можете выполнить исходящий VOIP-вызов, указав идентификатор пользователя в текстовом поле и нажав кнопку **Начать вызов**. При вызове `8:echo123` вы будете подключены к эхо-боту, что позволит начать работу и проверить работоспособность ваших аудиоустройств.
