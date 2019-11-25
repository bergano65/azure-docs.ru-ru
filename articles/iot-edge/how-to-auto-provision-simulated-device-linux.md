---
title: Automatically provision Linux devices with DPS - Azure IoT Edge | Microsoft Docs
description: Использование имитированного доверенного платформенного модуля на виртуальной машине Linux для тестирования службы подготовки устройств для Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 292ae570d4f2ddd0c09e667860ee2ba01b9fc6b8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457169"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Create and provision an IoT Edge device with a virtual TPM on a Linux virtual machine

Azure IoT Edge devices can be automatically provisioned using the [Device Provisioning Service](../iot-dps/index.yml). При необходимости ознакомьтесь с [процессом автоматической подготовки](../iot-dps/concepts-auto-provisioning.md), прежде чем продолжить.

This article shows you how to test auto-provisioning on a simulated IoT Edge device with the following steps:

* Создание виртуальной машины Linux в Hyper-V с имитированным доверенным платформенным модулем (TPM) для обеспечения безопасности оборудования.
* Создание экземпляра Службы подготовки устройств к добавлению в Центр Интернета вещей.
* Создание отдельной регистрации устройства
* Установка среды выполнения IoT Edge и подключение устройства к Центру Интернета вещей

> [!NOTE]
> TPM 2.0 is required when using TPM attestation with DPS and can only be used to create individual, not group, enrollments.

> [!TIP]
> This article describes how to test DPS provisioning using a TPM simulator, but much of it applies to physical TPM hardware such as the [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), an Azure Certified for IoT device.
>
> If you're using a physical device, you can skip ahead to the [Retrieve provisioning information from a physical device](#retrieve-provisioning-information-from-a-physical-device) section in this article.

## <a name="prerequisites"></a>Технические условия

