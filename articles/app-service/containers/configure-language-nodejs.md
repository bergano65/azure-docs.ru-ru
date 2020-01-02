---
title: Настройка приложений Node. js
description: Узнайте, как настроить предварительно созданный контейнер Node. js для приложения. В этой статье приведены наиболее распространенные задачи настройки.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6cf60472307a378d2fd4258a9777152344a11ded
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670274"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Настройка приложения Node. js Linux для службы приложений Azure

Приложения Node. js должны быть развернуты со всеми необходимыми зависимостями NPM. Подсистема развертывания службы приложений (KUDU) автоматически запускается `npm install --production` при развертывании [репозитория Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)или в [ZIP-пакете](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) с включенными процессами сборки. Однако при развертывании файлов с помощью [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)нужные пакеты необходимо загрузить вручную.

Это краткое описание содержит основные понятия и инструкции для разработчиков Node. js, использующих встроенный контейнер Linux в службе приложений. Если вы никогда не использовали службу приложений Azure, сначала ознакомьтесь с [кратким](quickstart-nodejs.md) руководством по Node. js и [node. js с MongoDB](tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Отображение версии Node. js

Чтобы отобразить текущую версию Node. js, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Чтобы отобразить все поддерживаемые версии Node. js, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Установка версии Node. js

Чтобы задать [поддерживаемую версию Node. js](#show-nodejs-version)для приложения, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Этот параметр указывает используемую версию Node. js как во время выполнения, так и во время автоматического восстановления пакетов в KUDU.

> [!NOTE]
> Необходимо задать версию Node. js в `package.json`проекта. Модуль развертывания выполняется в отдельном контейнере, который содержит все поддерживаемые версии Node. js.

## <a name="configure-nodejs-server"></a>Настройка сервера Node. js

Контейнеры Node. js поставляются с [PM2](https://pm2.keymetrics.io/), диспетчером рабочих процессов. Вы можете настроить приложение для запуска с PM2 или с помощью NPM или с помощью пользовательской команды.

- [Выполнить пользовательскую команду](#run-custom-command)
- [Запустить NPM запуск](#run-npm-start)
- [Запуск с помощью PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Выполнить пользовательскую команду

Служба приложений может запустить приложение с помощью пользовательской команды, такой как исполняемый файл, например *Run.sh*. Например, чтобы запустить `npm run start:prod`, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Запустить NPM запуск

Чтобы запустить приложение с помощью `npm start`, достаточно убедиться в том, что `start` сценарий находится в файле *Package. JSON* . Пример.

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Чтобы использовать пользовательский *пакет Package. JSON* в проекте, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Запуск с помощью PM2

Контейнер автоматически запускает приложение с PM2, когда в проекте обнаружен один из общих файлов Node. js:

- *bin/www*
- *Server. js*
- *app.js*
- *index.js*
- *хостингстарт. js*
- Один из следующих [файлов PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *Process. JSON* и *экосистема. config. js*

Можно также настроить пользовательский файл запуска со следующими расширениями:

- *JS* -файл
- [Файл PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) с расширением *JSON*, *config. js*, *. YAML*или *. yml*

Чтобы добавить пользовательский начальный файл, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Удаленная отладка

> [!NOTE]
> Удаленная отладка сейчас доступна в предварительной версии.

Вы можете выполнить отладку приложения Node. js удаленно в [Visual Studio Code](https://code.visualstudio.com/) , если он настроен для [работы с PM2](#run-with-pm2), за исключением случаев, когда вы запустите его с помощью *. config. js, *. yml или *. YAML*.

В большинстве случаев для приложения не требуется дополнительная настройка. Если приложение запускается с файлом *Process. JSON* (по умолчанию или настраиваемым), он должен иметь свойство `script` в корне JSON. Пример.

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Чтобы настроить Visual Studio Code для удаленной отладки, установите [расширение службы приложений](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Следуйте инструкциям на странице расширение и войдите в Azure в Visual Studio Code.

В Azure Explorer найдите приложение, которое нужно отладить, щелкните его правой кнопкой мыши и выберите команду **запустить удаленную отладку**. Нажмите кнопку **Да** , чтобы включить его для приложения. Служба приложений запускает прокси-сервер туннеля и присоединяет отладчик. Затем можно выполнить запросы к приложению и увидеть, что отладчик приостанавливается в точках останова.

После завершения отладки закройте отладчик, выбрав **Отключить**. При появлении запроса нажмите кнопку **Да** , чтобы отключить удаленную отладку. Чтобы отключить его позже, снова щелкните свое приложение в Azure Explorer и выберите **Отключить удаленную отладку**.

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) вне кода приложения. Затем к ним можно получить доступ с помощью стандартного шаблона Node. js. Например, для доступа к параметру приложения с именем `NODE_ENV` используйте следующий код:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Запуск grunt/Bower/gulp

По умолчанию KUDU выполняется `npm install --production` при распознавании приложения Node. js. Если приложению требуются какие-либо популярные средства автоматизации, такие как grunt, Bower или gulp, необходимо предоставить [Пользовательский скрипт развертывания](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) для его запуска.

Чтобы разрешить репозиторию запускать эти средства, необходимо добавить их в зависимости в *Package. JSON.* Пример.

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

В окне терминала перейдите в каталог корня репозитория и выполните следующие команды:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Корневой каталог репозитория теперь содержит два дополнительных файла: *. Deployment* и *deploy.sh*.

Откройте *deploy.sh* и найдите раздел `Deployment`, который выглядит следующим образом:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Этот раздел завершается выполнением `npm install --production`. Добавьте раздел кода, чтобы запустить требуемое средство *в конце* раздела `Deployment`:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

См. [пример в примере Sample. js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), где скрипт развертывания также выполняет пользовательскую команду `npm install`.

### <a name="bower"></a>Bower

Этот фрагмент кода выполняется `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Этот фрагмент кода выполняется `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Этот фрагмент кода выполняется `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Обнаружение сеанса HTTPS

В Службе приложений [завершение SSL-запросов](https://wikipedia.org/wiki/TLS_termination_proxy) происходит в подсистеме балансировки нагрузки сети, поэтому все HTTPS-запросы достигают вашего приложения в виде незашифрованных HTTP-запросов. Если логика вашего приложения проверяет, зашифрованы ли пользовательские запросы, проверяйте заголовок `X-Forwarded-Proto`.

Популярные веб-платформы позволяют получить доступ к информации `X-Forwarded-*` в стандартном шаблоне приложения. В [Express](https://expressjs.com/)можно использовать [Доверенные прокси-серверы](https://expressjs.com/guide/behind-proxies.html). Пример.

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Открытие сеанса SSH в браузере

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Устранение неисправностей

Когда работа приложения Node. js ведет себя иначе в службе приложений или с ошибками, попробуйте выполнить следующие действия.

- [Получите доступ к потоку журнала](#access-diagnostic-logs).
- Протестируйте приложение локально в рабочем режиме. Служба приложений запускает приложения Node. js в рабочем режиме, поэтому необходимо убедиться, что проект работает в режиме рабочей среды локально. Пример.
    - В зависимости от *пакета Package. JSON*различные пакеты могут быть установлены для рабочего режима (`dependencies` и `devDependencies`).
    - Некоторые веб-платформы могут развертывать статические файлы в рабочем режиме по-разному.
    - При работе в рабочем режиме некоторые веб-платформы могут использовать пользовательские сценарии запуска.
- Запустите приложение в службе приложений в режиме разработки. Например, в [среднем. js](https://meanjs.org/)можно настроить приложение в режиме разработки в среде выполнения, [задав параметр приложения `NODE_ENV`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Учебник. приложение Node. js с MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)
