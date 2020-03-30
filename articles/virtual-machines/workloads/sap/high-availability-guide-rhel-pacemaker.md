---
title: Настройка Pacemaker на RHEL в Azure (ru) Документы Майкрософт
description: Настройка кластера Pacemaker в Red Hat Enterprise Linux в Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: radeltch
ms.openlocfilehash: 21c551721815847eea4cb1435298ea6f7bf37966
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264483"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Настройка кластера Pacemaker в Red Hat Enterprise Linux в Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Прежде всего прочитайте следующие примечания и документы SAP:

* примечание к SAP [1928533], которое содержит:
  * список размеров виртуальных машин Azure, поддерживаемых для развертывания ПО SAP;
  * важные сведения о доступных ресурсах для каждого размера виртуальной машины Azure;
  * сведения о поддерживаемом программном обеспечении SAP и сочетаниях операционных систем и баз данных;
  * сведения о требуемой версии ядра SAP для Windows и Linux в Microsoft Azure.
* примечание к SAP [2015553], в котором описываются предварительные требования к SAP при развертывании программного обеспечения SAP в Azure;
* примечание к SAP [2002167], содержащее рекомендуемые параметры ОС для Red Hat Enterprise Linux;
* примечание к SAP [2009879], содержащее рекомендации по SAP HANA для Red Hat Enterprise Linux;
* примечание к SAP [2178632], содержащее подробные сведения обо всех доступных метриках мониторинга для SAP в Azure;
* примечание к SAP [2191498], содержащее сведения о необходимой версии агента SAP Host Agent для Linux в Azure;
* примечание к SAP [2243692], содержащее сведения о лицензировании SAP в Linux в Azure;
* примечание к SAP [1999351], содержащее дополнительные сведения об устранении неполадок, связанных с расширением для расширенного мониторинга Azure для SAP;
* [вики-сайт сообщества SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), содержащий все необходимые примечания к SAP для Linux;
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание виртуальных машин Azure для SAP на Linux (эта статья)][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide]
* [Репликация системы SAP HANA в кластере pacemaker](https://access.redhat.com/articles/3004101)
* Общая документация по RHEL
  * [Общие сведения о надстройке для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Администрирование надстройки для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Справочник по надстройке для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Политика поддержки кластеров высокой доступности RHEL - sbd и fence_sbd](https://access.redhat.com/articles/2800691)
* Документация RHEL, специфичная для Azure:
  * [Политики поддержки для кластеров высокой доступности RHEL — виртуальные машины Microsoft Azure как члены кластера](https://access.redhat.com/articles/3131341)
  * [Установка и настройка кластера высокой доступности Red Hat Enterprise Linux 7.4 (и более поздних версий) в Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Налажить SAP S/4HANA ASCS/ERS с автономным сервером Enqueue 2 (ENSA2) в Pacemaker на RHEL 7.6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Установка кластера

![Общие сведения о Pacemaker в RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat не поддерживает программно-эмулированный сторожевой пес. Red Hat не поддерживает SBD на облачных платформах. Для получения подробной информации [см. Политики поддержки для кластеров высокой доступности RHEL - sbd и fence_sbd.](https://access.redhat.com/articles/2800691)
> Единственным поддерживаемым механизмом ограждения для кластеров Pacemaker Red Hat Enterprise Linux на Azure является агент забора Azure.  

Следующие элементы прикрепляются либо **с «А»,** применимыми ко всем узлам, и это **применимо** только к узлам 1 или **No2,** применимо только к уму 2.

1. **[A]** Регистрация

   Зарегистрируйте виртуальные машины и подключите их к пулу, содержащему репозитории для RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Обратите внимание, что, прикрепив пул к изображению Azure Marketplace PAYG RHEL, вы будете эффективно с двойным счетом использовать RHEL: один раз для изображения PAYG и один раз для права RHEL в присоединенном пуле. Чтобы смягчить это, Azure теперь предоставляет изображения BYOS RHEL. Более подробная информация доступна [здесь](../redhat/byos.md).

1. **[A]** Включение RHEL для репозиториев SAP

   Чтобы установить необходимые пакеты, включите приведенные ниже репозитории.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Установка надстройки для обеспечения высокой доступности RHEL

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Мы рекомендуем следующие версии агента Azure Fence (или позже) для клиентов воспользоваться более быстрым временем сбоя, если остановка ресурса выходит из строя или кластерные узлы больше не могут общаться друг с другом:  
   > RHEL 7.6: забор-агенты-4.2.1-11.el7_6.8  
   > RHEL 7.5: забор-агенты-4.0.11-86.el7_5.8.8  
   > RHEL 7.4: забор-агенты-4.0.11-66.el7_4.12  
   > Для получения дополнительной информации см. [Azure VM работает как RHEL Высокая доступность кластера член занять очень много времени, чтобы быть огорожены, или ограждение не удается / раз, прежде чем VM выключается](https://access.redhat.com/solutions/3408711).

   Проверьте версию агента забора Azure. При необходимости обновите его до версии, равной или позже указанной выше.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Если вам нужно обновить агент Azure Fence, и если вы используете пользовательскую роль, не забудьте обновить пользовательскую роль, чтобы включить **действие powerOff.** Для получения подробной информации [см. Создать пользовательскую роль для агента забора](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** Установите разрешения имен.

   Можно использовать DNS-сервер или внести изменения в файл /etc/hosts на всех узлах. В этом примере показано, как использовать файл /etc/hosts.
   Замените IP-адрес и имя узла в следующих командах. Преимущество использования /etc/hosts в том, что кластер становится независимым от службы DNS, которая также может являться единой точкой отказа.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Вставьте следующие строки в /etc/hosts. Измените IP-адрес и имя узла в соответствии со своей средой.

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **Изменение** пароля hacluster на тот же пароль

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Добавление правил брандмауэра для pacemaker

   Добавьте приведенные ниже правила брандмауэра для всех взаимодействий между узлами кластера.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Включение основных служб кластера

   Чтобы включить службу Pacemaker и запустить ее, выполните приведенные ниже команды.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Создание кластера Pacemaker

   Чтобы выполнить проверку подлинности узлов и создать кластер, выполните приведенные ниже команды. Установите токен в значение 30 000, чтобы разрешить обслуживание с сохранением памяти. Дополнительные сведения см. в [этой статье для Linux][virtual-machines-linux-maintenance].

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
   sudo pcs status

   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** Задание требуемых голосов

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Создание устройства STONITH

Устройство STONITH использует субъект-службу для авторизации в Microsoft Azure. Чтобы создать субъект-службу, выполните следующее.

1. Перейдите на сайт <https://portal.azure.com>.
1. Откройте колонку "Azure Active Directory".  
   Перейдите в колонку "Свойства" и запишите идентификатор каталога. Это **идентификатор клиента**.
1. Щелкните "Регистрация приложений".
1. Нажмите новая регистрация
1. Введите имя, выберите "Счета только в каталоге этой организации" 
2. Выберите тип приложения "Web", введите URL-адрес регистрации (например, http:/localhost)\/и нажмите Добавить  
   URL-адрес входа не используется и может быть любым допустимым URL-адресом.
1. Выберите Сертификаты и Секреты, а затем нажмите Новый секрет клиента
1. Введите описание для нового ключа, выберите "Никогда не истекает" и нажмите Добавить
1. Запишите его значение. Он используется в качестве **пароля** для директора службы
1. Щелкните Обзор. Запишите идентификатор приложения. Он используется в качестве имени пользователя **(идентификатор входа** в приведенные ниже шаги) директора службы

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Создайте пользовательскую роль для агента ограждения.

У субъекта-службы по умолчанию нет разрешений на доступ к ресурсам Azure. Необходимо дать Главному сервису разрешения на запуск и остановку (отключение питания) всех виртуальных машин кластера. Если вы еще не создали эту пользовательскую роль, ее можно создать с помощью [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) или [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).

Используйте следующее содержимое для входного файла. Необходимо адаптировать содержимое для ваших подписок, поэтому замените c276fc76-9cd4-44c9-99a7-4fd71546436e и e91d47c4-76f3-4271-a796-21b4ecfe3624 идентификаторами своих подписок. Если у вас имеется только одна подписка, удалите вторую запись в AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Назначьте пользовательскую роль субъекту-службе.

Назначьте субъекту-службе пользовательскую роль Linux Fence Agent Role, созданную в последней главе. Больше не используйте роль владельца!

1. Перейдите на сайт https://portal.azure.com.
1. Откройте колонку "Все ресурсы".
1. Выберите виртуальную машину первого узла кластера.
1. Выберите "Управление доступом (IAM)".
1. Выберите "Добавить назначение ролей".
1. Выберите роль Linux Fence Agent Role.
1. Введите имя созданного ранее приложения.
1. Щелкните Сохранить

Повторите предыдущие шаги для второго узла кластера.

### <a name="1-create-the-stonith-devices"></a>**[1]** Создайте устройства STONITH.

После изменения разрешений для виртуальных машин можно настроить устройства STONITH в кластере.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Чтобы настроить устройство ограждения, выполните приведенную ниже команду.

> [!NOTE]
> Параметр pcmk_host_map требуется только в том случае, если имена узлов RHEL и Azure не совпадают. См. раздел команды, выделенный полужирным шрифтом.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Разрешите использование устройства STONITH.

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Дальнейшие действия

* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Azure][deployment-guide]
* [Развертывание СУБД виртуальных машин Azure для SAP][dbms-guide]
* Дополнительные сведения об установке высокого уровня доступности и плана для аварийного восстановления SAP HANA на виртуальных машинах Azure см. в статье [Высокий уровень доступности SAP HANA на виртуальных машинах Azure][sap-hana-ha].