* Компьютер для разработки с ОС Windows с [включенным Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). В этой статье используется виртуальная машина Ubuntu Server, запущенная в Windows 10.
* Действующий Центр Интернета вещей.
* If using a simulated TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 or later with the ['Desktop development with C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) workload enabled.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Создание виртуальной машины Linux с помощью виртуального доверенного платформенного модуля (TPM)

In this section, you create a new Linux virtual machine on Hyper-V. You configured this virtual machine with a simulated TPM so that you can use it for testing how automatic provisioning works with IoT Edge. 

### <a name="create-a-virtual-switch"></a>Создание виртуального коммутатора

Виртуальный коммутатор позволяет виртуальной машине подключаться к физической сети.

1. Open Hyper-V Manager on your Windows machine. 

2. В меню **Действия** выберите **Диспетчер виртуальных коммутаторов**. 

3. Выберите **Внешний** виртуальный коммутатор, а затем выберите **Создать виртуальный коммутатор**. 

4. Назовите новый виртуальный коммутатор, например **EdgeSwitch**. Убедитесь, что выбранный тип соединения имеет значение **Внешняя сеть**, а затем выберите **OK**.

5. Всплывающее окно предупредит, что сетевое подключение может быть разорвано. Чтобы продолжить, нажмите кнопку **Да**. 

В случае возникновения ошибок при создании нового виртуального коммутатора, убедитесь, что другие коммутаторы не используют адаптер Ethernet и что для других коммутаторов не используется то же самое имя. 

### <a name="create-virtual-machine"></a>Создание виртуальной машины

1. Скачайте и сохраните локально файл образа диска для виртуальной машины. Например, [Ubuntu server](https://www.ubuntu.com/download/server). 

2. In Hyper-V Manager again, select **New** > **Virtual Machine** in the **Actions** menu.

3. В ходе работы **Мастера создания виртуальной машины** укажите следующие настройки.

   1. **Укажите поколение**. Выберите **Generation 2**. Generation 2 virtual machines have nested virtualization enabled, which is required to run IoT Edge on a virtual machine.
   2. **Настройка сети**. В параметре **Подключение** укажите виртуальный коммутатор, созданный в предыдущем разделе. 
   3. **Варианты установки**. Выберите **Установить операционную систему из файла загрузочного образа** и укажите путь к сохраненному файлу образа диска.

4. Select **Finish** in the wizard to create the virtual machine.

Создание виртуальной машины может занять несколько минут. 

### <a name="enable-virtual-tpm"></a>Включение виртуального доверенного платформенного модуля

Once your VM is created, open its settings to enable the virtual trusted platform module (TPM) that lets you auto-provision the device.

1. Select the virtual machine, then open its **Settings**.

2. Перейдите в раздел **Безопасность**. 

3. Снимите флажок **Включить безопасную загрузку**.

4. Поставьте флажок **Включить доверенный платформенный модуль**. 

5. Последовательно выберите **ОК**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Запуск виртуальной машины и сбор данных доверенного платформенного модуля

In the virtual machine, build a tool that you can use to retrieve the device's **Registration ID** and **Endorsement key**.

1. Start your virtual machine and connect to it.

1. Follow the prompts within the virtual machine to finish the installation process and reboot the machine.

1. Sign in to your VM, then follow the steps in [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) to install and build the Azure IoT device SDK for C.

   >[!TIP]
   >In the course of this article, you'll copy to and paste from the virtual machine, which is not easy through the Hyper-V Manager connection application. You may want to connect to the virtual machine through Hyper-V Manager once to retrieve its IP address: `ifconfig`. Then, you can use the IP address to connect through SSH: `ssh <username>@<ipaddress>`.

1. Run the following commands to build the SDK tool that retrieves your device provisioning information from the TPM simulator.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. From a command window, navigate to the `azure-iot-sdk-c` directory and run the TPM simulator. Он ожидает передачи данных через сокет на портах 2321 и 2322. Do not close this command window; you will need to keep this simulator running.

   From the `azure-iot-sdk-c` directory, run the following command to start the simulator:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Using Visual Studio, open the solution generated in the `cmake` directory named `azure_iot_sdks.sln`, and build it using the **Build solution** command on the **Build** menu.

1. На панели **обозревателя решений** в Visual Studio перейдите в папку **Provision\_Tools**. Щелкните проект **tpm_device_provision** правой кнопкой мыши и выберите параметр **Назначить запускаемым проектом**.

1. Run the solution using either of the **Start** commands on the **Debug** menu. The output window displays the TPM simulator's **Registration ID** and the **Endorsement key**, which you should copy for use later when you create an individual enrollment for your device in You can close this window (with Registration ID and Endorsement key), but leave the TPM simulator window running.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Retrieve provisioning information from a physical device

On your device, build a tool that you can use to retrieve the device's provisioning information.

1. Follow the steps in [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) to install and build the Azure IoT device SDK for C.

1. Run the following commands to build the SDK tool that retrieves your device provisioning information from the TPM device.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copy the values for **Registration ID** and **Endorsement key**. Эти значения могут быть использованы при создании отдельной регистрации устройства в Службы подготовки устройств к добавлению в Центр Интернета вещей.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Настройка Службы подготовки устройств к добавлению в Центр Интернета вещей

Создайте экземпляр Службы подготовки устройств к добавлению в Центр Интернета вещей в Azure и свяжите его со своим Центром Интернета вещей. Следуйте инструкциям по [настройке Службы подготовки устройств к добавлению в Центр Интернета вещей на портале Azure](../iot-dps/quick-setup-auto-provision.md).

После запуска Службы подготовки устройств к добавлению в Центр Интернета вещей скопируйте значение **Область идентификатора** на странице обзора. Это значение используется при настройке среды выполнения IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Создание регистрации в Службе подготовки устройств к добавлению в Центр Интернета вещей

Получите сведения о подготовке из вашей виртуальной машины и используйте их, чтобы создать отдельную регистрацию в Службе подготовки устройств к добавлению в Центр Интернета вещей. 

При регистрации в Службе подготовки устройств к добавлению в Центр Интернета вещей есть возможность объявить **Первоначальное состояние двойника устройства**. В двойнике устройства можно задать теги для группировки устройств по любой требуемой для решения метрике, например по региону, среде, расположению или типу устройства. Эти теги используются для создания [автоматических развертываний](how-to-deploy-monitor.md). 

1. In the [Azure portal](https://portal.azure.com), navigate to your instance of IoT Hub Device Provisioning Service. 

2. В разделе **Параметры**выберите **Управление регистрациями**. 

3. Выберите **Добавить отдельную регистрацию** и выполните следующие действия для настройки регистрации.  

   1. Для параметра **Механизм** выберите **TPM**. 

   2. Provide the **Endorsement key** and **Registration ID** that you copied from your virtual machine.

      > [!TIP]
      > If you're using a physical TPM device, you need to determine the **Endorsement key**, which is unique to each TPM chip and is obtained from the TPM chip manufacturer associated with it. You can derive a unique **Registration ID** for your TPM device by, for example, creating an SHA-256 hash of the endorsement key.

   3. Select **True** to declare that this virtual machine is an IoT Edge device. 

   4. Выберите связанный **Центр Интернета вещей**, к которому планируется подключение устройства. You can choose multiple hubs, and the device will be assigned to one of them according to the selected allocation policy. 

   5. Если необходимо, укажите идентификатор устройства. Идентификаторы устройств можно использовать, чтобы указать отдельное устройство для развертывания модуля. If you don't provide a device ID, the registration ID is used.

   6. При необходимости добавьте значение тега в **Первоначальное состояние двойника устройства**. Теги можно использовать для указания групп устройств для развертывания модуля. Пример. 

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Щелкните **Сохранить**. 

Now that an enrollment exists for this device, the IoT Edge runtime can automatically provision the device during installation. 

## <a name="install-the-iot-edge-runtime"></a>Установка среды выполнения IoT Edge

Среда выполнения IoT Edge развертывается на всех устройствах IoT Edge. Ее компоненты выполняются в контейнерах и позволяют развертывать дополнительные контейнеры на устройстве, чтобы обеспечить возможность выполнения кода в граничной системе. Установите среду выполнения IoT Edge на вашей виртуальной машине. 

Узнайте **область идентификатора** Службы подготовки устройств к добавлению в Центр Интернета вещей и **идентификатор регистрации** устройства, прежде чем переходить к статье, соответствующей типу вашего устройства. Если был установлен приведенный в примере Ubuntu server, то используйте инструкции **x64**. Среда выполнения IoT Edge должна быть настроена на автоматическую подготовку, а не подготовку вручную. 

[Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Предоставление доступа IoT Edge доверенному платформенному модулю

Для проведения автоматической подготовки устройства среда выполнения IoT Edge должна иметь доступ к доверенному платформенному модулю. 

Это можно сделать, переопределив параметры systemd, чтобы назначить службе **iotedge** привилегии суперпользователя. Если вы не хотите повышать привилегии, доступ можно предоставить вручную. 

1. Найдите путь к аппаратному модулю доверенного платформенного модуля на устройстве и сохраните его в качестве локальной переменной. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Создайте новое правило, которое предоставит среде выполнения IoT Edge доступ к tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Откройте файл правил. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Скопируйте следующие сведения о доступе в файл правил. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Сохраните и закройте файл. 

6. Запустите систему udev, чтобы оценить новое правило. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Убедитесь, что правило было успешно применено.

   ```bash
   ls -l /dev/tpm0
   ```

   В случае успешного применения правила данные вывода выглядят следующим образом.

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   В противном случае попробуйте перезагрузить компьютер, чтобы обновить Udev. 

## <a name="restart-the-iot-edge-runtime"></a>Перезапуск среды выполнения IoT Edge

Перезапустите среду выполнения IoT Edge, чтобы активировать все изменения конфигурации, внесенные на устройстве. 

   ```bash
   sudo systemctl restart iotedge
   ```

Убедитесь, что среда выполнения IoT Edge запущена. 

   ```bash
   sudo systemctl status iotedge
   ```

Возникновение ошибки подготовки может означать, что изменения конфигурации еще не вступили в силу. Попробуйте перезапустить управляющую программу IoT Edge. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Или попробуйте перезапустить виртуальную машину, чтобы проверить, вступят ли изменения в силу при новом запуске. 

## <a name="verify-successful-installation"></a>Проверка успешного выполнения установки

Если среда выполнения запущена, перейдите в Центр Интернета вещей и проверьте, что новое устройство автоматически подготовлено и готово для запуска модулей IoT Edge. 

Проверьте состояние управляющей программы IoT Edge.

```cmd/sh
systemctl status iotedge
```

Изучите журналы управляющей программы.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Просмотрите список запущенных модулей.

```cmd/sh
iotedge list
```

You can verify that the individual enrollment that you created in Device Provisioning Service was used. Navigate to your Device Provisioning Service instance in the Azure portal. Open the enrollment details for the individual enrollment that you created. Notice that the status of the enrollment is **assigned** and the device ID is listed. 

## <a name="next-steps"></a>Дальнейшие действия

Процесс регистрации Службы подготовки устройств к добавлению в Центр Интернета вещей позволяет задать идентификатор устройства и теги двойников устройств параллельно с подготовкой нового устройства. Эти значения можно использовать для указания отдельных устройств или групп устройств с помощью автоматического управления устройствами. См. дополнительные сведения о развертывании и мониторинге модулей IoT Edge с поддержкой масштабирования с помощью [портала Azure](how-to-deploy-monitor.md) или [Azure CLI](how-to-deploy-monitor-cli.md).
