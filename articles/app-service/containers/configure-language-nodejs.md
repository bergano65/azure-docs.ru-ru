---
title: Настройка приложения Node.js — служба приложений Azure | Документация Майкрософт
description: Сведения о настройке приложения Node.js на работу в службе приложений Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 3074048dd4426a10e706e37e6d375ea4995fcbbb
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919780"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Настройка приложения Node.js в Linux для службы приложений Azure

Должны быть развернуты приложения node.js с помощью все необходимые зависимости NPM. Подсистема развертывания службы приложений (Kudu) автоматически выполняется `npm install --production` автоматически при развертывании [репозиторий Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), или [ZIP-пакет](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) с процессами сборки, включены ли. При развертывании файлов с помощью [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), тем не менее, необходимо вручную загрузить необходимые пакеты.

Это руководство содержит основные понятия и инструкции для разработчиков Node.js, которые используют встроенный контейнер Linux в службе приложений. Если вы раньше не использовали службы приложений Azure, выполните [краткое руководство по Node.js](quickstart-nodejs.md) и [Node.js с MongoDB руководством](tutorial-nodejs-mongodb-app.md) первого.

## <a name="show-nodejs-version"></a>Показать версию Node.js

Чтобы отобразить текущую версию Node.js, выполните следующую команду [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Чтобы отобразить всех поддерживаемых версий Node.js, выполните следующую команду [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Выбор версии Node.js

Для настройки приложения [поддерживаемую версию Node.js](#show-nodejs-version), выполните следующую команду [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Этот параметр указывает версию Node.js, чтобы использовать, как во время выполнения, так и при восстановлении автоматических пакетов в Kudu.

> [!NOTE]
> В проекте должна быть установлена версия Node.js `package.json`. Подсистема развертывания выполняется в отдельном контейнере, который содержит все поддерживаемые версии Node.js.

## <a name="configure-nodejs-server"></a>Настройка сервера Node.js

Состав контейнеров Node.js [PM2](https://pm2.keymetrics.io/), диспетчера рабочих процессов. Можно настроить приложение для запуска PM2, или с помощью NPM или с пользовательской команды.

- [Выполнить настраиваемую команду](#run-custom-command)
- [Запустите npm start](#run-npm-start)
- [Запуск с PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Выполнить настраиваемую команду

Службы приложений можно запустить приложение с помощью пользовательской команды, такие как исполняемый файл, например *run.sh*. Например, для запуска `npm run start:prod`, выполните следующую команду [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Запустите npm start

Чтобы запустить приложение, нажав `npm start`, просто убедитесь, что `start` сценарий находится в *package.json* файла. Например: 

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

Чтобы использовать пользовательскую *package.json* в проекте, выполните следующую команду [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Запуск с PM2

Контейнер автоматически запускает приложение с PM2, когда будет найден один из общих файлов Node.js в проекте:

- *Bin/www*
- *файл Server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Одно из следующих [файлы PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* и *ecosystem.config.js*

Можно также настроить файл настраиваемой начальной, со следующими расширениями:

- Объект *.js* файла
- Объект [файл PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) с расширением *.json*, *. config.js*, *.yaml*, или *.yml*

Чтобы добавить файл настраиваемой начальной, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Удаленная отладка

> [!NOTE]
> Удаленная отладка сейчас доступна Предварительная версия.

Можно отлаживать приложения Node.js удаленно в среде [Visual Studio Code](https://code.visualstudio.com/) если его необходимо настроить для [работы PM2](#run-with-pm2), за исключением при запуске его с помощью *. config.js, *.yml, или *.yaml*.

В большинстве случаев никакая дополнительная настройка не является обязательным для приложения. Если приложение выполняется с *process.json* файла (по умолчанию или пользовательский), он должен иметь `script` свойства в корне JSON. Например: 

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Для настройки Visual Studio Code для удаленной отладки, установите [расширение службы приложений](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Следуйте инструкциям на странице "расширения" и войдите в Azure в Visual Studio Code.

В Azure explorer, найдите приложение, вы хотите отладить, щелкните его правой кнопкой мыши и выберите **начать удаленную отладку**. Нажмите кнопку **Да** включить, то для вашего приложения. Служба приложений запускает туннель прокси-сервер для вас и присоединит отладчик. Затем можно выполнять запросы к приложению и отладчика Приостановка в точках останова см. в разделе.

После завершения отладки, остановите отладчик, выбрав **Disconnect**. При появлении запроса, следует нажать кнопку **Да** отключить удаленную отладку. Чтобы отключить его позже, щелкните правой кнопкой мыши по своему приложению в Azure explorer и выберите **отключить удаленную отладку**.

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) вне кода приложения. Затем они будут доступны с помощью стандартного шаблона Node.js. Например, для доступа к параметру приложения с именем `NODE_ENV` используйте следующий код:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Запустите Bower и Grunt/Gulp

По умолчанию выполняется Kudu `npm install --production` при обнаружении развертывается приложение Node.js. Если приложению требуются некоторые средства популярные службы автоматизации, например Grunt, Bower или Gulp, необходимо предоставить [пользовательский скрипт развертывания](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) для его запуска.

Чтобы включить репозиторий, чтобы запускать эти средства, необходимо добавить их к зависимостям в *package.json.* Например: 

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

В окне терминала на локальном измените каталог на корневой папке репозитория и выполните следующие команды:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Теперь в корневом каталоге репозитория расположены два дополнительных файла: *.deployment* и *deploy.sh*.

Откройте *deploy.sh* и найти `Deployment` раздел, который выглядит следующим образом:

```bash
##################################################################################################################################
# Deployment
# ----------
```

В этом разделе заканчивается выполнение `npm install --production`. Добавьте раздел кода, необходимо запустить средство требуется *в конце* из `Deployment` раздел:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

См. в разделе [образце MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), которой также выполняется скрипт развертывания пользовательского `npm install` команды.

### <a name="bower"></a>Bower

Этот фрагмент выполняется `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Этот фрагмент выполняется `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Этот фрагмент выполняется `grunt`.

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

Популярные веб-платформы позволяют получить доступ к информации `X-Forwarded-*` в стандартном шаблоне приложения. В [Express](https://expressjs.com/), можно использовать [доверия прокси-серверы](https://expressjs.com/guide/behind-proxies.html). Например: 

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

## <a name="troubleshooting"></a>Устранение неполадок

Когда рабочее приложение Node.js работает по-разному, в службе приложений или содержит ошибки, попробуйте сделайте следующее:

- [Получите доступ к потоку журнала](#access-diagnostic-logs).
- Тестирование приложения локально в рабочем режиме. Служба приложений запускает приложения Node.js в рабочем режиме, поэтому необходимо убедиться, что проект работает надлежащим образом в рабочем режиме локально. Например: 
    - В зависимости от вашей *package.json*, разные пакеты могут быть установлены для рабочий режим (`dependencies` и `devDependencies`).
    - Некоторые веб-платформы может развернуть статические файлы по-разному в рабочем режиме.
    - Некоторые веб-платформы может использовать специальные сценарии, при выполнении в рабочем режиме.
- Запустите приложение в службе приложений в режиме разработки. Например, в [MEAN.js](https://meanjs.org/), можно задать свое приложение в режим разработки в среде выполнения с [параметр `NODE_ENV` параметр приложения](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство Приложение Node.js с MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)
