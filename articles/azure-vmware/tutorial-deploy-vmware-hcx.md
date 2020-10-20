---
title: Учебник по развертыванию и настройке VMware HCX
description: Узнайте, как развернуть и настроить решение VMware HCX для частного облака Решения Azure VMware.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 58fd8b4518f60f1f736d8c19ddcf62729353f251
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058001"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Развертывание и настройка VMware HCX

В этой статье мы рассмотрим процедуры развертывания и настройки локального соединителя VMware HCX для частного облака Решения Azure VMware. VMware HCX позволяет переносить в Решение Azure VMware ваши рабочие нагрузки VMware и другие подключенные сайты, используя различные типы миграции. В Решении Azure VMware диспетчер облаков уже развернут и настроен, поэтому клиенту понадобится загрузить, активировать и настроить соединитель в своем локальном центре обработки данных VMware.

Соединитель VMware HCX Расширенный (предварительно развернутый в Решении Azure VMware) поддерживает до трех подключений к сайту (из локальной среды к облаку или из облака к облаку). Если требуются более трех подключений к сайту, у клиентов есть возможность включить надстройку [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) (которая в настоящее время находится на этапе *предварительной версии*), отправив [запрос на поддержку](https://rc.portal.azure.com/#create/Microsoft.Support). VMware HCX Enterprise Edition (EE) предоставляется в составе Решения Azure VMware в виде *предварительной версии* функции или службы. Пока VMware HCX EE для Решения Azure VMware находится на этапе *предварительной версии*, это бесплатная функция или служба, на которую распространяется действие условий использования предварительных версий служб. Когда служба VMware HCX EE станет общедоступной, за 30 дней до этой даты вы получите уведомление о том, что для нее будет включено выставление счетов. Вы также можете отключить эту службу или отказаться от ее использования.

Перед началом внимательно изучите разделы [Перед началом работы](#before-you-begin), [Требования к версиям программного обеспечения](#software-version-requirements) и [Предварительные требования](#prerequisites). 

Затем мы последовательно разберем все необходимые процедуры для выполнения следующих задач:

> [!div class="checklist"]
> * Развертывание локального OVA-файла VMware HCX (соединитель HCX)
> * Активация соединителя VMware HCX
> * Связывание локального соединителя HCX с диспетчером облаков HCX Решения Azure VMware
> * Настройка межсетевого соединения (сетевой профиль, профиль вычислений и сетка служб)
> * Завершение установки путем проверки состояния устройства и проверки возможности миграции

После этого можно выполнить рекомендуемые дальнейшие действия в конце этой статьи.  

## <a name="before-you-begin"></a>Перед началом
   
* Ознакомьтесь с [циклом учебников](tutorial-network-checklist.md) по программно определяемому центру обработки данных (SDDC) Решения Azure VMware.
* Просмотрите [документацию по VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html), включая руководство пользователя HCX, и используйте ее для справки.
* Ознакомьтесь с документацией VMware по [миграции виртуальных машин с помощью VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* При желании просмотрите [рекомендации по развертыванию VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* При желании просмотрите связанные материалы VMware по HCX, такие как [ряд блогов](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware vSphere по HCX. 
* При необходимости запросите активацию Azure VMware Solution HCX Enterprise через каналы поддержки Решения Azure VMware.
* При необходимости [проверьте сетевые порты, необходимые для HCX](https://ports.vmware.com/home/VMware-HCX).
* Хотя диспетчер облаков HCX Решения Azure VMware поставляется предварительно настроенным из сети /22, которая предоставляется для частного облака Решения Azure VMware, локальный соединитель HCX требует, чтобы клиент выделял диапазоны сети из своей локальной сети. Эти сети и диапазоны описаны ниже в документе.

Определение размера рабочих нагрузок с точки зрения использования вычислительных ресурсов и ресурсов хранилища — это обязательный этап планирования. Его следует выполнять при первоначальном планировании среды частного облака. 

Вы также можете определить размер рабочих нагрузок, выполнив [оценку Решения Azure VMware](../migrate/how-to-create-azure-vmware-solution-assessment.md) на портале службы "Миграция Azure".

## <a name="software-version-requirements"></a>Требования к версиям программного обеспечения

Ниже перечислены минимально необходимые версии компонентов инфраструктуры. 
                                                         
| Тип компонента    | Требования к исходной среде    | Требования к целевой среде   |
| --- | --- | --- |
| с сервером vCenter   | 5.1<br/><br/>Если используется версия 5.5 U1 или более ранняя версия, используйте автономный пользовательский интерфейс HCX для операций HCX.  | 6.0 U2 и более поздних версий   |
| ESXi   | 5.0    | ESXi 6.0 и более поздних версий   |
| NSX    | Для сетевого расширения HCX логических коммутаторов в источнике: NSXv 6.2+ или NSX-T 2.4+   | NSXv 6.2+ или NSX-T 2.4+<br/><br/>Для маршрутизации близкого взаимодействия HCX: NSXv 6.4+ (NSX-T не поддерживает маршрутизацию близкого взаимодействия) |
| vCloud Director   | Не требуется — на исходном сайте отсутствует взаимодействие с vCloud Director | Если целевая среда интегрируется с vCloud Director, необходима версия не ниже 9.1.0.2.  |

## <a name="prerequisites"></a>Предварительные требования

### <a name="network-and-ports"></a>Сеть и порты

* Для подключения между локальными средами и каналом ExpressRoute для SDDC Решения Azure VMware настроена служба [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md).

* Все необходимые порты должны быть открыты для обмена данными между локальными компонентами, а также передачи данных из локальных сред в SDDC Решения Azure VMware (см. [документацию по VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).


### <a name="ip-addresses"></a>IP-адреса

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Развертывание OVA-файла соединителя VMware HCX в локальной среде

>[!NOTE]
>Перед развертыванием виртуального устройства в локальном решении vCenter необходимо скачать OVA-файл соединителя VMware HCX. 

1. Откройте окно браузера, войдите в HCX Manager Решения Azure VMware на странице `https://x.x.x.9` через порт 443 под учетными данными пользователя **cloudadmin**, а затем перейдите на страницу **Поддержка**.

   >[!TIP]
   >Запишите IP-адрес диспетчера облаков HCX в Решении Azure VMware. Чтобы узнать IP-адрес диспетчера облаков HCX, в колонке Решения Azure VMware последовательно выберите **Управление** > **Подключение**, после чего перейдите на вкладку **HCX**. 
   >
   >Пароль vCenter был определен при настройке частного облака.

1. Щелкните ссылку **загрузки**, чтобы загрузить OVA-файл соединителя VMware HCX.

1. Перейдите к локальному решению vCenter и выберите шаблон OVF (загруженный вами OVA-файл), чтобы выполнить развертывание соединителя HCX в локальном решении vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Выберите имя и расположение ресурса или кластера, в котором развертывается соединитель HCX, а затем проверьте сведения и необходимые ресурсы.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Просмотрите условия лицензии и, если вы с ними согласны, выберите нужное хранилище и сеть, а затем нажмите **Далее**.

1. В разделе **Настройка шаблона** введите все необходимые сведения. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Нажмите кнопку **Далее**, проверьте конфигурацию, а затем нажмите **Готово**, чтобы развернуть OVA-файл соединителя HCX.
     
   >[!NOTE]
   >Как правило, для развертывания соединителя VMware HCX Manager используется сеть управления кластера.  
   
   > [!IMPORTANT]
   > Возможно, после завершения развертывания потребуется включить виртуальное устройство вручную.
   > Подождите 10–15 минут после включения устройства HCX перед переходом к следующему шагу.

Полный обзор этого шага см. в видео [Решение Azure VMware: развертывание устройства VMware HCX](https://www.youtube.com/embed/BwSnQeefnso) . 


## <a name="activate-vmware-hcx"></a>Активация VMware HCX

После развертывания OVA-файла соединителя VMware HCX в локальной среде и запуска устройства вы можете перейти к активации, однако сначала необходимо получить лицензионный ключ на портале Решения Azure VMware в Azure.

1. На портале Решения Azure VMware выберите **Управление** > **Подключение**, перейдите на вкладку **HCX**, а затем выберите **Добавить**.

1. Войдите в VMware HCX Manager в локальной среде по адресу `https://HCXManagerIP:9443`, используя учетные данные **администратора**. 

   > [!IMPORTANT]
   > Не забудьте указать номер порта `9443` вместе с IP-адресом VMware HCX Manager.

1. В разделе **Лицензирование** введите свой **расширенный ключ HCX**.  
   
    > [!NOTE]
    > VMware HCX Manager должен иметь открытый доступ к Интернету или настроенный прокси-сервер.

1. В поле **Datacenter Location** (Расположение центра обработки данных) укажите расположение, ближайшее к локальной среде, в которой установлен диспетчер VMware HCX Manager.

1. Измените **имя системы** или примите значение по умолчанию.
   
1. Можно либо завершить работу позже, либо продолжить. Выберите параметр **Yes, Continue** (Да, продолжить) для продолжения.
    
1. В разделе **Connect your vCenter** (Подключение к vCenter) укажите полное доменное имя или IP-адрес сервера vCenter и соответствующие учетные данные, а затем выберите **Continue** (Продолжить).
   
1. В разделе **Configure SSO/PSC** (Настройка единого входа или PSC) укажите полное доменное имя или IP-адрес контроллера PSC, а затем выберите **Continue** (Продолжить).
   
   >[!NOTE]
   >Обычно это значение совпадает с полным доменным именем или IP-адресом vCenter.

1. Убедитесь, что все входные данные указаны правильно, и выберите **Restart** (Перезапустить).
    
   > [!NOTE]
   > После перезапуска будет наблюдаться определенная задержка с отображением запроса следующего шага.
   >
   >После перезапуска служб очень важно убедиться, что для vCenter на экране отображается зеленый кружок. Параметры конфигурации vCenter и единого входа должны быть заданы надлежащим образом и совпадать с теми, которые показаны на предыдущем экране.

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Полный обзор этого шага см. в видео [Решение Azure VMware: активация VMware HCX](https://www.youtube.com/embed/BkAV_TNYxdE).


## <a name="configure-vmware-hcx-connector"></a>Настройка соединителя VMware HCX

Теперь вы можете добавить связывание сайтов, создать профиль сети и вычислений и включить такие службы, как миграция, расширение сети или аварийное восстановление. 

### <a name="add-a-site-pairing"></a>Добавление связывания сайта

Вы можете подключить диспетчер облаков VMware HCX в Решении Azure VMware к соединителю VMware HCX в центре обработки данных (связать их между собой). 

1. Войдите в vCenter в локальной среде и в разделе **Home** (Домашняя страница) выберите **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. В разделе **Infrastructure** (Инфраструктура) выберите **Site Pairing** (Связывание сайтов), а затем выберите в середине экрана **Connect To Remote Site** (Подключиться к удаленному сайту). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Введите **URL-адрес или IP-адрес удаленной платформы HCX**, который вы записали ранее, имя пользователя cloudadmin@vsphere.local и **пароль** Решения Azure VMware, а затем выберите **Подключить**.

   > [!NOTE]
   > **URL-адрес удаленной платформы HCX** — это IP-адрес диспетчера облаков HCX в частном облаке Решения Azure VMware. Обычно это заканчивающийся на ".9" адрес в сети управления.  Например, если адрес vCenter — 192.168.4.2, то URL-адресом HCX будет 192.168.4.9.
   >
   > **Пароль** такой же, как тот, что вы использовали для входа в vCenter. Этот пароль был определен на начальном экране развертывания.

   На экране будет показано, что диспетчер облаков HCX в Решении Azure VMware и соединитель HCX в локальной среде подключены (связаны) друг с другом.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter.":::

Полный обзор этого шага см. в видео [Решение Azure VMware: связывание сайтов VMware HCX](https://www.youtube.com/embed/sKizDCRHOko).



### <a name="create-network-profiles"></a>Создание сетевых профилей

VMware HCX развертывает подмножество виртуальных устройств (автоматически), которые требуют наличия нескольких сегментов IP.  При создании сетевых профилей вы задаете сегменты IP-адресов, которые были определены на [этапе предварительного планирования развертывания сегментов сети VMware HCX и подготовки к его осуществлению](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Вы создадите четыре сетевых профиля:

   - Управление
   - vMotion
   - Репликация
   - Исходящая связь

1. Выберите **Infrastructure**, select **Interconnect** > **Multi-Site Service Mesh** > **Network Profiles** > **Create Network Profile** (Инфраструктура > Межсоединение > Сетка многосайтовой службы > Сетевые профили > Создать сетевой профиль).

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Для каждого сетевого профиля выберите сеть и группу портов, укажите имя, создайте пул IP-адресов для конкретного сегмента, а затем выберите **Создать**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter.":::

Полный обзор этого шага см. в видео [Решение Azure VMware: создание сетевого профиля в VMware HCX](https://www.youtube.com/embed/NhyEcLco4JY).


### <a name="create-compute-profile"></a>Создание вычислительного профиля

1. Выберите **Compute Profiles** > **Create Compute Profile** (Вычислительный профиль >Создать вычислительный профиль).

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Введите имя профиля и нажмите кнопку **Continue** (Продолжить).  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Выберите службы, которые необходимо включить, такие как миграция, сетевое расширение и аварийное восстановление, а затем нажмите кнопку **Continue** (Продолжить).
  
   > [!NOTE]
   > Как правило, здесь ничего не меняется.

1. В разделе **Select Service Resources** (Выбор ресурсов служб) выберите один или несколько ресурсов служб (кластеры), для которых должны быть включены выбранные службы VMware HCX.  

1. Если кластеры отображаются в локальном центре обработки данных, выберите **Continue** (Продолжить).

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. На странице **Select Datastore** (Выбор хранилища данных) выберите ресурс хранилища данных для развертывания устройств VMware HCX Interconnect и нажмите кнопку **Continue** (Продолжить).

   Когда выбрано несколько ресурсов, VMware HCX использует первый выбранный ресурс, пока его емкость не будет исчерпана.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Выберите **Management Network Profile** (Профиль сети управления), созданный на предыдущих шагах, а затем нажмите кнопку **Continue** (Продолжить).  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Профиль сети управления позволяет устройствам VMware HCX обмениваться данными с vCenter и обеспечивает достижимость узлов ESXi.

1. Выберите **Uplink Network Profile** (Сетевой профиль канала исходящей связи), созданный на предыдущих шагах, и нажмите кнопку **Continue** (Продолжить).

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Выберите **vMotion Network Profile** (Сетевой профиль vMotion), созданный на предыдущих шагах, и нажмите кнопку **Continue** (Продолжить).

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Выберите **vSphere Replication Network Profile** (Сетевой профиль репликации vSphere), созданный на предыдущих шагах, и нажмите кнопку **Continue** (Продолжить).

   В большинстве случаев профиль репликации совпадает с сетевым профилем управления.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Выберите значения в поле **Distributed Switches for Network Extensions** (Распределенные коммутаторы для сетевых расширений), указав распределенные виртуальные коммутаторы, через которые подключаются виртуальные машины, переносимые в Решение Azure VMware в расширенной сети уровня 2, а затем нажмите кнопку **Continue** (Продолжить).

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Проверьте правила подключения и нажмите **Continue** (Продолжить).  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Нажмите **Готово**, чтобы создать вычислительный профиль.

   Появится экран, аналогичный показанному ниже.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Полный обзор этого шага см. в видео [Решение Azure VMware: создание профиля вычислений в VMware HCX](https://www.youtube.com/embed/qASXi5xrFzM).




### <a name="create-service-mesh"></a>Создание сетки служб

Теперь пришло время настроить сетку служб между локальной средой и SDDC Решения Azure VMware.

1. В разделе **Infrastructure** (Инфраструктура) выберите **Interconnect** > **Service Mesh** > **Create Service Mesh** (Межсоединение > Сетка служб > Создать сетку служб).    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Просмотрите предварительно заполненные сайты, а затем выберите **Continue** (Продолжить). 

   >[!NOTE]
   >Если сетка служб настраивается впервые, вам не потребуется ничего менять на этом экране.  

1. Выберите значения в раскрывающихся списках для вычислительных профилей исходной и удаленной среды, а затем выберите **Continue** (Продолжить).  

   От выбранных значений зависит, для каких ресурсов виртуальные машины смогут использовать службы VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Просмотрите службы, которые будут включены, а затем выберите **Continue** (Продолжить).  

1. В разделе **Расширенная конфигурация — переопределение сетевых профилей каналов исходящей связи** нажмите **Продолжить**.  Сетевые профили каналов исходящей связи обеспечивают подключение к сети, через которую можно обращаться к устройствам межсоединения удаленного сайта.  
  
1. Проверьте параметры на экране **Advanced Configuration — Network Extension Appliance Scale Out** (Расширенная конфигурация: горизонтальное увеличение масштаба устройство расширения сети) и выберите **Continue** (Продолжить). 

1. На экране **Advanced Configuration — Traffic Engineering** (Расширенная конфигурация: проектирование трафика) проверьте параметры и внесите необходимые изменения, а затем выберите **Continue** (Продолжить).

1. Проверьте предварительную версию топологии и нажмите **Продолжить**.

1. Введите понятное имя для этой сетки служб и нажмите **Finish** (Готово) для завершения.  

1. Выберите **View Tasks** (Просмотр задач) для мониторинга развертывания. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter.":::

   После успешного завершения развертывания сетки служб вы увидите, что службы обозначены зеленым цветом.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Проверьте работоспособность сетки службы, проверив состояние устройства. Для этого выберите **Interconnect** > **Appliances** (Межсоединение > Устройства).

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Полный обзор этого шага см. в видео [Решение Azure VMware: создание сетки служб VMware HCX](https://www.youtube.com/embed/FyZ0d3P_T24).



### <a name="optional-create-a-network-extension"></a>Создание расширения сети (необязательно)

Если вы хотите расширить какие-либо сети из локальной среды, включив в них Решение Azure VMware, выполните для этого следующие действия.

1. В разделе **Services** (Службы) выберите **Network Extension** (Расширение сети), а затем — **Create a Network Extension** (Создать расширение сети).

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::.

1. Выберите каждую из сетей, которые требуется расширить, включив в них Решение Azure VMware, а затем нажмите кнопку **Next** (Далее).

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter.":::

1. Введите IP-адрес локального шлюза для каждой из расширяемых сетей, а затем нажмите кнопку **Submit** (Отправить). 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter.":::

   Расширение сети занимает несколько минут. Когда оно будет выполнено, состояние изменится на **extension complete** (расширение завершено).

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Переход к локальному решению vCenter и выбор шаблона OVF для развертывания в локальном решении vCenter." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Полный обзор этого шага см. в видео [Решение Azure VMware: расширение сети VMware HCX](https://www.youtube.com/embed/cNlp0f_tTr0).


## <a name="next-steps"></a>Дальнейшие действия

Если вы выполнили все предыдущие шаги, состояние туннеля межсоединения устройства — **UP** (Активен), а сам он обозначен зеленым цветом, вы можете перенести и защитить виртуальные машины Решения Azure VMware с помощью VMware HCX.  Решение Azure VMware поддерживает миграцию рабочих нагрузок (с расширением сети или без).  Вы по-прежнему можете выполнять миграцию рабочих нагрузок в среде vSphere, создать сети в локальной среде и развернуть виртуальные машины в этих сетях.  Дополнительные сведения см.в [документации по VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) и разделе [Миграция виртуальных машин с помощью VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) в технической документации VMware, где подробно описано использование HCX.
