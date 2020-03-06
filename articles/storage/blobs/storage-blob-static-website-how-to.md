---
title: Размещение статического веб-сайта в службе хранилища Azure
description: Узнайте, как обслуживать статическое содержимое (HTML, CSS, JavaScript и файлы изображений) непосредственно из контейнера в учетной записи GPv2 хранилища Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: e312cc0dc6c58bb33a737e1fc28dd6eb3578b764
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330290"
---
# <a name="host-a-static-website-in-azure-storage"></a>Размещение статического веб-сайта в службе хранилища Azure

Вы можете предоставлять статическое содержимое (HTML, CSS, JavaScript и файлы изображений) непосредственно из контейнера в учетной записи GPv2 хранилища Azure. Дополнительные сведения см. в статье [Размещение статических веб-сайтов в службе хранилища Azure](storage-blob-static-website.md).

В этой статье показано, как включить размещение статических веб-сайтов с помощью портал Azure, Azure CLI или PowerShell.

## <a name="enable-static-website-hosting"></a>Включить размещение статических веб-сайтов

Размещение статических веб-сайтов — это функция, которую необходимо включить в учетной записи хранения.

### <a name="portal"></a>[Портал](#tab/azure-portal)

1. Чтобы начать, войдите на [портал Azure](https://portal.azure.com/).

2. Найдите учетную запись хранения и отобразите общие сведения о ней.

3. Выберите **Статический веб-сайт** для отображения на странице конфигурации статических веб-сайтов.

4. Выберите **Включено**, чтобы включить размещение статического веб-сайта в учетной записи хранения.

5. В поле **имя документа индекса** укажите страницу индекса по умолчанию (например *, index. HTML*). 

   Страница индексов по умолчанию отображается, когда пользователь переходит к корню статического веб-сайта.  

6. В поле « **путь к документу об ошибке** » укажите страницу ошибок по умолчанию (например *, 404. HTML*). 

   Страница ошибки по умолчанию отображается, когда пользователь пытается перейти на страницу, которой не существует на вашем статическом веб-сайте.

7. Выберите команду **Сохранить**. Теперь портал Azure отображает конечную точку вашего статического веб-сайта. 

    ![Включение размещения статического веб-сайта в учетной записи хранения](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli" />

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell" />

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

---

## <a name="upload-files"></a>Upload files 

### <a name="portal"></a>[Портал](#tab/azure-portal)

В этих инструкциях показано, как отправлять файлы с помощью версии Обозреватель службы хранилища, отображаемой в портал Azure. Однако можно также использовать версию [Обозреватель службы хранилища](https://azure.microsoft.com/features/storage-explorer/) , которая выполняется за пределами портал Azure. Вы можете использовать [AzCopy](../common/storage-use-azcopy-v10.md), POWERSHELL, CLI или любое пользовательское приложение, которое может передавать файлы в контейнер **$Web** вашей учетной записи. Пошаговое руководство по загрузке файлов с помощью Visual Studio Code см. в разделе [учебник. размещение статического веб-сайта в хранилище BLOB-объектов](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Выберите **Обозреватель службы хранилища (Предварительная версия)** .

2. Разверните узел **контейнеры больших двоичных объектов** , а затем выберите контейнер **$Web** .

3. Нажмите кнопку **Отправить** , чтобы отправить файлы.

   ![Upload files](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Если вы хотите, чтобы браузер отображал содержимое файла, убедитесь, что для типа содержимого этого файла задано значение `text/html`. 

   ![Проверка типов содержимого](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Обозреватель службы хранилища автоматически задает для этого свойства значение `text/html` для наиболее распознаваемых расширений, таких как `.html`. Однако в некоторых случаях это необходимо сделать самостоятельно. Если не задать для этого свойства значение `text/html`, браузер предложит пользователям загрузить файл, а не подготовит его к просмотру. Чтобы задать это свойство, щелкните правой кнопкой мыши файл и выберите пункт **Свойства**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Передайте объекты в контейнер *$web* из исходного каталога.

> [!NOTE]
> Если вы используете Azure Cloud Shell, обязательно добавьте escape-символ `\` при ссылке на контейнер `$web` (например: `\$web`). Если вы используете локальную установку Azure CLI, вам не придется использовать escape-символ.

В этом примере предполагается, что вы используете команды из сеанса Azure Cloud Shell.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

* Замените заполнитель `<source-path>` путем к расположению файлов, которые требуется передать.

> [!NOTE]
> Если вы используете установку расположения Azure CLI, можно использовать путь к любому расположению на локальном компьютере (например, `C:\myFolder`.
>
> Если вы используете Azure Cloud Shell, необходимо сослаться на общую папку, видимую для Cloud Shell. Это может быть общая папка в облачной общей папке или существующая общая папка, которую вы подключаете из Cloud Shell. Сведения о том, как это сделать, см. [в разделе Сохранение файлов в Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Передайте объекты в контейнер *$web* из исходного каталога.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* Замените значение заполнителя `<path-to-file>` полным путем к файлу, который требуется передать (например, `C:\temp\index.html`).

* Замените значение заполнителя `<blob-name>` именем, которое нужно присвоить результирующему большому двоичному объекту (например: `index.html`).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Поиск URL-адреса веб-сайта с помощью портал Azure

Страницы веб-сайта можно просмотреть в браузере с помощью общедоступного URL-адреса сайта.

### <a name="portal"></a>[Портал](#tab/azure-portal)

<a id="portal-find-url" />

На панели, расположенной рядом со страницей обзора учетной записи хранения, выберите **статический веб-сайт**. URL-адрес сайта отображается в поле **первичная конечная точка** .

![Выбор метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli-find-url" />

Найдите общедоступный URL-адрес статического веб-сайта с помощью следующей команды:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

* Замените значение заполнителя `<resource-group-name>` именем группы ресурсов.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell-find-url" />

Найдите общедоступный URL-адрес статического веб-сайта с помощью команды, выполнив следующую команду:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Замените значение заполнителя `<resource-group-name>` именем группы ресурсов.

* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

---

<a id="metrics" />

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

* Узнайте, как настроить личный домен со статическим веб-сайтом. См. раздел [сопоставьте личный домен с конечной точкой хранилища BLOB-объектов Azure](storage-custom-domain-name.md).

