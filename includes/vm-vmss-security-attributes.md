---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: df11493fa9663d3fcbf0a2f74a5acbead55a25fb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800280"
---
## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Шифрование неактивных данных (например, шифрование на стороне сервера, шифрование на стороне сервера с управляемыми клиентом ключами и другие функции шифрования) | Да | См. в разделе [шифрование виртуальной машины Linux в Azure](/azure/virtual-machines/linux/encrypt-disks.md) и [шифрование дисков на виртуальной Машине Windows](/azure/virtual-machines/windows/encrypt-disks.md). |
| Шифрование при обмене данными (например, шифрование ExpressRoute в виртуальной сети шифрования и шифрование между сетями)| Да | Azure поддерживает виртуальные машины [ExpressRoute](/azure/expressroute) и шифрование виртуальной сети. См. в разделе [шифрования на виртуальных машинах во время передачи](/azure/security/security-azure-encryption-overview.md#in-transit-encryption-in-vms). |
| Управление ключами шифрования (CMK, BYOK, и т.д.)| Да | Управляемые клиентом ключи — это сценарий поддерживается шифрование в Azure; см. в разделе [Общие сведения о шифровании Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms.md).|
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | С помощью HTTPS и SSL. |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Поддержка конечной точки службы| Да | |
| Внедрение поддержки виртуальной сети| Да | . |
| Поддержка сетевой изоляции и Firewalling| Да |  |
| Принудительного туннелирования поддержки| Да | См. в разделе [Настройка принудительного туннелирования с помощью модели развертывания Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Azure Monitor поддержки (Log analytics, Application insights, и т.д.)| Да | См. в разделе [мониторинг и обновление виртуальной машины Linux в Azure](/azure/virtual-machines/linux/tutorial-monitoring.md) и [мониторинг и обновление виртуальной машины Windows в Azure](/azure/virtual-machines/windows/tutorial-monitoring.md). |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Проверка подлинности| Да |  |
| Authorization| Да |  |


## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Управление и ведение журнала и аудит| Да |  |
| Ведение журнала данных и аудита | Нет |  |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка конфигурации management (Управление версиями конфигурации и т. д.)| Да |  | 
