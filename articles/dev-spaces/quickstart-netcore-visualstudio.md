---
title: 'Отладка и итерация в Kubernetes: Visual Studio и .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: В этом кратком руководстве описано, как использовать Azure Dev Spaces и Visual Studio для отладки и быстрого выполнения итерации приложения .NET Core в службе Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 909e4638b3b0919919320a09cbfa0e8d9ac92f2e
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995944"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Краткое руководство. Отладка и итерация в Kubernetes: Использование Visual Studio и .NET Core в Azure Dev Spaces

Из этого руководства вы узнаете, как выполнить следующие задачи:

- Настройка Azure Dev Spaces с помощью управляемого кластера Kubernetes в Azure.
- итеративно разрабатывать код в контейнерах с помощью Visual Studio.
- Отладка кода, запущенного в кластере, с помощью Visual Studio.

Azure Dev Spaces также позволяет выполнять отладку и итерацию с помощью:
- [Java и Visual Studio Code](quickstart-java.md);
- [Node.js и Visual Studio Code](quickstart-nodejs.md);
- [.NET Core и Visual Studio Code](quickstart-netcore.md);

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/free).
- Visual Studio 2019 в Windows с установленной рабочей нагрузкой для разработки в Azure. Если среда Visual Studio не установлена, скачайте ее [отсюда](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- [Установленный Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Вам нужно создать кластер AKS в [поддерживаемом регионе][supported-regions]. Следующие команды создают группу ресурсов *MyResourceGroup* и кластер AKS *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Включение Azure Dev Spaces в кластере AKS

С помощью команды `use-dev-spaces` включите Dev Spaces в кластере AKS и следуйте инструкциям на экране. Следующая команда включает Dev Spaces в кластере *MyAKS* в группе *MyResourceGroup* и создает пространство разработки *по умолчанию*.

> [!NOTE]
> Команда `use-dev-spaces` также установит интерфейс командной строки Azure Dev Spaces, если он еще не установлен. Интерфейс командной строки Azure Dev Spaces невозможно установить в Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="create-a-new-aspnet-web-app"></a>Создание веб-приложения ASP.NET

1. Запустите Visual Studio.
1. Создайте новый проект.
1. Выберите *Веб-приложение ASP.NET Core* и щелкните *Далее*.
1. Присвойте проекту имя *webfrontend* и нажмите кнопку *Создать*.
1. При появлении запроса выберите *Веб-приложение (модель-представление-контроллер)* в качестве шаблона.
1. Выберите *.NET Core* и *ASP.NET Core 2.1* вверху.
1. Нажмите кнопку *Создать*.

## <a name="connect-your-project-to-your-dev-space"></a>Подключение проекта к пространству разработки

В проекте в раскрывающемся списке параметров запуска выберите **Azure Dev Spaces**, как показано ниже.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

В диалоговом окне Azure Dev Spaces выберите соответствующие значения в полях *Подписка* и *Кластер AKS*. Параметр *Space* (Пространство) оставьте значение *по умолчанию* и установите флажок *Общедоступные*. Нажмите кнопку *ОК*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

При этом ваша служба будет развернута в пространстве разработки *по умолчанию* с общедоступным URL-адресом. Если выбран кластер, который еще не был настроен для работы с Azure Dev Spaces, вы увидите сообщение с вопросом, хотите ли вы настроить его. Нажмите кнопку *ОК*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Общедоступный URL-адрес для службы, запущенной в пространстве разработки *по умолчанию*, отображается в окне *Вывод*.

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

В нашем примере используется такой общедоступный URL-адрес: `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`. 

Выберите **Отладка** затем **Начать отладку**. Через несколько секунд служба запустится, и Visual Studio откроет браузер с общедоступным URL-адресом службы. Если браузер не открывается автоматически, перейдите в браузер по общедоступному URL-адресу службы, запущенной в вашем пространстве разработки, и проверьте ее работу.

Возможно, из-за этого процесса отключился общий доступ к вашей службе. Чтобы включить общий доступ, обновите [значение входящего трафика в *values.yaml*][ingress-update].

## <a name="update-code"></a>Обновление кода

Если пространство разработки еще подключено к Visual Studio, нажмите кнопку остановки. В файле `Controllers/HomeController.cs` замените строку 20 такими данными:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Сохраните изменения и выберите **Отладка** затем **Начать отладку**. Через несколько секунд служба запустится, и Visual Studio откроет браузер с общедоступным URL-адресом службы. Если браузер не открывается автоматически, перейдите в браузер по общедоступному URL-адресу службы и щелкните *О программе*. При этом отобразится обновленное сообщение.

Вместо того, чтобы повторно выполнять сборку и развертывание нового образа контейнера при каждой правке кода, Azure Dev Spaces пошагово перекомпилирует код в существующем контейнере для ускорения цикла редактирования и отладки.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Настройка и использование точек останова для отладки

Если пространство разработки еще подключено к Visual Studio, нажмите кнопку остановки. Откройте `Controllers/HomeController.cs` и щелкните строку 20, чтобы расположить в ней курсор. Чтобы задать точку останова, нажмите клавишу *F9* или щелкните *Отладка* и *Переключить точку останова*. Чтобы запустить службу в режиме отладки в пространстве разработки, нажмите клавишу *F5* или щелкните *Отладка* и *Начать отладку*.

Откройте службу в браузере. Вы увидите, что сообщение не отображается. Вернувшись в Visual Studio, вы увидите, что строка 20 выделена. Заданная вами точка останова приостановила выполнение службы на строке 20. Чтобы возобновить работу службы, нажмите клавишу *F5* или щелкните *Отладка*, а затем *Продолжить*. Вернитесь в браузер. Вы увидите, что сообщение теперь отображается.

Во время выполнения службы в Kubernetes с присоединенным отладчиком у вас есть полный доступ к отладочным сведениям, включая стек вызовов, локальные переменные и данные об исключениях.

Удалите точку останова, поместив курсор в строке 20 в `Controllers/HomeController.cs` и нажав клавишу *F9*.

## <a name="clean-up-your-azure-resources"></a>Очистка ресурсов Azure

Перейдите к группе ресурсов на портале Azure и щелкните *Удалить группу ресурсов*. Вы также можете воспользоваться командой [az aks delete](/cli/azure/aks#az-aks-delete).

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Работа с несколькими контейнерами и командной разработкой](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
