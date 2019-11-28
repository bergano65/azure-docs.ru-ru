---
title: Подключение функций к службе хранилища Azure с помощью Visual Studio Code
description: Сведения о добавлении выходной привязки для подключения функций к очереди службы хранилища Azure с помощью Visual Studio Code.
ms.date: 06/25/2019
ms.topic: quickstart
ms.openlocfilehash: baddb6f02fe3d9c66e3c52d826ffe70c151d313e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227438"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Подключение функций к службе хранилища Azure с помощью Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

В этой статье приведены сведения об использовании Visual Studio Code для подключения функции, которая была создана в рамках [предыдущей статьи](functions-create-first-function-vs-code.md), к службе хранилища Azure. Выходная привязка, которая была добавлена в эту функцию, записывает данные HTTP-запроса в сообщение очереди Хранилища очередей Azure. 

Большинство привязок требуют сохраненную строку подключения, которая будет использоваться Функциями Azure для доступа к привязанным службам. Чтобы упростить процесс, вам следует использовать учетную запись хранения, созданную в приложении-функции. Подключение к этой учетной записи уже хранится в параметрах приложения под названием `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этой статьей необходимо выполнить следующие требования:

* Установите [расширение службы хранилища Azure для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
* Установите [Обозреватель службы хранилища Azure](https://storageexplorer.com/). Обозреватель службы хранилища — это инструмент, который вы будете использовать для проверки сообщений очереди, создаваемых с помощью выходной привязки. Он поддерживается в операционных системах macOS, Windows и Linux.
* Установите [средства интерфейса командной строки .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) (только для проектов C#).
* Выполните инструкции, приведенные в статье [Создание функции в Azure с помощью Visual Studio Code](functions-create-first-function-vs-code.md). 

В этой статье предполагается, что вы уже выполнили вход в подписку Azure из Visual Studio Code. Вы можете войти в систему, выполнив команду `Azure: Sign In` в палитре команд. 

## <a name="download-the-function-app-settings"></a>Загрузка параметров приложения-функции

В [предыдущей статье](functions-create-first-function-vs-code.md) вы создавали приложение-функцию и соответствующую учетную запись хранения в Azure. Строка подключения данной учетной записи надежно хранится в параметрах приложения в Azure. В этой статье вы напишете сообщения в очередь хранилища в пределах одной учетной записи. Чтобы подключить учетную запись хранения при локальном выполнение функции, необходимо загрузить параметры приложения в файл local.settings.json. 

1. Нажмите клавишу F1, чтобы открыть палитру команд, а затем найдите и выполните команду `Azure Functions: Download Remote Settings....`. 

1. Выберите созданное в рамках предыдущей статьи приложение-функцию. Выберите **Да, для всех**, чтобы перезаписать имеющиеся локальные параметры. 

    > [!IMPORTANT]  
    > Из-за того, что содержимое local.settings.json является секретом, этот файл никогда не будет опубликован — он будет исключен из системы управления версиями.

1. Скопируйте значение `AzureWebJobsStorage`, которое является ключом для значения строки подключения к учетной записи хранения. Это подключение используется для проверки работы привязки для вывода.

## <a name="register-binding-extensions"></a>Регистрация расширений привязки

Так как вы используете выходную привязку Хранилища очередей, перед запуском проекта необходимо установить расширение привязок службы хранилища. 

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

За исключением триггеров HTTP и таймера, привязки реализованы в виде пакетов расширений. Выполните следующую команду [dotnet add package](/dotnet/core/tools/dotnet-add-package) в окне терминала, чтобы добавить пакет расширений службы хранилища в свой проект.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```
---
Теперь вы можете добавить выходную привязку службы хранилища к проекту.

## <a name="add-an-output-binding"></a>Добавление выходной привязки

В службе "Функции" для каждого типа привязок требуется `direction`, `type` и уникальное `name`, которое определяется в файле function.json. Способ определения этих атрибутов зависит от языка приложения-функции.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

## <a name="add-code-that-uses-the-output-binding"></a>Добавление кода, который использует выходную привязку

После определения привязки можно использовать `name` привязки для доступа к ней как к атрибуту в сигнатуре функции. После использования выходной привязки вам для проверки подлинности, получения ссылки на очередь или записи данных больше не потребуется код пакета SDK службы хранилища Azure. Вместо вас эти задачи будут выполнены выходной привязкой очереди и средой выполнения функции.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Новая очередь с именем **outqueue** создается в вашей учетной записи хранения средой выполнения Функций при первом использовании выходной привязки. Чтобы убедиться, что очередь и сообщение в ней были созданы, вам нужно будет использовать Обозреватель службы хранилища.

