---
title: Импорт пакета Edge в акселератор решения для удаленного мониторинга (Azure) | Документация Майкрософт
description: В этой статье описывается, как импортировать пакет IoT Edge в акселератор решения для удаленного мониторинга
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51827489"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Импорт пакета IoT Edge в акселератор решения для удаленного мониторинга

Манифест развертывания определяет модули, которые будут развернуты на устройстве IoT Edge. В этой статье предполагается, что разработчик в вашей организации уже создал манифест развертывания. Дополнительные сведения о том, как разработчику создать манифест, см. в одной из следующих статей с инструкциями, посвященных IoT Edge:

- [Развертывание модулей IoT Edge Azure с помощью портала Azure](../iot-edge/how-to-deploy-modules-portal.md)
- [Развертывание модулей IoT Edge Azure с помощью интерфейса командной строки Azure](../iot-edge/how-to-deploy-modules-cli.md)
- [Развертывание модулей Azure IoT Edge из Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Разработчик создает и проверяет манифест развертывания в среде разработки. Когда вы будете готовы, можно импортировать манифест в акселератор решения для удаленного мониторинга.

## <a name="export-a-manifest"></a>Экспорт манифеста

Экспортируйте манифест развертывания из вашей среды разработки, используя портал Azure:

1. На портале Azure перейдите в центр Интернета вещей, используемый для разработки и тестирования устройств IoT Edge. Щелкните **IoT Edge**, а затем **Развертывания IoT Edge**: ![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Щелкните развертывание с нужной конфигурацией. Отобразится страница **Сведения о развертывании**: ![Сведения о развертывании IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Щелкните **Скачать манифест IoT Edge**: ![Скачать манифест развертывания](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Сохраните JSON-файл как локальный файл с именем **deploymentmanifest.json**.

Теперь у вас есть файл, содержащий манифест развертывания. В следующем разделе вы импортируете этот манифест в виде пакета в решение для удаленного мониторинга.

## <a name="import-a-package"></a>Импорт пакета

Выполните следующие действия, чтобы импортировать манифест развертывания Edge как пакет в ваше решение:

1. В веб-интерфейсе решения для удаленного мониторинга перейдите на страницу **Пакеты**: ![Страница пакетов](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Щелкните **+ New Package** (+ Новый пакет), выберите тип пакета **Манифест Edge** и нажмите кнопку **Обзор**, чтобы выбрать файл **deploymentmanifest.json**, сохраненный в предыдущем разделе: ![Выбор манифеста](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Нажмите кнопку **Отправить**, чтобы добавить пакет в решение для удаленного мониторинга: ![Переданный пакет](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Вы передали манифест развертывания IoT Edge как пакет. На странице **Развертывания** можно развернуть этот пакет для своих подключенных устройств IoT Edge.

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как развертывать модули на устройстве IoT Edge из решения для удаленного мониторинга, ознакомьтесь с дополнительными сведениями об [IoT Edge](../iot-edge/about-iot-edge.md).
