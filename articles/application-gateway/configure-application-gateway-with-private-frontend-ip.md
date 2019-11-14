---
title: Настройка конечной точки внутренней подсистемы балансировки нагрузки (ILB)
titleSuffix: Azure Application Gateway
description: В этой статье содержатся сведения о настройке шлюза приложений с частным интерфейсным IP-адресом.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: a9e3150a5382e4d690ddf66c43bbe51e125509d3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075217"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Настройка шлюза приложений с конечной точкой внутренней подсистемы балансировки нагрузки (ILB)

Шлюз приложений Azure можно настроить с помощью виртуального IP-адреса, доступного в Интернете, или внутренней конечной точки, которая не доступна в Интернете (с помощью частного IP-адреса для внешнего шлюза), также известной как конечная точка внутренней подсистемы балансировки нагрузки (ILB). Настройка шлюза с помощью частного IP-адреса внешнего интерфейса полезна для внутренних бизнес-приложений, которые не доступны в Интернете. Кроме того, этот вариант конфигурации можно использовать для служб и уровней многоуровневого приложения, размещенного в периметре безопасности без доступа к Интернету, но требующего циклического перебора нагрузки, закрепления сеансов или завершения SSL-запросов.

В этой статье описано, как настроить шлюз приложений с частным внешним IP-адресом на портале Azure.

В этой статье раскрываются следующие темы:

- Создание частной внешней IP-конфигурации для шлюза приложений
- Создание шлюза приложений с частной интерфейсной IP-конфигурацией


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу <https://portal.azure.com>.

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

В Azure для обмена между создаваемыми ресурсами необходима виртуальная сеть. Вы можете создать новую виртуальную сеть или использовать существующую. В этом примере мы создадим новую виртуальную сеть. Вы можете создать виртуальную сеть во время создания шлюза приложений. Экземпляры Шлюза приложений создаются в отдельных подсетях. В этом примере создаются две подсети: одна — для шлюза приложений, а вторая — для внутренних серверов.

1. Нажмите кнопку **Создать** в левом верхнем углу портала Azure.
2. Щелкните **Сети**, а затем в списке "Рекомендованные" выберите **Шлюз приложений**.
3. Для имени шлюза приложений введите *myAppGateway*, а для новой группы ресурсов — *myResourceGroupAG*.
4. Оставьте значения по умолчанию для остальных параметров и нажмите кнопку **ОК**.
5. Щелкните **Выбрать виртуальную сеть**, выберите **Создать**, а затем введите следующие значения для виртуальной сети:
   - myVNet * — имя виртуальной сети.
   - 10.0.0.0/16 * — адресное пространство виртуальной сети.
   - *myAGSubnet* — имя подсети;
   - *10.0.0.0/24* — диапазон адресов подсети.  
     ![Private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Нажмите кнопку **ОК**, чтобы создать виртуальную сеть и подсеть.
7. Выберите IP-конфигурацию переднего плана как частный и по умолчанию это динамическое назначение IP-адресов. Первый доступный адрес выбранной подсети будет назначен в качестве внешнего IP-адреса.
8. Если вы хотите выбрать частный IP-адрес из диапазона адресов подсети (Статическое выделение), установите флажок **выбрать конкретный частный IP-адреса** и укажите IP-адрес.
   > [!NOTE]
   > После выделения тип IP-адреса (статический или динамический) нельзя изменить позже.
9. Выберите конфигурацию прослушивателя для протокола и порт, WAF конфигурацию (при необходимости) и нажмите кнопку ОК.
    ![Private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Просмотрите параметры на странице сводки и нажмите кнопку **ОК**, чтобы создать сетевые ресурсы и шлюз приложений. Процесс создания шлюза приложений может занять несколько минут. Дождитесь успешного завершения развертывания, прежде чем переходить к следующему разделу.

## <a name="add-backend-pool"></a>Добавление внутреннего пула

Внутренний пул используется для перенаправления запросов на внутренние серверы, на которых будет обслуживаться запрос. Серверы могут состоять из сетевых адаптеров, масштабируемых наборов виртуальных машин, общедоступных IP-адресов, внутренних IP-адресов, полных доменных имен и мультитенантых серверных частей, таких как служба приложений Azure. В этом примере мы будем использовать виртуальные машины в качестве целевых объектов серверной части. Мы можем использовать существующие виртуальные машины или создать новые. В этом примере мы создадим две виртуальные машины, которые будут использоваться в Azure как внутренние серверы для шлюза приложений. Для этого нам необходимо:

1. Создать 2 новые виртуальные машины, *myVM* и *myVM2*, которые будут использоваться в качестве внутренних серверов.
2. Установить службы IIS на виртуальных машинах, чтобы убедиться, что шлюз приложений успешно создан.
3. Добавить внутренние серверы к внутренним пулам.

### <a name="create-a-virtual-machine"></a>Создание виртуальной машины

1. Нажмите кнопку **Создать**.
2. Щелкните **Вычисления**, а затем в списке "Рекомендованные" выберите **Windows Server 2016 Datacenter**.
3. Введите следующие значения для виртуальной машины:
   - *myVM* — имя виртуальной машины;
   - *azureuser* — для имени пользователя учетной записи администратора;
   - *Azure123456!* — пароль.
   - Щелкните **Use existing** (Использовать существующую), а затем выберите *myResourceGroupAG*.
4. Нажмите кнопку **ОК**.
5. Выберите **DS1_V2** в поле Размер виртуальной машины и нажмите кнопку **выбрать**.
6. Убедитесь, что выбрана виртуальная сеть **myVNet** и подсеть **myBackendSubnet**.
7. Щелкните **Отключено**, чтобы отключить диагностику загрузки.
8. Нажмите кнопку **ОК**, просмотрите параметры на странице сводки и нажмите кнопку **Создать**.

### <a name="install-iis"></a>Установка служб IIS

1. Откройте интерактивную оболочку и убедитесь, что для нее задано значение **PowerShell**.
    ![Private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Чтобы установить службы IIS, выполните на виртуальной машине следующие команды:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
