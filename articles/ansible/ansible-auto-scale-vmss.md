---
title: Автоматическое масштабирование масштабируемого набора виртуальных машин в Azure с помощью Ansible
description: Сведения об использовании Ansible для масштабирования масштабируемого набора виртуальных машин с помощью функции автомасштабирования в Azure
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, scale, autoscale, virtual machine, virtual machine scale set, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: 578ad3207f62e74805be056ca11d3bd9b46513da
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792435"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>Автоматическое масштабирование масштабируемого набора виртуальных машин в Azure с помощью Ansible
Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. Ansible можно использовать для управления масштабируемым набором виртуальных машин (VMSS) в Azure так же, как любым другим ресурсом Azure. 

При создании масштабируемого набора вы определяете количество экземпляров виртуальных машин для запуска. По мере изменения потребностей приложения можно автоматически увеличивать или уменьшать это количество. Возможность автоматического масштабирования позволяет удовлетворить пользовательский спрос или среагировать на изменения производительности приложения на протяжении его жизненного цикла. В этой статье вы создадите параметр автомасштабирования и свяжете его с имеющимся масштабируемым набором виртуальных машин. Параметр автомасштабирования позволяет настроить правило для свертывания и развертывания (как вам удобно).

## <a name="prerequisites"></a>Предварительные требования
- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- Имеющийся масштабируемый набор виртуальных машин Azure. Если у вас нет такого набора, [создайте его в Azure с помощью Ansible](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss).

> [!Note]
> Для выполнения примеров сборников схем в этом руководстве требуется Ansible 2.7. 

## <a name="auto-scale-based-on-a-schedule"></a>Автомасштабирование по расписанию   
Чтобы включить автомасштабирование в масштабируемом наборе, сначала необходимо определить профиль автомасштабирования. Этот профиль определяет используемую по умолчанию, минимальную и максимальную емкости масштабируемого набора. Эти ограничения позволяют контролировать затраты за счет того, что экземпляры виртуальных машин не создаются непрерывно, и сбалансировать приемлемую производительность с минимальным числом экземпляров, которые остаются в масштабируемом событии. 

Масштабируемые наборы виртуальных машин можно свертывать и развертывать по расписанию или определенной дате. В этом разделе представлен пример сборника схем Ansible, с помощью которого создается параметр автомасштабирования, увеличивающий в масштабируемых наборах количество экземпляров виртуальных машин — до трех в 10:00 каждый понедельник по тихоокеанскому времени. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Сохраните этот сборник схем как файл *vmss-auto-scale.yml*. Чтобы запустить сборник схем Ansible, используйте команду **ansible-playbook** следующим образом:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>Автомасштабирование на основе данных производительности
При увеличении потребностей приложения в масштабируемом наборе увеличивается нагрузка на экземпляры виртуальной машины. Если такая увеличенная нагрузка представляет собой не просто краткий спрос, а является согласованной, можно настроить правила автомасштабирования, чтобы увеличить число экземпляров виртуальной машины в масштабируемом наборе. После создания этих экземпляров виртуальных машин и развертывания приложений масштабируемые наборы запускают распределение трафика между ними через подсистему балансировки нагрузки. Вы можете контролировать, какие метрики отслеживать, например ЦП или диск, продолжительность соответствия нагрузки приложения заданному пороговому значению, а также количество экземпляров виртуальной машины для добавления в масштабируемый набор.

Масштабируемые наборы виртуальных машин можно свертывать и развертывать на основе пороговых значений метрик производительности по расписанию или определенной дате. В этом разделе представлен пример сборника схем Ansible, с помощью которого выполняется проверка рабочей нагрузки за последние 10 минут в 18:00 каждый понедельник по тихоокеанскому времени. Кроме того, с его помощью выполняется развертывание в масштабируемых наборах до четырех экземпляров виртуальных машин или их свертывание до одного экземпляра в соответствии с процентными метриками ЦП. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Сохраните этот сборник схем как *vmss-auto-scale-metrics.yml*. Чтобы запустить сборник схем Ansible, используйте команду **ansible-playbook** следующим образом:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>Получение сведений об имеющихся параметрах автомасштабирования
Вы можете получить сведения о любом параметре автомасштабирования с помощью модуля *azure_rm_autoscale_facts* в сборнике схем следующим образом:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>Отключение параметров автомасштабирования
Вы можете отключить параметр автомасштабирования, изменив `enabled: true` на `enabled: false` или удалив параметры автомасштабирования с помощью сборника схем следующим образом:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Пример сборника схем Ansible для масштабируемых наборов виртуальных машин](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)