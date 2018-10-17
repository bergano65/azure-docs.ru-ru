---
title: Azure Terraform и Генератор программного модуля
description: Узнайте, как использовать Yeoman для создания базового шаблона Terraform.
services: terraform
ms.service: terraform
keywords: Terraform, DevOps, виртуальная машина, Azure
author: v-mavick
manager: jeconnoc
ms.author: v-mavick
ms.topic: tutorial
ms.date: 09/12/2018
ms.openlocfilehash: 513b123c44cf2cd37cf81a91e0d2da9599eb1fcd
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396356"
---
# <a name="create-a-terraform-base-template-using-yeoman"></a>Создание базового шаблона Terraform с использованием Yeoman

[Terraform](https://docs.microsoft.com/azure/terraform/
) позволяет легко создавать инфраструктуру на Azure. [Yeoman](http://yeoman.io/) значительно облегчает работу разработчика модуля при создании модулей Terraform, обеспечивая отличную структуру *лучшей методики*.

В этой статье вы узнаете, как использовать генератор модуля Yeoman для создания базового шаблона Terraform.

## <a name="prerequisites"></a>Предварительные требования

- Компьютер под управлением Windows 10, Linux или macOS 10.10 (или более поздней версии).
- **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
- **Visual Studio Code**. [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) используется для проверки файлов, созданных генератором Yeoman. Тем не менее можно использовать любой редактор кода по своему усмотрению.
- **Terraform**. Необходимо установить [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ), чтобы запускать модуль, созданной Yeoman.
- **Docker**. [Docker](https://www.docker.com/get-started) используется для запуска модуля, который создан с помощью генератора Yeoman. (При желании можно использовать Ruby вместо Docker для запуска образца модуля.)
- **Язык программирования Go**. Необходимо установить [Go](https://golang.org/) так, как тестовые случаи создаются с помощью Yeoman на языке Go.

>[!NOTE]
>Большая часть процедур, описанных в этом руководстве включают в себя операции командной строки. Описанные здесь шаги применяются для всех операционных систем и средств командной строки. В наших примерах используется PowerShell. Тем не менее можно использовать несколько вариантов, например Git Bash, командную строку Windows или команды командной строки Linux или macOS.

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
        >Укажите `y`. При выборе **n**, созданной код модуля будет поддерживать выполнение только в собственном режиме.

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

- **build**. Проверяет форматирование файла main.tf.
- **unit**. Сгенерированный скелет модуля не содержит код для модульного теста. Чтобы добавить здесь код, необходимо указать сценарий модульного теста.
- **e2e**. Запускает комплексный тест модуля.

### <a name="test"></a>Тестировать

- Тестовые случаи написаны на языке Go.
- Все коды в тесте — комплексные.
- Комплексные тесты используют Terraform для подготовки к работе всех элементов, определенных в разделе **Средство тестирования** и сравнивает выходные данные в коде **template_output.go** с помощью предопределенного среднего ожидаемого значения.
- **Gopkg.lock** и **Gopkg.toml**. Определяют зависимости. 

## <a name="test-the-module-using-docker"></a>Использование Docker для тестирования модуля

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

1. Укажите `bundle install`.

    Дождитесь сообщения **Завершение пакета**, а затем перейдите к следующему шагу.

1. Укажите `rake build`.

    ![Сборка с помощью Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="perform-the-end-to-end-test"></a>Пройдите комплексный тест

1. Укажите `rake e2e`.

1. Через несколько секунд появится сообщение **Проверка пройдена**.

    ![Проверка пройдена](media/terraform-vscode-module-generator/ymg-pass.png)

1. Введите `exit`, чтобы завершить комплексный тест.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Установка и использование расширения Visual Studio Code для Azure Terraform.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
