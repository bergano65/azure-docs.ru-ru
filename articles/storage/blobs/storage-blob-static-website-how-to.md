---
title: Размещение статического веб-сайта в службе хранилища Azure
description: Узнайте, как обслуживание статического содержимого (HTML, CSS, JavaScript и файлы изображений) непосредственно из контейнера в учетной записи GPv2 хранилища Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 5ab24a99b22fae172b5308ba7477953f27ecfd44
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435957"
---
# <a name="host-a-static-website-in-azure-storage"></a>Размещение статического веб-сайта в службе хранилища Azure

Может служить статическое содержимое (HTML, CSS, JavaScript и файлы изображений) непосредственно из контейнера в учетной записи GPv2 хранилища Azure. Дополнительные сведения см. в разделе [размещения статического веб-сайта в службе хранилища Azure](storage-blob-static-website.md).

В этой статье показано, как для размещения статического веб-сайта с помощью портала Azure, Azure CLI или PowerShell.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Использование портала Azure

Пошаговые инструкции см. в разделе [руководства: по размещению статического веб-сайта в хранилище BLOB-объектов](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

После включения статического размещения веб-сайтов, можно просмотреть страницы узла из браузера, используя общедоступный URL-адрес веб-сайта.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Поиск URL-адрес веб-сайта с помощью портала Azure

В области, который появляется рядом страница обзора учетной записи вашей учетной записи хранения, выберите **статический веб-сайт**. URL-адрес сайта отображается в **основную конечную точку** поля.

![Выбор метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Использование Azure CLI

Вы можете включить размещение статического веб-сайта с помощью [интерфейса командной строки Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Сначала откройте [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), или если вы [установлен](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI локально, откройте командного консольного приложения, такие как Windows PowerShell.

2. Если вашей личности связан с более чем одной подписки, задайте в активной подписки к подписке учетной записи хранения, где будет размещаться в статический веб-сайт.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Замените `<subscription-id>` значение заполнителя с Идентификатором вашей подписки.

3. Включите размещение статического веб-сайта.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

   * Замените `<error-document-name>` заполнитель с именем документ об ошибке, которое будет отображаться для пользователей, когда браузер запрашивает страницу на сайте, который не существует.

   * Замените `<index-document-name>` заполнитель имя документа индекса. Этот документ часто является «index.html».

4. Передайте объекты в контейнер *$web* из исходного каталога.

   > [!NOTE]
   > Если вы используете Azure Cloud Shell, убедитесь в том добавить `\` escape-символ, при ссылке на `$web` контейнера (например: `\$web`). Если вы используете локальную установку Azure CLI, не придется использовать escape-символ.

   В этом примере предполагается, что вы используете команды из сеанса Azure Cloud Shell.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

   * Замените `<source-path>` заполнитель с путем к расположению файлов, которые требуется отправить.

   > [!NOTE]
   > Если вы используете расположение установки Azure CLI, то можно использовать путь в любое расположение на локальном компьютере (например: `C:\myFolder`.
   >
   > Если вы используете Azure Cloud Shell, необходимо будет указать ссылку файловый ресурс, который является видимым для Cloud Shell. Это расположение может быть общий доступ к общей папке облака, сам или существующий файловый ресурс подключения из Cloud Shell. Чтобы узнать, как это сделать, см. в разделе [сохранение файлов в Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Поиск URL-адрес веб-сайта с помощью Azure CLI

Можно просмотреть содержимое из браузера, используя общедоступный URL-адрес веб-сайта.

Определите URL-адрес, выполнив следующую команду:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

* Замените `<resource-group-name>` заполнитель с именем группы ресурсов.

<a id="powershell" />

## <a name="use-powershell"></a>Использование PowerShell

Вы можете включить размещение статического веб-сайта с помощью модуля Azure PowerShell.

1. Откройте командное окно Windows PowerShell.

2. Убедитесь, что у вас есть модуль Azure PowerShell Az версии 0,7 или более поздней версии.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

3. Войдите в подписку Azure с помощью команды `Connect-AzAccount` и следуйте инструкциям на экране.

   ```powershell
   Connect-AzAccount
   ```

4. Если вашей личности связан с более чем одной подписки, задайте в активной подписки к подписке учетной записи хранения, где будет размещаться в статический веб-сайт.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Замените `<subscription-id>` значение заполнителя с Идентификатором вашей подписки.

5. Получите контекст учетной записи хранения, который определяет учетную запись хранения, которую вы хотите использовать.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Замените `<resource-group-name>` заполнитель с именем группы ресурсов.

   * Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

6. Включите размещение статического веб-сайта.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Замените `<error-document-name>` заполнитель с именем документ об ошибке, которое будет отображаться для пользователей, когда браузер запрашивает страницу на сайте, который не существует.

   * Замените `<index-document-name>` заполнитель имя документа индекса. Этот документ часто является «index.html».

7. Передайте объекты в контейнер *$web* из исходного каталога.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Замените `<path-to-file>` полный путь к файлу, который вы хотите передать значение заполнителя (например: `C:\temp\index.html`).

   * Замените `<blob-name>` заполнитель с именем, которое вы хотите предоставить итоговый большого двоичного объекта (например: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Поиск URL-адрес веб-сайта с помощью PowerShell

Можно просмотреть содержимое из браузера, используя общедоступный URL-адрес веб-сайта.

Определите URL-адрес, выполнив следующую команду:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Замените `<resource-group-name>` заполнитель с именем группы ресурсов.

* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Включить метрики на страницах статического веб-сайта

После включения метрик, трафик статистики для файлов в **$web** контейнера отображаются в панели мониторинга метрик.

1. Щелкните **параметры** > **мониторинга** > **метрики**.

   Данные метрик создаются с помощью подключения к API для различных метрик. На портале отображаются только элементы API, используемые в течение заданного промежутка времени, чтобы сосредоточиться только на тех элементах, которые возвращают данные. Чтобы убедиться, что вы сможете выбрать необходимые API член, первым делом расширить временные рамки.

2. Нажмите кнопку "интервал времени" и выберите **за последние 24 часа** и нажмите кнопку **применить**.

   ![Диапазон времени метрик для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Выберите **BLOB-объектов** из *пространства имен* раскрывающийся список.

   ![Пространство имен метрик для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Затем выберите метрику **Исходящий трафик**.

   ![Выбор метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Выберите **Сумма** в селекторе *Агрегирование*.

   ![Агрегирование метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Нажмите кнопку **добавить фильтр** кнопку и выберите **имя API** из *свойство* селектор.

   ![Имя API метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Проверка поле рядом с полем **GetWebContent** в *значения* селектор для заполнения отчета на основе метрик.

   ![Поле GetWebContent метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Размещение статических веб-сайтов в службе хранилища Azure](storage-blob-static-website.md)
* [Использование Azure CDN для доступа к большим двоичным объектам с пользовательскими доменами по протоколу HTTPS](storage-https-custom-domain-cdn.md)
* [Настройка имени личного домена для конечной точки хранилища BLOB-объектов](storage-custom-domain-name.md)
* [Функции Azure](/azure/azure-functions/functions-overview)
* [службе приложений Azure](/azure/app-service/overview)
* [Создание первого бессерверного веб-приложения](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Учебник. по размещению домена в Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
