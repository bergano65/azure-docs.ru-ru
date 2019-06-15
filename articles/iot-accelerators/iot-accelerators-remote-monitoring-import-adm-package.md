---
title: Решение для удаленного мониторинга импортировать пакет — Azure | Документация Майкрософт
description: В этой статье описывается, как импортировать пакет автоматического управления устройствами в акселератор решения для удаленного мониторинга
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61443451"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Импорт пакета автоматического управления устройствами в акселератор решения для удаленного мониторинга

Конфигурация автоматического управления устройствами определяет изменения конфигурации для развертывания в группу устройств. В этой статье предполагается, что разработчик в вашей организации уже создал конфигурацию автоматического управления устройствами. Дополнительные сведения о том, как разработчику создать конфигурацию, см. в одной из следующих статей с инструкциями, посвященных Центру Интернета вещей:

- [Настройка и мониторинг устройств Центра Интернета вещей с помощью портала Azure](../iot-hub/iot-hub-auto-device-config.md)
- [Настройка и мониторинг устройств Центра Интернета вещей в масштабе с помощью Azure CLI](../iot-hub/iot-hub-auto-device-config-cli.md)

Разработчик создает и тестирует конфигурацию автоматического управления устройствами в среде разработки. Когда вы будете готовы, можно импортировать конфигурацию в акселератор решения для удаленного мониторинга.

## <a name="export-a-configuration"></a>Экспорт конфигурации

Чтобы экспортировать конфигурацию автоматического управления устройствами из среды разработки, используйте портал Azure:

1. На портале Azure перейдите в центр Интернета вещей, используемый для разработки и тестирования устройств Интернета вещей. Щелкните **Конфигурация устройства Интернета вещей**.

    [![Конфигурация устройства Интернета вещей](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Щелкните конфигурацию, которую вы хотите использовать. Отобразится страница **Сведения о конфигурации устройства**:

    [![Сведения о конфигурации устройства Интернета вещей](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Щелкните **Download configuration file** (Скачать файл конфигурации):

    [![Кнопка скачивания файла конфигурации](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Сохраните JSON-файл как локальный файл с именем **configuration.json**.

Теперь у вас есть файл, содержащий конфигурацию автоматического управления устройствами. В следующем разделе вы импортируете эту конфигурацию в виде пакета в решение для удаленного мониторинга.

## <a name="import-a-package"></a>Импорт пакета

Выполните следующие действия, чтобы импортировать в решение конфигурацию автоматического управления устройствами в виде пакета.

1. В веб-интерфейсе решения для удаленного мониторинга перейдите на страницу **Пакеты**:  ![Страница пакетов](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Щелкните **+ New Package** (+ Новый пакет), выберите тип пакета **Конфигурация** и нажмите кнопку **Обзор**, чтобы выбрать файл **configuration.json**, сохраненный в предыдущем разделе:

    ![Выбор конфигурации](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Нажмите кнопку **Отправить**, чтобы добавить пакет в решение для удаленного мониторинга:

    ![Переданный пакет](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Вы загрузили конфигурацию автоматического управления устройствами в виде пакета. На странице **Развертывания** можно развернуть этот пакет для своих подключенных устройств.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как создать пакет конфигурации и импортировать его в решение для удаленного мониторинга, изучите статью [Руководство. Управление подключенными устройствами в пакетном режиме](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
