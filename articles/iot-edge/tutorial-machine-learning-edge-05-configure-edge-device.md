---
title: Руководство по настройке устройства IoT Edge — Машинное обучение Azure в Azure IoT Edge
description: Работая с этим учебником, вы настроите виртуальную машину Azure под управлением Linux как устройство Azure IoT Edge, которое выполняет функцию прозрачного шлюза.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d2bad581c925bb62cbe65a45000f6d3ae35db011
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372694"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Руководство по Настройка устройства IoT Edge

> [!NOTE]
> Эта статья входит в серию учебников по использованию Машинного обучения Azure в IoT Edge. Если вы перешли к этой статье по прямой ссылке, для оптимальных результатов рекомендуем начать с изучения [первой статьи](tutorial-machine-learning-edge-01-intro.md) этой серии.

В этой статье описано, как настроить виртуальную машину Azure под управлением Linux как устройство IoT Edge, которое выполняет функцию прозрачного шлюза. Так устройства смогут подключаться к Центру Интернета вещей через прозрачный шлюз, ничего не зная о нем. Точно так же и пользователь, взаимодействующий с устройствами в Центре Интернета вещей, не будет знать о промежуточном устройстве шлюза. В итоге мы добавим в систему компонент для передачи аналитических сведений о пограничных устройствах, добавив к прозрачному шлюзу модули IoT Edge.

Описанные в этой статье действия обычно выполняются разработчиком облачной системы.

## <a name="create-certificates"></a>Создание сертификатов

Чтобы устройство могло функционировать в качестве шлюза, оно должно безопасно подключаться к подчиненным устройствам. Решение Azure IoT Edge позволяет настраивать безопасные подключения между устройствами с использованием инфраструктуры открытых ключей (PKI). В этом случае мы разрешаем подчиненному устройству Интернета вещей подключаться к устройству IoT Edge, выполняющему роль прозрачного шлюза. Чтобы обеспечить достаточный уровень защиты, подчиненное устройство должно подтвердить удостоверение устройства IoT Edge. См. подробнее об [использовании сертификатов на устройствах IoT Edge](iot-edge-certs.md).

В этом разделе мы создадим самозаверяющие сертификаты с помощью образа Docker, который мы скомпилируем и запустим. Мы выбрали для этого действия именно образ Docker, так как он значительно сокращает процедуру создания сертификатов на компьютере разработки Windows. См. [Создание демонстрационных сертификатов для тестирования функций устройств IoT Edge](how-to-create-test-certificates.md), чтобы понять, что автоматизируется с помощью образа Docker.

1. Войдите на виртуальную машину разработки.

2. Создайте папку, указав путь и имя: `c:\edgeCertificates`.

3. Запустите приложение **Docker для Windows** из меню Пуск в Windows, если еще не сделали это.

4. Откройте Visual Studio Code.

5. Выберите **Файл** > **Открыть папку** и укажите путь **C:\\source\\IoTEdgeAndMlSample\\CreateCertificates**.

6. На панели "Проводник" щелкните файл **dockerfile** правой кнопкой мыши и выберите **Собрать образ**.

