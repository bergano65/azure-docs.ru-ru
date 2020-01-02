---
title: Учебник. Активация группы контейнеров по функциям Azure
description: Создание бессерверной функции PowerShell, которая активируется HTTP-запросами, для автоматического создания экземпляров контейнеров Azure
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 49eb0721972a92f33bda2532367bc78280b6e655
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533374"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Руководство по применению функции Azure, активируемой HTTP-запросами, для создания группы контейнеров

[Функции Azure](../azure-functions/functions-overview.md) — это бессерверная служба вычислений, которая умеет выполнять скрипты или код в ответ на такие события, как HTTP-запрос, таймер или сообщение в очереди службы хранилища Azure.

Работая с этим руководством, вы создадите функцию Azure, которая принимает HTTP-запрос и активирует развертывание [группы контейнеров](container-instances-container-groups.md). В этом примере показано, как применить базовые возможности Функций Azure для автоматического создания ресурсов в службе "Экземпляры контейнеров Azure". Вы можете изменить или дополнить этот пример, чтобы создать более сложные сценарии или применить другие триггеры событий. 

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Использование Visual Studio Code в сочетании с [расширением Функций Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) для создания простой функции PowerShell, активируемой по HTTP-запросу.
> * Создание удостоверения в приложении-функции и предоставление ему разрешений на создание ресурсов Azure.
> * Изменение и повторная публикация функции PowerShell для автоматизации развертывания группы контейнеров с одним контейнером.
> * Проверка развертывания контейнера, активируемого по HTTP-запросу.

> [!IMPORTANT]
> Сейчас доступна предварительная версия PowerShell для Функций Azure. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="prerequisites"></a>Предварительные требования

