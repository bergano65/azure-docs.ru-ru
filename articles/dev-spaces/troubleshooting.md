---
title: Устранение неполадок
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 044e997703f5b274215fb05c7152186948b331b4
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761405"
---
# <a name="troubleshooting-guide"></a>Руководство по устранению неполадок

Это руководство содержит сведения о распространенных проблемах, которые могут возникнуть при использовании Azure Dev Spaces.

Если имеется проблема, при использовании пространств разработки Azure, создайте [проблему в репозитории GitHub пробелы разработки Azure](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Включение подробного ведения журнала

Чтобы более эффективно устранять неполадки, можно создать более подробные журналы для проверки.

Для расширения Visual Studio задайте переменной среды `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` значение 1. Обязательно перезапустите Visual Studio, чтобы изменения переменной среды вступили в силу. После включения подробные журналы будут записаны в каталог `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

В CLI вы можете выводить больше сведений во время выполнения команды, используя параметр `--verbose`. Вы также можете просмотреть более подробные журналы в `%TEMP%\Azure Dev Spaces`. На компьютере Mac каталог TEMP можно найти, выполнив `echo $TMPDIR` в окне терминала. На компьютере с ОС Linux каталог TEMP обычно называется `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Отладка служб с несколькими экземплярами

В настоящее время Azure Dev Spaces лучше всего работает при отладке одного экземпляра или pod. Файл azds.yaml содержит параметр *replicaCount*, указывающий количество модулей pod, которые будут выполняться в Kubernetes для вашей службы. Если вы измените replicaCount, чтобы настроить приложение для запуска нескольких модулей pod для данной службы, отладчик присоединится к первому модулю pod (при перечислении в алфавитном порядке). Отладчик присоединяется к другому модулю pod при перезапуске оригинального модуля pod, что может привести к непредвиденному поведению.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Ошибка создания контроллера Azure Dev Spaces

Вы можете увидеть эту ошибку, если что-то пойдет не так с созданием контроллера. Если это временная ошибка, ее можно устранить, удалив контроллер, а затем повторно создав его.

### <a name="try"></a>Попробуйте выполнить следующее.

Чтобы удалить контроллер, используйте интерфейс командной строки Azure Dev Spaces. Это невозможно сделать в Visual Studio или Cloud Shell. Чтобы установить CLI AZDS, сначала установите Azure CLI, а затем выполните эту команду:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Чтобы удалить контроллер, выполните эту команду:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Чтобы повторно создать контроллер, можно использовать CLI или Visual Studio. Следуйте инструкциям в руководствах, если вы ранее не выполняли эти задачи.


## <a name="error-service-cannot-be-started"></a>Ошибка Service cannot be started (Запуск службы невозможен).

Эта ошибка может возникать, когда не удается запустить код службы. Чаще всего причина в пользовательском коде. Чтобы получить дополнительные диагностические сведения, внесите следующие изменения в команды и параметры:

### <a name="try"></a>Попробуйте выполнить следующее.

В командной строке:

При использовании _azds.exe_ используйте параметр командной строки --verbose и параметр командной строки --output, чтобы указать формат выходных данных.
 
```cmd
azds up --verbose --output json
```

В Visual Studio:

1. Откройте меню **Сервис > Параметры** и в разделе **Проекты и решения** выберите **Сборка и запуск**.
2. Измените значение параметра **Степень подробности сообщений при сборке проекта MSBuild** на **Подробно** или **Диагностика**.

    ![Снимок экрана диалогового окна "Сервис > Параметры"](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>Многоэтапный Dockerfile
Вы получите сообщение об ошибке *Не удается запустить службу* при использовании многоэтапного Dockerfile. В этом случае подробный вывод содержит следующий текст:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Эта ошибка возникает из-за выполнения узлов AKS на старой версии Docker, которая не поддерживает многоэтапные сборки. Чтобы избежать многоэтапных сборок, повторно создайте Dockerfile.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Повторный запуск службы после повторного создания контроллера
Ошибка *Не удается запустить службу* может появиться при попытке перезапустить службу после удаления и повторного создания контроллера Azure Dev Spaces, связанного с этим кластером. В этом случае подробный вывод содержит следующий текст:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Эта ошибка возникает в связи с тем, что удаление контроллера Dev Spaces не приводит к удалению служб, ранее установленных этим контроллером. Воссоздание контроллера и последующая попытка запустить службы с использованием нового контроллера завершаются неудачно, поскольку старые службы все еще находятся на месте.

Чтобы решить эту проблему, используйте команду `kubectl delete`, чтобы вручную удалить старые службы из кластера, а затем повторно запустите Dev Spaces для установки новых служб.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Сбой разрешения DNS-имен для общедоступных URL-адресов, связанных со службой Dev Spaces

Вы можете настроить общедоступную конечную точку URL-адреса для службы, указав параметр `--public` в команде `azds prep` или установив флажок `Publicly Accessible` в Visual Studio. Общедоступное DNS-имя автоматически регистрируется при запуске службы в Dev Spaces. Если DNS-имя не зарегистрировано, в веб-браузере во время подключения к общедоступному URL-адресу вы увидите ошибки *Не удается отобразить страницу* или *Этот сайт недоступен*.

### <a name="try"></a>Попробуйте выполнить следующее.

Чтобы вывести список всех URL-адресов, связанных со службами Dev Spaces, можно использовать следующую команду:

```cmd
azds list-uris
```

Если URL-адрес находится в режиме*Ожидание*, это значит, что Dev Spaces по-прежнему ожидает завершения регистрации DNS. Иногда для выполнения регистрации требуется несколько минут. Для каждой службы Dev Spaces также открывается туннель localhost, который можно использовать во время ожидания регистрации DNS.

Если URL-адрес остается в состоянии *Ожидание* более 5 минут, это может указывать на проблему с внешним модулем DNS pod, который создает общедоступную конечную точку или контроллер nginx-входа, который приобретает общедоступную конечную точку. Чтобы удалить модули pod можно использовать следующие команды. AKS автоматически восстанавливает удаленные модули pod.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Ошибка Required tools and configurations are missing (Отсутствуют необходимые средства и конфигурации)

Эта ошибка может возникать при запуске VS Code: "[Azure Dev Spaces] Отсутствуют необходимые средства и конфигурации для сборки и отладки "[имя проекта]"".
Ошибка означает, что это средство azds.exe не находится в переменной среды PATH, как показано в VS Code.

### <a name="try"></a>Попробуйте выполнить следующее.

Запустите VS Code из командной строки, где переменная среды PATH задана должным образом.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Ошибка "Required tools to build and debug 'projectname' are out of date " ("Необходимые инструменты для сборки и отладки "projectname" устарели")

Вы увидите эту ошибку в Visual Studio Code, если у вас есть более новая версия расширения VS Code для Azure Dev Spaces, но более старая версия Azure Dev Spaces CLI.

### <a name="try"></a>Попытка

Скачайте и установите последнюю версию Azure Dev Spaces CLI.

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Ошибка azds is not recognized as an internal or external command, operable program, or batch file (azds не распознано как внутренняя или внешняя команда, исполняемая программа или пакетный файл)
 
Эта ошибка может возникать, если средство azds.exe неправильно установлено или настроено.

### <a name="try"></a>Попробуйте выполнить следующее.

1. Попробуйте найти azds.exe в расположении %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (Preview). Если файл есть в этой папке, добавьте это расположение в переменную среды PATH.
2. Если средство azds.exe не установлено, выполните следующую команду:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Предупреждение о невозможности создания файла Docker из-за неподдерживаемого языка
Azure Dev Spaces предоставляет встроенную поддержку C# и Node.js. При запуске команды *azds prep* в каталоге, содержащем код, написанный на одном из этих языков, Azure Dev Spaces автоматически создает для вас соответствующий файл Docker.

Вы все еще можете использовать Azure Dev Spaces с кодом, написанным на других языках, но вам нужно будет вручную создать файл Docker перед первым запуском *azds up*.

### <a name="try"></a>Попробуйте выполнить следующее.
Если ваше приложение написано на языке, который изначально не поддерживается в Azure Dev Spaces, вам необходимо предоставить соответствующий файл Docker для создания образа контейнера, в котором выполняется ваш код. Необходимые сведения для написания файла Docker, который отвечает вашим потребностям, см. в статье с [рекомендациями по написанию файлов Docker](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) и справочнике по [файлам Docker](https://docs.docker.com/engine/reference/builder/).

После создания соответствующего файла Docker вы можете продолжить выполнение команды *azds up* для запуска приложения в Azure Dev Spaces.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Ошибка "upstream connect error or disconnect/reset before headers" (ошибка вышестоящего подключения или отключение либо сброс до прохождения заголовков)
Вы можете увидеть эту ошибку при попытке получить доступ к службе. Например, при переходе по URL-адресу службы в браузере. 

### <a name="reason"></a>Причина 
Порт контейнера недоступен. Эта проблема может возникать по следующим причинам: 
* Процесс сборки или развертывания контейнера еще не завершен. Это может случиться, если вы запускаете `azds up` или отладчик, а затем пытаетесь получить доступ к контейнеру до его успешного развертывания.
* Конфигурация порта не согласуется в _Dockerfile_, диаграмме Helm и серверном коде, который открывает порт.

### <a name="try"></a>Попробуйте выполнить следующее.
1. Если процесс сборки или развертывания еще не завершен, вы можете подождать 2–3 секунды и повторить попытку доступа к службе. 
1. Проверьте конфигурацию порта. Указанные номера портов должны **совпадать** во всех следующих ресурсах:
    * **Dockerfile:** задается инструкцией `EXPOSE`.
    * **[Диаграмма Helm](https://docs.helm.sh):** задается значениями `externalPort` и `internalPort` для службы (часто находится в файле `values.yml`).
    * Все порты, открытые в коде приложения, например в Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Файл конфигурации не найден
Вы запускаете `azds up`, и появляется следующая ошибка: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Причина
Необходимо запустить `azds up` из корневого каталога кода, который вы хотите выполнить, и инициализировать папку с кодом для запуска с Azure Dev Spaces.

### <a name="try"></a>Попробуйте выполнить следующее.
1. Измените текущий каталог на корневую папку, содержащую код службы. 
1. Если у вас нет файла _azds.yaml_ в папке кода, запустите `azds prep` для создания ресурсов Docker, Kubernetes и Azure Dev Spaces.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Ошибка: "The pipe program 'azds' exited unexpectedly with code 126" (Программа канала azds неожиданно завершила работу с кодом 126)
Запуск отладчика VS Code иногда может привести к этой ошибке.

### <a name="try"></a>Попробуйте выполнить следующее.
1. Закройте и снова откройте VS Code.
2. Нажмите клавишу F5 еще раз.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Ошибка отладки "Не удалось найти расширение отладчика для типа: coreclr"
При запуске отладчика VS Code возникает ошибка: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Причина
На компьютере, на котором ведется разработка, не установлено расширение VS Code для C#. C# Расширение включает поддержку отладки для .NET Core (CoreCLR).

### <a name="try"></a>Попробуйте выполнить следующее.
Установите [расширение VS Code для C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Ошибка отладки "Configured debug type 'coreclr' is not supported" (Настроенный тип отладки "coreclr" не поддерживается)
При запуске отладчика VS Code возникает ошибка: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Причина
На компьютере, на котором ведется разработка, не установлено расширения VS Code для Azure Dev Spaces.

### <a name="try"></a>Попробуйте выполнить следующее.
Установите [расширение VS Code для Azure Dev Spaces](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Ошибка отладки "Invalid 'cwd' value '/src'. The system cannot find the file specified" ("Недопустимое значение "/src" каталога "cwd". Системе не удается найти указанный файл") или "launch: program '/src/[path to project binary]' does not exist" ("запуск: программа "/src/[путь к двоичному файлу проекта]" не существует")
При запуске отладчика VS Code возникает ошибка `Invalid 'cwd' value '/src'. The system cannot find the file specified.` и (или) `launch: program '/src/[path to project executable]' does not exist`.

### <a name="reason"></a>Причина
По умолчанию расширение VS Code использует `src` в качестве рабочего каталога для проекта в контейнере. Эта ошибка может возникать, если вы обновили свой `Dockerfile`, указав другой рабочий каталог.

### <a name="try"></a>Попробуйте выполнить следующее.
Обновите файл `launch.json` в подкаталоге `.vscode` папки проекта. Измените каталог `configurations->cwd`, чтобы он указывал на тот же каталог, что и `WORKDIR`, определенный в `Dockerfile` проекта. Возможно, вам также потребуется обновить директиву `configurations->program`.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Не удалось найти имя типа или пространства имен "MyLibrary"

### <a name="reason"></a>Причина 
Контекст сборки находится на уровне проекта или службы по умолчанию, поэтому используемый проект библиотеки невозможно найти.

### <a name="try"></a>Попробуйте выполнить следующее.
Что необходимо сделать:
1. В файле _azds.yaml_ задайте для контекста сборки уровень решения.
2. Измените файлы _Dockerfile_ и _Dockerfile.develop_ для корректного обращения к _CSPROJ_-файлам проекта в зависимости от нового контекста сборки.
3. Поместите _DOCKERIGNORE_-файл рядом с SLN-файлом и внесите необходимые изменения.

Вы можете найти пример по ссылке https://github.com/sgreenmsft/buildcontextsample.

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Ошибка авторизации "Microsoft.DevSpaces/register/action"
Вам потребуется доступ *владельца* или *участника* в подписке Azure, чтобы управлять Azure Dev Spaces. Эта ошибка может возникать, если вы пытаетесь управлять Dev Spaces, но у вас отсутствует доступ *владельца* или *участника* к соответствующей подписке Azure.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Причина
Выбранная подписка Azure не зарегистрировала пространство имен `Microsoft.DevSpaces`.

### <a name="try"></a>Попробуйте выполнить следующее.
Пользователь с правами владельца или участника в подписке Azure может запустить следующую команду Azure CLI для регистрации пространства имен `Microsoft.DevSpaces` вручную:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Время ожидания Dev Spaces истекает на шаге *Ожидание создания сборки образа контейнера...* с виртуальными узлами AKS

### <a name="reason"></a>Причина
Это время ожидания происходит при попытке использовать пробелы разработки для запуска службы, который настроен для запуска на [виртуального узла AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Служба Dev Spaces в настоящее время не поддерживает создание служб или их отладку на виртуальных узлах.

При запуске `azds up` с параметром `--verbose` или включении подробного ведения журнала в Visual Studio можно просмотреть дополнительные сведения:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Приведенная выше команда показывает, что pod службы был присвоен *виртуального node-aci-linux*, который является виртуального узла.

### <a name="try"></a>Попробуйте выполнить следующее.
Обновите диаграмму Helm для службы, чтобы удалить любые значения *nodeSelector* и/или *tolerations*, позволяющие службе выполняться на виртуальном узле. Обычно эти значения определяются в файле `values.yaml` диаграммы.

Вы можете по-прежнему использовать кластер AKS со включенной функцией виртуальных узлов, если служба, которую требуется создать или отладить в Dev Spaces, выполняется на узле виртуальной машины. Это конфигурация по умолчанию.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Ошибка. Не удалось найти готовый pod tiller" при запуске Dev Spaces

### <a name="reason"></a>Причина
Эта ошибка возникает, если клиент Helm больше не может обратиться к модулю pod Tiller, который выполняется в кластере.

### <a name="try"></a>Попробуйте выполнить следующее.
Перезапуск узлов агента в кластере обычно устраняет эту проблему.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Прокси-сервер Azure Dev Spaces может конфликтовать с модулями pod, запущенными в пространстве разработки

### <a name="reason"></a>Причина
При включении Dev Spaces в пространстве имен кластера AKS дополнительный контейнер _mindaro-proxy_ устанавливается в каждом модуле pod, выполняющемся в этом пространстве имен. Этот контейнер перехватывает вызовы к службам в модуле pod, который является неотъемлемой частью возможностей, реализованных командой Dev Spaces, однако он может мешать определенным службам в этих модулях pod. Известно, мешают модулей запуск кэша Azure для Redis, в результате чего подключения ошибки и сбои в связи с основным и дополнительным.

### <a name="try"></a>Попробуйте выполнить следующее.
Вы можете переместить затронутые модули pod в пространство имен внутри кластера, в котором _не включены_ Dev Spaces. Остальная часть приложения может продолжать работу в пространстве имен с поддержкой Dev Spaces. Dev Spaces не будет устанавливать контейнер _mindaro-proxy_ в пространствах имен, в которых не включен Dev Spaces.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces не использует существующий файл Dockerfile для сборки контейнера

### <a name="reason"></a>Причина
Настройте службу Azure Dev Spaces так, чтобы она указывала на конкретный файл _Dockerfile_ в вашем проекте. Если Azure Dev Spaces не использует файл _Dockerfile_, который вы хотели использовать для сборки своих контейнеров, то можно явно указать Azure Dev Spaces, какой файл Dockerfile нужно использовать. 

### <a name="try"></a>Попробуйте выполнить следующее.
Откройте файл _azds.yaml_, который служба Azure Dev Spaces создала в проекте. Используйте директиву *configurations->develop->build->dockerfile*, чтобы указать Dockerfile, который нужно использовать:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Ошибка «внутренняя Контрольные значения, которые не удалось: смотреть ENOSPC» при присоединении отладки для приложения Node.js

### <a name="reason"></a>Причина

Превысил узла, работающего с приложением Node.js, вы пытаетесь подключить к с помощью отладчика pod *fs.inotify.max_user_watches* значение. В некоторых случаях [значение по умолчанию *fs.inotify.max_user_watches* может быть слишком мал для обработки, подключить отладчик напрямую к pod](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Попытка
Временного решения этой проблемы является увеличение значения *fs.inotify.max_user_watches* на каждом узле в кластере и перезапустить этот узел, чтобы изменения вступили в силу.

## <a name="new-pods-are-not-starting"></a>Новых модулей POD не запускается

### <a name="reason"></a>Причина

Инициализатор Kubernetes не удается применить PodSpec для новых модулей POD, из-за изменения разрешений RBAC для *администратором кластера* роль в кластере. Новый модуль может иметь недопустимые PodSpec, например учетной записи службы, связанной с pod больше не существует. Чтобы увидеть POD, содержащихся в *ожидающие* состояния из-за проблемы инициализатор, используйте `kubectl get pods` команды:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Эта проблема может повлиять на POD, содержащихся в *все пространства имен* в кластере, включая пространства имен, не поддерживающих пробелы разработки Azure.

### <a name="try"></a>Попытка

[Обновление до последней версии интерфейса командной строки пространств разработки](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) и удалив *azds InitializerConfiguration* из контроллера пробелы разработки Azure:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

После удаления *azds InitializerConfiguration* из контроллера пробелы разработки Azure, используйте `kubectl delete` удалить POD, содержащихся в *ожидающие* состояния. В конце концов ожидающих модулей будут удалены, повторное развертывание модулей будет удален.

Если новых групп POD в по-прежнему находятся в *ожидающие* состояния после повторного развертывания, используйте `kubectl delete` удалить POD, содержащихся в *ожидающие* состояния. В конце концов ожидающих модулей будут удалены, удалите контроллер из кластера и повторно установить его:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

После переустановки контроллере, повторное развертывание модулей будет удален.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Неправильные разрешения RBAC для вызова контроллера пробелы разработки и API-интерфейсов

### <a name="reason"></a>Причина
Доступ к контроллеру пробелы разработки Azure пользователь должен иметь доступ на чтение администратор *kubeconfig* в кластере AKS. Например, это разрешение доступно в [встроенные Azure роль администратора кластера Kubernetes службы](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Пользователь, доступ к контроллеру пробелы разработки Azure должен также иметь *участник* или *владельца* роли RBAC для контроллера.

### <a name="try"></a>Попытка
Дополнительные сведения об изменении разрешений пользователя для кластера AKS доступны [здесь](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user).

Обновление пользователя роли RBAC для контроллера:

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. Перейдите к группе ресурсов, содержащей контроллера, обычно это то же самое, что и кластер AKS.
1. Включить *Показать скрытые типы* флажок.
1. Щелкните на контроллере.
1. Откройте *управление доступом (IAM)* области.
1. Щелкните *назначения ролей* вкладки.
1. Нажмите кнопку *добавить* затем *добавить назначение роли*.
    * Для *роли* выберите либо *участник* или *владельца*.
    * Для *назначение доступа к* выберите *пользователя, группы или субъекта-службы Azure AD*.
    * Для *выберите* поиска для пользователя, необходимо предоставить разрешения на доступ.
1. Выберите команду *Сохранить*.
