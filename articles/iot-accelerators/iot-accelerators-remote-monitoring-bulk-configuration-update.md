---
title: Управление устройствами, подключенными к решению для удаленного мониторинга, в пакетном режиме в Azure | Документация Майкрософт
description: В этом руководстве объясняется, как управлять устройствами, подключенными к решению для удаленного мониторинга, в пакетном режиме.
author: aditidugar
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: adugar
ms.openlocfilehash: 8a5c74c76662a089675fcbdcd8d5a7ea54b58fd1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799046"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Руководство по управлению подключенными устройствами в пакетном режиме

В этом руководстве показано, как управлять конфигурацией подключенных устройств в пакетном режиме с помощью акселератора решений для удаленного мониторинга.

Как оператору в компании Contoso вам необходимо настроить группу устройств с помощью новой версии встроенного ПО. Вы не хотите обновлять встроенное ПО на каждом устройстве по отдельности. Чтобы обновить встроенное ПО для группы устройств, можно использовать группы устройств и автоматическое управление устройствами в акселераторе решений для удаленного мониторинга. Любое устройство, добавляемое в группу устройств, получает встроенное ПО последней версии сразу же после подключения устройства к сети.

Изучив это руководство, вы:

>[!div class="checklist"]
> * создадите группу устройств;
> * подготовите и разместите встроенное ПО;
> * создадите конфигурацию устройства на портале Azure;
> * импортируете конфигурацию устройства в решение для удаленного мониторинга;
> * развернете конфигурацию на устройства в группе устройств.
> * Мониторинг развертывания

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам понадобится развернутый экземпляр акселератора решения для удаленного мониторинга в подписке Azure.

Если акселератор решения для удаленного мониторинга еще не развернут, выполните краткое руководство по [развертыванию облачного решения для удаленного мониторинга](quickstart-remote-monitoring-deploy.md).

Для размещения файлов встроенного ПО потребуется учетная запись хранения Azure. Вы можете использовать имеющуюся учетную запись хранения или [создать новую учетную запись хранения](../storage/common/storage-quickstart-create-account.md) в своей подписке.

В этом руководстве используется устройство [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) в качестве примера устройства.

На локальном компьютере необходимо установить следующее программное обеспечение:

