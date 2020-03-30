---
title: Размещение статического веб-сайта в Хранилище Azure
description: Узнайте, как обслуживать статическое содержимое (HTML, CSS, JavaScript и файлы изображений) непосредственно из контейнера в учетной записи Azure Storage GPv2.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 056e23f0f0cf1a3a1c70042cef3c92dd41f14f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247016"
---
# <a name="host-a-static-website-in-azure-storage"></a>Размещение статического веб-сайта в Хранилище Azure

Можно обслуживать статическое содержимое (HTML, CSS, JavaScript и файлы изображений) прямо из контейнера в учетной записи Azure Storage GPv2. Чтобы узнать больше, смотрите [веб-хостинг Static в Azure Storage.](storage-blob-static-website.md)

В этой статье показано, как включить статический хостинг веб-сайтов с помощью портала Azure, Azure CLI или PowerShell.

## <a name="enable-static-website-hosting"></a>Включить статический хостинг веб-сайта

Статический хостинг веб-сайта — это функция, которую необходимо включить в учетную запись хранения.

### <a name="portal"></a>[Портал](#tab/azure-portal)

1. Чтобы начать, войдите на [портал Azure](https://portal.azure.com/).

2. Найдите учетную запись хранения и отобразите общие сведения о ней.

3. Выберите **Статический веб-сайт** для отображения на странице конфигурации статических веб-сайтов.

4. Выберите **Включено**, чтобы включить размещение статического веб-сайта в учетной записи хранения.

5. В поле **имени документа «Индекс»** укажите страницу индекса по умолчанию (например: *index.html*). 

   Страница индексов по умолчанию отображается, когда пользователь переходит к корню статического веб-сайта.  

6. В поле **пути документа ошибка** укажите страницу ошибки по умолчанию (Например: *404.html*). 

   Страница ошибки по умолчанию отображается, когда пользователь пытается перейти на страницу, которой не существует на вашем статическом веб-сайте.

7. Нажмите **Сохранить**. Теперь портал Azure отображает конечную точку вашего статического веб-сайта. 

    ![Включение размещения статического веб-сайта в учетной записи хранения](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

<a id="cli" />

Включить статический хостинг веб-сайта с помощью [интерфейса командования Azure (CLI).](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

1. Во-первых, откройте [облачную оболочку Azure,](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)или если вы [установили](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI локально, откройте приложение командной консоли, такое как Windows PowerShell.

2. Если ваша личность связана с более чем одной подпиской, то установите активную подписку на подписку на учетную запись хранения, в размещении которого будет размещен ваш статический веб-сайт.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Замените `<subscription-id>` значение заполнителя идентификатором вашей подписки.

3. Включить статический хостинг веб-сайта.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

   * Замените `<error-document-name>` заполнитель с именем документа об ошибке, который появится у пользователей, когда браузер запрашивает страницу на вашем сайте, которая не существует.

   * Замените `<index-document-name>` заполнитель на название индексного документа. Этот документ обычно является "index.html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell" />

Включить статический веб-сайт с помощью модуля Azure PowerShell.

1. Откройте окно команды Windows PowerShell.

2. Убедитесь, что у вас есть модуль Azure PowerShell Az версия 0.7 или позже.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

3. Войдите в подписку Azure с помощью команды `Connect-AzAccount` и следуйте инструкциям на экране.

   ```powershell
   Connect-AzAccount
   ```

4. Если ваша личность связана с более чем одной подпиской, то установите активную подписку на подписку на учетную запись хранения, в размещении которого будет размещен ваш статический веб-сайт.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Замените `<subscription-id>` значение заполнителя идентификатором вашей подписки.

5. Получите контекст учетной записи хранилища, определяющий учетную запись хранилища, которую вы хотите использовать.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Замените `<resource-group-name>` значение заполнителя на имя группы ресурсов.

   * Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

6. Включить статический хостинг веб-сайта.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Замените `<error-document-name>` заполнитель с именем документа об ошибке, который появится у пользователей, когда браузер запрашивает страницу на вашем сайте, которая не существует.

   * Замените `<index-document-name>` заполнитель на название индексного документа. Этот документ обычно является "index.html".

---

## <a name="upload-files"></a>Upload files 

### <a name="portal"></a>[Портал](#tab/azure-portal)

Эти инструкции показывают, как загружать файлы с помощью версии Storage Explorer, которая отображается на портале Azure. Тем не менее, можно также использовать версию [Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/) которая выполняется за пределами портала Azure. Вы можете использовать [AzCopy,](../common/storage-use-azcopy-v10.md)PowerShell, CLI или любое пользовательское приложение, которое может загружать файлы в **$web** контейнер вашей учетной записи. Для поэтапного учебника, который загружает файлы с помощью кода Visual Studio, [см.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)

1. Выберите **исследователь хранения (предварительный просмотр)**.

2. Расширьте узла **BLOB CONTAINERS,** а затем выберите **$web** контейнер.

3. Выберите кнопку **Загрузка** для загрузки файлов.

   ![Upload files](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Если вы намереваетесь, чтобы браузер отобразил содержимое файла, `text/html`убедитесь, что тип содержимого этого файла настроен на . 

   ![Проверка типов содержимого](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Storage Explorer автоматически устанавливает `text/html` это свойство для `.html`общепризнанных расширений, таких как . Однако, в некоторых случаях, вам придется установить это самостоятельно. Если вы не установите `text/html`это свойство, браузер подскажет пользователям загрузить файл вместо рендеринга содержимого. Чтобы установить это свойство, нажмите правой кнопкой мыши файла, а затем нажмите **Свойства**.

### <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Загрузить объекты в *контейнер $web* из исходного каталога.

> [!NOTE]
> Если вы используете Azure Cloud Shell, `\` не забудьте добавить символ побега при обращении к контейнеру `$web` (например: `\$web`). Если вы используете локальную установку Azure CLI, вам не придется использовать символ побега.

Этот пример предполагает, что вы приводите в работу команды из сеанса Azure Cloud Shell.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

* Замените `<source-path>` заполнитель на путь к расположению файлов, которые вы хотите загрузить.

> [!NOTE]
> Если вы используете установку местоположения Azure CLI, то вы можете использовать путь к `C:\myFolder`любому местоположению на локальном компьютере (например: .
>
> Если вы используете Azure Cloud Shell, вам придется ссылаться на долю файлов, которая видна облачной оболочке. Это место может быть долей файла самой совместной части облака или существующей долей файла, которую вы устанавливаете из облачной оболочки. Чтобы узнать, как это сделать, смотрите [файлы Persist в Облачной оболочке Azure.](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Загрузить объекты в *контейнер $web* из исходного каталога.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* Замените `<path-to-file>` значение заполнителя полностью квалифицированным способом загрузки `C:\temp\index.html`файла (например: ).

* Замените `<blob-name>` значение заполнителя на имя, которое вы хотите `index.html`дать результирующей капли (Например: ).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Найти URL-адрес веб-сайта с помощью портала Azure

Вы можете просматривать страницы вашего сайта из браузера, используя общедоступный URL-адрес веб-сайта.

### <a name="portal"></a>[Портал](#tab/azure-portal)

<a id="portal-find-url" />

В панели, которая отображается рядом со страницей обзора учетной записи вашей учетной записи, выберите **Static Website.** URL-адрес вашего сайта отображается в поле **конечных точек Первоначального общества.**

![Выбор метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

<a id="cli-find-url" />

Найдите общедоступный URL вашего статического веб-сайта, используя следующую команду:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

* Замените `<resource-group-name>` значение заполнителя на имя группы ресурсов.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell-find-url" />

Найдите общедоступный URL вашего статического веб-сайта, используя следующую команду:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Замените `<resource-group-name>` значение заполнителя на имя группы ресурсов.

* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Включить метрики на страницах статических веб-сайтов

После включения метрик статистика трафика файлов в **$web** контейнере сообщается в панели мониторинга метрик.

1. Нажмите **"Метрики"** в разделе **"Монитор"** меню учетной записи хранилища.

   > [!div class="mx-imgBorder"]
   > ![Ссылка на метрика](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Данные метрик создаются с помощью подключения к API для различных метрик. На портале отображаются только элементы API, используемые в течение заданного промежутка времени, чтобы сосредоточиться только на тех элементах, которые возвращают данные. Для того, чтобы убедиться, что вы сможете выбрать необходимый член API, первым шагом является расширение временных рамок.

2. Нажмите на кнопку таймфрейм, выберите временные рамки, а затем нажмите **Apply**.

   ![Диапазон времени метрик для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Выберите **Blob** из *Namespace* упасть.

   ![Пространство имен метрик для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Затем выберите метрику **Исходящий трафик**.

   ![Выбор метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Выберите **Сумма** в селекторе *Агрегирование*.

   ![Агрегирование метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Нажмите кнопку **«Добавить фильтр»** и выберите **имя API** из селектора *свойств.*

   ![Имя API метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Проверьте поле рядом с **GetWebContent** в селекторе *значений,* чтобы заполнить отчет о метриках.

   ![Поле GetWebContent метрики для статических веб-сайтов службы хранилища Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > Флажок **GetWebContent** отображается только в том случае, если этот участник API использовался в течение определенного периода времени. На портале отображаются только элементы API, используемые в течение заданного промежутка времени, чтобы сосредоточиться только на тех элементах, которые возвращают данные. Если вы не можете найти в этом списке определенного участника API, расширьте временные рамки.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как настроить пользовательский домен с вашего статического веб-сайта. См [Карта пользовательский домен в azure Blob хранения конечная точка](storage-custom-domain-name.md).

