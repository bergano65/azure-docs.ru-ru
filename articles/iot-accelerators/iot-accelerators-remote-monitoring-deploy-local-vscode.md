---
title: Локальное развертывание решения для удаленного мониторинга — Visual Studio Code Azure | Документация Майкрософт
description: В этом руководстве показано, как развернуть акселератор решения для удаленного мониторинга на локальном компьютере с помощью Visual Studio Code для тестирования и разработки.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890954"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Локальное развертывание акселератора решения для удаленного мониторинга в Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

В этой статье показано, как развернуть акселератор решения для удаленного мониторинга на локальном компьютере в целях тестирования и разработки. Вы узнаете, как запустить микрослужбы в Visual Studio Code. Локальное развертывание микрослужб использует следующие облачные службы: центр Интернета вещей, Cosmos DB, Azure Stream Analytics и аналитика временных рядов Azure.

## <a name="prerequisites"></a>предварительным требованиям

Для развертывания служб Azure, используемых акселератором решения для удаленного мониторинга, требуется активная подписка Azure.

Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Настройки компьютера

Для завершения локального развертывания необходимо установить следующие средства на локальный компьютер разработчика:

* [Git.](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [расширение C# для VS Code](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js версии 8](https://nodejs.org/). Это программное обеспечение является необходимым компонентом для интерфейса командной строки PCS, который используется в сценариях для создания ресурсов Azure. Не используйте Node.js версии 10

> [!NOTE]
> Служба Visual Studio Code доступна для Windows, Mac и Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Запуск микрослужб

В этом разделе вы запустите микрослужбы для удаленного мониторинга. Веб-интерфейс запускается в собственной среде, служба "Симулятор устройств" — в Docker, а микрослужбы — в Visual Studio Code.

### <a name="build-the-code"></a>Сборка кода

Перейдите к azure-iot-pcs-remote-monitor-dotnet\services в командной строке и выполните следующие команды для сборки кода.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Разверните все микрослужбы на локальном компьютере.

Ниже описано, как запустить микрослужбы удаленного мониторинга в Visual Studio Code.

1. Запустите Visual Studio Code.
1. В VS Code откройте папку **Azure-IOT-PCS-Remote-Monitoring-DotNet** .
1. Создайте новую папку с именем **. vscode** в папке **Azure-IOT-PCS-Remote-Monitoring-DotNet** .
1. Скопируйте файлы **Launch. JSON** и **tasks. JSON** из сервицес\скриптс\локал\лаунч\идесеттингс\вскоде в только что созданную папку **. vscode** .
1. Откройте **панель "Отладка** " в VS Code и запустите конфигурацию " **запустить все микрослужбы** ". Эта конфигурация запускает микрослужбу симулятора устройства в Docker и запускает другие микрослужбы в отладчике.

Выходные данные **выполнения ALL микрослужбы** в консоль отладки выглядят следующим образом:

[![ное развертывание — локальные микрослужбы](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Запуск веб-интерфейса

На этом этапе вы запустите веб-интерфейс. Перейдите к папке **azure-iot-pcs-remote-monitoring-dotnet\webui** в вашей локальной копии и выполните следующие команды.

```cmd
npm install
npm start
```

Когда начало завершится, в браузере отобразится страница **http:\//ЛОКАЛХОСТ: 3000/Dashboard**. На этой странице могут возникать ошибки. Чтобы просмотреть приложение без ошибок, выполните следующие действия.

### <a name="configure-and-run-nginx"></a>Настройка и запуск NGINX

Настройте обратный прокси-сервер, чтобы связать веб-приложение и микрослужбы, запущенные на локальном компьютере:

* Скопируйте файл **nginx.conf** из папки **webui\scripts\localhost** в каталог установки **nginx\conf**.
* Запустите **nginx**.

Дополнительные сведения о запуске **nginx** см. в разделе [nginx для Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Подключение к панели мониторинга

Чтобы получить доступ к панели мониторинга решения для удаленного мониторинга, перейдите в браузере http:\//ЛОКАЛХОСТ: 9000.

## <a name="clean-up"></a>Очистка

Чтобы избежать ненужных расходов, после завершения тестирования удалите облачные службы из подписки Azure. Чтобы удалить службы, перейдите на [портал Azure](https://ms.portal.azure.com) и удалите группу ресурсов, созданную при запуске сценария **start.cmd**.

Также можно удалить локальную копию репозитория удаленного мониторинга, созданную при клонировании исходного кода из GitHub.

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы развернули решение для удаленного мониторинга, ознакомьтесь со статьей [Краткое руководство. Использование облачного решения для удаленного мониторинга](quickstart-remote-monitoring-deploy.md).
