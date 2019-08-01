---
title: Руководство по Запуск Иммерсивного средства чтения с помощью Node.js
titleSuffix: Azure Cognitive Services
description: В этом учебнике описано, как создать приложение Node.js, которое запускает иммерсивное средство чтения.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: ea9728ac54ae357925bd666ffecf270172137f77
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688301"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Руководство по Запуск Иммерсивного средства чтения (Node.js)

В [обзорной статье](./overview.md) вы узнали о том, что представляет собой иммерсивное средство чтения и каким образом в нем реализованы проверенные методы, улучшающие понимание прочитанного для начинающих, тех, кто изучает язык, и учащихся с особыми потребностями. В этом учебнике описано, как создать веб-приложение Node.js, которое запускает иммерсивное средство чтения. Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание веб-приложения Node.js с помощью средства Express.
> * Получение маркера доступа
> * Запуск иммерсивного средства чтения с примером содержимого.
> * Указание языка содержимого.
> * Указание языка интерфейса иммерсивного средства чтения.
> * Запуск иммерсивного средства чтения с математическим содержимым.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Ресурс "Иммерсивное средство чтения", настроенный для проверки подлинности Azure Active Directory (Azure AD). Инструкции по настройке см. [здесь](./azure-active-directory-authentication.md). Вам потребуются некоторые значения, созданные здесь при настройке свойств среды. Сохраните результаты своего сеанса в текстовом файле для использования в будущем.
* [Node.js](https://nodejs.org/) и [Yarn](https://yarnpkg.com).
* Интегрированная среда разработки, такая как [Visual Studio Code](https://code.visualstudio.com/).

## <a name="create-a-nodejs-web-app-with-express"></a>Создание веб-приложения Node.js с помощью средства Express

Создайте веб-приложение Node.js с помощью средства `express-generator`.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Установите зависимости yarn и добавьте зависимости `request` и `dotenv`, которые будут далее использоваться в этом учебнике.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Получение маркера проверки подлинности Azure AD

Затем напишите API серверной части, чтобы получить токен проверки подлинности Azure AD.

Для этой части потребуются некоторые значения из описанного выше предварительного требования конфигурации проверки подлинности Azure AD. Вернитесь к текстовому файлу, который вы сохранили в этом сеансе.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Получив эти значения, создайте новый файл с именем _.env_ и вставьте в него следующий код, указав значения пользовательских свойств сверху.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Не забудьте зафиксировать этот файл в системе управления версиями, так как он содержит секреты, для которых не следует предоставлять открытый доступ.

Затем откройте файл _app.js_ и добавьте следующее в его начало. В Node загрузятся свойства, указанные в файле .env в качестве переменных среды.

```javascript
require('dotenv').config();
```

Откройте файл _routes\index.js_ и добавьте в его начало следующее:

```javascript
var request = require('request');
```

Затем добавьте следующий код непосредственно под этой строкой. Этот код создает конечную точку API, которая получает токен проверки подлинности Azure AD, используя пароль субъект-службы, а затем возвращает этот токен. Существует также вторая конечная точка для получения поддомена.

```javascript
router.get('/getimmersivereadertoken', function(req, res) {
  request.post ({
          headers: {
              'content-type': 'application/x-www-form-urlencoded'
          },
          url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
          form: {
              grant_type: 'client_credentials',
              client_id: process.env.CLIENT_ID,
              client_secret: process.env.CLIENT_SECRET,
              resource: 'https://cognitiveservices.azure.com/'
          }
      },
      function(err, resp, token) {
          if (err) {
              return res.status(500).send('CogSvcs IssueToken error');
          }

          return res.send(JSON.parse(token).access_token);
      }
  );
});

router.get('/subdomain', function (req, res) {
    return res.send(process.env.SUBDOMAIN);
});
```

Конечная точка API **getimmersivereadertoken** должна быть защищена с помощью формы проверки подлинности (например, [OAuth](https://oauth.net/2/)), чтобы предотвратить получение неавторизованными пользователями токенов для использования относительно службы "Иммерсивное средство чтения" и выставления счетов. Эти сведения выходят за рамки этого руководства.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Запуск иммерсивного средства чтения с примером содержимого

1. Откройте файл _views\layout.pug_ и добавьте следующий код под тег `head` перед тегом `body`. Эти теги `script` загрузят [пакет SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Откройте файл _views\index.pug_ и замените его содержимое следующим кодом. Этот код заполнит страницу примером содержимого и добавит кнопку, которая запустит иммерсивное средство чтения.

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.

        function getImmersiveReaderTokenAsync() {
            return new Promise((resolve, reject) => {
                $.ajax({
                    url: '/getimmersivereadertoken',
                    type: 'GET',
                    success: token => {
                        resolve(token);
                    },
                    error: err => {
                        console.log('Error in getting token!', err);
                        reject(err);
                    }
                });
            });
        }

        function getSubdomainAsync() {
            return new Promise((resolve, reject) => {
                $.ajax({
                    url: '/subdomain',
                    type: 'GET',
                    success: subdomain => { resolve(subdomain); },
                    error: err => { reject(err); }
                });
            });
        }

        async function launchImmersiveReader() {
            const content = {
                title: document.getElementById('title').innerText,
                chunks: [{
                    content: document.getElementById('content').innerText + '\n\n',
                    lang: 'en'
                }]
            };

            const token = await getImmersiveReaderTokenAsync();
            const subdomain = await getSubdomainAsync();

            ImmersiveReader.launchAsync(token, subdomain, content);
        }
    ```

3. Теперь наше веб-приложение готово. Запустите приложение, выполнив следующую команду:

    ```bash
    npm start
    ```

4. Откройте веб-браузер и перейдите по адресу _http://localhost:3000_ . Вы увидите упомянутое выше содержимое на странице. Нажмите кнопку **Immersive Reader** (Иммерсивное средство чтения) для запуска иммерсивного средства чтения с вашим содержимым.

## <a name="specify-the-language-of-your-content"></a>Указание языка содержимого

Иммерсивное средство чтения поддерживает много различных языков. Вы можете указать язык своего содержимого, выполнив следующие действия.

1. Откройте файл _views\index.pug_ и добавьте следующий код ниже тега `p(id=content)`, который был добавлен в предыдущем шаге. Этот код добавляет на вашу страницу содержимое на испанском языке.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. В коде JavaScript над вызовом `ImmersiveReader.launchAsync` добавьте следующее. Этот код передает содержимое на испанском языке в иммерсивное средство чтения.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Перейдите по адресу _http://localhost:3000_ еще раз. На странице должен отображаться текст на испанском языке. Когда вы нажмете кнопку **Immersive Reader** (Иммерсивное средство чтения), этот текст также отобразится в этом средстве.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Указание языка интерфейса иммерсивного средства чтения

По умолчанию язык интерфейса иммерсивного средства чтения соответствует языковым настройкам браузера. Язык интерфейса этого средства также можно указать с помощью следующего кода.

1. В файле _views\index.pug_ замените вызов `ImmersiveReader.launchAsync(token, content)` на приведенный ниже код.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. Перейдите по адресу _http://localhost:3000_ . При запуске иммерсивного средства чтения интерфейс будет отображаться на французском языке.

## <a name="launch-the-immersive-reader-with-math-content"></a>Запуск иммерсивного средства чтения с математическим содержимым

Вы можете добавить математические содержимое в иммерсивное средство чтения, используя [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Добавьте в файл _views\index.pug_ следующий код над вызовом `ImmersiveReader.launchAsync`:

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Перейдите по адресу _http://localhost:3000_ . Запустив иммерсивное средство чтения и прокрутив вниз страницы, вы увидите математическую формулу.

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь с разделом о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справочнике по этому пакету](./reference.md).
* Просмотрите примеры кода на сайте [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp).