### <a name="connect-storage-explorer-to-your-account"></a>Подключение Обозревателя службы хранилища к учетной записи

Пропустите этот раздел, если вы уже установили Обозреватель службы хранилища Azure и подключили его к своей учетной записи Azure.

1. Запустите [Azure Storage Explorer;], щелкните значок подключения слева и выберите **Добавить учетную запись**.

    ![Добавление учетной записи Azure в Обозреватель службы хранилища Microsoft Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. В диалоговом окне **Подключение** щелкните **Add an Azure account** (Добавить учетную запись Azure), выберите свою **среду Azure** и щелкните **Sign in...** (Войти...). 

    ![Вход в учетную запись Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

После успешного входа вы увидите все подписки Azure, связанные с этой учетной записью.

### <a name="examine-the-output-queue"></a>Проверка выходной очереди

1. В Visual Studio Code нажмите клавишу F1, чтобы открыть палитру команд, а затем найдите и выполните команду `Azure Storage: Open in Storage Explorer`, указав имя своей учетной записи хранения. Ваша учетная запись хранения откроется в Обозревателе службы хранилища Azure.  

1. Разверните узел **Очереди**, а затем выберите очередь с именем **outqueue**. 

   В ней содержится сообщение о том, что выходная привязка очереди создана при запуске функции, активируемой HTTP. Если вы вызывали функцию со значением по умолчанию `name` *Azure*, в сообщении очереди будет указано *Имя переданной функции: Azure*.

    ![Сообщение очереди в Обозревателе службы хранилища Azure](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Запустите функцию еще раз, отправьте другой запрос, и в очереди появится новое сообщение.  

Теперь пора выполнить повторную публикацию обновленного приложения-функции в Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Повторное развертывание и проверка обновленного приложения

1. В Visual Studio Code нажмите клавишу F1, чтобы открыть палитру команд. В палитре команд найдите и щелкните `Azure Functions: Deploy to function app...`.

1. Выберите созданное в рамках первой статьи приложение-функцию. Так как вы повторно развертываете свой проект в том же приложении, выберите **Развернуть**, чтобы закрыть предупреждение о перезаписи файлов.

1. По завершении развертывания вы можете снова использовать cURL или браузер для проверки повторно развернутой функции. Как прежде, добавьте строку запроса `&name=<yourname>` URL-адресу, как показано в следующем примере.

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Еще раз [просмотрите сообщение в очереди хранилища](#examine-the-output-queue), чтобы убедиться, что выходная привязка снова создает новое сообщение в очереди.

## <a name="clean-up-resources"></a>Очистка ресурсов

Под *ресурсами* в Azure подразумеваются приложения-функции, функции, учетные записи хранения и т. д. Они объединяются в *группы ресурсов*, при удалении которых удаляются и все данные в них.

Вы создали ресурсы для завершения этих кратких руководств. Вам могут быть выставлены счета за эти ресурсы в зависимости от [состояния учетной записи](https://azure.microsoft.com/account/) и [цен на службы](https://azure.microsoft.com/pricing/). Если вам больше не нужны ресурсы, их можно удалить следующим образом:

1. В Visual Studio Code нажмите клавишу F1, чтобы открыть палитру команд. В палитре команд найдите и щелкните `Azure Functions: Open in portal`.

1. Выберите приложение-функцию и нажмите клавишу ВВОД. Страница приложения функции откроется на [портале Azure](https://portal.azure.com).

1. На вкладке **Обзор** выберите именованную ссылку в разделе **Группа ресурсов**.

    ![Выберите группу ресурсов, которую требуется удалить со страницы приложения-функции.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. На странице **Resource group** (Группа ресурсов) просмотрите список включенных ресурсов и убедитесь, что именно их требуется удалить.
 
1. Выберите **Удалить группу ресурсов** и следуйте инструкциям.

   Удаление может занять несколько минут. После этого на несколько секунд появится уведомление. Кроме того, можно выбрать значок колокольчика в верхней части страницы, чтобы просмотреть уведомление.

## <a name="next-steps"></a>Дополнительная информация

Вы обновили функцию, активируемую HTTP, которую теперь можно использовать для записи данных в очередь хранилища. Дополнительные сведения см. в статье [Разработка функций Azure с помощью Visual Studio Code](functions-develop-vs-code.md).

Затем для мониторинга приложения-функции следует включить Application Insights.

> [!div class="nextstepaction"]
> [Включение интеграции с Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer;]: https://storageexplorer.com/
