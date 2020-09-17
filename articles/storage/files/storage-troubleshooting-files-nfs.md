---
title: Устранение неполадок с общей папкой Azure NFS — файлы Azure
description: Устранение неполадок с общей папкой Azure NFS.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 661cfd5bb410a714bc42e0cd9676ac2ec08f8a45
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90709101"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Устранение неполадок файловых ресурсов Azure NFS

В этой статье перечислены некоторые распространенные проблемы, связанные с файловыми ресурсами Azure NFS. Он предоставляет потенциальные причины и способы их решения при обнаружении этих проблем.

## <a name="unable-to-create-an-nfs-share"></a>Не удалось создать общий ресурс NFS

### <a name="cause-1-subscription-is-not-enabled"></a>Причина 1. Подписка не включена

Возможно, ваша подписка не зарегистрирована для предварительной версии NFS службы файлов Azure. Чтобы включить эту функцию, необходимо выполнить несколько дополнительных командлеты Cloud Shell или локальный терминал.

> [!NOTE]
> Для завершения регистрации может потребоваться подождать до 30 минут.


#### <a name="solution"></a>Решение

Используйте следующий скрипт для регистрации компонента и поставщика ресурсов, замените `<yourSubscriptionIDHere>` перед запуском скрипта:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares - ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Причина 2. неподдерживаемые параметры учетной записи хранения

NFS доступна только в учетных записях хранения со следующей конфигурацией:

- Уровень Premium
- Тип учетной записи — Филестораже
- Избыточность — LRS
- Регионы — Восточная часть США, Восточная часть США 2, южная часть Соединенного Королевства, Юго-Восточная Азия

#### <a name="solution"></a>Решение

Следуйте инструкциям в статье [Создание общего ресурса NFS](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Причина 3. учетная запись хранения была создана до завершения регистрации

Чтобы учетная запись хранения использовала эту функцию, ее необходимо создать после того, как подписка завершит регистрацию для NFS. Для завершения регистрации может потребоваться до 30 минут.

#### <a name="solution"></a>Решение

После завершения регистрации следуйте инструкциям в статье [Создание общего ресурса NFS](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Не удается подключиться к общей папке NFS Azure или подключить ее

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Причина 1: запрос получен от клиента в ненадежном сетевом или ненадежном IP-адресе

В отличие от SMB, NFS не имеет проверки подлинности на основе пользователей. Проверка подлинности для общей папки основана на конфигурации правила безопасности сети. Из-за этого, чтобы обеспечить наличие только безопасных подключений к общему ресурсу NFS, необходимо использовать конечную точку службы или частные конечные точки. Для доступа к общим ресурсам из локальной среды в дополнение к частным конечным точкам необходимо настроить VPN или ExpressRoute. IP-адреса, добавленные в список разрешений учетной записи хранения для брандмауэра, игнорируются. Для настройки доступа к общей папке NFS необходимо использовать один из следующих методов:


- [Конечная точка службы](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Доступ к общедоступной конечной точке
    - Доступно только в том же регионе.
    - Пиринг виртуальных сетей не предоставляет доступ к общему ресурсу.
    - Каждая виртуальная сеть или подсеть должна быть отдельно добавлена в список разрешений.
    - Для локального доступа конечные точки службы можно использовать с ExpressRoute, подключением типа "точка — сеть" и VPN-подключением "сеть — сеть", но мы рекомендуем использовать закрытую конечную точку, так как она более безопасна.

На следующей схеме показано подключение с использованием общедоступных конечных точек.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Схема подключения к общедоступной конечной точке." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Частная конечная точка](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - Доступ более безопасен, чем конечная точка службы.
    - Доступ к общему ресурсу NFS через частную связь можно получить в регионе Azure или за пределами региона учетной записи хранения (между регионами).
    - Пиринг виртуальных сетей с виртуальными сетями, размещенными в частной конечной точке, предоставляет NFS общий доступ к клиентам в одноранговых виртуальных сетях.
    - Частные конечные точки можно использовать с ExpressRoute, подключением типа "точка — сеть" и VPN-подключение "сеть — сеть".

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Схема подключения к частной конечной точке." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Причина 2. требуется безопасное перемещение

Двойное шифрование еще не поддерживается для общих папок NFS. Azure обеспечивает уровень шифрования для всех данных при передаче между центрами обработки данных Azure с помощью Максек. Доступ к общим папкам NFS можно получить только из доверенных виртуальных сетей и через VPN-туннели. В общих папках NFS нет доступного дополнительного шифрования транспортного уровня.

#### <a name="solution"></a>Решение

В колонке настройки учетной записи хранения необходимо отключить безопасное перемещение.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Снимок экрана: колонка конфигурации учетной записи хранения, требуется отключить безопасное перемещение.":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Причина 3. общий пакет NFS не установлен
Перед выполнением команды mount установите пакет, выполнив команду дистрибутив, указанную ниже.

Чтобы проверить, установлен ли пакет NFS, выполните: `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Решение

Если пакет не установлен, установите пакет в дистрибутиве.

##### <a name="ubuntu-or-debian"></a>Ubuntu или Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8 +, CentOS 8 +

Используйте диспетчер пакетов ДНФ: `sudo dnf install nfs-common` .

Более старые версии Red Hat Enterprise Linux и CentOS используют диспетчер пакетов yum: `sudo yum install nfs-common` .

##### <a name="opensuse"></a>openSUSE

Используйте диспетчер пакетов zypper: `sudo zypper install-nfscommon` .

### <a name="cause-4-firewall-blocking-port-2049"></a>Причина 4. Порт блокировки брандмауэра 2049

Протокол NFS обменивается данными с сервером через порт 2049, убедитесь, что этот порт открыт для учетной записи хранения (сервера NFS).

#### <a name="solution"></a>Решение

Убедитесь, что на клиенте открыт порт 2049, выполнив следующую команду: `telnet <storageaccountnamehere>.file.core.windows.net 2049` . Если порт не открыт, откройте его.

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.
Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.