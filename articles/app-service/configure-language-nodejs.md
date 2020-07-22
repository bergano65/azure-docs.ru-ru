---
title: Настройка приложений Node.js Windows
description: Узнайте, как настроить Node.js приложение в собственных экземплярах Windows службы приложений. В этой статье показаны наиболее распространенные задачи настройки.
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 9f4ccdd04b8d57784f452dc28fa4507fb7ea94c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907992"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Настройка приложения Node.js Windows для службы приложений Azure

Node.js приложения должны быть развернуты со всеми необходимыми зависимостями NPM. Модуль развертывания службы приложений автоматически запускается `npm install --production` при развертывании [репозитория Git](deploy-local-git.md)или [ZIP-пакета](deploy-zip.md) с включенной автоматизацией сборок. Однако при развертывании файлов с помощью [FTP/S](deploy-ftp.md)нужные пакеты необходимо загрузить вручную. Сведения о приложениях Linux см. [в статье Настройка приложения PHP для Linux для службы приложений Azure](containers/configure-language-nodejs.md).

Это краткое описание содержит основные понятия и инструкции для Node.js разработчиков, развернутых в службе приложений. Если вы никогда не использовали службу приложений Azure, сначала следуйте инструкциям в [кратком](app-service-web-get-started-nodejs.md) руководстве поNode.js и [Node.js с MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Показывать Node.js версию

Чтобы отобразить текущую версию Node.js, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Чтобы отобразить все поддерживаемые версии Node.js, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Задать версию Node.js

Чтобы настроить для приложения [поддерживаемую версию Node.js](#show-nodejs-version), выполните следующую команду в [Cloud Shell](https://shell.azure.com) , чтобы установить `WEBSITE_NODE_DEFAULT_VERSION` поддерживаемую версию:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Этот параметр определяет используемую версию Node.js во время выполнения и во время автоматического восстановления пакета во время автоматизации сборки службы приложений.

> [!NOTE]
> Необходимо задать версию Node.js в проекте `package.json` . Модуль развертывания выполняется в отдельном процессе, который содержит все поддерживаемые версии Node.js.

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](configure-common.md) вне кода приложения. Затем к ним можно получить доступ с помощью стандартного шаблона Node.js. Например, для доступа к параметру приложения с именем `NODE_ENV` используйте следующий код:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Запуск grunt/Bower/gulp

По умолчанию Автоматизация сборки службы приложений выполняется `npm install --production` при обнаружении Node.jsного приложения с помощью Git (или развертывания ZIP с включенной автоматизацией сборки). Если приложению требуются какие-либо популярные средства автоматизации, такие как grunt, Bower или gulp, необходимо предоставить [Пользовательский скрипт развертывания](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) для его запуска.

Чтобы разрешить репозиторию запускать эти средства, необходимо добавить их в зависимости в *package.js.* Пример:

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

Откройте *deploy.sh* и найдите `Deployment` раздел, который выглядит следующим образом:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Этот раздел завершается выполнением `npm install --production` . Добавьте раздел кода, в котором необходимо запустить требуемое средство *в конце* `Deployment` раздела:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

См. [пример в примере MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), где скрипт развертывания также выполняет пользовательскую `npm install` команду.

### <a name="bower"></a>Bower

Этот фрагмент кода выполняется `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Этот фрагмент кода выполняется `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Этот фрагмент кода выполняется `grunt` .

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

Популярные веб-платформы позволяют получить доступ к информации `X-Forwarded-*` в стандартном шаблоне приложения. В [Express](https://expressjs.com/)можно использовать [Доверенные прокси-серверы](https://expressjs.com/guide/behind-proxies.html). Пример:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Когда работающий Node.js приложение работает иначе в службе приложений или с ошибками, попробуйте выполнить следующие действия.

- [Получите доступ к потоку журнала](#access-diagnostic-logs).
- Протестируйте приложение локально в рабочем режиме. Служба приложений запускает приложения Node.js в рабочем режиме, поэтому вам необходимо убедиться в том, что проект работает надлежащим образом локально в рабочем режиме. Пример:
    - В зависимости от *package.jsв*рабочем режиме могут быть установлены различные пакеты ( `dependencies` VS `devDependencies` ).
    - Некоторые веб-платформы в рабочем режиме могут выполнять другие операции при развертывании статических файлов.
    - Некоторые веб-платформы могут использовать специальные скрипты запуска в рабочем режиме.
- Запустите приложение в службе приложений в режиме разработки. Например, в [MEAN.js](https://meanjs.org/)можно настроить приложение в режиме разработки в среде выполнения, [задав `NODE_ENV` параметр приложения](configure-common.md).

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Руководство. Приложение Node.js с MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

