---
title: Учебник. Создание иерархии устройств IoT Edge — Azure IoT Edge
description: В этом учебнике показано, как создать иерархическую структуру устройств IoT Edge с помощью шлюзов.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: db77df29d1b9b0adf07c7da377c028dee5312617
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579204"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Руководство по созданию иерархии устройств IoT Edge (предварительная версия)

Разверните узлы Azure IoT Edge по сетям, организованным в виде иерархических слоев. Каждый слой в иерархии — это устройство шлюза, которое обрабатывает сообщения и запросы от устройств в слое под ним.

>[!NOTE]
>Для этого компонента требуется служба IoT Edge 1.2, которая предоставляется в общедоступной предварительной версии, в которой выполняются контейнеры Linux.

Структурировать иерархию устройств можно так, чтобы только верхний слой имел возможность подключения к облаку, а нижние слои могли взаимодействовать только с соседними северным и южным слоями. Такое сетевое наложение — это основа большинства промышленных сетей, соответствующих стандарту [ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

Цель этого учебника — создать иерархию устройств IoT Edge, имитирующих рабочую среду. В итоге вы развернете [моделируемый модуль датчика температуры](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) на устройстве нижнего слоя без доступа к Интернету, загрузив образы контейнеров в иерархию.

Для этого в этом учебнике рассматривается создание иерархии устройств IoT Edge, развертывание контейнеров среды выполнения IoT Edge на устройствах и настройка локальных устройств. В этом руководстве вы узнаете, как:

> [!div class="checklist"]
>
> * создавать и определять связи в иерархии устройств IoT Edge;
> * настраивать среду выполнения IoT Edge на устройствах в иерархии;
> * устанавливать согласованные сертификаты в иерархии устройств;
> * добавлять рабочие нагрузки на устройства в иерархии;
> * использовать модуль прокси-сервера API для безопасной маршрутизации трафика HTTP через один порт на устройствах нижнего слоя.

В этом учебнике определены следующие слои сети.

* **Верхний слой**. Устройства IoT Edge на этом слое могут подключаться непосредственно к облаку.

* **Нижний слой**. Устройства IoT Edge на этом слое не могут подключаться непосредственно к облаку. Для отправки и получения данных им нужно миновать одно или несколько промежуточных устройств IoT Edge.

Для простоты в этом учебнике используется иерархия двух устройств. Первое устройство **topLayerDevice** представляет устройство на верхнем слое иерархии, которое может подключаться непосредственно к облаку. Это устройство также будет называться **родительским устройством**. Второе устройство **lowerLayerDevice** представляет устройство на нижнем слое иерархии, которое не может подключаться непосредственно к облаку. Это устройство также будет называться **дочерним устройством**. Для представления рабочей среды можно добавить дополнительные устройства нижнего слоя. Конфигурация дополнительных устройств нижнего слоя будет соответствовать конфигурации устройства **lowerLayerDevice**.

## <a name="prerequisites"></a>Обязательные условия

Чтобы создать иерархию устройств IoT Edge, потребуются следующие ресурсы:

* Компьютер (Windows или Linux) с подключением к Интернету.
* Два устройства Linux, настроенные в качестве устройств IoT Edge. Если у вас нет доступных устройств, можно использовать [виртуальные машины Azure](https://docs.microsoft.com/azure/virtual-machines/linux/).
* Учетная запись Azure с действительной подпиской. Если у вас еще нет [подписки Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начать работу.
* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure.
* Azure CLI версии 2.3.1 с установленным расширением Azure IoT версии 0.10.6 или более поздней версии. В этом учебнике используется [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Если вы не работали с Azure Cloud Shell, [ознакомьтесь с этим кратким руководством](https://docs.microsoft.com/azure/iot-edge/quickstart-linux#use-azure-cloud-shell).

Вы также можете протестировать этот сценарий, используя скрипт [примера Azure IoT Edge для промышленного Интернета вещей](https://aka.ms/iotedge-nested-sample), который развертывает виртуальные машины Azure в качестве предварительно настроенных устройств для имитации заводской среды.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Настройка иерархии устройств IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Создание иерархии устройств IoT Edge

Первый шаг, создание устройств IoT Edge, можно выполнить с помощью портала Azure или Azure CLI. В рамках работы с этим учебником будет создана иерархия из двух устройств IoT Edge: **topLayerDevice** и его дочернего устройства **lowerLayerDevice**.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Найдите нужный Центр Интернета вещей на [портале Azure](https://ms.portal.azure.com/).

1. В меню слева в разделе **Автоматическое управление устройствами** выберите **IoT Edge**.

1. Выберите **Добавить устройство IoT Edge**. Это устройство будет являться устройством верхнего слоя, поэтому введите соответствующий уникальный идентификатор устройства. Нажмите кнопку **Сохранить**.

1. Выберите **Добавить устройство IoT Edge** еще раз. Это устройство будет являться устройством нижнего слоя, поэтому введите соответствующий уникальный идентификатор устройства.

1. Щелкните **Задать родительское устройство**, выберите устройство верхнего слоя в списке устройств и нажмите кнопку **ОК**. Нажмите кнопку **Сохранить**.

   ![Настройка родительского элемента для устройства нижнего слоя](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Чтобы создать устройство IoT Edge в своем центре, введите следующую команду в [Azure Cloud Shell](https://shell.azure.com/). Это устройство будет являться устройством верхнего слоя, поэтому введите соответствующий уникальный идентификатор устройства.

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Введите следующую команду, чтобы создать дочернее устройство IoT Edge и отношение "родители — потомки" между устройствами:

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

Запишите строку подключения каждого устройства IoT Edge. Они будут использоваться позже.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. На [портале Azure](https://ms.portal.azure.com/) перейдите в раздел **IoT Edge** Центра Интернета вещей.

1. Щелкните идентификатор одного из устройств в списке устройств.

1. Нажмите кнопку **Копировать** в поле **Первичная строка подключения** и запишите строку.

1. Повторите эти действия для всех остальных устройств.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. В [Azure Cloud Shell](https://shell.azure.com/) для каждого устройства введите следующую команду, чтобы получить строку подключения устройства и записать ее:

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

### <a name="create-certificates"></a>Создание сертификатов

Всем устройствам в [сценарии шлюза](iot-edge-as-gateway.md) требуется общий сертификат для настройки безопасных соединений между ними. Чтобы создать демонстрационные сертификаты для обоих устройств в этом сценарии, выполните следующие действия.

Чтобы создать демонстрационные сертификаты на устройстве Linux, необходимо клонировать скрипты создания и настроить их для локального запуска в bash.

1. Клонируйте репозиторий Git службы IoT Edge, который содержит скрипты для создания демонстрационных сертификатов.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. Перейдите в каталог, в котором вы планируете работать. Все файлы сертификатов и ключей будут созданы в этом каталоге.

1. Скопируйте файлы конфигурации и скриптов из клонированного репозитория IoT Edge в рабочий каталог.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. Создайте корневой сертификат ЦС и один промежуточный сертификат.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Эта команда скрипта создает несколько файлов сертификатов и ключей, но в качестве **корневого сертификата ЦС** для иерархии шлюза используется следующий файл:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. Создайте два набора сертификатов ЦС устройств IoT Edge и закрытых ключей с помощью следующей команды: один набор для устройства верхнего слоя и один набор для устройства нижнего слоя. Укажите запоминающиеся имена сертификатов ЦС, чтобы отличать их друг от друга.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   Эта команда скрипта создает несколько файлов сертификатов и ключей, но мы используем следующий сертификат и пару ключей на каждом устройстве IoT Edge, на которое указывает ссылка в файле config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Каждому устройству требуется копия корневого сертификата ЦС и копия его собственного сертификата ЦС устройства и закрытого ключа. Для перемещения сертификатов на каждое устройство можно использовать USB-накопитель или [безопасное копирование файлов](https://www.ssh.com/ssh/scp/).

1. После передачи сертификатов установите корневой ЦС для каждого устройства.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Эта команда должна вывести данные о том, что один сертификат добавлен в каталог /etc/ssl/certs.

### <a name="install-iot-edge-on-the-devices"></a>Установка IoT Edge на устройства

Установите IoT Edge, выполнив следующие действия на обоих устройствах.

1. Обновите списки пакетов на устройстве.

   ```bash
   sudo apt-get update
   ```

1. Установите модуль Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Установите средство hsmlib и управляющую программу IoT Edge. <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/libiothsm-std_1.2.0.rc1-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/iotedge_1.2.0_rc1-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>Настройка среды выполнения IoT Edge

Настройте среду выполнения IoT Edge, выполнив следующие действия на обоих устройствах. Настройка среды выполнения IoT Edge для устройств состоит из четырех шагов, которые выполняются путем изменения файла конфигурации IoT Edge:

1. Вручную предоставьте каждое устройство, добавив строку подключения этого устройства в файл конфигурации.

1. Завершите настройку сертификатов устройства, указав файл конфигурации для сертификата ЦС устройства, закрытый ключ ЦС устройства и корневой сертификат ЦС.

1. Выполните начальную загрузку системы с помощью агента IoT Edge.

1. Обновите параметр **hostname** для устройства **верхнего слоя** и параметры **hostname** и **parent_hostname** для устройств **нижнего слоя**.

Выполните следующие действия и перезапустите службу IoT Edge, чтобы настроить устройства.

1. На каждом устройстве откройте файл конфигурации IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Найдите конфигурации подготовки файла и раскомментируйте раздел **Manual provisioning configuration using a connection string** (Настройка подготовки вручную с помощью строки подключения).

1. Замените значение **device_connection_string** строкой подключения для устройства IoT Edge. Убедитесь, что все остальные разделы подготовки закомментированы. Убедитесь, что перед строкой **provisioning:** нет пробелов, а вложенные элементы отображаются с отступом в два пробела.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >Чтобы вставить содержимое буфера обмена в Nano, нажмите клавиши `Shift+Right Click` или `Shift+Insert`.

1. Найдите раздел **Сертификаты**. Раскомментируйте и обновите три поля сертификата, чтобы указать сертификаты, созданные в предыдущем разделе, и переместить их на устройство IoT Edge. Укажите пути URI файла, которые принимают формат `file:///<path>/<filename>`.

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. Для устройства **верхнего слоя** найдите параметр **hostname**. Измените значение на полное доменное имя (FQDN) или IP-адрес устройства IoT Edge. Используйте любое значение, которое будет согласованно выбрано на всех устройствах в иерархии.

   ```yml
   hostname: <device fqdn or IP>
   ```

1. Для устройств IoT Edge **более низких слоев** обновите файл конфигурации, чтобы он указывал на полное доменное имя или IP-адрес родительского устройства, совпадающие с теми, что находятся в поле **hostname** родительского устройства. Для устройств IoT Edge **верхнего слоя** оставьте этот параметр закомментированным.

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > Для иерархий с более чем одним нижним слоем обновите поле *parent_hostname* с полным доменным именем устройства в слое, расположенном выше.

1. Для устройства **верхнего слоя** найдите раздел **agent** YAML и обновите значение образа до нужной версии агента IoT Edge. В этом случае в реестре контейнеров Azure будет указываться агент IoT Edge верхнего слоя с общедоступной предварительной версией образа агента IoT Edge.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc1"
       auth: {}
   ```

1. Для устройств IoT Edge **более низких слоев** обновите имя домена значения образа, чтобы оно указывало на FQDN или IP-адрес родительского устройства, а затем порт прокси-сервера API (8000). В рамках следующего раздела вы добавите модуль прокси-сервера API.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc1"
       auth: {}
   ```

1. Сохраните файл и закройте его.

   `CTRL + X`, `Y`, `Enter`

1. Когда введете сведения о подготовке в файл конфигурации, перезапустите управляющую программу:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>Развертывание модулей на устройстве верхнего слоя

Оставшиеся действия для завершения настройки среды выполнения IoT Edge и развертывания рабочих нагрузок выполняются из облака с помощью портала Azure или Azure CLI.

# <a name="portal"></a>[Портал](#tab/azure-portal)

[На портале Azure](https://ms.portal.azure.com/)

1. Перейдите в Центр Интернета вещей.

1. В меню слева в разделе **Автоматическое управление устройствами** выберите **IoT Edge**.

1. В списке устройств щелкните идентификатор устройства **верхнего слоя**.

1. На верхней панели выберите **Задание модулей**.

1. Щелкните пункт **Параметры среды выполнения** рядом со значком шестеренки.

1. В разделе **Центр Edge** в поле образа введите `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1`.

   ![Изменение образа центра Edge](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Добавьте следующие переменные среды в модуль центра Edge:

    | Имя | Значение |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Изменение переменных среды центра Edge](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. В разделе **Агент Edge** в поле образа введите `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc1`. Нажмите кнопку **Сохранить**.

1. Добавьте модуль реестра DOCKER на устройство верхнего слоя. Щелкните **Добавить** и выберите **Модуль IoT Edge** в раскрывающемся списке. Укажите имя `registry` для модуля реестра DOCKER и введите `registry:latest` в качестве URI образа. Затем добавьте переменные среды и создайте параметры для указания локального модуля реестра в реестре контейнеров Майкрософт, чтобы скачать образы контейнеров и обслужить эти образы в реестре 5000.

1. На вкладке переменных среды введите следующую пару имя-значение переменной среды:

    | Имя | Значение |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. На вкладке параметров создания контейнера введите следующий код JSON:

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. Затем добавьте модуль прокси-сервера API на устройство верхнего слоя. Щелкните **Добавить** и выберите **Модуль в Marketplace** в раскрывающемся списке. Найдите `IoT Edge API Proxy` и выберите модуль. Прокси-сервер API IoT Edge использует порт 8000 и по умолчанию настроен для использования модуля реестра с именем `registry` для порта 5000.

1. Выберите **Просмотр и создание**, а затем нажмите кнопку **Создать** для завершения развертывания. Среда выполнения IoT Edge устройства верхнего слоя, которая имеет доступ к Интернету, выберет и запустит **общедоступную предварительную версию** конфигураций центра IoT Edge и агента IoT Edge.

   ![Полное развертывание с центром Edge, агентом Edge, модулем реестра и модулем прокси-сервера API](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. В [Azure Cloud Shell](https://shell.azure.com/) введите следующую команду, чтобы создать файл deployment.json:

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. Скопируйте содержимое приведенного ниже файла JSON в deployment.json, сохраните его и закройте.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc1",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Введите следующую команду, чтобы создать развертывание на устройстве Edge верхнего слоя:

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

## <a name="deploy-modules-to-the-lower-layer-device"></a>Развертывание модулей на устройстве нижнего слоя

Для развертывания рабочих нагрузок из облака на устройствах **нижнего слоя** можно использовать как портал Azure, так и Azure CLI.

# <a name="portal"></a>[Портал](#tab/azure-portal)

[На портале Azure](https://ms.portal.azure.com/)

1. Перейдите в Центр Интернета вещей.

1. В меню слева в разделе **Автоматическое управление устройствами** выберите **IoT Edge**.

1. Щелкните идентификатор устройства нижнего слоя в списке устройств IoT Edge.

1. На верхней панели выберите **Задание модулей**.

1. Щелкните пункт **Параметры среды выполнения** рядом со значком шестеренки.

1. В разделе **Центр Edge** в поле образа введите `$upstream:8000/azureiotedge-hub:1.2.0-rc1`.

1. Добавьте следующие переменные среды в модуль центра Edge:

    | Имя | Значение |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. В разделе **Агент Edge** в поле образа введите `$upstream:8000/azureiotedge-agent:1.2.0-rc1`. Нажмите кнопку **Сохранить**.

1. Добавьте модуль датчика температуры. Щелкните **Добавить** и выберите **Модуль в Marketplace** в раскрывающемся списке. Найдите `Simulated Temperature Sensor` и выберите модуль.

1. В разделе **Модули IoT Edge** выберите только что добавленный модуль `Simulated Temperature Sensor` и обновите его URI, чтобы он указывал на `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0`.

1. Щелкните **Сохранить**, выберите **Просмотр и создание**, а затем нажмите кнопку **Создать** для завершения развертывания.

   ![Полное развертывание с центром Edge, агентом Edge и смоделированным датчиком температуры](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. В [Azure Cloud Shell](https://shell.azure.com/) введите следующую команду, чтобы создать файл deployment.json:

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. Скопируйте содержимое приведенного ниже файла JSON в deployment.json, сохраните его и закройте.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc1",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc1",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Введите следующую команду, чтобы создать развертывание набора модулей на устройстве Edge нижнего слоя:

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## View generated data

The **Simulated Temperature Sensor** module that you pushed generates sample environment data. It sends messages that include ambient temperature and humidity, machine temperature and pressure, and a timestamp.

You can watch the messages arrive at your IoT hub by using the [Azure IoT Hub extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

You can also view these messages through the [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы избежать расходов, можно удалить локальные конфигурации и ресурсы Azure, созданные при работе с этой статьей.

Удаление ресурсов:

1. Войдите в портал [Azure](https://portal.azure.com) и выберите **Группы ресурсов**.

2. Выберите группу ресурсов, содержащую тестовые ресурсы IoT Edge. 

3. Просмотрите список ресурсов, содержащихся в группе ресурсов. Если вы хотите удалить их все, щелкните **Удалить группу ресурсов**. Если вы хотите удалить только некоторые из них, щелкните нужные ресурсы отдельно. 

## <a name="next-steps"></a>Дальнейшие действия

При работе с этим учебником вы настроили два устройства IoT Edge в качестве шлюзов и настроили одно из них для выполнения роли родительского устройства для другого. Затем вы выполнили извлечение образа контейнера в дочернее устройство через шлюз. Вы также можете протестировать этот сценарий, используя скрипт [примера Azure IoT Edge для промышленного Интернета вещей](https://aka.ms/iotedge-nested-sample), который развертывает виртуальные машины Azure в качестве предварительно настроенных устройств для имитации заводской среды.

Вы можете перейти к изучению других руководств, чтобы узнать, как Azure IoT Edge может создавать решения для вашего бизнеса.

> [!div class="nextstepaction"]
> [Руководство: развертывание службы "Машинное обучение Azure" в качестве модуля IoT Edge (предварительная версия)](tutorial-deploy-machine-learning.md)