7. В диалоговом окне сохраните значения по умолчанию для имени образа и тега (**createcertificates:latest**).

    ![Создание сертификатов в Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Дождитесь завершения сборки.

    > [!NOTE]
    > Может появиться предупреждение об отсутствии открытого ключа. Эти предупреждения можно спокойно игнорировать. Также вы увидите предупреждение системы безопасности с предложением проверить или изменить разрешения в образе. Его тоже можно игнорировать.

9. В окне терминала Visual Studio Code запустите контейнер createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker запросит доступ к диску **c:\\** . Выберите **Предоставить общий доступ**.

11. При появлении запроса предоставьте свои учетные данные.

12. Когда завершится выполнение контейнера, проверьте следующие файлы в каталоге **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Отправка сертификатов в Azure Key Vault

Чтобы безопасно хранить сертификаты, а также сделать их доступными с разных устройств, мы отправим эти сертификаты в Azure Key Vault. Как видно в приведенном выше списке, у нас есть два типа файлов сертификатов: PFX и PEM. Мы рассматриваем PFX-файлы в качестве сертификатов хранилища ключей, которые будут отправлены в Key Vault. PEM-файлы имеют обычный текстовый формат, и мы будем использовать их как секреты Key Vault. Мы выберем хранилище Key Vault, связанное с рабочей областью Машинного обучения Azure, которую мы создали с помощью [Записных книжек Azure](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к рабочей области Машинного обучения Azure.

2. На странице обзора для рабочей области Машинного обучения Azure найдите имя хранилища **Key Vault**.

    ![Копирование имени Key Vault](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. На компьютере для разработки передайте сертификаты в Key Vault. Замените строки **\<subscriptionId\>** и **\<keyvaultname\>** реальными значениями идентификатора подписки и имени хранилища.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Выполните вход в Azure, если потребуется.

5. Скрипт будет выполняться в течение нескольких минут, после чего вернет список новых записей в Key Vault.

    ![Выходные данные скрипта для Key Vault](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Создание устройства IoT Edge

Чтобы подключить устройство Azure IoT Edge к Центру Интернета вещей, сначала следует создать в этом концентраторе удостоверение устройства. Мы извлечем строку подключения из удостоверения устройства в облаке и с ее помощью настроим среду выполнения на устройстве IoT Edge. Когда настроенное устройство подключится к центру, мы сможем развертывать модули и отправлять сообщения. Мы также можем изменить конфигурацию физического устройства IoT Edge, изменив удостоверение устройства в центре Интернета вещей.

В этом руководстве описано, как создать новое удостоверение устройства с помощью Visual Studio Code. Эти действия также можно выполнить с помощью [портала Azure](how-to-register-device.md#register-in-the-azure-portal) или [Azure CLI](how-to-register-device.md#register-with-the-azure-cli).

1. Откройте Visual Studio Code на компьютере для разработки.

2. В представлении проводника Visual Studio Code разверните фрейм **Центр Интернета вещей Azure**.

3. Щелкните символ многоточия и выберите **Создать устройство IoT Edge**.

4. Присвойте имя этому устройству. Для удобства мы используем имя **aaTurbofanEdgeDevice**, чтобы оно оказалось первым в списке устройств.

5. Новое устройство отобразится в списке устройств.

    ![Просмотр нового устройства AaTurbofanEdgeDevice в проводнике VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Развертывание виртуальной машины Azure

В этом руководстве мы применим образ [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) из Azure Marketplace, чтобы создать новое устройство IoT Edge. Этот образ при первом запуске устанавливает самую свежую версию среды выполнения Azure IoT Edge и все ее зависимости. Для развертывания виртуальной машины мы применим скрипт PowerShell `Create-EdgeVM.ps1`, шаблон Resource Manager `IoTEdgeVMTemplate.json` и скрипт оболочки `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Включение программного развертывания

Чтобы использовать в скрипте для развертывания образ из Marketplace, нужно включить для него программное развертывание.

1. Войдите на портал Azure.

1. Выбор пункта **Все службы**.

1. В поле поиска введите и выберите **Marketplace**.

1. В поле поиска Marketplace введите и выберите **Azure IoT Edge on Ubuntu**.

1. Щелкните гиперссылку **Начало работы**, чтобы выполнить развертывание с помощью программных средств.

1. Щелкните **Включить**, а затем **Сохранить**.

    ![Включение развертывания, выполняемого программным способом, для виртуальной машины](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Вы получите уведомление об успешном выполнении.

### <a name="create-virtual-machine"></a>Создание виртуальной машины

Теперь запустите скрипт, который создаст виртуальную машину для устройства IoT Edge.

1. Откройте окно PowerShell и перейдите к папке **EdgeVM**.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Запустите скрипт, чтобы создать виртуальную машину.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Предоставьте значения для каждого параметра, когда появится соответствующий запрос. Мы рекомендуем использовать те же подписку, группу ресурсов и расположение, что и для всех остальных ресурсов в этом руководстве.

    * **Идентификатор подписки Azure** можно узнать на портале Azure.
    * **Имя группы ресурсов.** Удобное и запоминающееся имя для группировки всех ресурсов, используемых в этом руководстве.
    * **Расположение.** Расположение Azure, где будет создана виртуальная машина. Например, westus2 или northeurope. См. подробнее о [расположениях Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername.** Имя учетной записи администратора, с котором вы будете входить на виртуальную машину.
    * **adminPassword.** Пароль для пользователя AdminUsername на виртуальной машине.

4. Чтобы скрипт мог настроить виртуальную машину, вам нужно войти в Azure с учетными данными, связанными с используемой подпиской Azure.

5. Скрипт подтвердит сведения для создания виртуальной машины. Нажмите клавишу **y** или **ВВОД**, чтобы продолжить.

6. Скрипт будет выполнять следующие действия в течение нескольких минут:

    * создание группы ресурсов, если она еще не существует;
    * Создание виртуальной машины
    * добавление в группу безопасности сети исключений портов виртуальной машины 22 (SSH), 5671 (AMQP), 5672 (AMPQ) и 443 (TLS);
    * установка [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest).

7. Скрипт выведет строку SSH для подключения к виртуальной машине. Скопируйте ее для выполнения следующего шага.

    ![Копирование строки SSH-подключения для виртуальной машины](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Подключение к устройству IoT Edge

В следующих разделах описано, как настроить созданную виртуальную машину Azure. Сначала нужно подключиться к виртуальной машине.

1. Откройте командную строку и вставьте строку SSH-подключения, которую вы скопировали из выходных данных скрипта. Введите собственные значения имени пользователя, суффикса и региона, которые вы передали в скрипт PowerShell, как описано в предыдущем разделе.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Когда появится запрос на подтверждение подлинности узла, введите **yes** (да) и нажмите клавишу **ВВОД**.

3. В ответ на запрос введите свой пароль.

4. В Ubuntu отобразится приветственное сообщение, а затем появится строка в формате `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Скачивание сертификатов Key Vault

Ранее в этой статье описывалось, как отправить в Key Vault сертификаты, чтобы сделать их доступными для устройства IoT Edge и конечного устройства. Конечным устройством называется подчиненное устройство, которое использует устройство IoT Edge в качестве шлюза для обмена данными с Центром Интернета вещей.

С этим устройством мы будем работать позже. Для выполнения операций, описанных в этом разделе, скачайте сертификаты на устройство IoT Edge.

1. В сеансе SSH-подключения к виртуальной машине Linux выполните вход в Azure с помощью Azure CLI.

    ```azurecli
    az login
    ```

1. Вам будет предложено открыть в браузере страницу <https://microsoft.com/devicelogin> и предоставить уникальный код. Эти шаги вы можете выполнить на локальном компьютере. После завершения проверки подлинности закройте окно браузера.

1. Если проверка подлинности пройдет успешно, на виртуальной машине Linux будет выполнен вход, после чего отобразится список подписок Azure.

1. Укажите подписку Azure, которую вы хотите использовать для выполнения команд Azure CLI.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Создайте на виртуальной машине каталог для сертификатов.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Скачайте сертификаты, которые хранятся в хранилище ключей: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem и azure-iot-test-only.root.ca.cert.pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Обновление конфигурации устройства IoT Edge.

Среда выполнения IoT Edge использует для хранения конфигурации файл `/etc/iotedge/config.yaml`. В этом файле нам нужно изменить три элемента:

* **Строка подключения устройства** — строка подключения из удостоверения устройства в Центре Интернета вещей.
* **Сертификаты** — сертификаты для подключения к подчиненным устройствам.
* **Имя узла** — полное доменное имя (FQDN) виртуальной машины, которая действует как устройство IoT Edge.

Образ *Azure IoT Edge on Ubuntu*, который мы использовали для создания виртуальной машины IoT Edge, содержит готовый скрипт оболочки, который включает в файл config.yaml строку подключения.

1. В Visual Studio Code щелкните устройство IoT Edge правой кнопкой мыши и выберите **Копировать строку подключения к устройству**.

    ![Копирование строки подключения в Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. В сеансе SSH-подключения выполните команду, которая включает строку подключения к устройству в файле config.yaml.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Теперь мы обновим сертификаты и имя узла, вручную изменив содержимое файла config.yaml.

1. Откройте файл config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. В разделе certificates файла config.yaml удалите первый элемент `#` и измените путь следующим образом:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Убедитесь, что перед строкой **certificates:** нет пробелов, а перед каждым вложенным сертификатом есть по два пробела.

    В редакторе Nano щелкните правой кнопкой мыши, чтобы вставить содержимое буфера обмена в место, где находится курсор. Чтобы заменить строку, перейдите к ней с помощью клавиш со стрелками, удалите ее, а затем щелкните правой кнопкой мыши, чтобы вставить содержимое буфера обмена.

3. На портале Azure перейдите к виртуальной машине. Скопируйте DNS-имя (полное доменное имя компьютера) из раздела **Обзор**.

4. Вставьте полное доменное имя в раздел hostname файла config.yml. Все символы имени должны быть в нижнем регистре.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Сохраните и закройте файл (`Ctrl + X`, `Y`, `Enter`).

6. Перезапустите управляющую программу iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Проверьте состояние работы управляющей программы IoT Edge (после выполнения команды введите :q, чтобы выйти).

    ```bash
    systemctl status iotedge
    ```

8. Если вы увидите ошибки (выделенный цветом текст с префиксом \[ERROR\]), изучите подробные сведения о них в журналах управляющей программы.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Дальнейшие действия

Мы завершили настройку виртуальной машины Azure, которая будет выполнять роль прозрачного шлюза IoT Edge. Мы начали с создания тестовых сертификатов, которые затем отправили в Azure Key Vault. После этого мы применили скрипт и шаблон Resource Manager для развертывания виртуальной машины из образа "Ubuntu Server 16.04 LTS + Azure IoT Edge runtime" из Azure Marketplace. К запущенной виртуальной машине мы подключились по протоколу SSH, вошли в Azure и скачали сертификаты из Key Vault. Мы внесли несколько изменений в конфигурацию среды выполнения IoT Edge, обновив файл config.yaml.

Дополнительные сведения см. в статьях [об использовании устройства IoT Edge в качестве шлюза](iot-edge-as-gateway.md) и [о настройке устройства IoT Edge в качестве прозрачного шлюза](how-to-create-transparent-gateway.md).

Теперь вы можете перейти к следующей статье, чтобы создать модули IoT Edge.

> [!div class="nextstepaction"]
> [Создание и развертывание пользовательских модулей IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md)
