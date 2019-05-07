---
title: Локальное развертывание решения для удаленного мониторинга (через IntelliJ IDE) в Azure | Документация Майкрософт
description: В этом руководстве показано, как развернуть акселератор решения для удаленного мониторинга на локальном компьютере с использованием IntelliJ в целях тестирования и разработки.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 209a91453875531e81ddbcead8c1ed2fb309a0a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147451"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Локальное развертывание акселератора решения для удаленного мониторинга IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

В этой статье показано, как развернуть акселератор решения для удаленного мониторинга на локальном компьютере в целях тестирования и разработки. Вы узнаете, как запустить микрослужбы в IntelliJ. При локальном развертывании микрослужб используются следующие облачные службы: Службы центра Интернета вещей, Cosmos DB, Azure Streaming Analytics и Azure Time Series Insights в облаке.

Если вы хотите запустить акселератор решения для удаленного мониторинга в Docker на локальном компьютере, см. статью [Deploy the Remote Monitoring solution accelerator locally — Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md) (Локальное развертывание акселератора решения для удаленного мониторинга в Docker).

## <a name="prerequisites"></a>Технические условия

Для развертывания служб Azure, используемых акселератором решения для удаленного мониторинга, требуется активная подписка Azure.

Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Настройки компьютера

Для завершения локального развертывания необходимо установить следующие средства на локальный компьютер разработчика:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html);
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/);
* [подключаемый модуль IntelliJ Scala](https://plugins.jetbrains.com/plugin/1347-scala);
* [подключаемый модуль IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt);
* [подключаемый модуль IntelliJ SBT Executor](https://plugins.jetbrains.com/plugin/7247-sbt-executor);
* [Nginx](https://nginx.org/en/download.html)
* [Node.js версии 8](https://nodejs.org/). Это программное обеспечение является необходимым компонентом для интерфейса командной строки PCS, который используется в сценариях для создания ресурсов Azure. Не используйте Node.js версии 10.

> [!NOTE]
> Служба IntelliJ IDE доступна для Windows и Mac.

[!INCLUDE [iot-accelerators-local-setup-java](../../includes/iot-accelerators-local-setup-java.md)]

## <a name="run-the-microservices"></a>Запуск микрослужб

В этом разделе вы запустите микрослужбы для удаленного мониторинга. Веб-интерфейс запускается в собственной среде, службы "Симулятор устройств", "Аутентификация" и "Диспетчер ASA" — в Docker, а микрослужбы — в IntelliJ.

### <a name="run-the-device-simulation-service"></a>Запустите службу моделирования устройств

Чтобы иметь доступ к переменным средам, установленным сценарием **start.cmd**, откройте окно командной строки в предыдущем разделе.

Выполните следующую команду, чтобы запустить контейнер Docker для службы моделирования устройств. Служба моделирует устройства для решения для удаленного мониторинга.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Запуск службы "Аутентификация"

Откройте новое окно командной строки и выполните следующую команду, чтобы запустить контейнер Docker для службы "Аутентификация". Служба позволяет управлять пользователями с правами на доступ к решениям Интернета вещей Azure.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>Запуск службы "Диспетчер ASA"

Откройте новое окно командной строки и выполните следующую команду, чтобы запустить контейнер Docker для службы "Диспетчер ASA". Эта служба позволяет управлять заданиями Azure Stream Analytics (ASA), включая настройку конфигурации, запуск, остановку и мониторинг их состояния.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Разверните все микрослужбы на локальном компьютере.

Ниже показано, как запускать микрослужбы удаленного мониторинга в IntelliJ:

#### <a name="import-project"></a>Импорт проекта

1. Запустите IntelliJ IDE.
1. Выберите **Import Project** (Импорт проекта) и щелкните **azure-iot-pcs-remote-monitoring-java\services\build.sbt**.

#### <a name="create-run-configurations"></a>Создание конфигураций запуска

1. Выберите **Run > Edit Configurations** (Запуск > Изменить конфигурации).
1. Выберите **Add New Configuration > sbt task** (Добавить новую конфигурацию > Задача SBT). 
1. Укажите **имя** и название **задач** при запуске. 
1. Выберите **рабочий каталог** в зависимости от службы, которую необходимо запустить.
1. Щелкните **Apply > OK** (Применить > ОК), чтобы сохранить изменения.
1. Создайте конфигурации запуска для следующих служб:
    * WebService (services\config);
    * WebService (services\device-telemetry);
    * WebService (services\iothub-manager);
    * WebService (services\storage-adapter).

В качестве примера на изображении ниже показано добавление конфигурации для службы.

[![Добавление конфигурации](./media/deploy-locally-intelliJ/run-configurations.png)](./media/deploy-locally-intelliJ/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Создание комплексной конфигурации

1. Для запуска всех служб выберите **Add new Configuration > Compound** (Добавить новую конфигурацию > Комплексная).
1. Укажите **имя** и **добавьте задачи SBT**.
1. Щелкните **Apply > OK** (Применить > ОК), чтобы сохранить изменения.

В качестве примера на изображении ниже показано добавление всех задач SBT в одну конфигурацию.


[![Добавление всех служб](./media/deploy-locally-intelliJ/all-services.png)](./media/deploy-locally-intelliJ/all-services.png#lightbox)



1. Щелкните **Run** (Запуск), чтобы создать и запустить веб-службы на локальном компьютере.

Для каждой веб-службы откроется командная строка и окно веб-браузера. В командной строке вы увидите выходные данные из запущенной службы, а окно браузера позволяет отслеживать состояние. Не закрывайте окна командной строки или веб-страницы, так как работа веб-службы прекратится.


Для получения доступа к состоянию служб вы можете перейти по следующим URL-адресам:
* Диспетчер Центра Интернета вещей: [http://localhost:9002/v1/status](http://localhost:9002/v1/status).
* Телеметрия устройства: [http://localhost:9004/v1/status](http://localhost:9004/v1/status).
* Конфигурация: [http://localhost:9005/v1/status](http://localhost:9005/v1/status).
* Адаптер хранения: [http://localhost:9022/v1/status](http://localhost:9022/v1/status).


### <a name="start-the-stream-analytics-job"></a>Запуск задания Stream Analytics

Чтобы запустить задание Stream Analytics, выполните следующие действия:

1. Перейдите на [портал Azure](https://portal.azure.com).
1. Перейдите к **группе ресурсов**, созданной для решения. Имя группы ресурсов совпадает с именем решения, выбранного при запуске сценария **start.cmd**.
1. В списке ресурсов щелкните **Задание Stream Analytics**.
1. На странице **Обзор** задания Stream Analytics нажмите кнопку **Запуск**. Затем, чтобы немедленно начать задание, щелкните **Пуск**.

### <a name="run-the-web-ui"></a>Запуск веб-интерфейса

На этом этапе вы запустите веб-интерфейс. Чтобы иметь доступ к переменным среды, установленным сценарием **start.cmd**, откройте окно командной строки. Перейдите в папку **webui** в локальной копии репозитория и выполните следующие команды:

```cmd
npm install
npm start
```

По завершении запуска браузер отображает страницу **http:\//localhost:3000 / панели мониторинга**. На этой странице могут возникать ошибки. Чтобы просмотреть приложение без ошибок, выполните следующие действия.

### <a name="configure-and-run-nginx"></a>Настройка и запуск NGINX

Настройте обратный прокси-сервер, чтобы связать веб-приложение и микрослужбы, запущенные на локальном компьютере:

* Скопируйте файл **nginx.conf** из папки **webui\scripts\localhost** в локальной копии репозитория в каталог установки **nginx\conf**.
* Запустите **nginx**.

Дополнительные сведения о запуске **nginx** см. в разделе [nginx для Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Подключение к панели мониторинга

Чтобы получить доступ к панели мониторинга решения для удаленного мониторинга, перейдите к http:\//localhost:9000 в браузере.

## <a name="clean-up"></a>Очистка

Чтобы избежать ненужных расходов, после завершения тестирования удалите облачные службы из подписки Azure. Чтобы удалить службы, перейдите на [портал Azure](https://ms.portal.azure.com) и удалите группу ресурсов, созданную при запуске сценария **start.cmd**.

Также можно удалить локальную копию репозитория удаленного мониторинга, созданную при клонировании исходного кода из GitHub.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы развернули решение для удаленного мониторинга, ознакомьтесь со статьей [Краткое руководство. Использование облачного решения для удаленного мониторинга](quickstart-remote-monitoring-deploy.md).
