---
title: Получение доступа к хранилищу BLOB-объектов из Azure Databricks с использованием хранилища ключей
description: В этом учебнике описано, как получить доступ к хранилищу BLOB-объектов Azure из Azure Databricks, используя секреты, хранящиеся в хранилище ключей.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 7983e18066578e3f036da84c73b6554ead2c40a1
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791572"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Руководство по Доступ к хранилищу BLOB-объектов Azure из Azure Databricks с помощью Azure Key Vault

В этом учебнике описано, как получить доступ к хранилищу BLOB-объектов Azure из Azure Databricks, используя секреты, хранящиеся в хранилище ключей.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание контейнера BLOB-объектов и учетной записи хранения.
> * Создание Azure Key Vault и добавление секрета.
> * Создание рабочей области Azure Databricks и области секретов.
> * Получение доступа к контейнеру BLOB-объектов из Azure Databricks.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com/).

> [!Note]
> Инструкции из этого руководство нельзя выполнять с **бесплатной пробной версией подписки**.
> Если у вас есть бесплатная учетная запись, перейдите к профилю и измените подписку на подписку с **оплатой по мере использования**. Дополнительные сведения см. на странице [создания бесплатной учетной записи Azure](https://azure.microsoft.com/free/). Затем [удалите предельную сумму расходов](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit) и [запросите увеличение квоты](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) на ЦП в своем регионе. При создании рабочей области Azure Databricks можно выбрать ценовую категорию **Пробная версия ("Премиум" — 14 дней бесплатно (DBU))** для предоставления рабочей области доступа к бесплатным DBU Azure Databricks уровня "Премиум" на 14 дней.

## <a name="create-a-storage-account-and-blob-container"></a>Создание контейнера BLOB-объектов и учетной записи хранения.

1. Выберите **Создать ресурс** > **Хранилище** на портале Azure. Выберите **учетную запись хранения**.

   ![Поиск ресурса учетной записи хранения Azure](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Выберите свою подписку и группу ресурсов или создайте группу ресурсов. Затем введите имя учетной записи хранения и выберите расположение. Выберите **Review + Create** (Просмотреть и создать).

   ![Настройка свойств учетной записи хранения.](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Если проверка не удалась, устраните проблемы и попробуйте снова. Если проверка прошла успешно, выберите **Создать** и дождитесь создания учетной записи хранения.

4. Перейдите к созданной учетной записи хранения и выберите **BLOB-объекты** в разделе **Службы** на странице **Обзор**. Щелкните **+ Container** (+ Контейнер) и введите имя контейнера. Нажмите кнопку **ОК**.

   ![Создание контейнера](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Найдите файл, который вы хотите отправить в контейнер хранилища BLOB-объектов. Если у вас нет файла, используйте текстовый редактор, чтобы создать текстовый файл с некоторыми данными. В этом примере файл с именем **hw.txt** содержит текст Hello World. Сохраните текстовый файл локально и отправьте его в контейнер хранилища BLOB-объектов.

   ![Отправка файла в контейнер](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Вернитесь в свою учетную запись хранения и выберите **Ключи доступа** в разделе **Параметры**. Скопируйте **имя учетной записи хранения** и **ключ 1** в текстовый редактор для дальнейшего использования в этом учебнике.

   ![Ключи доступа к учетной записи хранения](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Создание Azure Key Vault и добавление секрета

1. На портале Azure перейдите на страницу **Создать ресурс** и введите **Key Vault** в строке поиска.

   ![Поле поиска создания ресурсов Azure](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. Ресурс Key Vault выбирается автоматически. Нажмите кнопку **Создать**.

   ![Создание ресурса хранилища ключей](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. На странице **Создать Key Vault** введите следующую информацию и оставьте значения по умолчанию для остальных полей:

   |Свойство|Описание|
   |--------|-----------|
   |ИМЯ|Укажите уникальное имя для хранилища ключей.|
   |Subscription|Выберите подписку.|
   |группа ресурсов.|Выберите группу ресурсов или создайте новую.|
   |Location|Выберите расположение.|

   ![Свойства хранилища ключей Azure](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Указав приведенные выше сведения, выберите **Создать**. 

4. Перейдите к созданному хранилищу ключей на портале Azure и выберите **Секреты**. Выберите **+ Generate/Import** (+ Создать или импортировать). 

   ![Создание секрета хранилища ключей](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. На странице **Создание секрета** введите следующую информацию и оставьте значения по умолчанию для остальных полей:

   |Свойство|Значение|
   |--------|-----------|
   |Параметры отправки|Руководство|
   |ИМЯ|Понятное имя для вашего ключа учетной записи хранения.|
   |Значение|key1 из вашей учетной записи хранения.|

   ![Свойства нового секрета хранилища ключей](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Сохраните имя ключа в текстовом редакторе для последующего использования в этом учебнике и выберите **Создать**. Перейдите на вкладку **Свойства**. Скопируйте **имя DNS** и **идентификатор ресурса** в текстовый редактор для использования в дальнейшем в учебнике.

   ![Копирование DNS-имени Azure Key Vault и идентификатора ресурса](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Создание рабочей области Azure Databricks и области секретов

1. На портале Azure выберите **Создать ресурс** > **Analytics** > **Azure Databricks**.

    ![Databricks на портале Azure](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. В разделе **Служба Azure Databricks** укажите следующие значения, чтобы создать рабочую область Databricks:

   |Свойство  |ОПИСАНИЕ  |
   |---------|---------|
   |имя рабочей области.     | Укажите имя рабочей области Databricks.        |
   |Subscription     | Выберите подписку Azure в раскрывающемся списке.        |
   |группа ресурсов.     | Выберите группу ресурсов, которая содержит нужное хранилище ключей. |
   |Location     | Предназначено для выбора расположения, идентичного тому, где находится Azure Key Vault. Другие доступные регионы см. в статье о [доступности служб Azure по регионам](https://azure.microsoft.com/regions/services/).        |
   |Ценовая категория     |  Вы можете выбрать уровень **Стандартный** или **Премиум**. Дополнительные сведения об этих ценовых категориях см. на [странице цен на Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Свойства рабочей области Databricks](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Нажмите кнопку **Создать**.

3. На портале Azure перейдите к службе Azure Databricks, а затем выберите **Launch Workspace** (Запустить рабочую область).

   ![Создание рабочей области Azure Databricks](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Как только ваше рабочее пространство Azure Databricks откроется в отдельном окне, добавьте **#secrets/createScope** к URL-адресу. URL должен иметь следующий формат: 

   **https://<\location>.azuredatabricks.net/?o=<\id>#secrets/createScope**.

5. Введите имя области, а также DNS-имя Azure Key Vault и идентификатор ресурса, сохраненный ранее. Сохраните имя области в текстовом редакторе для последующего использования в этом учебнике. Затем выберите **Создать**.

   ![Создание области секретов в рабочей области Azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Получение доступа к контейнеру BLOB-объектов из Azure Databricks

1. На домашней странице рабочей области Azure Databricks выберите **Создать кластер** в разделе **Общие задачи**.

   ![Создание записной книжки в Azure Databricks](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Введите имя кластера и выберите **Создать кластер**. Создание кластера займет несколько минут.

3. После создания кластера на домашней странице рабочей области Azure Databricks выберите **New Notebook** (Новая записная книжка) в разделе **Общие задачи**.

   ![Создание записной книжки в Azure Databricks](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Введите имя записной книжки и установите язык Python. Задайте имя кластера, созданного на предыдущем шаге.

5. Выполните следующую команду, чтобы смонтировать контейнер хранения BLOB-объектов. Измените значения для следующих свойств:

   * your-container-name;
   * your-storage-account-name;
   * mount-name;
   * config-key;
   * scope-name;
   * key-name.

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **mount-name** — это путь DBFS, представляющий, куда будет смонтирован контейнер хранилища BLOB-объектов или папка внутри контейнера (указана в источнике).
   * Значение **conf-key** может быть `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` или `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`.
   * **scope-name** — это имя области секрета, созданного при работе с предыдущим разделом. 
   * **key-name** — это имя секрета, который вы создали для ключа учетной записи хранения в своем хранилище ключей.

   ![Создание подключения хранилища BLOB-объектов в записной книжке](./media/store-secrets-azure-key-vault/command1.png)

6. Выполните следующую команду, чтобы прочитать текстовый файл в контейнере хранилища BLOB-объектов в кадр данных. Измените значения в команде, чтобы они соответствовали вашему имени монтирования и имени файла.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Чтение файла в кадре данных](./media/store-secrets-azure-key-vault/command2.png)

7. Используйте следующую команду, чтобы отобразить содержимое файла:

   ```python
   df.show()
   ```
   ![Отображение кадра данных](./media/store-secrets-azure-key-vault/command3.png)

8. Выполните следующую команду, чтобы смонтировать хранилище BLOB-объектов.

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Монтирование учетной записи хранения](./media/store-secrets-azure-key-vault/command4.png)

9. Обратите внимание, что после отключения монтирования вы больше не можете читать из своей учетной записи хранения BLOB-объектов.

   ![Ошибка при отключении учетной записи хранения](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите всю группу ресурсов, выполнив следующие действия.

1. На портале Azure в меню слева щелкните **Группы ресурсов**, а затем выберите группу ресурсов.

2. Выберите **Удалить группу ресурсов** и введите имя своей группы ресурсов. Теперь щелкните **Удалить**. 

## <a name="next-steps"></a>Дополнительная информация

В следующей статье описывается, как реализовать среду Databricks, внедренную в виртуальную сеть, с помощью конечной точки службы с поддержкой Cosmos DB.
> [!div class="nextstepaction"]
> [Руководство. Внедрение Azure Databricks с помощью конечной точки Cosmos DB](service-endpoint-cosmosdb.md)
