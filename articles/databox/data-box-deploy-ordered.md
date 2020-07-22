---
title: Учебник по заказу Azure Data Box | Документация Майкрософт
description: Изучите предварительные условия развертывания и порядок заказа Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: cfb95f2fb02544197f9b2796a705844e33eca201
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392491"
---
# <a name="tutorial-order-azure-data-box"></a>Руководство по Заказ Azure Data Box

Azure Data Box — это гибридное решение, которое позволяет импортировать локальные данные в Azure быстрым, простым и надежным способом. Вы переносите свои данные на накопитель с доступной емкостью 80 ТБ, поставляемый Майкрософт, и затем отправляете устройство обратно. Затем эти данные передаются в Azure.

В этом руководстве описано, как заказать Azure Data Box. В этом руководстве вы рассмотрите следующее:

> [!div class="checklist"]
>
> * Необходимые условия для развертывания Data Box
> * Заказ Data Box
> * Отслеживание заказа
> * Отмена заказа

## <a name="prerequisites"></a>Предварительные требования

# <a name="portal"></a>[Портал](#tab/portal)

Перед развертыванием устройства выполните следующие предварительные требования службы и устройства Data Box.

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

Вы можете войти в Azure и выполнить команды Azure CLI одним из двух способов:

* Установить CLI и выполнить команды CLI локально.
* Выполнить команды CLI на портале Azure в Azure Cloud Shell.

Для работы с этим учебником мы используем Azure CLI в Windows PowerShell, но вы можете выбрать любой из этих вариантов.

### <a name="install-the-cli-locally"></a>Установка CLI локально

