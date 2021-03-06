---
title: Размещение статического веб-сайта в службе хранилища Azure
description: Узнайте, как обслуживать статическое содержимое (HTML, CSS, JavaScript и файлы изображений) непосредственно из контейнера в учетной записи GPv2 хранилища Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: d5b8121c7888903f3e4552a21a6ddc175ecc5176
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489088"
---
# <a name="host-a-static-website-in-azure-storage"></a>Размещение статического веб-сайта в службе хранилища Azure

Вы можете предоставлять статическое содержимое (HTML, CSS, JavaScript и файлы изображений) непосредственно из контейнера в учетной записи GPv2 хранилища Azure. Дополнительные сведения см. в статье [Размещение статических веб-сайтов в службе хранилища Azure](storage-blob-static-website.md).

В этой статье показано, как включить размещение статических веб-сайтов с помощью портал Azure, Azure CLI или PowerShell.

<a id="portal" />

## <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Пошаговое руководство см. в разделе [учебник. размещение статического веб-сайта в хранилище BLOB-объектов](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

После включения статического размещения веб-сайтов можно просмотреть страницы веб-сайта в браузере с помощью общедоступного URL-адреса сайта.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Поиск URL-адреса веб-сайта с помощью портал Azure

На панели, расположенной рядом со страницей обзора учетной записи хранения, выберите **статический веб-сайт**. URL-адрес сайта отображается в поле **первичная конечная точка** .

![Выбор метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Включить размещение статического веб-сайта можно с помощью [интерфейс командной строки Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Сначала откройте [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)или, если вы [установили](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI локально, Откройте консольное приложение командной консоли, например Windows PowerShell.

2. Если ваше удостоверение связано с более чем одной подпиской, установите активную подписку на подписку учетной записи хранения, в которой будет размещен статический веб-сайт.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Замените значение заполнителя `<subscription-id>` ИДЕНТИФИКАТОРом подписки.

3. Включить размещение статических веб-сайтов.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

   * Замените заполнитель `<error-document-name>` именем документа об ошибке, который будет отображаться пользователям при запросе веб-браузером страницы, которая не существует.

   * Замените заполнитель `<index-document-name>` именем документа индекса. Этот документ обычно имеет формат index. HTML.

4. Передайте объекты в контейнер *$web* из исходного каталога.

   > [!NOTE]
   > Если вы используете Azure Cloud Shell, обязательно добавьте escape-символ `\` при ссылке на контейнер `$web` (например: `\$web`). Если вы используете локальную установку Azure CLI, вам не придется использовать escape-символ.

   В этом примере предполагается, что вы используете команды из сеанса Azure Cloud Shell.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

   * Замените заполнитель `<source-path>` путем к расположению файлов, которые требуется передать.

   > [!NOTE]
   > Если вы используете установку расположения Azure CLI, можно использовать путь к любому расположению на локальном компьютере (например, `C:\myFolder`.
   >
   > Если вы используете Azure Cloud Shell, необходимо сослаться на общую папку, видимую для Cloud Shell. Это может быть общая папка в облачной общей папке или существующая общая папка, которую вы подключаете из Cloud Shell. Сведения о том, как это сделать, см. [в разделе Сохранение файлов в Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Поиск URL-адреса веб-сайта с помощью Azure CLI

Просмотреть содержимое из браузера можно с помощью общедоступного URL-адреса сайта.

Найдите URL-адрес с помощью следующей команды:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

* Замените значение заполнителя `<resource-group-name>` именем группы ресурсов.

<a id="powershell" />

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Включить размещение статических веб-сайтов можно с помощью модуля Azure PowerShell.

1. Откройте командное окно Windows PowerShell.

2. Убедитесь, что у вас есть модуль Azure PowerShell AZ версии 0,7 или более поздней.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

3. Войдите в подписку Azure с помощью команды `Connect-AzAccount` и следуйте инструкциям на экране.

   ```powershell
   Connect-AzAccount
   ```

4. Если ваше удостоверение связано с более чем одной подпиской, установите активную подписку на подписку учетной записи хранения, в которой будет размещен статический веб-сайт.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Замените значение заполнителя `<subscription-id>` ИДЕНТИФИКАТОРом подписки.

5. Получите контекст учетной записи хранения, определяющий учетную запись хранения, которую вы хотите использовать.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Замените значение заполнителя `<resource-group-name>` именем группы ресурсов.

   * Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

6. Включить размещение статических веб-сайтов.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Замените заполнитель `<error-document-name>` именем документа об ошибке, который будет отображаться пользователям при запросе веб-браузером страницы, которая не существует.

   * Замените заполнитель `<index-document-name>` именем документа индекса. Этот документ обычно имеет формат index. HTML.

7. Передайте объекты в контейнер *$web* из исходного каталога.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Замените значение заполнителя `<path-to-file>` полным путем к файлу, который требуется передать (например, `C:\temp\index.html`).

   * Замените значение заполнителя `<blob-name>` именем, которое нужно присвоить результирующему большому двоичному объекту (например: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Поиск URL-адреса веб-сайта с помощью PowerShell

Просмотреть содержимое из браузера можно с помощью общедоступного URL-адреса сайта.

Найдите URL-адрес с помощью следующей команды:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Замените значение заполнителя `<resource-group-name>` именем группы ресурсов.

* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

<a id="metrics" />

---

## <a name="enable-metrics-on-static-website-pages"></a>Включить метрики на статических страницах веб-сайта

После включения метрик в панели мониторинга метрик отображается статистика трафика по файлам в контейнере **$Web** .

1. Щелкните **параметры** > **мониторинг** **метрик** > .

   Данные метрик создаются с помощью подключения к API для различных метрик. На портале отображаются только элементы API, используемые в течение заданного промежутка времени, чтобы сосредоточиться только на тех элементах, которые возвращают данные. Чтобы убедиться, что вы можете выбрать необходимый элемент API, сначала нужно развернуть временной интервал.

2. Нажмите кнопку интервал времени и выберите **последние 24 часа** и нажмите кнопку **Применить**.

   ![Диапазон времени метрик для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Выберите **BLOB-объект** из раскрывающегося списка *пространство имен* .

   ![Пространство имен метрик для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Затем выберите метрику **Исходящий трафик**.

   ![Выбор метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Выберите **Сумма** в селекторе *Агрегирование*.

   ![Агрегирование метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Нажмите кнопку **Добавить фильтр** и выберите **имя API** в селекторе *свойств* .

   ![Имя API метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Установите флажок рядом с **жетвебконтент** в селекторе *значений* , чтобы заполнить отчет метрики.

   ![Поле GetWebContent метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Размещение статических веб-сайтов в службе хранилища Azure](storage-blob-static-website.md)
* [Использование Azure CDN для доступа к BLOB-объектам с пользовательскими доменами по протоколу HTTPS](storage-https-custom-domain-cdn.md)
* [Настройка имени личного домена для конечной точки хранилища BLOB-объектов](storage-custom-domain-name.md)
* [Функции Azure](/azure/azure-functions/functions-overview)
* [службе приложений Azure](/azure/app-service/overview)
* [Создание первого бессерверного веб-приложения](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Руководство. Размещение домена в Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
