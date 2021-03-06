---
title: Установка или обновление расширения привязки Функции Azure вручную
description: Дополнительные сведения о том, как установить или обновить расширения привязки Функции Azure для развернутых приложений функции.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: 83ae2cdd16bce0b0a5b11b8c24e996306453a7a9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977494"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Выполнение установки или обновления расширения привязки Функции Azure вручную на портале

Начиная с версии 2. x среда выполнения функций Azure использует расширения привязки для реализации кода для триггеров и привязок. Расширения привязки входят в состав пакетов NuGet. Чтобы зарегистрировать расширение, необходимо установить пакет. Способ установки расширений привязки при разработке функций зависит от среды разработки. Дополнительные сведения см. в разделе [Регистрация расширений привязки](./functions-bindings-register.md) статьи о триггерах и привязках.

Иногда расширения привязки на портале Azure придется устанавливать или обновлять вручную. Например, может потребоваться обновить зарегистрированную привязку. Может потребоваться также регистрировать поддерживаемые привязки, которые невозможно установить на вкладку **Интеграция** на портале.

## <a name="install-a-binding-extension"></a>Установка расширения привязки

Чтобы вручную установить или обновить расширения на портале, выполните следующие действия.

1. Найдите и выберите свое приложение-функцию на [портале Azure](https://portal.azure.com). Выберите вкладку **Обзор** и щелкните **Остановить**.  После остановки приложения-функции файлы станут разблокированными, что позволит вносить в них изменения.

1. Выберите вкладку **Функции платформы** в разделе **Инструменты разработки**, а затем — **Дополнительные инструменты (Kudu)** . В новом окне откроется конечная точка Kudu (`https://<APP_NAME>.scm.azurewebsites.net/`).

1. В окне Kudu выберите **Консоль отладки** > **CMD**.  

1. Чтобы удалить папку в окне командной строки, перейдите к `D:\home\site\wwwroot` и выберите значок удаления рядом с `bin`. Чтобы подтвердить удаление, нажмите кнопку **ОК**.

1. Выберите значок редактирования рядом с файлом `extensions.csproj`. С его помощью можно определить расширение привязки для приложения-функции. Файл проекта откроется в онлайн-редакторе.

1. Выполните необходимые дополнения и обновления для элементов **PackageReference** в **ItemGroup**, а затем нажмите кнопку **Сохранить**. Текущий список поддерживаемых пакетов версии можно найти в вики-статье [What packages do I need?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) (Требуемые пакеты NuGet). Пакет Microsoft.Azure.WebJobs.Extensions.Storage является необходимым для всех трех привязок службы хранилища Azure.

1. Чтобы повторно собрать упоминаемые сборки в папке `bin`, перейдите в папку `wwwroot` и выполните следующую команду.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Чтобы перезапустить приложение-функцию, вернитесь на вкладку **Сводка** на портале и выберите **Начало**.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
