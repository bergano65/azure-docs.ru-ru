---
title: Руководство по Запуск иммерсивного средства чтения с помощью Python
titleSuffix: Azure Cognitive Services
description: В этом учебнике описано, как создать приложение Python, которое запускает иммерсивное средство чтения.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/02/2019
ms.author: dylankil
ms.openlocfilehash: 5e33108c9fc674abaf980a1272cca31aa21cffff
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991131"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Руководство по запуска иммерсивного средства чтения с помощью тестового проекта Python

В [обзорной статье](./overview.md) вы узнали о том, что представляет собой иммерсивное средство чтения и каким образом в нем реализованы проверенные методы, улучшающие понимание прочитанного для начинающих, тех, кто изучает язык, и учащихся с особыми потребностями. В этом учебнике описано, как создать веб-приложение Python, которое запускает иммерсивное средство чтения. Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание веб-приложения Python с поддержкой PIP, Flask, Jinja и virtualenv на основе примера проекта;
> * Получение маркера доступа
> * Запуск иммерсивного средства чтения с примером содержимого

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Ресурс "Иммерсивное средство чтения", настроенный для проверки подлинности Azure Active Directory (Azure AD). Инструкции по настройке см. [здесь](./azure-active-directory-authentication.md). Вам потребуются некоторые значения, созданные здесь при настройке свойств среды. Сохраните результаты своего сеанса в текстовом файле для использования в будущем.
* [Git](https://git-scm.com/)
* [Пакет SDK иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) и [pip](https://docs.python.org/3/installing/index.html) Начиная с Python 3.4, pip включается по умолчанию с двоичными установщиками Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) и [virtualenvwrapper-win для Windows](https://pypi.org/project/virtualenvwrapper-win/) или [virtualenvwrapper для OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [модуль запросов](https://pypi.org/project/requests/2.7.0/)
* Интегрированная среда разработки, такая как [Visual Studio Code](https://code.visualstudio.com/).

## <a name="acquire-an-azure-ad-authentication-token"></a>Получение маркера проверки подлинности Azure AD

Напишите API серверной части, чтобы получать токен проверки подлинности AAD.

Для этой части потребуются некоторые значения из описанного выше предварительного требования конфигурации проверки подлинности Azure AD. Вернитесь к текстовому файлу, который вы сохранили в этом сеансе.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Получив эти значения, создайте новый файл с именем _.env_ и вставьте в него следующий код, указав значения пользовательских свойств сверху. Заменит файл _.env_. из примера приложения созданным вами файлом.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Не забудьте зафиксировать этот файл в системе управления версиями, так как он содержит секреты, для которых не следует предоставлять открытый доступ.

Конечная точка API **getimmersivereadertoken** должна быть защищена с помощью формы проверки подлинности (например, [OAuth](https://oauth.net/2/)), чтобы предотвратить получение неавторизованными пользователями токенов для использования относительно службы "Иммерсивное средство чтения" и выставления счетов. Эти сведения выходят за рамки этого руководства.

## <a name="create-a-python-web-app-on-windows"></a>Создание веб-приложения Python в Windows

Создайте веб-приложение Python с помощью `flask` в Windows

Установите [Git](https://git-scm.com/).

После установки Git откройте командную строку и "клонируйте" репозиторий пакета SDK для иммерсивного средства чтения в папку на компьютере

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

установите [Python](https://www.python.org/downloads/);

Установите флажок "Добавить Python в путь".

![Диалоговое окно установки Python в Windows, шаг 1](./media/pythoninstallone.jpg)

Добавьте дополнительные функции, установив нужные флажки, затем нажмите кнопку "Далее".

![Диалоговое окно установки Python в Windows, шаг 2](./media/pythoninstalltwo.jpg)

Выберите "Выборочная установка" и укажите в качестве корневой папки путь установки, например `C:\Python37-32\`, а затем нажмите кнопку "Установить".

![Диалоговое окно установки Python в Windows, шаг 3](./media/pythoninstallthree.jpg)

После завершения установки Python откройте командную строку и командой `cd` перейдите в папку со скриптами Python.

```cmd
cd C:\Python37-32\Scripts
```

Установите Flask.

```cmd
pip install flask
```

Установите Jinja2. Полнофункциональный механизм шаблонов для Python.

```cmd
pip install jinja2
```

Установите virtualenv. Средство для создания изолированных сред Python

```cmd
pip install pip install virtualenv
```

Установите virtualenvwrapper-win. virtualenvwrapper предназначен для упрощения работы с virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Установите модуль "requests". Requests (Запросы) содержит лицензированную для Apache2 библиотеку HTTP на языке Python.

```cmd
pip install requests
```

Создайте виртуальную среду

```cmd
mkvirtualenv advanced-python
```

Командой `cd` перейдите в корневую папку проекта.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Подключите пример проекта к этой среде. Это действие сопоставляет новую виртуальную среду корневой папкой примера проекта.

```cmd
setprojectdir .
```

Включите виртуальную среду.

```cmd
activate
```

Теперь проект должен стать активным, и в командной строке отобразится примерно такое: `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>`.

Отключите среду.

```cmd
deactivate
```

Префикс `(advanced-python)` исчезнет, так как среда отключена.

Чтобы повторно включить среду, выполните команду `workon advanced-python` из корневой папки примера проекта.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Запуск иммерсивного средства чтения с примером содержимого

При включенной среде запустите пример проекта, введя команду `flask run` в корневой папке проекта.

```cmd
flask run
```

Откройте веб-браузер и перейдите по адресу _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Создание веб-приложения Python в OSX

Создайте веб-приложение Python с помощью `flask` в OSX.

Установите [Git](https://git-scm.com/).

После установки Git откройте терминал и "клонируйте" репозиторий пакета SDK для иммерсивного средства чтения в папку на компьютере

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

установите [Python](https://www.python.org/downloads/);

Корневая папка Python, например `Python37-32`, должна находиться в папке Applications.

После завершения установки Python откройте терминал и командой `cd` перейдите в папку со скриптами Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

установить pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Затем выполните следующую команду, чтобы установить PIP для активного пользователя, чтобы избежать проблем с разрешениями.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- В ответ на запрос введите свой пароль.
- Добавьте расположение установки pip в переменную пути.
- Перейдите в нижнюю часть файла и введите путь, который нужно добавить в конец списка, например `PATH=$PATH:/usr/local/bin`.
- Для выхода нажмите сочетание клавиш Control-X.
- Введите `Y` для сохранения изменений буфера.
- Вот и все! Чтобы проверить установку, в новом окне терминала введите `echo $PATH`.

Установите Flask.

```bash
pip install flask --user
```

Установите Jinja2. Полнофункциональный механизм шаблонов для Python.

```bash
pip install Jinja2 --user
```

Установите virtualenv. Средство для создания изолированных сред Python

```bash
pip install virtualenv --user
```

Установите virtualenvwrapper. virtualenvwrapper предназначен для упрощения работы с virtualenv.

```bash
pip install virtualenvwrapper --user
```

Установите модуль "requests". Requests (Запросы) содержит лицензированную для Apache2 библиотеку HTTP на языке Python.

```bash
pip install requests --user
```

Выберите папку, в которой вы хотите разместить виртуальные среды, и выполните эту команду.

```bash
mkdir ~/.virtualenvs
```

Командой `cd` перейдите а папку примера приложения Python для иммерсивного средства чтения.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Создайте виртуальную среду

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Подключите пример проекта к этой среде. Это действие сопоставляет новую виртуальную среду корневой папкой примера проекта.

```bash
setprojectdir .
```

Включите виртуальную среду.

```bash
activate
```

Теперь проект должен стать активным, и в командной строке отобразится примерно такое: `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>`.

Отключите среду.

```bash
deactivate
```

Префикс `(advanced-python)` исчезнет, так как среда отключена.

Чтобы повторно включить среду, выполните команду `workon advanced-python` из корневой папки примера проекта.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Запуск иммерсивного средства чтения с примером содержимого

При включенной среде запустите пример проекта, введя команду `flask run` в корневой папке проекта.

```bash
flask run
```

Откройте веб-браузер и перейдите по адресу _http://localhost:5000_ .

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь с разделом о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справочнике по этому пакету](./reference.md).
* Просмотрите примеры кода на сайте [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