* [Visual Studio Code (VS Code)](https://code.visualstudio.com/);
* расширение VS Code [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench).

Перед началом работы:

* Убедитесь, что версия [загрузчика на вашем устройстве IoT DevKit — 1.4.0 или более поздняя](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Убедитесь, что версия пакета SDK для IoT DevKit совпадает с версией загрузчика. Обновить пакет SDK для IoT DevKit можно с помощью Azure IoT Workbench в VS Code. Откройте палитру команд и введите **Arduino: Board Manager** (Диспетчер плат Arduino). Дополнительные сведения см. в разделе [Подготовка среды разработки](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Вам также необходимо подключить по крайней мере одно устройство IoT DevKit к акселератору решений для удаленного мониторинга. Если вы еще не подключили устройство IoT DevKit, обратитесь к статье [Подключение устройства IoT DevKit к акселератору решений удаленного мониторинга](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Перейдите к панели мониторинга.

Чтобы просмотреть панель мониторинга решения для удаленного мониторинга в браузере, перейдите к [акселераторам решений Интернета вещей Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard). Вам может быть предложено войти, используя учетные данные своей подписки Azure.

Нажмите кнопку **Запустить** на плитке акселератора решения для удаленного мониторинга, развернутого с помощью этого [краткого руководства](quickstart-remote-monitoring-deploy.md).

## <a name="create-a-device-group"></a>Создание группы устройств

Устройства должны принадлежать к группе устройств в решении удаленного мониторинга, чтобы в них автоматически обновлялось встроенное ПО:

1. На странице **Устройства** выберите все устройства **IoT DevKit**, подключенные к акселератору решений. Щелкните **Задания**.

1. На панели **Задания** выберите **Теги**, присвойте заданию имя **AddDevKitTag**, а затем добавьте текстовый тег с именем **IsDevKitDevice** и значением **Y**. Нажмите кнопку **Применить**:

1. Теперь значения тегов можно использовать для группы устройств. На странице **Устройства** выберите **Manage device groups** (Управление группами устройств).

1. Создайте текстовый фильтр, который использует имя тега **IsDevKitDevice** и значение **Y** в условии. Сохраните группу устройств как **устройства IoT DevKit**.

Далее в этом руководстве эта группа устройств будет использоваться для применения конфигурации устройства, обновляющей встроенное ПО всех устройств.

## <a name="prepare-and-host-the-firmware"></a>Подготовка и размещение встроенного ПО

Расширение VS Code [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) содержит пример кода устройства для обновления встроенного ПО.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Открытие примера OTA встроенного ПО в VS Code

1. Подключите плату IoT DevKit к компьютеру. Сначала запустите VS Code, а затем подключите плату DevKit к компьютеру.

1. Нажмите клавишу **F1**, чтобы открыть палитру команд, потом введите и выберите **IoT Workbench: Examples** (Примеры IoT Workbench). Затем выберите **IoT DevKit** в качестве платы.

1. Найдите **Firmware OTA** (OTA встроенного ПО) и нажмите кнопку **Open Sample** (Открыть пример). Откроется новое окно VS Code и отобразится папка проекта **firmware_ota**:

    ![IoT Workbench, выбор примера OTA встроенного ПО](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Создание встроенного ПО

Исходная версия встроенного ПО устройства — 1.0.0. Номер версии нового встроенного ПО должен быть выше.

1. В VS Code откройте файл **FirmwareOTA.ino** и измените `currentFirmwareVersion` с `1.0.0` на `1.0.1`:

    ![Изменение версии встроенного ПО](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Откройте палитру команд, а потом введите и выберите **IoT Workbench: Device** (Устройство IoT Workbench). Затем выберите **Device Compile** (Компиляция устройства), чтобы скомпилировать код:

    ![Компиляция устройства](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    VS Code сохраняет скомпилированный файл в папке `.build` проекта. В зависимости от параметров VS Code может скрыть папку `.build` в представлении обозревателя.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Создание значения CRC и расчет размера файла встроенного ПО

1. Откройте палитру команд, а потом введите и выберите **IoT Workbench: Device** (Устройство IoT Workbench). Выберите **Generate CRC** (Создать CRC).

    ![Создание CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. VS Code создает и выводит значение CRC, имя файла встроенного ПО и путь к нему, а также размер файла в окне вывода. Запишите эти значения на будущее:

    ![Сведения о CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Отправка встроенного ПО в облако

Чтобы разместить новый файл встроенного ПО в облаке, воспользуйтесь учетной записью хранения Azure.

1. Войдите в свою учетную запись хранения на портале Azure. В разделе "Службы" выберите **BLOB-объекты**. Создайте общедоступный контейнер с именем **firmware** для хранения файлов встроенного ПО:

    ![Создание папки](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. Чтобы отправить файл встроенного ПО в контейнер, выберите контейнер **firmware** и нажмите кнопку **Отправить**.

1. Выберите **FirmwareOTA.ino.bin**. Вы записали полный путь к этому файлу в предыдущем разделе.

1. После завершения отправки файла встроенного ПО запишите URL-адрес файла.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Создание и отправка исходного встроенного ПО на устройство IoT DevKit

1. В VS Code откройте файл **FirmwareOTA.ino** и измените значение `currentFirmwareVersion` обратно на `1.0.0`:

    ![Версия 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Откройте палитру команд, а потом введите и выберите **IoT Workbench: Device** (Устройство IoT Workbench). Затем выберите **Device Upload** (Отправка устройства):

    ![Отправка устройства](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. VS Code проверяет код и отправляет его на устройство DevKit.

1. По завершении отправки устройство IoT DevKit перезагрузится. После завершения перезагрузки на экране IoT DevKit отображается **версия FW: 1.0.0** и выполнение проверки нового встроенного ПО:

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Создание конфигурации устройства

Конфигурация устройства указывает требуемое состояние устройств. Как правило, разработчик [создает конфигурацию](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) на странице **Конфигурация устройства Интернета вещей** на портале Azure. Конфигурация устройства — это документ JSON, в котором указано требуемое состояние устройств и набор метрик.

Сохраните следующую конфигурацию как файл с именем **firmware-update.json** на локальном компьютере. Замените заполнители `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM` и `YOURPACKAGESIZE` значениями, записанными ранее:

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

Вы используете этот файл конфигурации в следующем разделе.

## <a name="import-a-configuration"></a>Импорт конфигурации

В этом разделе вы импортируете конфигурацию устройства в виде пакета в акселератор решений для удаленного мониторинга. Как правило, эту задачу выполняет оператор.

1. В веб-интерфейсе решения для удаленного мониторинга перейдите на страницу **Пакеты** и щелкните **+ New Package** (+ Новый пакет):

    ![Новый пакет](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. На панели **New Package** (Новый пакет) выберите **Конфигурация устройства** в качестве типа пакета и **Встроенное ПО** в качестве типа конфигурации. Нажмите кнопку **Обзор**, чтобы найти файл **firmware-update.json** на локальном компьютере, и нажмите кнопку **Отправить**:

    ![Отправка пакета](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. В список пакетов теперь входит пакет **firmware-update.json**.

## <a name="deploy-the-configuration-to-your-devices"></a>Развертывание конфигурации на устройстве

В этом разделе вы создадите и выполните развертывание, которое применяет конфигурацию устройств для устройств IoT DevKit.

1. В веб-интерфейсе решения для удаленного мониторинга перейдите на страницу **Развертывания** и щелкните **+ Новое развертывание**:

    ![Новое развертывание](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. На панели **Новое развертывание** создайте развертывание со следующими параметрами:

    |Параметр|Значение|
    |---|---|
    |ИМЯ|Развертывание обновления встроенного ПО|
    |Тип пакета|Конфигурация устройств|
    |Тип конфигурации|Встроенное ПО|
    |Package|firmware-update.json|
    |Группа устройств|Устройства IoT DevKit|
    |Приоритет|10|

    ![Создать развертывание](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Нажмите кнопку **Применить**. Новое развертывание появится на странице **Развертывания**, содержащей следующие метрики:

    * **Целевой** — число устройств в группе устройств.
    * **Применено** — число устройств, в которых обновлено содержимое конфигурации.
    * **Успешно** — число устройств, на которых развертывание выполнено успешно.
    * **Сбой** — число устройств, на которых развертывание завершилось сбоем.

## <a name="monitor-the-deployment"></a>Мониторинг развертывания

Через несколько минут IoT DevKit получит сведения о новом встроенном ПО и начнет его скачивание на устройство:

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

В зависимости от скорости сети скачивание может занять несколько минут. После скачивания встроенного ПО устройство проверяет размер файла и значение CRC. На экране MXChip отображается **passed** (выполнено), если проверка прошла успешно.

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Если проверка выполнена успешно, устройство перезагружается. Вы увидите обратный отсчет от **5** до **0**, прежде чем произойдет перезагрузка.

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

После перезагрузки загрузчик IoT DevKit обновляет встроенное ПО до новой версии. Обновление может занять несколько секунд. На этом этапе светодиодный индикатор RGB на устройстве горит красным цветом и экран пуст.

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

После завершения перезагрузки устройство IoT DevKit теперь работает под управлением версии 1.0.1 встроенного ПО.

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

На странице **Развертывания** щелкните развертывание, чтобы просмотреть сведения о состоянии своих устройств в ходе обновления. Вы увидите сведения о состоянии каждого устройства в вашей группе устройств и пользовательские метрики, которые вы определили.

![Сведения о развертывании](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве показано, как обновить встроенное ПО группы устройств, подключенных к решению. Для обновления устройств в вашем решении используется автоматическое управление устройствами. Дополнительные сведения о возможности автоматического управления устройствами в основном центре Интернета вещей вашего решения см. в статье [Настройка и мониторинг устройств Центра Интернета вещей с помощью портала Azure](../iot-hub/iot-hub-auto-device-config.md).