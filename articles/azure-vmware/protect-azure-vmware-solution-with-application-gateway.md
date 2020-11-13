---
title: Использование шлюза приложений Azure для защиты веб-приложений в решении Azure VMware
description: Настройте шлюз приложений Azure, чтобы безопасно предоставлять веб-приложения, выполняющиеся в решении VMware для Azure.
ms.topic: how-to
ms.date: 10/13/2020
ms.openlocfilehash: 7956ea51421f5cfa893942401c1d9a5871039689
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578488"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Использование шлюза приложений Azure для защиты веб-приложений в решении Azure VMware

[Шлюз приложений Azure](https://azure.microsoft.com/services/application-gateway/) — это балансировщик нагрузки веб-трафика уровня 7, который позволяет управлять трафиком к веб-приложениям. Она предлагается как в Azure VMware версии 1.0, так и в версии 2.0. Обе версии протестированы с веб-приложениями, работающими в решении VMware для Azure.

и включают следующие возможности: 
- сходство сеансов на основе файлов cookie;
- Маршрутизация на основе URL-адреса
- Брандмауэр веб-приложения (WAF)

Полный список функций см. в статье [возможности шлюза приложений Azure](../application-gateway/features.md). 

В этой статье показано, как использовать шлюз Аппликате перед фермой веб-серверов для защиты веб-приложения, работающего в решении VMware для Azure. 

## <a name="topology"></a>Топология
На схеме показано, как шлюз приложений используется для защиты виртуальных машин Azure IaaS (ВМ), масштабируемых наборов виртуальных машин Azure или локальных серверов. Шлюз приложений рассматривает виртуальные машины Azure VMware в качестве локальных серверов. 

![Шлюз приложений защищает виртуальные машины Azure VMware.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Шлюз приложений Azure в настоящее время является единственным поддерживаемым методом предоставления веб-приложений, работающих на виртуальных машинах Azure VMware.

На схеме показан сценарий тестирования, используемый для проверки шлюза приложений с помощью веб-приложений решения Azure VMware.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Интеграция шлюза приложений с решением VMware для Azure, которое запускает веб-приложения" border="false":::

Экземпляр шлюза приложений развертывается в концентраторе в выделенной подсети. Он имеет общедоступный IP-адрес Azure. Рекомендуется активировать стандартную защиту от атак DDoS для виртуальной сети. Веб-сервер размещается в частном облаке решения Azure VMware на основе маршрутизаторов НСКС T0 и T1. Решение VMware для Azure использует [Global REACH ExpressRoute](../expressroute/expressroute-global-reach.md) для подключения к концентратору и локальным системам.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской.
- Развернутое и работающее частное облако решения Azure VMware.

## <a name="deployment-and-configuration"></a>Развертывание и настройка

1. В портал Azure найдите **шлюз приложений** и выберите **создать шлюз приложений**.

2. Укажите основные сведения, как показано на следующем рисунке. затем выберите **Далее:** внешние интерфейсы>. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Создание шлюза приложений":::

3. Выберите тип внешнего IP-адреса. Для общедоступного выберите существующий общедоступный IP-адрес или создайте новый. Нажмите кнопку **Далее:>в конце**.

    > [!NOTE]
    > Для частных интерфейсов поддерживаются только номера SKU "Стандартный" и "брандмауэр веб-приложения" (WAF).

4. Добавьте серверный пул виртуальных машин, работающих в инфраструктуре решения VMware в Azure. Укажите сведения о веб-серверах, которые работают в частном облаке решения Azure VMware, и нажмите кнопку **Добавить**.  Затем нажмите кнопку **Далее:>конфигурации**.

1. На вкладке **Конфигурация** выберите **Добавить правило маршрутизации**.

6. На вкладке **прослушиватель** укажите сведения для прослушивателя. Если выбран вариант HTTPS, необходимо предоставить сертификат из PFX-файла или существующего сертификата Azure Key Vault. 

7. Перейдите на вкладку **серверные целевые объекты** и выберите созданный ранее серверный пул. В поле **Параметры HTTP** выберите **Добавить новое**.

8. Настройте параметры для параметров HTTP. Выберите **Добавить**.

9. Если необходимо настроить правила на основе пути, выберите **Добавить несколько целевых объектов, чтобы создать правило на основе пути**. 

10. Добавьте правило на основе пути и нажмите кнопку **Добавить**. Повторите, чтобы добавить дополнительные правила на основе пути. 

11. Завершив добавление правил на основе пути, нажмите кнопку **Добавить** еще раз. затем нажмите кнопку **Далее: теги>**. 

12. Добавьте теги, а затем нажмите кнопку **Далее: Проверка и создание>**.

13. Проверка будет выполняться на шлюзе приложений; в случае успеха выберите **создать** для развертывания.

## <a name="configuration-examples"></a>Примеры конфигурации

В этом разделе вы узнаете, как настроить шлюз приложений с виртуальными машинами Azure VMware в качестве серверных пулов для таких вариантов использования: 

- [Размещение нескольких сайтов](#hosting-multiple-sites)
- [Маршрутизация по URL-адресу](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Размещение нескольких сайтов

В этой процедуре показано, как определить серверные пулы адресов с помощью виртуальных машин, работающих в частном облаке решения Azure VMware в существующем шлюзе приложений. 

>[!NOTE]
>В этой процедуре предполагается, что у вас есть несколько доменов, поэтому мы будем использовать примеры www.contoso.com и www.fabrikam.com.


1. В частном облаке создайте два разных пула виртуальных машин. Один представляет Contoso, а второй — Fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs.png" alt-text="Создайте виртуальные машины.":::

    Для демонстрации этого руководства мы использовали Windows Server 2016 с установленной ролью службы IIS (IIS). После установки виртуальных машин выполните следующие команды PowerShell, чтобы настроить службы IIS на каждой из виртуальных машин. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. В существующем экземпляре шлюза приложений выберите **серверные пулы** в меню слева, щелкните  **Добавить** и введите сведения о новых пулах. На правой панели выберите **Добавить** .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png" alt-text="Добавьте серверные пулы." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png":::

3. В разделе **Listeners (прослушиватели** ) создайте новый прослушиватель для каждого веб-сайта. Введите сведения для каждого прослушивателя и нажмите кнопку **Добавить**.

4. Слева выберите **Параметры HTTP** и нажмите кнопку **Добавить** в левой области. Введите сведения для создания нового параметра HTTP и нажмите кнопку **сохранить**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png" alt-text="Введите сведения для создания нового параметра HTTP и нажмите кнопку Сохранить." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png":::

5. Создайте правила в разделе **правила** в меню слева. Свяжите каждое правило с соответствующим прослушивателем. Выберите **Добавить**.

6. Настройте соответствующий серверный пул и параметры HTTP. Выберите **Добавить**.

7. Проверьте подключение. Откройте предпочтительный браузер и перейдите на другие веб-сайты, размещенные в среде решения Azure VMware, например http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-07.png" alt-text="Проверьте подключение.":::

### <a name="routing-by-url"></a>Маршрутизация по URL-адресу

В этой процедуре показано, как определить серверные пулы адресов с помощью виртуальных машин, работающих в частном облаке решения Azure VMware в существующем шлюзе приложений. Затем мы создадим правила маршрутизации, которые обеспечивают поступление веб-трафика на соответствующие серверы в пуле.

1. В частном облаке создайте пул виртуальных машин для представления веб-фермы. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs.png" alt-text="Создайте пул виртуальных машин в решении VMware для Azure.":::

    Для демонстрации этого руководства используется Windows Server 2016 с установленной ролью IIS. После установки виртуальных машин выполните следующие команды PowerShell, чтобы настроить службы IIS для каждой виртуальной машины. 

    На первой виртуальной машине Contoso-Web-01 будет размещен основной веб-сайт.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    На второй виртуальной машине Contoso-Web-02 будет размещен сайт образов.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    На третьей виртуальной машине Contoso-Web-03 будет размещен сайт видео.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Добавьте три новых серверных пула в существующий экземпляр шлюза приложений. 

   1. Выберите **Серверные пулы** в меню слева. 
   1. Выберите **Добавить** и введите сведения о первом пуле, **contoso-Web**. 
   1. Добавьте одну виртуальную машину в качестве целевой. 
   1. Выберите **Добавить**. 
   1. Повторите эту процедуру для **contoso-Images** и **contoso-Video** , добавив одну уникальную виртуальную машину в качестве целевого объекта. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Добавьте три новых серверных пула." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png":::

3. В разделе **прослушиватели** создайте новый прослушиватель типа Basic с помощью порта 8080.

4. На панели навигации слева выберите **Параметры HTTP** и нажмите кнопку **Добавить** в левой области. Введите сведения для создания нового параметра HTTP и нажмите кнопку **сохранить**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Конфигурация параметров ХТП.":::

5. Создайте правила в разделе **правила** в меню слева. Свяжите каждое правило с ранее созданным прослушивателем. Затем настройте основной внутренний пул и параметры HTTP. Выберите **Добавить**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Создайте правила в разделе правила в меню слева.":::

6. Выполните проверку настройки. Получите доступ к шлюзу приложений на портал Azure и скопируйте общедоступный IP-адрес в разделе **Обзор** . 

   1. Откройте новое окно браузера и введите URL-адрес `http://<app-gw-ip-address>:8080` . 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Протестируйте конфигурацию из портал Azure.":::

   1. Изменить URL-адрес на `http://<app-gw-ip-address>:8080/images/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Измените URL-адрес.":::

   1. Снова измените URL-адрес на `http://<app-gw-ip-address>:8080/video/test.htm` .

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Измените URL-адрес еще раз.":::

## <a name="next-steps"></a>Next Steps

Дополнительные примеры настройки см. в [документации по шлюзу приложений Azure](../application-gateway/index.yml) .