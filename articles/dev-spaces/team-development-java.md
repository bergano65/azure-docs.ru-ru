---
title: Коллективная разработка с помощью Azure Dev Spaces, Java и VS Code | Документация Майкрософт
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: stepro
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: mmontwil
ms.openlocfilehash: 4019bc3eb1a0071abc330cb7fe6127eeca856cc0
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476372"
---
# <a name="team-development-with-azure-dev-spaces"></a>Коллективная разработка с помощью Azure Dev Spaces

В этом руководстве описано, как использовать несколько пространств разработки для параллельной работы в разных средах разработки, когда отдельные проекты выполняются в отдельных пространствах в пределах одного кластера.

## <a name="call-a-service-running-in-a-separate-container"></a>Вызов службы, запущенной в отдельном контейнере

В этом разделе создается вторая служба `mywebapi`, которую будет вызывать `webfrontend`. Каждая служба будет выполняться в отдельных контейнерах. Затем вы выполните отладку в обоих контейнерах.

![Несколько контейнеров](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Скачайте пример кода для *mywebapi*.
Ради экономии времени давайте загрузим образец кода из репозитория GitHub. Перейдите к https://github.com/Azure/dev-spaces и выберите **Clone or download** (Клонировать или скачать), чтобы скачать репозиторий GitHub. Код для этого раздела находится в папке `samples/java/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Запуск *mywebapi*
1. Откройте папку `mywebapi` в *отдельном окне VS Code*.
1. Откройте **палитру команд** (с помощью меню **Вид | Палитра команд**), включите автоматическое завершение ввода и выберите эту команду: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.
1. Нажмите клавишу F5 и подождите, пока выполнится сборка и развертывание службы. Когда все будет готово, откроется панель отладки VS Code.
1. URL-адрес конечной точки будет выглядеть приблизительно так: http://localhost:\<portnumber\>. **Совет. В строке состояния VS Code отобразится URL-адрес, щелкнув по которому, можно перейти на соответствующий ресурс**. Кажется, что контейнер выполняется локально, но фактически он выполняется в нашей среде разработки в Azure. В адресе указано localhost, потому что для `mywebapi` не определены общедоступные конечные точки и доступ осуществляется только в пределах экземпляра Kubernetes. Для вашего удобства и упрощения взаимодействия с закрытой службой на локальном компьютере служба Azure Dev Spaces создает временный туннель SSH для контейнера, запущенного в Azure.
1. Когда `mywebapi` будет готово, откройте в браузере адрес localhost.
1. Если все шаги были успешными, вы должны увидеть ответ от службы `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Запрос из *webfrontend* к *mywebapi*
Давайте теперь напишем код в `webfrontend`, который отправляет запрос к `mywebapi`.
1. Перейдите в окно VS Code для `webfrontend`.
1. *Добавьте* следующие инструкции `import` под `package`.

   ```java
   import java.io.*;
   import java.net.*;
   ```
1. *Замените* код метода приветствия.

    ```java
    @RequestMapping(value = "/greeting", produces = "text/plain")
    public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
        URLConnection conn = new URL("http://mywebapi/").openConnection();
        conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
        {
            return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
        }
    }
    ```

В предыдущем примере код перенаправляет заголовок `azds-route-as` из входящего запроса в исходящий. Позже вы увидите, как это помогает повысить производительность при коллективной разработке.

### <a name="debug-across-multiple-services"></a>Отладка в нескольких службах
1. На этом этапе служба `mywebapi` по-прежнему должна выполняться с подключенным отладчиком. Если это не так, нажмите клавишу F5 в проекте `mywebapi`.
1. Установите точку останова в метод `index()` проекта `webapi`.
1. В проекте `webfrontend` установите точку останова до отправки запроса GET к `mywebapi` на строку, которая начинается с `try`.
1. Нажмите клавишу F5 в проекте `webfrontend` (или перезапустите отладчик, если он запущен).
1. Вызовите веб-приложение и пошагово выполните код в обеих службах.
1. В веб-приложении на странице About (Сведения) будет отображаться объединенное сообщение от двух служб: "Hello from webfrontend and Hello from mywebapi".

Все готово! Теперь у вас есть многоконтейнерное приложение, где каждый контейнер можно разрабатывать и развертывать отдельно.

## <a name="learn-about-team-development"></a>Сведения о коллективной разработке

[!INCLUDE [](../../includes/team-development-1.md)]

Давайте посмотрим на это в действии. Перейдите в окно VS Code для `mywebapi` и измените код в методе `String index()`, например:

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

[!INCLUDE [](../../includes/team-development-2.md)]

### <a name="well-done"></a>Все готово!
Вы выполнили руководство по началу работы. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка Azure Dev Spaces с помощью управляемого кластера Kubernetes в Azure.
> * итеративная разработка кода в контейнерах;
> * независимая разработка двух отдельных служб и вызов другой службы с помощью обнаружения службы DNS Kubernetes;
> * эффективная разработка и тестирование кода в среде командной работы.

Теперь, после ознакомления с Azure Dev Spaces, [предоставьте доступ к пространству разработки своему сотруднику](how-to/share-dev-spaces.md) и покажите ему, как просто работать вместе.

## <a name="clean-up"></a>Очистка
Чтобы полностью удалить экземпляр Azure Dev Spaces из кластера, включая все среды разработки и работающие службы, используйте команду `az aks remove-dev-spaces`. Не забывайте, что это действие необратимо. Позднее вы сможете снова добавить в кластер поддержку Azure Dev Spaces, но в этом случае вам придется начать работу с нуля. Прежние службы и среды не будут восстановлены.

С помощью приведенного ниже примера кода можно вывести список контроллеров Azure Dev Spaces в активной подписке, а затем удалить контроллер Azure Dev Spaces, связанный с кластером AKS myaks в группе ресурсов myaks-rg.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
