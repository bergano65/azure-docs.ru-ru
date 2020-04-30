---
title: Поддержка карт исходного кода для приложений JavaScript — Azure Monitor Application Insights
description: Узнайте, как отправить карты исходного кода в контейнер больших двоичных объектов учетной записи хранения с помощью Application Insights.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79474889"
---
# <a name="source-map-support-for-javascript-applications"></a>Поддержка карт исходного кода для приложений JavaScript

Application Insights поддерживает отправку карт исходного кода в контейнер больших двоичных объектов учетной записи хранения.
Карты исходного кода можно использовать для деминификацию стеков вызовов, найденных на странице сведений о сквозной транзакции. Любое исключение, отправленное [пакетом SDK для JavaScript][ApplicationInsights-JS] или [пакетом SDK для Node. js][ApplicationInsights-Node.js] , может быть унминифиед с картами исходного кода.

![Деминификацию стек вызовов путем связывания с учетной записью хранения](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Создание новой учетной записи хранения и контейнера больших двоичных объектов

Если у вас уже есть учетная запись хранения или контейнер больших двоичных объектов, этот шаг можно пропустить.

1. [Создание учетной записи хранения][create storage account]
2. [Создайте контейнер больших двоичных объектов][create blob container] в вашей учетной записи хранения. Не забудьте установить для `Private`параметра "общий доступ" значение, чтобы убедиться, что исходные карты не являются общедоступными.

> [!div class="mx-imgBorder"]
>![Для уровня доступа к контейнеру необходимо задать значение "частный"](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Отправка карт исходного кода в контейнер больших двоичных объектов

Вы должны интегрировать конвейер непрерывного развертывания с учетной записью хранения, настроив его на автоматическую отправку исходных карт в настроенный контейнер больших двоичных объектов. Не следует передавать исходные карты во вложенную папку в контейнере больших двоичных объектов. Сейчас исходная таблица будет извлечена только из корневой папки.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Отправка карт источника с помощью Azure Pipelines (рекомендуется)

Если вы используете Azure Pipelines для непрерывного создания и развертывания приложения, добавьте в конвейер задачу [копирования файлов Azure][azure file copy] для автоматической отправки карт исходного кода.

> [!div class="mx-imgBorder"]
> ![Добавление задачи копирования файлов Azure в конвейер для передачи исходных карт в хранилище BLOB-объектов Azure](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Настройка ресурса Application Insights с помощью исходной учетной записи хранения

### <a name="from-the-end-to-end-transaction-details-page"></a>Страница сведений о сквозной транзакции

На вкладке сведения о сквозной транзакции можно щелкнуть *деминификацию* и отобразить запрос на настройку, если ресурс не настроен.

1. На портале просмотрите сведения об исключении минифицированные.
2. Щелкните *деминификацию*
3. Если ресурс не был настроен, появится сообщение с запросом на настройку.

### <a name="from-the-properties-page"></a>На странице «свойства»

Если вы хотите настроить или изменить учетную запись хранения или контейнер больших двоичных объектов, связанный с Application Insightsным ресурсом, это можно сделать, просмотрев вкладку *свойств* ресурса Application Insights.

1. Перейдите на вкладку *Свойства* ресурса Application Insights.
2. Щелкните ссылку *изменить источник больших двоичных объектов для гиперкарты*.
3. Выберите другой контейнер больших двоичных объектов в качестве контейнера карт исходного кода.
4. Нажмите кнопку `Apply`.

> [!div class="mx-imgBorder"]
> ![Перенастройте выбранный контейнер больших двоичных объектов Azure, перейдя в колонку свойств.](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Необходимые параметры управления доступом на основе ролей (RBAC) в контейнере больших двоичных объектов

Любой пользователь на портале, использующий эту функцию, должен быть по крайней мере назначен [модулем чтения данных BLOB-объекта хранилища][storage blob data reader] для контейнера больших двоичных объектов. Эту роль необходимо назначить другим пользователям, которые будут использовать карты исходного кода с помощью этой функции.

> [!NOTE]
> В зависимости от способа создания контейнера это может быть не было автоматически назначено вам или вашей команде.

### <a name="source-map-not-found"></a>Исходная таблица не найдена

1. Убедитесь, что соответствующая исходная таблица отправлена в правильный контейнер больших двоичных объектов.
2. Убедитесь, что исходный файл сопоставления назван после файла JavaScript, к которому он соответствует, с `.map`суффиксом.
    - Например, `/static/js/main.4e2ca5fa.chunk.js` будет искать большой двоичный объект с именем`main.4e2ca5fa.chunk.js.map`
3. Проверьте наличие ошибок в консоли браузера. Включите это в любой запрос в службу поддержки.

## <a name="next-steps"></a>Дальнейшие шаги

* [Задача копирования файлов Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme