---
title: Учебник по развертыванию и настройке VMware HCX
description: Узнайте, как развернуть и настроить решение VMware HCX для частного облака Решения Azure VMware.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: c78eae11497702054bb54b5980228fd0a3962577
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367777"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Развертывание и настройка VMware HCX

В этой статье показано, как развернуть и настроить локальный соединитель VMware HCX для частного облака Решения Azure VMware. С помощью VMware HCX вы можете перенести рабочие нагрузки VMware в Решение Azure VMware и на другие подключенные сайты, используя разные типы миграции. Решение Azure VMware развертывает и настраивает HCX Cloud Manager, поэтому вам нужно скачать, активировать и настроить соединитель HCX в локальном центре обработки данных VMware.

Расширенный соединитель VMware HCX уже развернут в Решении Azure VMware. Он поддерживает до трех подключений к сайтам (из локальной среды в облако или из облака в облако). Если вам нужно более трех подключений к сайтам, создайте [запрос в службу поддержки](https://rc.portal.azure.com/#create/Microsoft.Support), чтобы включить надстройку [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/). Сейчас надстройка доступна в предварительной версии. 

>[!NOTE]
>VMware HCX Enterprise Edition (EE) предоставляется в составе Решения Azure VMware как предварительная версия службы. Эту службу можно использовать бесплатно на условиях предварительной версии. Когда служба VMware HCX EE станет общедоступной, вы получите уведомление о начале выставления счетов за 30 дней до этого момента. Вы сможете отключить эту службу или отказаться от ее использования.

Прежде всего внимательно изучите разделы этой статьи [Перед началом работы](#before-you-begin), [Требования к версиям программного обеспечения](#software-version-requirements) и [Предварительные требования](#prerequisites). 

Затем мы последовательно разберем все необходимые процедуры для выполнения следующих задач:

> [!div class="checklist"]
> * развертывание локального OVA-файла VMware HCX (соединитель HCX);
> * активация соединителя VMware HCX;
> * связывание локального соединителя HCX с HCX Cloud Manager Решения Azure VMware;
> * настройка межсетевого соединения (сетевой профиль, профиль вычислений и сетка служб);
> * завершение установки путем проверки состояния устройства и проверки возможности миграции.

Завершив работу, вы сможете выполнить рекомендуемые дальнейшие действия, которые перечислены в конце этой статьи.  

## <a name="before-you-begin"></a>Перед началом
   
* Ознакомьтесь с [циклом руководств](tutorial-network-checklist.md) по программно-определяемому центру обработки данных (SDDC) Решения Azure VMware.
* Просмотрите [документацию по VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html), включая руководство пользователя HCX, и используйте ее для справки.
* Ознакомьтесь с документацией VMware по [миграции виртуальных машин с помощью VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* При желании просмотрите [рекомендации по развертыванию VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* При желании просмотрите связанные материалы VMware по HCX, например [цикл блогов](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html), посвященных VMware vSphere. 
* При необходимости запросите активацию Azure VMware Solution HCX Enterprise через каналы поддержки Решения Azure VMware.
* При необходимости [проверьте сетевые порты, необходимые для HCX](https://ports.vmware.com/home/VMware-HCX).
* В HCX Cloud Manager для Решения Azure VMware предварительно уже настроена сеть /22, которая предоставляется для частного облака Решения Azure VMware. Для локального соединителя HCX нужно выделить диапазоны адресов из локальной сети клиента. Эти сети и диапазоны описаны далее в этой статье.

Определение размера рабочих нагрузок с точки зрения использования вычислительных ресурсов и ресурсов хранилища — это обязательный этап планирования. Этап определения размера следует выполнять при первоначальном планировании среды для частного облака. 

Вы можете оценить размер рабочих нагрузок, выполнив [оценку Решения Azure VMware](../migrate/how-to-create-azure-vmware-solution-assessment.md) на портале Миграции Azure.

## <a name="software-version-requirements"></a>Требования к версиям программного обеспечения

Ниже перечислены минимально необходимые версии компонентов инфраструктуры. 
                                                         
| Тип компонента    | Требования к исходной среде    | Требования к целевой среде   |
| --- | --- | --- |
| с сервером vCenter   | 5.1<br/><br/>Если вы используете версию 5.5 U1 или более раннюю, используйте для операций HCX автономный пользовательский интерфейс HCX.  | 6.0 U2 и более поздних версий   |
| ESXi   | 5.0    | ESXi 6.0 и более поздних версий   |
| NSX    | Для сетевого расширения HCX логических коммутаторов в источнике: NSXv 6.2+ или NSX-T 2.4+   | NSXv 6.2+ или NSX-T 2.4+<br/><br/>Для маршрутизации близкого взаимодействия HCX: NSXv 6.4+. (NSX-T не поддерживает маршрутизацию близкого взаимодействия). |
| vCloud Director   | Не требуется. На исходном сайте отсутствует взаимодействие с vCloud Director | Если ваша целевая среда интегрируется с vCloud Director, требуется версия не ниже 9.1.0.2.  |

## <a name="prerequisites"></a>Предварительные требования

### <a name="network-and-ports"></a>Сеть и порты

* Настройте службу [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) между каналами ExpressRoute в локальной среде и программно-определяемом центре обработки данных Решения Azure VMware.

* Откройте [все требуемые порты](https://ports.vmware.com/home/VMware-HCX) для обмена данными между локальными компонентами и программно-определяемым центром обработки данных Решения Azure VMware.

Дополнительные сведения см. в [документации по VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html).


### <a name="ip-addresses"></a>IP-адреса

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Развертывание OVA-файла соединителя VMware HCX в локальной среде

> [!NOTE]
> Перед развертыванием виртуального устройства в локальном решении vCenter необходимо скачать OVA-файл соединителя VMware HCX. 

1. Откройте окно браузера, войдите в HCX Manager Решения Azure VMware на странице `https://x.x.x.9` через порт 443 под учетными данными пользователя **cloudadmin** , а затем перейдите на страницу **Support** (Поддержка).

   > [!TIP]
   > Запишите IP-адрес HCX Cloud Manager в Решении Azure VMware. Чтобы узнать этот IP-адрес, на панели Решения Azure VMware последовательно выберите **Manage** > **Connectivity** (Управление — Подключение), а затем перейдите на вкладку **HCX** . 
   >
   >Пароль vCenter был определен при настройке частного облака.

1. Щелкните ссылку **загрузки** , чтобы загрузить OVA-файл соединителя VMware HCX.

1. Перейдите к локальному развертыванию vCenter. Выберите шаблон OVF (скачанный OVA-файл), чтобы развернуть соединитель HCX в локальном экземпляре vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Выберите имя и расположение, а затем ресурс или кластер, в котором вы развертываете соединитель HCX. Затем проверьте все сведения и необходимые ресурсы.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Ознакомьтесь с условиями лицензии. Если вы с ними согласны, выберите нужное хранилище и сеть, а затем щелкните **Далее** .

1. В разделе **Настройка шаблона** введите все необходимые сведения. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Нажмите кнопку **Далее** , проверьте конфигурацию и щелкните **Готово** , чтобы развернуть OVA-файл соединителя HCX.
     
   > [!NOTE]
   > Как правило, этот соединитель VMware HCX развертывается в сеть управления кластером.  
   
   > [!IMPORTANT]
   > Возможно, вам нужно будет вручную включить виртуальное устройство.  В этом случае подождите 10–15 минут, прежде чем переходить к следующему шагу.

Полный обзор этой процедуры см. в видео [Azure VMware Solution: HCX Appliance Deployment](https://www.youtube.com/embed/BwSnQeefnso) (Решение Azure VMware — развертывание устройства HCX). 


## <a name="activate-vmware-hcx"></a>Активация VMware HCX

Завершив развертывание OVA-файла соединителя VMware HCX в локальной среде и запустив устройство, можно переходить к активации. Для этого необходимо получить лицензионный ключ на портале Решения Azure VMware.

1. На портале Решения Azure VMware выберите **Manage** > **Connectivity** (Управление — Подключение), перейдите на вкладку **HCX** , а затем щелкните **Add** (Добавить).

1. Войдите в VMware HCX Manager в локальной среде по адресу `https://HCXManagerIP:9443`, используя учетные данные **администратора** . 

   > [!IMPORTANT]
   > Не забудьте указать номер порта `9443` вместе с IP-адресом VMware HCX Manager.

1. В разделе **Licensing** (Лицензирование) введите свой ключ в поле **HCX Advanced Key** (Расширенный ключ HCX).  
   
    > [!NOTE]
    > Для VMware HCX Manager требуется открытый доступ к Интернету или настроенный прокси-сервер.

1. В поле **Datacenter Location** (Расположение центра обработки данных) укажите ближайшее расположение к локальной среде с VMware HCX Manager.

1. Измените имя системы в поле **System Name** (Имя системы) или подтвердите значение по умолчанию.
   
1. Щелкните **Yes, Continue** (Да, продолжить).
    
1. В разделе **Connect your vCenter** (Подключение к vCenter) укажите полное доменное имя или IP-адрес сервера vCenter и соответствующие учетные данные, а затем выберите **Continue** (Продолжить).
   
1. В разделе **Configure SSO/PSC** (Настройка единого входа или PSC) укажите полное доменное имя или IP-адрес Platform Services Controller, а затем щелкните **Continue** (Продолжить).
   
   >[!NOTE]
   >Обычно это значение полного доменного имени или IP-адреса vCenter.

1. Убедитесь, что все входные данные указаны правильно, и щелкните **Restart** (Перезапустить).
    
   > [!NOTE]
   > После перезапуска будет наблюдаться определенная задержка с отображением запроса следующего шага.

После перезапуска служб очень важно убедиться, что для vCenter на экране отображается зеленый кружок. Параметры конфигурации vCenter и единого входа должны быть заданы надлежащим образом и совпадать с теми, которые показаны на предыдущем экране.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Полный обзор этой процедуры см. в видео [Azure VMware Solution: Activate HCX](https://www.youtube.com/embed/BkAV_TNYxdE) (Решение Azure VMware — активация HCX).


## <a name="configure-the-vmware-hcx-connector"></a>Настройка соединителя VMware HCX

Теперь вы можете добавить связывание сайтов, создать профиль сети и вычислений и включить службы миграции, расширения сети или аварийного восстановления. 

### <a name="add-a-site-pairing"></a>Добавление связывания сайта

Вы можете подключить диспетчер облаков VMware HCX в Решении Azure VMware к соединителю VMware HCX в центре обработки данных (связать их между собой). 

1. Войдите в vCenter в локальной среде и в разделе **Home** (Домашняя страница) выберите **HCX** .

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. В разделе **Infrastructure** (Инфраструктура) выберите **Site Pairing** (Связывание сайтов), а затем в середине экрана щелкните **Connect To Remote Site** (Подключиться к удаленному сайту). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Введите URL-адрес или IP-адрес удаленной платформы HCX, который вы сохранили ранее, а также имя пользователя cloudadmin@vsphere.local и пароль Решения Azure VMware. В этом случае выберите **Подключиться** .

   > [!NOTE]
   > Чтобы связывание сайтов прошло успешно, соединитель HCX должен маршрутизировать IP-адрес HCX Cloud Manager через порт 443.
   >
   > Пароль здесь используется тот же, что и для входа в vCenter. Этот пароль был определен на начальном экране развертывания.

   На этом экране будет показано, что HCX Cloud Manager в Решении Azure VMware и соединитель HCX в локальной среде успешно подключены (связаны друг с другом).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF":::

Полный обзор этой процедуры см. в видео [Azure VMware Solution: HCX Site Pairing](https://www.youtube.com/embed/sKizDCRHOko) (Решение Azure VMware — связывание сайтов HCX).



### <a name="create-network-profiles"></a>Создание сетевых профилей

VMware HCX развертывает подмножество виртуальных устройств (автоматически), которым нужно несколько сегментов IP-адресов. При создании сетевых профилей вы задаете сегменты IP-адресов, которые были определены на [этапе предварительного планирования развертывания сегментов сети VMware HCX и подготовки к его реализации](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Вы создадите четыре сетевых профиля:

   - Управление
   - vMotion
   - Репликация
   - Исходящая связь

1. Выберите **Infrastructure** , select **Interconnect** > **Multi-Site Service Mesh** > **Network Profiles** > **Create Network Profile** (Инфраструктура > Межсоединение > Сетка многосайтовой службы > Сетевые профили > Создать сетевой профиль).

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Для каждого сетевого профиля выберите сеть и группу портов, укажите имя и создайте пул IP-адресов для конкретного сегмента. Щелкните **Создать** . 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF":::

Полный обзор этой процедуры см. в видео [Azure VMware Solution: HCX Network Profile](https://www.youtube.com/embed/NhyEcLco4JY) (Решение Azure VMware — сетевой профиль HCX).


### <a name="create-a-compute-profile"></a>Создание вычислительного профиля

1. Выберите **Compute Profiles** > **Create Compute Profile** (Вычислительный профиль >Создать вычислительный профиль).

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Введите имя профиля и нажмите кнопку **Continue** (Продолжить).  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Выберите службы, которые нужно включить, такие как службы миграции, сетевого расширения или аварийного восстановления, а затем нажмите кнопку **Continue** (Продолжить).
  
   > [!NOTE]
   > Обычно здесь не нужно ничего изменять.

1. В разделе **Select Service Resources** (Выбор ресурсов служб) выберите один или несколько ресурсов служб (кластеры), для которых должны быть включены выбранные службы VMware HCX.  

1. Если кластеры отображаются в локальном центре обработки данных, выберите **Continue** (Продолжить).

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. На странице **Select Datastore** (Выбор хранилища данных) выберите ресурс хранилища данных для развертывания устройств VMware HCX Interconnect. Затем выберите **Continue** (Продолжить).

   Когда выбрано несколько ресурсов, VMware HCX использует первый выбранный ресурс, пока его емкость не будет исчерпана.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. В разделе **Select Management Network Profile** (Выбор сетевого профиля управления) выберите профиль, созданный на предыдущих шагах. Затем выберите **Continue** (Продолжить).  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Профиль сети управления позволяет устройствам VMware HCX обмениваться данными с vCenter. Через этот профиль можно обращаться к узлам ESXi.

1. В разделе **Select Uplink Network Profile** (Выбор сетевого профиля канала исходящей связи) выберите сетевой профиль, созданный на предыдущих шагах. Затем выберите **Continue** (Продолжить).

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. В разделе **Select vMotion Network Profile** (Выбор сетевого профиля vMotion) выберите сетевой профиль, созданный на предыдущих шагах. Затем выберите **Continue** (Продолжить).

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. В разделе **Select vSphere Replication Network Profile** (Выбор сетевого профиля репликации vSphere) выберите сетевой профиль, созданный на предыдущих шагах. Затем выберите **Continue** (Продолжить).

   В большинстве случаев профиль репликации совпадает с сетевым профилем управления.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. В разделе **Select Distributed Switches for Network Extensions** (Выбор распределенных коммутаторов для сетевых расширений) выберите распределенные виртуальные коммутаторы, через которые подключаются виртуальные машины, переносимые в Решение Azure VMware в расширенной сети уровня 2. Затем выберите **Continue** (Продолжить).

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Проверьте правила подключения и нажмите **Continue** (Продолжить).  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Нажмите **Готово** , чтобы создать вычислительный профиль.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Полный обзор этой процедуры см. в видео [Azure VMware Solution: Compute Profile](https://www.youtube.com/embed/qASXi5xrFzM) (Решение Azure VMware — вычислительный профиль).

### <a name="create-a-service-mesh"></a>Создание сетки служб

Теперь нужно настроить сетку служб между локальной средой и программно-определяемым центром обработки данных Решения Azure VMware.

   > [!NOTE]
   > Чтобы связать сетку служб с Решением Azure VMware, требуется следующее:
   >
   > открытые порты UDP 500/4500 между локальными адресами сетевого профиля исходящей связи, определенными в локальном соединителе HCX, и адресами сетевого профиля исходящей связи облака HCX в Решении Azure VMware.
   >
   > Не забудьте проверить [требуемые порты для HCX](https://ports.vmware.com/home/VMware-HCX).

1. В разделе **Infrastructure** (Инфраструктура) выберите **Interconnect** > **Service Mesh** > **Create Service Mesh** (Межсоединение > Сетка служб > Создать сетку служб).    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Просмотрите предварительно заполненные сайты, а затем выберите **Continue** (Продолжить). 

   >[!NOTE]
   >Если сетка служб настраивается впервые, вам не потребуется ничего менять на этом экране.  

1. Выберите значения для вычислительных профилей исходной и удаленной среды в раскрывающихся списках, а затем щелкните **Continue** (Продолжить).  

   От выбранных значений зависит, для каких ресурсов виртуальные машины смогут использовать службы VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Просмотрите службы, которые будут включены, а затем щелкните **Continue** (Продолжить).  

1. В разделе **Advanced Configuration - Override Uplink Network profiles** (Расширенная конфигурация — переопределение сетевых профилей каналов исходящей связи) щелкните **Continue** (Продолжить).  

   Сетевые профили каналов исходящей связи обеспечивают подключение к сети, через которую можно обращаться к устройствам Interconnect удаленного сайта.  
  
1. Проверьте параметры на экране **Advanced Configuration — Network Extension Appliance Scale Out** (Расширенная конфигурация: горизонтальное увеличение масштаба устройство расширения сети) и выберите **Continue** (Продолжить). 

1. На экране **Advanced Configuration — Traffic Engineering** (Расширенная конфигурация: проектирование трафика) проверьте параметры и внесите необходимые изменения, а затем щелкните **Continue** (Продолжить).

1. Проверьте предварительную версию топологии и нажмите **Продолжить** .

1. Введите понятное имя для этой сетки служб и нажмите **Finish** (Готово) для завершения.  

1. Выберите **View Tasks** (Просмотр задач) для мониторинга развертывания. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF":::

   После успешного завершения развертывания сетки служб вы увидите, что службы обозначены зеленым цветом.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Проверьте работоспособность сетки службы, проверив состояние устройства. 
1. Для этого выберите **Interconnect** > **Appliances** (Interconnect > Устройства).

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Полный обзор этой процедуры см. в видео [Azure VMware Solution: Service Mesh](https://www.youtube.com/embed/FyZ0d3P_T24) (Решение Azure VMware — сетка служб).



### <a name="optional-create-a-network-extension"></a>Создание расширения сети (необязательно)

Чтобы расширить какие-либо сети из локальной среды, включив в них Решение Azure VMware, сделайте следующее:

1. В разделе **Services** (Службы) выберите **Network Extension** (Расширение сети), а затем — **Create a Network Extension** (Создать расширение сети).

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Выберите все сети, которые нужно расширить в Решение Azure VMware, и щелкните **Next** (Далее).

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF":::

1. Введите IP-адрес локального шлюза для каждой из расширяемых сетей, а затем нажмите кнопку **Submit** (Отправить). 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF":::

   Расширение сети занимает несколько минут. Когда оно будет выполнено, отобразится новое состояние **Extension complete** (Расширение завершено).

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Снимок экрана: обзор папок для выбора шаблона OVF" lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Полный обзор этого шага см. в видео [Azure VMware Solution: Network Extension](https://www.youtube.com/embed/cNlp0f_tTr0) (Решение Azure VMware — расширение сети).


## <a name="next-steps"></a>Дальнейшие шаги

Если вы выполнили все предыдущие шаги, и туннель подключения устройств имеет состояние **UP** (Активен) и обозначен зеленым цветом, вы можете перенести и защитить виртуальные машины Решения Azure VMware с помощью VMware HCX. Решение Azure VMware поддерживает миграцию рабочих нагрузок (с расширением сети или без). Вы по-прежнему можете переносить рабочие нагрузки в среду vSphere, создавать сети в локальной среде и развертывать виртуальные машины в этих сетях.  

Дополнительные сведения об использовании HCX см. в технической документации по VMware:

* [Документация по VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Миграция виртуальных машин с помощью VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Требуемые порты HCX](https://ports.vmware.com/home/VMware-HCX)
