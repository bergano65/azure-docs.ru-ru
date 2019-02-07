---
title: Создание пространства разработки Kubernetes в облаке | Документация Майкрософт
titleSuffix: Azure Dev Spaces
author: stepro
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: stephpr
ms.date: 09/26/2018
ms.topic: quickstart
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: mmontwil
ms.openlocfilehash: 24bb5a044db813e1ee3cf2c9ff3e36a9b9ff86d7
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657065"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-java-and-vs-code"></a>Краткое руководство. Создание среды разработки Kubernetes с помощью Azure Dev Spaces (Java и VS Code)

Из этого руководства вы узнаете, как выполнить следующие задачи:

- Настройка Azure Dev Spaces с помощью управляемого кластера Kubernetes в Azure.
- итеративная разработка кода в контейнерах с помощью VS Code и командной строки;
- Отладка кода в среде разработки с помощью VS Code

> [!Note]
> **Если на каком-то этапе у вас возникли трудности**, см. статью [Устранение неполадок](troubleshooting.md) или оставьте комментарий на этой странице. Можно также ознакомиться с более подробным [руководством](get-started-java.md).

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/download).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) версии 2.0.43 или выше.
- Кластер Kubernetes, работающий на платформе Kubernetes версии 1.10.3 или выше, размещенный в регионе "Восточная часть США", "Восточная часть США 2", "Центральная часть США", "Западная часть США 2", "Западная Европа", "Юго-Восточная Азия", "Центральная Канада" или "Восточная Канада" и с включенным параметром **Маршрутизация HTTP для приложений**.

    ```cmd
    az group create --name MyResourceGroup --location <region>
    az aks create -g MyResourceGroup -n myAKS --location <region> --kubernetes-version 1.10.9 --enable-addons http_application_routing --generate-ssh-keys
    ```

## <a name="set-up-azure-dev-spaces"></a>Настройка Azure Dev Spaces

