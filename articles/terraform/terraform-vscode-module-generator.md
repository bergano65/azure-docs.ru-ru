---
title: Создание базового шаблона Terraform в Azure с использованием Yeoman
description: Здесь вы узнаете, как создать базовый шаблон Terraform в Azure с использованием Yeoman.
services: terraform
ms.service: azure
keywords: Terraform, DevOps, виртуальная машина, Azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/08/2018
ms.openlocfilehash: 8405624831c572f194fa0afaad2f0caa40bcf9ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58001281"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Создание базового шаблона Terraform в Azure с использованием Yeoman

[Terraform](https://docs.microsoft.com/azure/terraform/
) позволяет легко создавать инфраструктуру на Azure. [Yeoman](https://yeoman.io/) значительно облегчает работу разработчика модуля при создании модулей Terraform, обеспечивая отличную структуру *лучшей методики*.

В этой статье вы узнаете, как использовать генератор модуля Yeoman для создания базового шаблона Terraform. Затем вы узнаете, как протестировать новый шаблон Terraform, используя два разных метода:

- Запуск модуля Terraform с помощью файла Docker, созданного в этой статье.
- Запуск модуля Terraform в собственном режиме в Azure Cloud Shell.

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure.** Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/) , прежде чем начинать работу.
- **Visual Studio Code**. [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) используется для проверки файлов, созданных генератором Yeoman. Тем не менее можно использовать любой редактор кода по своему усмотрению.
- **Terraform**. Необходимо установить [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ), чтобы запускать модуль, созданный с помощью Yeoman.
- **Docker**. [Docker](https://www.docker.com/get-started) используется для запуска модуля, который создан с помощью генератора Yeoman. (При желании можно использовать Ruby вместо Docker для запуска образца модуля.)
- **Язык программирования Go**. Необходимо установить [Go](https://golang.org/), так как тестовые случаи создаются с помощью Yeoman на языке Go.

>[!NOTE]
>Большая часть процедур, описанных в этом руководстве включают в себя операции командной строки. Описанные здесь шаги применяются для всех операционных систем и средств командной строки. В наших примерах мы используем PowerShell для локальной среды и Git Bash для среды Cloud Shell.

## <a name="prepare-your-environment"></a>Подготовка среды

### <a name="install-nodejs"></a>Установка Node.js

Чтобы использовать Terraform в Cloud Shell, вам нужно [установить Node.js](https://nodejs.org/en/download/) версии 6.0 или более поздней.

>[!NOTE]
>Чтобы убедиться, что Node.js установлен, откройте окно терминала и введите команду `node --version`.

### <a name="install-yeoman"></a>установка Yeoman;

Из командной строки введите `npm install -g yo`.

![установка Yeoman;](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Установка шаблона Yeoman для модуля Terraform

Из командной строки введите `npm install -g generator-az-terra-module`.

![Установить generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>Чтобы убедиться, что Yeoman установлено, в окне терминала введите `yo --version`.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Создайте пустую папку для хранения модуля созданного Yeoman

Шаблон Yeoman создает файлы в **текущем каталоге**. По этому вам необходимо создать каталог.

>[!Note]
>Этот пустой каталог необходимо поместить под $GOPATH/src. Выполнить эту задачу можно [здесь](https://github.com/golang/go/wiki/SettingGOPATH).

Из командной строки введите следующее.

1. Перейдите к родительскому каталогу, который будет содержать новый, пустой каталог, которой будет создан.
1. Укажите `mkdir <new-directory-name>`.

    >[!NOTE]
    >Замените <new-directory-name> на имя созданного каталога. Например, новый каталог с именем `GeneratorDocSample`.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Введите `cd <new directory's name>`, чтобы перейти в созданный каталог, а затем нажмите кнопку **ВВОД**.

    ![Перейдите в новый каталог.](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >Введите `ls`, чтобы убедиться, что этот каталог — пустой. В результирующих выходных данных этой команды не должно быть списка файлов.

## <a name="create-a-base-module-template"></a>Создание шаблона базового модуля

Из командной строки введите следующее.

1. Укажите `yo az-terra-module`.

1. Чтобы предоставить следующие сведения, следуйте инструкциям на экране.

    - *Имя проекта модуля Terraform*

        ![Имя проекта](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >Например, `doc-sample-module`.

    - *Использовать файл образа Docker?*

        ![Использовать файл образа Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Укажите `y`. При выборе **n** созданный код модуля будет поддерживать выполнение только в собственном режиме.

3. Для просмотра созданных конечных файлов, введите `ls`.

    ![Созданный список файлов](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Просмотр созданного кода модуля

1. Запустите Visual Studio Code.

1. На панели меню выберите **Файл > Открыть папку**, а затем найдите и выберите созданную папку.

    ![Visual Studio Code.](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Давайте рассмотрим некоторые файлы, созданные с помощью генератора модуля Yeoman.

>[!Note]
>В этой статье мы используем файлы main.tf, variables.tf и outputs.tf, созданные при помощи генератора модуля Yeoman. Тем не менее при создании собственных модулей, необходимо совершить правку над этими файлами, чтобы вместить функциональные возможности вашего модуля Terraform. Для более подробного обсуждения этих файлов и их использования см. в разделе [Terratest в модулях Terraform.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

### <a name="maintf"></a>main.tf

Определяет модуль с именем *random-shuffle*. Входные данные — *string_list*. Выходные данные — это количество перестановок.

### <a name="variablestf"></a>variables.tf

Определяет входные и выходные переменные, которые использует модуль.

### <a name="outputstf"></a>outputs.tf

Определяет выходные данные модуля. **random_shuffle** — встроенный модуль Terraform, который возвращает это значение.

### <a name="rakefile"></a>Rakefile

Определяет шаги сборки. А именно:

- **build**: проверяет форматирование файла main.tf.
- **unit**: сгенерированная структура модуля не содержит код для модульного теста. Чтобы добавить здесь код, необходимо указать сценарий модульного теста.
- **e2e**: запускает комплексный тест модуля.

### <a name="test"></a>Тестировать

- Тестовые случаи написаны на языке Go.
- Все коды в тесте — комплексные.
- Комплексные тесты используют Terraform для подготовки к работе всех элементов, определенных в разделе **Средство тестирования** и сравнивает выходные данные в коде **template_output.go** с помощью предопределенного среднего ожидаемого значения.
- **Gopkg.lock** и **Gopkg.toml**: отслеживание зависимостей. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Тестирование нового модуля Terraform с помощью файла Docker

>[!NOTE]
>Как пример мы запускаем модуль как локальный фактически не имеем дело с Azure.

### <a name="confirm-docker-is-installed-and-running"></a>Убедитесь, что Docker установлен и запущен

Из командной строки введите `docker version`.

![Версия Docker](media/terraform-vscode-module-generator/ymg-docker-version.png)

Полученный результат подтверждает, что платформа Docker установлена.

Введите `docker info`, чтобы убедиться, что Docker действительно запущен.

![Сведения о Docker](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Настройка контейнера Docker

1. Из командной строки введите

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    Будет отображаться сообщение **Успешно создан**

    ![Успешно создан](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Из командной строки введите `docker image ls`

    В списке появится только что созданный модуль *terra-mod-example*.

    ![Результаты репозитория](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >Имя модуля, *terra-mod-example*, было указано в команде, которую вы задавали выше на предыдущем шаге.

1. Укажите `docker run -it terra-mod-example /bin/sh`.

    Теперь вы работает в Docker и можно вывести список файла, введя `ls`.

    ![Получение списков файла Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Сборка модуля

1. Укажите `bundle install`.

    Дождитесь сообщения **Завершение пакета**, а затем перейдите к следующему шагу.

1. Укажите `rake build`.

    ![Сборка с помощью Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Запуск комплексного теста

1. Укажите `rake e2e`.

1. Через несколько секунд появится сообщение **Проверка пройдена**.

    ![Проверка пройдена](media/terraform-vscode-module-generator/ymg-pass.png)

1. Введите `exit`, чтобы завершить комплексный тест и выйти из среды Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Создание и тестирование модуля в Cloud Shell с использованием генератора Yeoman

В предыдущем разделе вы узнали, как тестировать модуль Terraform с помощью файла Docker. В этом разделе вы создадите и протестируете модуль в Cloud Shell, используя генератор Yeoman.

Использование Cloud Shell вместо файла Docker значительно упрощает процесс. При использовании Cloud Shell:

- Вам не нужно устанавливать Node.js.
- Вам не нужно устанавливать Yeoman.
- Вам не нужно устанавливать Terraform.

Все эти элементы предварительно установлены в Cloud Shell.

### <a name="start-a-cloud-shell-session"></a>Запуск сеанса Cloud Shell

1. Запустите сеанс Azure Cloud Shell через портал [Azure](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com) или [мобильное приложение Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Откроется страница **приветствия Azure Cloud Shell**. Выберите **Bash (Linux)**. (Power Shell не поддерживается.)

    ![Страница приветствия Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >В нашем примере выбран вариант Bash (Linux).

1. Если вы еще не настроили учетную запись хранения Azure, откроется следующий экран. Щелкните **Создать хранилище**.

    ![Подключенное хранилище отсутствует](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell запускается в оболочке, которую вы выбрали ранее, и отображает сведения о только что созданном облачном диске.

    ![Ваш облачный диск успешно создан](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Подготовка папки для хранения модуля Terraform

1. На этом этапе служба Cloud Shell уже настроила для вас расположение GOPATH в переменных среды. Введите `go env`, чтобы увидеть путь.

1. Создайте папку $GOPATH, если она еще не существует. Укажите `mkdir ~/go`.

1. Создайте папку в папке $GOPATH: Укажите `mkdir ~/go/src`. Эта папка будет использоваться для хранения и упорядочения различных папок проекта, которые вы создадите, например папки <имя_модуля>, которую мы создадим на следующем шаге.

1. Создайте папку для хранения вашего модуля Terraform: Укажите `mkdir ~/go/src/<your-module-name>`.

    >[!NOTE]
    >В этом примере мы выбрали `my-module-name` в качестве имени папки.

1. Перейдите в папку модуля: Введите `cd ~/go/src/<your-module-name>`

### <a name="create-and-test-your-terraform-module"></a>Создание и тестирование модуля Terraform

1. Введите `yo az-terra-module` и следуйте указаниям мастера.

    >[!NOTE]
    >Когда появится вопрос о необходимости создания файлов Docker, вы можете ввести `N`.

1. Введите `bundle install`, чтобы установить зависимости.

    Дождитесь сообщения **Завершение пакета**, а затем перейдите к следующему шагу.

1. Введите `rake build`, чтобы создать модуль.

    ![Сборка с помощью Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Введите `rake e2e`, чтобы запустить комплексный тест.

1. Через несколько секунд появится сообщение **Проверка пройдена**.

    ![Проверка пройдена](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Установка и использование расширения Visual Studio Code для Azure Terraform.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
