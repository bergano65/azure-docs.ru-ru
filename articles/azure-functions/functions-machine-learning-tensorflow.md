---
title: Руководство по Использование Python и TensorFlow в Функциях Azure для получения выводов машинного обучения | Документация Майкрософт
description: В этом руководстве показано, как применять модели машинного обучения TensorFlow в Функциях Azure.
services: functions
author: anthonychu
manager: gwallace
ms.service: azure-functions
ms.devlang: python
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: abc7302ee59103a9cbab156b95a41b77eb95d474
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68729161"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Руководство по Применение моделей машинного обучения в Функциях Azure с использованием Python и TensorFlow

В статье демонстрируется, как Функции Azure позволяют использовать Python и TensorFlow с моделью машинного обучения, чтобы классифицировать изображение на основе его содержимого.

Из этого руководства вы узнаете, как выполнять такие задачи. 

> [!div class="checklist"]
> * инициализация локальной среды разработки Функций Azure в Python;
> * импорт настраиваемой модели машинного обучения TensorFlow в приложение-функцию;
> * создание бессерверного API HTTP для прогнозирования того, кто изображен на фотографии — собака или кот;
> * использование API из веб-приложения.

![Снимок экрана готового проекта](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования 

Для работы в Функциях Azure с Python необходимо установить несколько средств:

- [Python 3.6](https://www.python.org/downloads/release/python-360/);
- [Основные инструменты службы "Функции Azure"](functions-run-local.md#install-the-azure-functions-core-tools)
- Редактор кода, например [Visual Studio Code](https://code.visualstudio.com/).

## <a name="clone-the-tutorial-repository"></a>Клонирование репозитория руководства

Для начала откройте терминал и клонируйте следующий репозиторий, используя Git.

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

В репозитории находятся такие папки:

- *start* —  это папка для работы с этим руководством;
- *end* — справочная папка с итоговым результатом проекта и полной его реализацией;
- *resources:* папка с моделью машинного обучения и вспомогательными библиотеками;
- *frontend* — папка с веб-сайтом, который вызывает приложение-функцию.

## <a name="create-and-activate-a-python-virtual-environment"></a>Создание и активация виртуальной среды Python

Для работы с Функциями Azure требуется Python 3.6.x. Вы создадите виртуальную среду, чтобы убедиться, что используется требуемая версия Python.

Измените текущий рабочий каталог на папку *start*. Затем создайте и активируйте виртуальную среду с именем *.venv*. В разных версиях Python команды для создания виртуальной среды Python 3.6 могут отличаться от показанных ниже.

#### <a name="linux-and-macos"></a>Linux и macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

Теперь в командной строке терминала есть префикс `(.venv)`, свидетельствующий о том, что виртуальная среда успешно активирована. Проверьте, что в виртуальной среде `python` действительно используется Python 3.6.x.

```console
python --version
```

> [!NOTE]
> В оставшейся части руководства предполагается, что команды выполняются в виртуальной среде. Если необходимо повторно активировать виртуальную среду в терминале, выполните соответствующую команду активации для своей операционной системы.

## <a name="create-an-azure-functions-project"></a>Создание проекта Функций Azure

В папке *start* инициализируйте приложение-функцию Python с помощью Azure Functions Core Tools.

```console
func init --worker-runtime python
```

Приложение-функция может содержать одну или несколько экземпляров Функций Azure. Откройте в редакторе папку *start* и изучите ее содержимое.

- [*local.settings.json*](functions-run-local.md#local-settings-file) содержит параметры приложения, используемые для локальной разработки;
- [*host.json*](functions-host-json.md) содержит параметры для узла и расширений Функций Azure;
- [*requirements.txt*](functions-reference-python.md#python-version-and-package-management) содержит пакеты Python, необходимые для этого приложения.

## <a name="create-an-http-function"></a>Создание функции HTTP

Приложению требуется одна конечная точка API HTTP, которая принимает URL-адрес изображения в качестве входных данных и возвращает прогноз того, присутствует на изображении собака или кот.

В терминале с помощью Azure Functions Core Tools создайте функцию HTTP с именем *classify*.

```console
func new --language python --template HttpTrigger --name classify
```

При этом будет создана папка *classify* с двумя файлами:

- *\_\_init\_\_.py* — это файл функции main;
- *function.json* —  файл с описанием триггера функции и его входных и выходных привязок.

### <a name="run-the-function"></a>Выполнение функции

В терминале с активированной виртуальной средой Python запустите приложение-функцию.

```console
func start
```

Откройте браузер и перейдите по следующему URL-адресу: Функция должна выполняться и возвращать *Hello Azure!*

```
http://localhost:7071/api/classify?name=Azure
```

Используйте `Ctrl-C` для завершения работы приложения-функции.

## <a name="import-the-tensorflow-model"></a>Импорт модели TensorFlow

Вы будете использовать предварительно созданную модель TensorFlow, которая была обучена и экспортирована из Пользовательского визуального распознавания Azure.

> [!NOTE]
> Если вы хотите создать собственную модель с помощью Пользовательского визуального распознавания уровня "Бесплатный", следуйте [инструкциям из репозитория примера проекта](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

Модель состоит из двух файлов, находящихся в папке *<REPOSITORY_ROOT>/resources/model*, *model.db* и *labels.txt*. Скопируйте их в папку функции *classify*.

#### <a name="linux-and-macos"></a>Linux и macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

Не забудьте добавить знак \* в эту команду. Убедитесь, что файлы *model.pb* и *labels.txt* находятся в папке *classify*.

## <a name="add-the-helper-functions-and-dependencies"></a>Добавление вспомогательных функций и зависимостей

Некоторые вспомогательные функции для подготовки входного изображения и создания прогноза с помощью TensorFlow содержатся в файле *predict.py* из папки *resources*. Скопируйте этот файл в папку функции *classify*.

#### <a name="linux-and-macos"></a>Linux и macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

Убедитесь, что файл *predict.py* находится в папке *classify*.

### <a name="install-dependencies"></a>Установка зависимостей

Вспомогательная библиотека содержит некоторые зависимости, которые необходимо установить. Откройте в редакторе файл *start/requirements.txt* и добавьте в него следующие зависимости.

```txt
tensorflow
Pillow
requests
```

Сохраните файл.

В терминале с активированной виртуальной средой выполните следующую команду в папке *start*, чтобы установить зависимости. Выполнение некоторых шагов установки может занять несколько минут.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>Кэширование модели в глобальных переменных

В редакторе откройте файл *predict.py* и найдите функцию `_initialize` в его верхней части. Обратите внимание, что модель TensorFlow загружается с диска при первом выполнении функции и сохраняется в глобальных переменных. Загрузка с диска пропускается при последующих выполнениях функции `_initialize`. Кэширование модели в памяти с помощью этого метода ускоряет прогнозирование.

Дополнительные сведения о глобальных переменных см. в [справочнике разработчика Python по Функциям Azure](functions-reference-python.md#global-variables).

## <a name="update-function-to-run-predictions"></a>Обновление функции для выполнения прогнозов

Откройте в редакторе файл *classify/\_\_init\_\_.py*. Импортируйте библиотеку *predict*, ранее добавленную в эту же папку. Добавьте следующие операторы `import` под другими операторами импорта, которые присутствуют в файле.

```python
import json
from .predict import predict_image_from_url
```

Замените код шаблона функции следующим кодом.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

Не забудьте сохранить изменения.

Эта функция получает URL-адрес изображения в параметре строки запроса с именем `img`. Он вызывает `predict_image_from_url` из вспомогательной библиотеки, которая скачивает изображение и возвращает прогноз с помощью модели TensorFlow. Затем функция возвращает ответ HTTP с результатами.

Так как конечная точка HTTP вызывается веб-страницей, размещенной в другом домене, ответ HTTP содержит заголовок `Access-Control-Allow-Origin` в соответствия с требованиями к общему доступ к ресурсам независимо от источника (CORS), предъявляемых браузером.

> [!NOTE]
> В рабочем приложении измените `*` на источник веб-страницы для повышения безопасности.

### <a name="run-the-function-app"></a>Запуск приложения-функции

Убедитесь, что виртуальная среда Python активирована, и запустите приложение-функцию с помощью следующей команды.

```console
func start
```

В браузере откройте следующий URL-адрес, который вызывает функцию с URL-адресом фотографии кота. Убедитесь, что возвращен правильный результат прогноза.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

Не завершайте работу приложения-функции.

### <a name="run-the-web-app"></a>Запуск веб-приложения

В папке *frontend* находится простое веб-приложение, использующее API HTTP в приложении-функции.

В *отдельном* окне терминала перейдите к папке *frontend*. Запустите HTTP-сервер с Python 3.6.

#### <a name="linux-and-macos"></a>Linux и macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

В браузере перейдите по URL-адресу HTTP-сервера, который отображается в окне терминала. Должно отобразиться веб-приложение. В текстовое поле введите один из следующих URL-адресов фотографий. Вы также можете использовать URL-адрес общедоступной фотографии кота или собаки.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

При нажатии кнопки отправки вызывается приложение-функция и на странице отображается результат.

## <a name="clean-up-resources"></a>Очистка ресурсов
Все инструкции из этого руководство выполняются локально на компьютере, поэтому нет необходимости в очистке ресурсов или служб Azure.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать и настроить API HTTP с помощью Функций Azure для создания прогнозов с использованием модели TensorFlow. Вы также научились вызывать API из веб-приложения.

С помощью описанных в этом руководстве методов можно создавать API любой сложности, работая при этом в бессерверной вычислительной модели, которую предоставляют Функции Azure.

Чтобы развернуть приложение-функцию в Azure, используйте [Azure Functions Core Tools](./functions-run-local.md#publish) или [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).

> [!div class="nextstepaction"]
> [Azure Functions Python Developer Guide](./functions-reference-python.md) (Справочник по Функциям Azure для разработчика Python)
