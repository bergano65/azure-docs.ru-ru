---
title: Настройка сервера обработки в Azure для восстановления размещения во время аварийного восстановления виртуальных машин VMware и физических серверов с помощью Azure Site Recovery | Документация Майкрософт
description: В этой статье описывается, как настроить сервер обработки в Azure для восстановления размещения в локальной среде во время аварийного восстановления виртуальных машин VMware и физических серверов.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: ramamill
ms.openlocfilehash: 6849ffb6fa46365aa775b9410067cb0874c70ef8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59362152"
---
# <a name="scale-for-failback-with-additional-process-servers"></a>Масштабирование для восстановления размещения с помощью дополнительных серверов обработки

По умолчанию при репликации виртуальных машин VMware или физических серверов в Azure с помощью [Site Recovery](site-recovery-overview.md) сервер обработки устанавливается на сервере конфигурации и используется для координации передачи данных между Site Recovery и локальной инфраструктурой. Чтобы увеличить емкость и масштабировать среду репликации, можно добавить дополнительные изолированные серверы обработки. Из этой статьи вы узнаете, как это сделать.

## <a name="before-you-start"></a>Перед началом работы

### <a name="capacity-planning"></a>Планирование загрузки

Убедитесь, что [планирование емкости](site-recovery-plan-capacity-vmware.md) для репликации VMware выполнено. Это позволяет узнать, как и когда следует развернуть дополнительные серверы обработки.

> [!NOTE]
> Использование клонированного компонента сервера обработки не поддерживается. Выполнить шаги в этой статье в ходе каждого горизонтального масштабирования сервера обработки.

### <a name="sizing-requirements"></a>Требования к размерам 

Проверьте требования к размерам, описанные в таблице. В общем, если среда вырастает более чем на 200 компьютеров-источников или объем ежедневно изменяемых данных превысит 2 ТБ, для обработки такой нагрузки потребуются дополнительные серверы обработки.

| **Дополнительный сервер обработки** | **Размер диска кэша** | **Частота изменения данных** | **Защищенные компьютеры** |
| --- | --- | --- | --- |
|4 виртуальных ЦП (2 сокета * 2 ядра с частотой \@ 2,5 ГГц), 8 ГБ памяти |300 ГБ |250 ГБ или менее |Репликация до 85 компьютеров |
|8 виртуальных ЦП (2 сокета * 4 ядра с частотой \@ 2,5 ГГц), 12 ГБ памяти |600 ГБ |От 250 ГБ до 1 ТБ |Репликация от 85 до 150 компьютеров |
|12 виртуальных ЦП (2 сокета * 6 ядер с частотой \@ 2,5 ГГц), 24 ГБ памяти |1 TБ |От 1 ТБ до 2 ТБ |Репликация от 150 до 225 компьютеров |

Для каждого защищенного исходного компьютера настраивается три диска объемом 100 ГБ каждый.

### <a name="prerequisites"></a>Технические условия

Предварительные требования для дополнительного сервера обработки приведены в следующей таблице.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]



## <a name="download-installation-file"></a>Загрузка установочного файла

Загрузите установочный файл для сервера обработки здесь:

1. Войдите на портал Azure и перейдите в хранилище служб восстановления.
2. Откройте **Инфраструктура Site Recovery** > **VMWare и физические компьютеры** > **Серверы конфигурации** (в разделе "VMware и физические компьютеры").
3. Выберите сервер конфигурации, чтобы просмотреть подробные сведения о нем. Затем щелкните **+ Сервер обработки**.
4. В разделе **Добавление сервера обработки** >  **Выберите, где следует развернуть сервер обработки** выберите **Локальное развертывание сервера обработки масштабирования**.

   ![Страница "Добавление серверов"](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Щелкните **Download the Microsoft Azure Site Recovery Unified Setup** (Загрузить совмещенную установку Microsoft Azure Site Recovery). После этого вы загрузите последнюю версию установочного файла.

   > [!WARNING]
   > Версия установки сервера обработки должна быть такой же, как и предыдущая версия используемого сервера конфигурации. Простой способ обеспечить совместимость версий – использовать ту же установку, которая использовалась совсем недавно для установки или обновления сервера конфигурации.

## <a name="install-from-the-ui"></a>Установка с помощью пользовательского интерфейса

Установите сервер следующим образом. После настройки сервера необходимо перевести исходные компьютеры на его использование.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Установка из командной строки

Установите , выполнив следующую команду:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Параметры командной строки:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Например: 

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Создание файла параметров прокси-сервера

Если необходимо настроить прокси-сервер, значения для параметра ProxySettingsFilePath берутся из файла. Этот файл можно создать, как показано ниже, и передать его в качестве входных данных для параметра ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об [управлении параметрами сервера обработки](vmware-azure-manage-process-server.md)
