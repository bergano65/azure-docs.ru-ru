---
title: Поддержка исходной карты для приложений JavaScript - Azure Monitor Application Insight
description: Узнайте, как загрузить исходные карты в свой собственный контейнер для хранения данных Blob с помощью Application Insights.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474889"
---
# <a name="source-map-support-for-javascript-applications"></a>Поддержка исходной карты для приложений JavaScript

Application Insights поддерживает загрузку исходных карт в свой собственный контейнер для хранения данных Blob Container.
Исходные карты могут использоваться для unminify стеки вызовов, найденные на странице данных о транзакциях от окончания до конца. Любое исключение, отправленное [JavaScript SDK][ApplicationInsights-JS] или [Node.js SDK,][ApplicationInsights-Node.js] может быть неисчерпанным с помощью исходных карт.

![Unminify стек вызова, связывая с учетной записью хранения](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Создание новой учетной записи хранения данных и контейнера Blob

Если у вас уже есть существующая учетная запись хранения или контейнер с каплей, вы можете пропустить этот шаг.

1. [Создание учетной записи хранения][create storage account]
2. [Создайте контейнер с каплей][create blob container] внутри учетной записи хранения. Обязательно установите "Уровень общественного `Private`доступа", чтобы убедиться, что ваши исходные карты не являются общедоступными.

> [!div class="mx-imgBorder"]
>![Уровень доступа к контейнерам должен быть установлен на private](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Нажмите исходные карты в контейнер Blob

Необходимо интегрировать конвейер непрерывного развертывания с учетной записью хранилища, напланировав его для автоматической загрузки исходных карт в настроенный контейнер Blob. Вы не должны загружать исходные карты в субфолдер в контейнере Blob; в настоящее время исходная карта будет извлечена только из корневой папки.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Загрузка исходных карт через Azure Pipelines (рекомендуется)

Если вы используете конвейеры Azure для непрерывной сборки и развертывания приложения, добавьте задачу [Azure File Copy][azure file copy] в конвейер для автоматической загрузки исходных карт.

> [!div class="mx-imgBorder"]
> ![Добавьте задачу копирования файлов Azure в конвейер для загрузки исходных карт в хранилище Azure Blob](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Наверскакните ресурс Insights с помощью учетной записи хранения исходной карты

### <a name="from-the-end-to-end-transaction-details-page"></a>Со страницы деталей сквозной транзакции

Со вкладки «Детали сквозной транзакции» вы можете нажать на *Unminify,* и она будет отображать подсказку для настройки, если ваш ресурс не настроен.

1. На портале просмотрите сведения об исключении, которое минифицировано.
2. Нажмите на *Unminify*
3. Если ресурс не настроен, появится сообщение, что побудит вас настроиться.

### <a name="from-the-properties-page"></a>Со страницы свойств

Если вы хотите настроить или изменить учетную запись хранения или контейнер Blob, который связан с вашим ресурсом Application Insights, вы можете сделать это, просмотрев вкладку *Ресурса Свойство.ru.*

1. Перейдите на вкладку *«Свойства»* ресурса Application Insights.
2. Нажмите на *изменение исходной карты капли контейнера*.
3. Выберите другой контейнер Blob в качестве контейнера исходных карт.
4. Нажмите кнопку `Apply`.

> [!div class="mx-imgBorder"]
> ![Перенастроить выбранный контейнер Azure Blob, перенаместившись в Blade Properties](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Необходимые настройки управления доступом на основе ролей (RBAC) в контейнере Blob

Любой пользователь на портале, использующий эту функцию, должен быть назначен в качестве [читателя данных хранилища][storage blob data reader] в контейнер Blob. Вы должны назначить эту роль кому-либо еще, кто будет использовать исходные карты через эту функцию.

> [!NOTE]
> В зависимости от того, как был создан контейнер, это не может быть автоматически назначено вам или вашей команде.

### <a name="source-map-not-found"></a>Карта источника не найдена

1. Удостоверьте, что соответствующая исходная карта загружена в правильный контейнер с каплями
2. Убедитесь, что файл исходной карты назван в честь файла `.map`JavaScript, на который он отображается, суффиксированный с .
    - Например, `/static/js/main.4e2ca5fa.chunk.js` будет искать капли с именем`main.4e2ca5fa.chunk.js.map`
3. Проверьте консоль браузера, чтобы увидеть, если какие-либо ошибки регистрируются. Включите это в любой билет поддержки.

## <a name="next-steps"></a>Next Steps

* [Задача копирования файлов Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme