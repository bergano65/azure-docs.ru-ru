---
title: Настройка приложений Node.js
description: Узнайте, как настроить предварительно построенный контейнер Node.js для вашего приложения. В этой статье показаны наиболее распространенные задачи настройки.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252731"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Настройте приложение Linux Node.js для службы приложений Azure

Приложения Node.js должны быть развернуты со всеми необходимыми зависимостями NPM. Двигатель развертывания службы App (Kudu) автоматически работает `npm install --production` для вас при [развертывании репозитория Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)или пакета [с](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) включенными процессами сборки. Однако при развертывании файлов с помощью [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)необходимо загрузить необходимые пакеты вручную.

В этом руководстве содержатся ключевые концепции и инструкции для разработчиков Node.js, которые используют встроенный контейнер Linux в App Service. Если вы никогда не пользовались службой приложений Azure, сначала следуйте [quickstart](quickstart-nodejs.md) и [Node.js с помощью учебника MongoDB.](tutorial-nodejs-mongodb-app.md)

## <a name="show-nodejs-version"></a>Показать версию Node.js

Чтобы показать текущую версию Node.js, запустите следующую команду в [облачной оболочке:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Чтобы показать все поддерживаемые версии Node.js, запустите следующую команду в [облачной оболочке:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Версия Set Node.js

Чтобы настроить приложение в [поддерживаемую версию Node.js,](#show-nodejs-version)запустите следующую команду в [облачной оболочке:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Эта настройка определяет версию Node.js для использования, как во время выполнения, так и во время автоматического восстановления пакета в Kudu.

> [!NOTE]
> Вы должны установить версию Node.js `package.json`в вашем проекте. Двигатель развертывания работает в отдельном контейнере, который содержит все поддерживаемые версии Node.js.

## <a name="customize-build-automation"></a>Настройка автоматизации сборки

Если развертывать приложение с помощью пакетов Git или zip с включенной автоматизацией сборки, служба App Service выполняет этапы автоматизации через следующую последовательность:

1. Выполнить пользовательский `PRE_BUILD_SCRIPT_PATH`скрипт, если указано .
1. Запуск `npm install` без каких-либо `preinstall` флагов, который включает в себя npm и `postinstall` скрипты, а также устанавливает `devDependencies`.
1. Выполнить, `npm run build` если скрипт сборки указан в вашем *package.json.*
1. Выполнить, `npm run build:azure` если в скрипте сборки указан ассоциируется в вашем *пакете.json*.
1. Выполнить пользовательский `POST_BUILD_SCRIPT_PATH`скрипт, если указано .

> [!NOTE]
> Как описано в [npm документы](https://docs.npmjs.com/misc/scripts), скрипты, названные `prebuild` и `postbuild` запустить до и после `build`, соответственно, если указано. `preinstall`и `postinstall` работать до `install`и после, соответственно.

`PRE_BUILD_COMMAND`и `POST_BUILD_COMMAND` являются переменными среды, которые по умолчанию пусты. Чтобы запустить команды предварительной `PRE_BUILD_COMMAND`сборки, определите. Чтобы запустить команды после сборки, определите. `POST_BUILD_COMMAND`

В следующем примере указаны две переменные к серии команд, разделенных запятыми.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Для дополнительных переменных среды для [Oryx configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)настройки автоматизации сборки см.

Для получения дополнительной информации о том, как Служба приложений приложений работает и создает приложения Node.js в Linux, см. [документацию Oryx: Как обнаруживаются и строятся приложения Node.js.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)

## <a name="configure-nodejs-server"></a>Настройка сервера Node.js

Контейнеры Node.js поставляются с [PM2](https://pm2.keymetrics.io/), менеджером производственного процесса. Вы можете настроить приложение, чтобы начать с PM2, или с NPM, или с пользовательской командой.

- [Запуск пользовательской команды](#run-custom-command)
- [Запуск npm начала](#run-npm-start)
- [Запуск с PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Запуск пользовательской команды

Служба приложений может запустить приложение с помощью пользовательской команды, такой как исполняемая, *например, run.sh.* Например, для `npm run start:prod`выполнения, запуска следующей команды в [облачной оболочке:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Запуск npm начала

Чтобы запустить приложение `npm start`с помощью, просто убедитесь, что `start` скрипт находится в файле *package.json.* Пример:

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

Чтобы использовать пользовательский *package.json* в проекте, запустите следующую команду в [облачной оболочке:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Запуск с PM2

Контейнер автоматически запускает приложение с PM2, когда в проекте находится один из общих файлов Node.js:

- *бин/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Один из следующих [файлов PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* и *ecosystem.config.js*

Вы также можете настроить пользовательский файл запуска со следующими расширениями:

- Файл *.js*
- [Файл PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) с расширением *.json*, *.config.js,* *.yaml*или *.yml*

Чтобы добавить пользовательский файл запуска, запустите следующую команду в [облачной оболочке:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Удаленная отладка

> [!NOTE]
> Удаленная отладка в настоящее время находится в предварительном просмотре.

Вы можете отладить приложение Node.js удаленно в [Visual Studio Code,](https://code.visualstudio.com/) если настроить его для [запуска с PM2](#run-with-pm2), за исключением случаев, когда вы запустите его с помощью q.config.js, q.yml, или *.yaml*.

В большинстве случаев для вашего приложения не требуется дополнительная конфигурация. Если ваше приложение работает с файлом *process.json* (по `script` умолчанию или на заказ), оно должно иметь свойство в корне JSON. Пример:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Чтобы настроить visual Studio Code для удаленной отладки, установите [расширение Службы App.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) Следуйте инструкциям на странице расширения и войдите в Azure в Visual Studio Code.

В Explorer Azure найдите приложение, необходимое для отладки, нажмите на него вправо и выберите **Start Remote Debugging.** Нажмите **Yes,** чтобы включить его для вашего приложения. Служба приложения запускает прокси-сервер туннеля для вас и прикрепляет отладчика. Затем вы можете сделать запросы в приложение и увидеть отладчик паузы в точках перерыва.

После завершения отладки остановите отладку, выбрав **отключение.** При запросе следует нажать **Yes,** чтобы отключить удаленную отладку. Чтобы отключить его позже, нажмите правое приложение снова в Explorer Azure и выберите **отключить удаленную удаленную отладку.**

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) вне кода приложения. Затем вы можете получить к ним доступ, используя стандартный шаблон Node.js. Например, для доступа к параметру приложения с именем `NODE_ENV` используйте следующий код:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Выполнить Grunt/ Бауэр / Гульп

По умолчанию Kudu запускается, `npm install --production` когда распознает развертывание приложения Node.js. Если вашему приложению требуется какой-либо из популярных инструментов автоматизации, таких как Grunt, Bower или Gulp, для его выполнения необходимо предоставить [пользовательский сценарий развертывания.](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)

Чтобы включить репозиторий в эти инструменты, необходимо добавить их в зависимости в *package.json.* Пример:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Из окна локального терминала измените каталог на корень репозитория и запустите следующие команды:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Ваш корень репозитория теперь имеет два дополнительных файла: *.deployment* и *deploy.sh*.

Откройте *deploy.sh* `Deployment` и найдите раздел, который выглядит следующим образом:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Этот раздел заканчивается запуском. `npm install --production` Добавьте раздел кода, необходимый для запуска необходимого инструмента *в конце* раздела: `Deployment`

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Смотрите [пример в примере MEAN.js,](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)где скрипт `npm install` развертывания также выполняет пользовательскую команду.

### <a name="bower"></a>Bower

Этот фрагмент работает `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Этот фрагмент работает `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Этот фрагмент работает `grunt`.

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

Популярные веб-платформы позволяют получить доступ к информации `X-Forwarded-*` в стандартном шаблоне приложения. В [Express](https://expressjs.com/)вы можете использовать [доверенные прокси.](https://expressjs.com/guide/behind-proxies.html) Пример:

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

Когда работающая приложение Node.js ведет себя по-разному в App Service или имеет ошибки, попробуйте следующее:

- [Получите доступ к потоку журнала](#access-diagnostic-logs).
- Проверьте приложение локально в режиме производства. Служба приложения App запускает приложения Node.js в производственном режиме, поэтому необходимо убедиться, что ваш проект работает в обычном режиме локально. Пример:
    - В зависимости от *вашего package.json,* различные`dependencies` пакеты `devDependencies`могут быть установлены для режима производства ( по сравнению с ).
    - Некоторые веб-платформы могут развертывать статические файлы по-разному в режиме производства.
    - Некоторые веб-платформы могут использовать пользовательские сценарии запуска при запуске в режиме производства.
- Запустите приложение в службе приложений в режиме разработки. Например, в [MEAN.js](https://meanjs.org/)вы можете настроить приложение в режим разработки во время выполнения, [установив `NODE_ENV` настройку приложения.](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Учебник: Приложение Node.js с MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)