* Установите [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) версии 2.0.67 или более поздней. Кроме того, вы можете [выполнить установку с помощью MSI](https://aka.ms/installazurecliwindows).

#### <a name="sign-in-to-azure"></a>Вход в Azure

Откройте командное окно Windows PowerShell и войдите в Azure с помощью команды [az login](/cli/azure/reference-index#az-login):

```azurecli
PS C:\Windows> az login
```

Ниже приведены выходные данные успешной операции входа:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

#### <a name="install-the-azure-data-box-cli-extension"></a>Установка расширения Azure Data Box CLI

Прежде чем использовать команды интерфейса командной строки Azure Data Box, вам нужно установить расширение. Расширения Azure CLI предоставляют доступ к экспериментальным командам и предварительным выпускам команд, которые еще не поставлялись как часть основного CLI. Дополнительные сведения о расширениях см. в статье [Использование расширений с Azure CLI](/cli/azure/azure-cli-extensions-overview).

Чтобы установить расширение для Azure Data Box, выполните следующую команду: `az extension add --name databox`:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Если расширение установлено успешно, вы увидите следующие выходные данные:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

### <a name="use-azure-cloud-shell"></a>Использование Azure Cloud Shell

Вы можете применить для выполнения команд CLI размещенную в Azure интерактивную оболочку среды [Azure Cloud Shell](https://shell.azure.com/), с которой можно работать в браузере. Azure Cloud Shell поддерживает для работы со службами Azure оболочки bash и Windows PowerShell. Предварительно установленный интерфейс Azure CLI настроен для использования с вашей учетной записью. Нажмите кнопку Cloud Shell в меню в правом верхнем углу окна портала Azure:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Эта кнопка запускает интерактивную оболочку, с помощью которой можно выполнять действия, описанные в этой статье.

<!-- To start Azure Cloud Shell:

| Option | Example/Link |
|-----------------------------------------------|---|
| Select **Try It** in the upper-right corner of a code block. Selecting **Try It** doesn't automatically copy the code to Cloud Shell. | ![Example of Try It for Azure Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Go to [https://shell.azure.com](https://shell.azure.com), or select the **Launch Cloud Shell** button to open Cloud Shell in your browser. | [![Launch Cloud Shell in a new window](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Select the **Cloud Shell** button on the menu bar at the upper right in the [Azure portal](https://portal.azure.com). | ![Cloud Shell button in the Azure portal](../../includes/media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

To run the code in this article in Azure Cloud Shell:

1. Start Cloud Shell.

2. Select the **Copy** button on a code block to copy the code.

3. Paste the code into the Cloud Shell session by selecting **Ctrl**+**Shift**+**V** on Windows and Linux or by selecting **Cmd**+**Shift**+**V** on macOS.

4. Select **Enter** to run the code.

For this tutorial, we use Windows PowerShell command prompt to run Azure CLI commands. -->

<!-- This goes away, we'll show this later when we show how to order a Data Box. -->
<!-- ## Change the output format type

All Azure CLI commands will use json as the output format by default unless you change it. You can change the output format by using the global parameter `--output <output-format>`. -->

<!-- ```azurecli

az databox job <command> --output <output-format>

```

Azure Data Box CLI commands support the following output formats:

* json (default setting)
* jsonc
* table
* tsv
* yaml
* yamlc
* none

You can use the parameter `--output` with all Azure Data Box CLI commands. -->

<!-- To set the output format to yaml: -->

<!-- ```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "yaml"

``` -->
<!-- 
To set the out format to tabular form (easier to read):

```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "table"

``` -->

<!-- Here's the example output of `az databox job show` after changing the output format to table:

```azurecli
PS C:\WINDOWS\system32> az databox job show --resource-group "GDPTest" --name "mydataboxtest3" --output "table"
Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.

DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
--------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
NonScheduled    True             True                       False          True                         westus      mydataboxorder  myresourcegroup          2020-06-11T22:05:49.436622+00:00  DeviceOrdered

``` -->

---

## <a name="order-data-box"></a>Оформление заказа Data Box

# <a name="portal"></a>[Портал](#tab/portal)

Чтобы заказать устройство, выполните следующие шаги на портале Azure.

1. Используйте учетные данные Microsoft Azure для входа по этому URL-адресу: [https://portal.azure.com](https://portal.azure.com).
2. Щелкните **+ Create a resource** (+ Создать ресурс) и выполните поиск *Azure Data Box*. Щелкните **Azure Data Box**.

   [![Найдите Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Нажмите кнопку **Создать**.

4. Проверьте, доступен ли сервис Data Box в вашем регионе. Введите или выберите следующие сведения и нажмите кнопку **Применить**.

    |Параметр  |Значение  |
    |---------|---------|
    |Подписка     | Выберите для использования службы Data Box подписку EA, CSP или "Спонсорское предложение Azure". <br> Подписка привязана к учетной записи для выставления счетов.       |
    |Тип передачи     | Выберите **Импорт в Azure**.        |
    |Страна или регион отправки    |    Выберите страну/регион, в которой находятся ваши данные.         |
    |Конечный регион Azure     |     Выберите регион Azure, куда вы хотите передать данные.        |

5. Выберите **Data Box**. Максимальная полезная емкость для одного заказа составляет 80 ТБ. Для больших размеров данных можно создать несколько заказов.

      [![Выберите Data Box вариант 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. В разделе **Заказ** укажите **Сведения о заказе**. Введите или выберите следующие сведения и нажмите кнопку **Далее**.

    |Параметр  |Значение  |
    |---------|---------|
    |Имя     |  Введите понятное имя для отслеживания заказа. <br> Имя может содержать от 3 до 24 знаков, среди которых могут быть буквы, цифры и дефисы. <br> Имя должно начинаться и заканчиваться буквой или цифрой.      |
    |Группа ресурсов     |    Используйте имеющуюся или создайте новую. <br> Группа ресурсов — это логический контейнер для ресурсов, которые могут управляться или развертываться вместе.         |
    |Конечный регион Azure     | Выберите регион для учетной записи хранения. <br> Дополнительные сведения см. в разделе о [доступности по регионам](data-box-overview.md#region-availability).        |
    |Целевое хранилище     | Выберите из учетной записи хранения и/или управляемых дисков. <br> Исходя из заданного региона Azure, выберите в отфильтрованном списке существующую учетную запись хранения. Data Box можно связать с 10 учетными записями хранения. <br> Вы также можете создать новую **Учетную запись общего назначения версии 1**, **Учетную запись общего назначения версии 2** или **Учетную запись хранения BLOB-объекта**. <br>Поддерживаются учетные записи хранения в виртуальных сетях. Чтобы служба Data Box могла работать с защищенными учетными записями хранения, включите доверенные службы в настройках сетевого брандмауэра учетной записи хранения. Дополнительные сведения см. в статье о [добавлении Azure Data Box в качестве доверенной службы](../storage/common/storage-network-security.md#exceptions).|

    При использовании учетной записи хранения в качестве целевого хранилища, вы увидите следующий снимок экрана.

    ![Заказ Data Box для учетной записи хранения](media/data-box-deploy-ordered/order-storage-account.png)

    При использовании Data Box для создания управляемых дисков на основе локальных виртуальных жестких дисков (VHD) необходимо будет также указать следующие сведения:

    |Параметр  |Значение  |
    |---------|---------|
    |Группы ресурсов     | Если вы планируете создать управляемые диски из локальных жестких дисков, создайте новые группы ресурсов. Вы можете использовать существующую группу ресурсов только в том случае, если она была создана ранее при создании заказа Data Box для управляемого диска службой Data Box. <br> Укажите несколько групп ресурсов, разделив их точкой с запятой. Поддерживается максимум 10 групп ресурсов.|

    ![Заказ Data Box для управляемого диска](media/data-box-deploy-ordered/order-managed-disks.png)

    Учетная запись хранения, указанная для управляемых дисков, используется в качестве промежуточной учетной записи хранения. Служба Data Box выгружает виртуальные жесткие диски в виде страничных BLOB-объектов в промежуточную учетную запись хранения, а затем преобразовывает их в управляемые диски и перемещает в группы ресурсов. Дополнительные сведения см. в разделе [Проверка передачи данных в Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. Во вкладке **Адрес доставки** укажите свое имя и фамилию, название и почтовый адрес компании и действительный номер телефона. Щелкните **Проверить адрес**. Данная служба проверяет адрес доставки на доступность услуг. Если служба доступна для указанного адреса доставки, вы получите соответствующее уведомление.

   Если вы выбрали самоуправляемую доставку, после успешного размещения заказа вы получите уведомление по электронной почте. Дополнительные сведения см. в статье [об использовании самостоятельной доставки](data-box-portal-customer-managed-shipping.md).

8. После успешной проверки сведений о доставке нажмите кнопку **Далее**.

9. В разделе **Сведения в уведомлении** укажите адрес электронной почты. Служба отправляет уведомления по электронной почте об обновлениях состояния заказа на указанные адреса электронной почты.

    Рекомендуется использовать групповой адрес электронной почты, чтобы продолжить получать уведомления после ухода администратора группы.

10. Просмотрите сведения в разделе **Сводка**, относящиеся к заказу, контактам, уведомлениям и условиям конфиденциальности. Установите флажок, соответствующий соглашению с условиями конфиденциальности.

11. Щелкните **Заказать**. Создание заказа может занять несколько минут.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы заказать устройство, выполните следующие действия в Azure CLI:

1. Запишите параметры заказа Data Box. Эти параметры содержат личные данные или сведения об организации, имя подписки, сведения об устройстве и доставке. Эти сведения нужно указать в качестве параметров при выполнении команды CLI, которая создает заказ на Data Box. В следующей таблице приведены значения параметров, примененные для `az databox job create`.

   | Настройка (параметр) | Описание |  Образец значения |
   |---|---|---|
   |resource-group| Используйте имеющуюся или создайте новую. Группа ресурсов — это логический контейнер для ресурсов, которые могут управляться или развертываться вместе. | "myresourcegroup"|
   |name| Имя заказа, который вы создаете. | "mydataboxorder"|
   |contact-name| Имя, связанное с указанным адресом доставки. | "Gus Poland"|
   |phone| Номер телефона сотрудника или организации, которые получат этот заказ.| "14255551234"
   |location| Ближайший к вам регион Azure, из которого будет доставлено устройство.| "US West"|
   |sku| Конкретное устройство Data Box, которое вы заказываете. Допустимые значения: "DataBox", "DataBoxDisk" и "DataBoxHeavy"| "DataBox" |
   |email-list| Адреса электронной почты, связанные с этим заказом.| "gusp@contoso.com" |
   |street-address1| Физический адрес, по которому будет отправлен заказ. | "15700 NE 39th St" |
   |street-address2| Дополнительные сведения об адресе, например номер корпуса или квартиры. | "Bld 123" |
   |city| Город, в который будет отправлено устройство. | "Redmond" |
   |state-or-province| Штат или регион, в который будет отправлено устройство.| "WA" |
   |country| Страна, в которую будет отправлено устройство. | "Соединенные Штаты" |
   |postal-code| Почтовый код или индекс адреса доставки.| "98052"|
   |company-name| Название компании, в которой вы работаете.| "Contoso, LTD" |
   |запись хранения Azure| Учетная запись хранения Azure, из которой вы будете импортировать данные.| "mystorageaccount"|
   |debug| Включение отладочной информации для подробного ведения журнала.  | --debug |
   |help| Отображение справочной информации по этой команде. | --help -h |
   |only-show-errors| Показывать только ошибки, блокируя предупреждения. | --only-show-errors |
   |--output -o| Формат выходных данных.  Допустимые значения: json, jsonc, none, table, tsv, yaml, yamlc. По умолчанию используется значение json. | --output "json" |
   |query| Строка запроса JMESPath. Дополнительные сведения см. в [документации по JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Включение подробного ведения журнала. | --verbose |

2. В выбранном сеансе командной строки или терминале введите [az databox job create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create), чтобы создать заказ на Azure Data Box.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Ниже приведен пример применения этой команды:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Ниже представлен результат выполнения этой команды:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Все команды Azure CLI по умолчанию будут использовать JSON в качестве выходного формата, если вы не измените его. Формат вывода можно изменить с помощью глобального параметра `--output <output-format>`. Если вы укажете формат "table", выходные данные будут более удобными для чтения.

   Вот та же команда, для которой мы немного изменили параметры форматирования:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Ниже представлен результат выполнения этой команды:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

---

## <a name="track-the-order"></a>Отслеживание заказа

# <a name="portal"></a>[Портал](#tab/portal)

После размещения заказа можно отслеживать его состояние на портале Azure. Перейдите к заказу Data Box, а затем щелкните **Обзор** для просмотра состояния. Заказ на портале отображается в состоянии **Заказано**.

Если устройство недоступно, вы получите уведомление. Если устройство доступно, корпорация Майкрософт определяет устройство для поставки и подготавливает отгрузку. Во время подготовки устройства выполняются следующие действия.

* Для каждой учетной записи хранилища создаются общие ресурсы SMB, связанные с устройством.
* Для каждого общего ресурса создаются учетные данные доступа, такие как имя пользователя и пароль.
* Также создается пароль устройства, который позволяет его разблокировать.
* Устройство Data Box заблокировано для предотвращения несанкционированного доступа к устройству.

После завершения подготовки устройства на портале отображается состояние заказа — **Обработано**.

![Порядок обработки заказа Data Box](media/data-box-overview/data-box-order-status-processed.png)

Майкрософт подготавливает и отправляет диски с помощью регионального поставщика. Когда устройство будет отправлено, вы получите номер для отслеживания. На портале отобразится состояние заказа — **Отправлено**.

![Порядок отправки Data Box](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>Отслеживание одного заказа

Чтобы получить сведения об одном уже существующем заказе на Azure Data Box, выполните команду [az databox job show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show). Эта команда отображает различные сведения о заказе, в том числе следующие: имя, группа ресурсов, сведения для отслеживания, идентификатор подписки, контактная информация, тип доставки и ценовая категория устройства.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   В следующей таблице приведены значения параметров для `az databox job show`.

   | Параметр | Описание |  Образец значения |
   |---|---|---|
   |resource-group [обязательный]| Имя группы ресурсов, связанной с этим заказом. Группа ресурсов — это логический контейнер для ресурсов, которые могут управляться или развертываться вместе. | "myresourcegroup"|
   |name [обязательный]| Отображаемое имя для заказа. | "mydataboxorder"|
   |debug| Включение отладочной информации для подробного ведения журнала. | --debug |
   |help| Отображение справочной информации по этой команде. | --help -h |
   |only-show-errors| Показывать только ошибки, блокируя предупреждения. | --only-show-errors |
   |--output -o| Формат выходных данных.  Допустимые значения: json, jsonc, none, table, tsv, yaml, yamlc. По умолчанию используется значение json. | --output "json" |
   |query| Строка запроса JMESPath. Дополнительные сведения см. в [документации по JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Включение подробного ведения журнала. | --verbose |

   Ниже приведен пример команды, для которой выходной формат имеет значение table:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Ниже представлен результат выполнения этой команды:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> Упорядочение списка может поддерживаться на уровне подписки, и в этом случае группа ресурсов становится необязательным параметром.

### <a name="list-all-orders"></a>Список всех заказов

Если вы заказали несколько устройств, с помощью команды [az databox job list](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list) можно просмотреть все заказы на Azure Data Box. Эта команда выводит список всех заказов, которые относятся к определенной группе ресурсов. В выходные данные также включаются следующие сведения: имя заказа, состояние доставки, регион Azure, тип доставки, состояние заказа. Отмененные заказы также включаются в список.
Среди прочего, эта команда отображает для каждого заказа отметки времени.

```azurecli
az databox job list --resource-group <resource-group>
```

В следующей таблице приведены значения параметров для `az databox job list`.

   | Параметр | Описание |  Образец значения |
   |---|---|---|
   |resource-group [обязательный]| Имя группы ресурсов, в которой содержатся заказы. Группа ресурсов — это логический контейнер для ресурсов, которые могут управляться или развертываться вместе. | "myresourcegroup"|
   |debug| Включение отладочной информации для подробного ведения журнала. | --debug |
   |help| Отображение справочной информации по этой команде. | --help -h |
   |only-show-errors| Показывать только ошибки, блокируя предупреждения. | --only-show-errors |
   |--output -o| Формат выходных данных.  Допустимые значения: json, jsonc, none, table, tsv, yaml, yamlc. По умолчанию используется значение json. | --output "json" |
   |query| Строка запроса JMESPath. Дополнительные сведения см. в [документации по JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Включение подробного ведения журнала. | --verbose |

   Ниже приведен пример команды, для которой выходной формат имеет значение table:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Ниже представлен результат выполнения этой команды:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        GDPTest          2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  GDPTest          2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       GDPTest          2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       GDPTest          2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       GDPTest          2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

---

## <a name="cancel-the-order"></a>Отмена заказа

# <a name="portal"></a>[Портал](#tab/portal)

Чтобы отменить заказ, на портале Azure перейдите к вкладке **Обзор** и выберите **Отменить** на панели команд.

После размещения заказа его можно отменить в любой момент, пока состояние заказа не будет отмечено как "Обработано".

Чтобы удалить отмененный заказ, перейдите к вкладке **Обзор** и выберите **Удалить** на панели команд.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>Отмена заказа

Чтобы отменить заказ на Azure Data Box, выполните команду [az databox job cancel](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel). Вам придется указать причину отмены заказа.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   В следующей таблице приведены значения параметров для `az databox job cancel`.

   | Параметр | Описание |  Образец значения |
   |---|---|---|
   |resource-group [обязательный]| Имя группы ресурсов, связанной с удаляемым заказом. Группа ресурсов — это логический контейнер для ресурсов, которые могут управляться или развертываться вместе. | "myresourcegroup"|
   |name [обязательный]| Имя удаляемого заказа. | "mydataboxorder"|
   |reason [обязательный]| Причина для отмены заказа. | "I entered erroneous information and needed to cancel the order." (Заказ пришлось отменить из-за ошибки при вводе информации). |
   |да| Не запрашивать подтверждение. | --yes (-y)| --yes -y |
   |debug| Включение отладочной информации для подробного ведения журнала. | --debug |
   |help| Отображение справочной информации по этой команде. | --help -h |
   |only-show-errors| Показывать только ошибки, блокируя предупреждения. | --only-show-errors |
   |--output -o| Формат выходных данных.  Допустимые значения: json, jsonc, none, table, tsv, yaml, yamlc. По умолчанию используется значение json. | --output "json" |
   |query| Строка запроса JMESPath. Дополнительные сведения см. в [документации по JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Включение подробного ведения журнала. | --verbose |

   Ниже представлен пример такой команды с результатом ее выполнения:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Ниже представлен результат выполнения этой команды:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Удаление заказа

Если вы отменили заказ на Azure Data Box, с помощью команды [az databox job delete](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete) вы можете удалить этот заказ.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   В следующей таблице приведены значения параметров для `az databox job delete`.

   | Параметр | Описание |  Образец значения |
   |---|---|---|
   |resource-group [обязательный]| Имя группы ресурсов, связанной с удаляемым заказом. Группа ресурсов — это логический контейнер для ресурсов, которые могут управляться или развертываться вместе. | "myresourcegroup"|
   |name [обязательный]| Имя удаляемого заказа. | "mydataboxorder"|
   |Подписка| Имя или идентификатор (GUID) подписки Azure. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |да| Не запрашивать подтверждение. | --yes (-y)| --yes -y |
   |debug| Включение отладочной информации для подробного ведения журнала. | --debug |
   |help| Отображение справочной информации по этой команде. | --help -h |
   |only-show-errors| Показывать только ошибки, блокируя предупреждения. | --only-show-errors |
   |--output -o| Формат выходных данных.  Допустимые значения: json, jsonc, none, table, tsv, yaml, yamlc. По умолчанию используется значение json. | --output "json" |
   |query| Строка запроса JMESPath. Дополнительные сведения см. в [документации по JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Включение подробного ведения журнала. | --verbose |

Ниже представлен пример такой команды с результатом ее выполнения:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Ниже представлен результат выполнения этой команды:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

---

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве освещены следующие темы, касающиеся Azure Data Box:

> [!div class="checklist"]
>
> * Необходимые условия для развертывания Data Box
> * Оформление заказа Data Box
> * Отслеживание заказа
> * Отмена заказа

Перейдите к следующему руководству, чтобы узнать, как настроить Data Box.

> [!div class="nextstepaction"]
> [Руководство. Распаковка, подключение и разблокировка диска Azure Data Box](./data-box-deploy-set-up.md)