1. Настройка Dev Spaces в кластере AKS: `az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. Загрузите [расширение Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) для VS Code. Один раз щелкните "Установить" на странице расширения в Marketplace и еще раз — в VS Code.
1. Скачайте [расширение Java Debugger для Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) для VS Code. Один раз щелкните "Установить" на странице расширения в Marketplace и еще раз — в VS Code.

## <a name="build-and-run-code-in-kubernetes"></a>Сборка и запуск кода в Kubernetes

1. Загрузите пример кода из репозитория GitHub: [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. Перейдите в папку webfrontend: `cd dev-spaces/samples/java/getting-started/webfrontend`
1. Создайте ресурсы диаграмм Docker и Helm: `azds prep --public`
1. Создайте и запустите свой код в AKS. В окне терминала из **папки проекта webfrontend** выполните следующую команду: `azds up`
1. Просмотрите выходные данные консоли, чтобы найти сведения об URL-адресе, который был создан командой `up`. Он будет выглядеть следующим образом:

   ```output
    (pending registration) Service 'webfrontend' port 'http' will be available at <url>
    Service 'webfrontend' port 80 (TCP) is available at http://localhost:<port>
   ```

   При открытии этого URL-адреса в окне браузера должна начаться загрузка веб-приложения.

   > [!Note]
   > При первом запуске подготовка общедоступной записи DNS может занять несколько минут. Если не удается разрешить общедоступный URL-адрес, вместо него можно использовать альтернативный URL-адрес http://localhost:<portnumber>, который отображается в выходных данных консоли. Если вы используете URL-адрес localhost, может показаться, что контейнер выполняется локально, но на самом деле он выполняется в AKS. Для вашего удобства и упрощения взаимодействия со службой на локальном компьютере служба Azure Dev Spaces создает временный туннель SSH для контейнера, запущенного в Azure. Вы можете опробовать общедоступный URL-адрес позже, когда запись DNS будет готова.

### <a name="update-a-code-file"></a>Обновление файла кода

1. В окне терминала нажмите клавишу `Ctrl+C` (чтобы остановить `azds up`).
1. Откройте файл `src/main/java/com/ms/sample/webfrontend/Application.java` с кодом и измените текст приветствия: `return "Hello from webfrontend in Azure!";`.
1. Сохраните файл.
1. Запустите `azds up` в окне терминала.

Эта команда перестроит образ контейнера и повторно развернет диаграмму Helm. Чтобы увидеть изменения в работающем приложении, просто обновите страницу браузера.

Но есть еще один *более быстрый метод* разработки кода, который вы рассмотрите в следующем разделе.

## <a name="debug-a-container-in-kubernetes"></a>Отладка контейнера в Kubernetes

В этом разделе используйте VS Code для прямой отладки контейнера, работающего в Azure. Также вы узнаете, как быстрее вносить изменения, выполнять тестирование и запуск.

![](./media/common/edit-refresh-see.png)

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Инициализация ресурсов отладки с помощью расширения VS Code
Сначала необходимо настроить проект кода, чтобы редактор VS Code мог взаимодействовать с нашим пространством разработки в Azure. Расширение VS Code для Azure Dev Spaces содержит вспомогательную команду для настройки конфигурации отладки.

Откройте **палитру команд** (с помощью меню **Вид | Палитра команд**), включите автоматическое завершение ввода и выберите эту команду: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

В папку `.vscode` будет добавлена конфигурация отладки для Azure Dev Spaces.

![](./media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Выбор конфигурации отладки AZDS
1. Чтобы открыть представление отладки, щелкните значок "Отладка" на **панели действия** сбоку VS Code.
1. Выберите **Launch Java Program (AZDS)** (Запустить программу на Java (AZDS)) как активную конфигурацию отладки.

![](media/get-started-java/debug-configuration.png)

> [!Note]
> Если вы не видите никаких команд Azure Dev Spaces на палитре команд, убедитесь, что вы установили расширение VS Code для Azure Dev Spaces. Убедитесь, что рабочая область, которую вы открыли в VS Code, — это папка, содержащая файл azds.yaml.

### <a name="debug-the-container-in-kubernetes"></a>Отладка контейнера в Kubernetes
Нажмите клавишу **F5**, чтобы отладить свой код в Kubernetes.

Как и команда `up`, код синхронизируется со средой разработки, а контейнер создается и развертывается в Kubernetes. На этот раз, конечно, отладчик подключен к удаленному контейнеру.

> [!Tip]
> В строке состояния VS Code отобразится URL-адрес, щелкнув по которому, можно перейти на соответствующий ресурс.

Установите точку останова в файле кода на стороне сервера, например в функции `greeting()` в исходном файле `src/main/java/com/ms/sample/webfrontend/Application.java`. Обновление страницы браузера инициирует срабатывание точки останова.

У вас есть полный доступ к отладочной информации, как если бы код выполнялся локально, например к стеку вызовов, локальным переменным, информации об исключениях и т. д.

### <a name="edit-code-and-refresh"></a>Изменение и обновление кода
С помощью активного отладчика отредактируйте код. Например, измените приветствие в файле `src/main/java/com/ms/sample/webfrontend/Application.java`. 

```java
public String greeting()
{
    return "I'm debugging Java code in Azure!";
}
```

Сохраните файл и в области **действий отладки** нажмите кнопку **Обновить**. 

![](media/get-started-java/debug-action-refresh.png)

Вместо того, чтобы перестраивать и повторно развертывать новый образ контейнера при каждой правке кода, что часто занимает много времени, Azure Dev Spaces пошагово перекомпилирует код в существующем контейнере, чтобы ускорить цикл редактирования и отладки.

Обновите веб-приложение в браузере. Вы должны увидеть настраиваемое сообщение в пользовательском интерфейсе.

**Теперь у вас есть метод быстрой итерации кода и отладки непосредственно в Kubernetes**.

## <a name="next-steps"></a>Дополнительная информация

Узнайте, каким образом в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, работая с разными версиями или ветвями кода в разных средах.

> [!div class="nextstepaction"]
> [Работа с несколькими контейнерами и командной разработкой](multi-service-java.md)