Предварительные требования для установки Visual Studio Code и его использования с Функциями Azure см. в статье [Создание первой функции PowerShell в Azure](../azure-functions/functions-create-first-function-powershell.md#prerequisites).

Некоторые инструкции из этого руководства предусматривают использование Azure CLI. Для их выполнения можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Создание базовой функции PowerShell

Выполните инструкции из статьи [Создание первой функции PowerShell в Azure](../azure-functions/functions-create-first-function-powershell.md), чтобы создать функцию PowerShell из шаблона Триггер HTTP. Присвойте этой функции Azure имя **HttpTrigger**, предложенное по умолчанию. Протестируйте эту функцию локально и опубликуйте проект в виде приложения-функции Azure, как описано в этом кратком руководстве. В этом примере создается простая функция, которая активируется HTTP-запросом и возвращает текстовую строку. Далее вы измените функцию, чтобы она создавала группу контейнеров.

В этой статье предполагается, что вы публикуете проект с именем *myfunctionapp* в группе ресурсов Azure, которой автоматически присваивается имя, совпадающее с именем приложения-функции (также *myfunctionapp*). При выполнении последующих инструкций замените их фактическими именами приложения-функции и группы ресурсов.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Настройка в приложении-функции удостоверения, управляемого Azure

Здесь вы настроите в приложении-функции [управляемое удостоверение](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#adding-a-system-assigned-identity), назначаемое системой. С его помощью выполняющий приложение узел PowerShell сможет автоматически выполнять проверку подлинности, позволяя функциям выполнять действия со службами Azure, к которым предоставляет доступ это удостоверение. Далее вы предоставите управляемому удостоверению права на создание ресурсов в группе ресурсов для приложения-функции. 

Прежде всего выполните команду [az group show][az-group-show], чтобы получить идентификатор группы ресурсов для приложения-функции и сохраните его в переменной среды. В этом примере предполагается, что команда выполняется в оболочке Bash.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Выполните команду [az functionapp identity app assign][az-functionapp-identity-app-assign], чтобы назначить локальное удостоверение приложению-функции и присвоить ему роль участника в группе ресурсов. Эта роль позволит удостоверению создавать дополнительные ресурсы в группе ресурсов, например группы контейнеров.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Изменение функции HttpTrigger

Измените код PowerShell в функции **HttpTrigger**, чтобы она создавала группу контейнеров. Откройте файл функции `run.ps1` и найдите в нем следующий блок кода. Этот код отображает значение имени, если оно было передано как строка запроса в URL-адресе функции:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Замените эту строку следующим блоком кода. Теперь, если в строке запроса передано значение имени, оно используется для создания и именования группы контейнеров с помощью командлета [New-AzContainerGroup][new-azcontainergroup]. Не забудьте заменить имя *myfunctionapp* реальным именем группы ресурсов для приложения-функции.

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

В этом примере создается группа контейнеров с одним контейнером, в котором выполняется образ `alpine`. Этот контейнер выполняет одну команду `echo` и завершает работу. В реальной системе можно точно так же запускать создание одной или нескольких групп контейнеров для выполнения пакетного задания.
 
## <a name="test-function-app-locally"></a>Локальное тестирование приложения-функции

Перед повторной публикацией проекта приложения-функции в Azure убедитесь, что функция правильно работает в локальной среде. Добавьте локальную точку останова в скрипт PowerShell и вызов [ сразу перед ней, как показано в ](../azure-functions/functions-create-first-function-powershell.md)этом кратком руководстве по PowerShell`Wait-Debugger`. Инструкции по отладке см. в [этой статье](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Повторная публикация приложения-функции Azure

Убедившись, что функция выполняется правильно на локальном компьютере, опубликуйте проект в уже существующем приложении-функции Azure.

> [!NOTE]
> Не забудьте удалить все вызовы `Wait-Debugger` перед публикацией своих функций в Azure.

1. Откройте палитру команд в Visual Studio Code. Найдите и выберите элемент `Azure Functions: Deploy to function app...`.
1. Выберите текущую рабочую папку для архивации и развертывания.
1. Выберите подписку, а затем имя существующего приложения-функции (в нашем примере это *myfunctionapp*). Подтвердите, что вы хотите перезаписать предыдущее развертывание.

После создания приложения-функции и применения пакета развертывания отобразится уведомление. Выберите **View Output** (Просмотреть выходные данные) в уведомлении, чтобы просмотреть результаты создания и развертывания обновленных ресурсов Azure.

## <a name="run-the-function-in-azure"></a>Запуск функции в Azure

После успешного завершения развертывания получите URL-адрес функции. Для этого можно открыть область **Azure: Функции** в Visual Studio Code и скопировать URL-адрес функции **HttpTrigger** или получить этот URL-адрес на [портале Azure](../azure-functions/functions-create-first-azure-function.md#test-the-function).

URL-адрес функции содержит уникальный код и имеет следующий формат:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Запуск функции без передачи имени

Для первого теста выполните команду `curl` и передайте ей URL-адрес функции без добавления строки запроса `name`. Не забудьте добавить уникальный код реальной функции.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

Функция возвращает код состояния 400 и текст `Please pass a name on the query string or in the request body`:

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Запуск функции с передачей имени для группы контейнеров

Теперь еще раз выполните команду `curl`, добавив имя группы контейнеров (в нашем примере это *mycontainergroup*) в качестве строки запроса `&name=mycontainergroup`:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

Функция возвращает код состояния 200 и активирует создание группы контейнеров:

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Убедитесь, что контейнер был выполнен с помощью команды [az container logs][az-container-logs]:

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Пример выходных данных:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы, созданные при работе с этим руководством, больше не требуются, можно выполнить команду [az group delete][az-group-delete], чтобы удалить группу ресурсов и все ресурсы, которые она содержит. Эта команда удаляет созданный вами реестр контейнеров, а также запущенный контейнер и все связанные с ним ресурсы.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Дополнительная информация

В результате работы с этим руководством вы создали функцию Azure, которая принимает HTTP-запрос и активирует развертывание группы контейнеров. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Использование Visual Studio Code в сочетании с расширением Функций Azure для создания простой функции PowerShell, активируемой по HTTP-запросу.
> * Создание удостоверения в приложении-функции и предоставление ему разрешений на создание ресурсов Azure.
> * Изменение кода функции PowerShell для автоматизации развертывания группы контейнеров с одним контейнером.
> * Проверка развертывания контейнера, активируемого по HTTP-запросу.

Подробный пример запуска и мониторинга контейнерного задания см. в [этой публикации](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) и прилагаемом [примере кода](https://github.com/anthonychu/functions-powershell-run-aci).

Подробные инструкции по созданию функций Azure и публикации проекта функций см. в [документации по Функциям Azure](/azure/azure-functions/). 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
