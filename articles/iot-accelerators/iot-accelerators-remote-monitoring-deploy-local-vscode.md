---
title: Локальное развертывание решения для удаленного мониторинга (Visual Studio Code) в Azure | Документация Майкрософт
description: В этом руководстве показано, как развернуть акселератор решения для удаленного мониторинга на локальном компьютере с помощью Visual Studio Code для тестирования и разработки.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 946f815cecea4cc172fac35c0b260d795317e6e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61446176"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Локальное развертывание акселератора решения для удаленного мониторинга в Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

В этой статье показано, как развернуть акселератор решения для удаленного мониторинга на локальном компьютере в целях тестирования и разработки. Вы узнаете, как запустить микрослужбы в Visual Studio Code. При локальном развертывании микрослужб используются следующие облачные службы: Центр Интернета вещей, Cosmos DB, Azure Streaming Analytics и Аналитика временных рядов Azure.

## <a name="prerequisites"></a>Технические условия

Для развертывания служб Azure, используемых акселератором решения для удаленного мониторинга, требуется активная подписка Azure.

Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Настройки компьютера

Для завершения локального развертывания необходимо установить следующие средства на локальный компьютер разработчика:

* [Git](https://git-scm.com/)
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

Ниже показано, как запускать микрослужбы удаленного мониторинга в Visual Studio 2017:

1. Запустите Visual Studio Code.
1. Откройте модуль **azure-iot-pcs-remote-monitoring-dotnet** из локальной копии в VS Code.
1. Скопируйте файлы **launch.json** и **tasks.json** из scripts\local\launch\idesettings\vscode\. Создайте новую папку **azure-iot-pcs-remote-monitoring-dotnet\.vscode** и вставьте в него файлы.
1. Откройте панель отладки в VS Code и выполните конфигурацию **Run all microservices** (Запуск всех микрослужб). Эта конфигурация запускает микрослужбу симулятора устройства в Docker и запускает другие микрослужбы в отладчике.

Например, выходные данные для службы **Проверка подлинности** на **консоли отладки** выглядят следующим образом:

[![Deploy-Local-Auth-Service](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Запуск веб-интерфейса

На этом этапе вы запустите веб-интерфейс. Перейдите к папке **azure-iot-pcs-remote-monitoring-dotnet\webui** в вашей локальной копии и выполните следующие команды.

```cmd
npm install
npm start
```

По завершении запуска браузер отображает страницу **http:\//localhost:3000 / панели мониторинга**. На этой странице могут возникать ошибки. Чтобы просмотреть приложение без ошибок, выполните следующие действия.

### <a name="configure-and-run-nginx"></a>Настройка и запуск NGINX

Настройте обратный прокси-сервер, чтобы связать веб-приложение и микрослужбы, запущенные на локальном компьютере:

* Скопируйте файл **nginx.conf** из папки **webui\scripts\localhost** в каталог установки **nginx\conf**.
* Запустите **nginx**.

Дополнительные сведения о запуске **nginx** см. в разделе [nginx для Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Подключение к панели мониторинга

Чтобы получить доступ к панели мониторинга решения для удаленного мониторинга, перейдите к http:\//localhost:9000 в браузере.

## <a name="clean-up"></a>Очистка

Чтобы избежать ненужных расходов, после завершения тестирования удалите облачные службы из подписки Azure. Чтобы удалить службы, перейдите на [портал Azure](https://ms.portal.azure.com) и удалите группу ресурсов, созданную при запуске сценария **start.cmd**.

Также можно удалить локальную копию репозитория удаленного мониторинга, созданную при клонировании исходного кода из GitHub.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы развернули решение для удаленного мониторинга, ознакомьтесь со статьей [Краткое руководство. Использование облачного решения для удаленного мониторинга](quickstart-remote-monitoring-deploy.md).
