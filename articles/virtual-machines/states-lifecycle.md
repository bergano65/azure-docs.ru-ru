---
title: Жизненный цикл и состояния виртуальной машины в Azure
description: Общие сведения о жизненном цикле виртуальной машины в Azure, включая описание различных состояний, в которых виртуальная машина может находиться в любое время.
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: 0613b4c444b9eacaaf2b9d3e0795f4872cb903f3
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182955"
---
# <a name="virtual-machines-lifecycle-and-states"></a>Жизненный цикл и состояния виртуальных машин

Виртуальные машины Azure проходят различные состояния, которые могут быть отнесены к состояниям *подготовки* и состояниям *включения*. Цель этой статьи — описать эти состояния и показать, когда клиентам будет выставлен счет, например, за использование экземпляра. 

## <a name="power-states"></a>Состояния включения

Состояние включения представляет собой последнее известное состояние виртуальной машины.

![Схема состояния включения виртуальной машины](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
В следующей таблице представлено описание каждого состояния экземпляра и указано, будет ли выставлен счет за его использование.

:::row:::
   :::column span="":::

   **Состояние**
   
   :::column-end:::
   :::column span="":::

   **Описание**

   :::column-end:::
   :::column span="":::

   **Счет за использование экземпляра**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Запуск**

   :::column-end:::
   :::column span="":::

   Выполняется запуск виртуальной машины.

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **Счет не выставляется**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Выполнение**

   :::column-end:::
   :::column span="":::

   Нормальное рабочее состояние для виртуальной машины

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **Выставляются**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Остановка**

   :::column-end:::
   :::column span="":::

   Это переходное состояние. По завершении оно будет отображаться как **Остановлено**.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Выставляются**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Остановлена**

   :::column-end:::
   :::column span="":::

   Виртуальная машина была отключена из гостевой ОС или с помощью API PowerOff.

   Оборудование по-прежнему выделяется на виртуальную машину и остается на узле.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Выставляются***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Отмена выделения**

   :::column-end:::
   :::column span="":::

   Переходное состояние. По завершении виртуальная машина будет отображаться как **Освобождена**.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Не оплачивается***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Освобождено**

   :::column-end:::
   :::column span="":::

   Виртуальная машина была успешно остановлена и удалена с узла.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Счет не выставляется**

   :::column-end:::
:::row-end:::


&#42; некоторые ресурсы Azure, например диски и сети, взимается плата. Лицензии на программное обеспечение на экземпляре не несут расходы.

## <a name="provisioning-states"></a>Состояния подготовки

Состояние подготовки — это состояние инициированной пользователем операции уровня управления на виртуальной машине. Эти состояния отделены от состояния включения виртуальной машины.

- **Создание**. Создается виртуальная машина.

- **Обновление**. Обновление модели для существующей виртуальной машины. Некоторые немодельные изменения в виртуальной машине, такие как "Запуск/Перезапуск", также подпадают под обновление.

- **Удаление**. Удаление виртуальной машины.

- **Освобождение**. Это место, где виртуальная машина остановлена и удалена с узла. Освобождение виртуальной машины считается обновлением, поэтому она отображает состояния подготовки, связанные с обновлением.



Ниже приведены состояния переходной операции после того, как платформа приняла инициированное пользователем действие.

:::row:::
   :::column span="":::

   **Состояние**
   
   :::column-end:::
   :::column span="2":::

   **Описание**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Создание**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Обновление**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Удаление**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Состояния подготовки ОС**
   
   :::column-end:::
   :::column span="2":::

   **Описание**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   Если виртуальная машина создается с помощью образа ОС, а не специализированного образа, то могут наблюдаться следующие подсостояния.

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **оспровисионингинпрогресс**

   :::column-end:::
   :::column span="2":::

   Виртуальная машина запущена, и выполняется установка гостевой ОС.
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **оспровисионингкомплете**

   :::column-end:::
   :::column span="2":::
   
   Кратковременное состояние. Виртуальная машина быстро переходит к состоянию **Успешно**, если не нужно устанавливать какие-либо расширения. Установка расширений может занять некоторое время.
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **Примечание**. Подготовка ОС может перейти в состояние **Сбой**, если произошел сбой ОС или ОС не устанавливается вовремя. Клиентам будет выставлен счет за развернутую виртуальную машину в инфраструктуре.

   :::column-end:::

:::row-end:::

После завершения операции виртуальная машина перейдет в одно из следующих состояний.

- **Успешно**. Действия, инициированные пользователем, завершены.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Сбой**. Представляет собой сбой при выполнении операции. Для получения дополнительных сведений и возможных решений см. коды ошибок.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Представление экземпляра виртуальной машины

API представления экземпляра предоставляет информацию о состоянии выполнения виртуальной машины. Дополнительные сведения см. в разделе [Virtual Machines — Instance View](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) (Просмотр экземпляров виртуальных машин в документации по API).

Обозреватель ресурсов Azure предоставляет простой пользовательский интерфейс для просмотра состояния выполнения виртуальной машины — [Обозреватель ресурсов](https://resources.azure.com/).

Состояния подготовки отображаются в свойствах виртуальной машины и в представлении экземпляра. Состояния включения доступно в представлении экземпляра виртуальной машины.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о мониторинге виртуальной машины см. в статье [мониторинг виртуальных машин в Azure](../azure-monitor/insights/monitor-vm-azure.md).