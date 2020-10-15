---
title: Учебник. Развертывание Azure Spring Cloud в виртуальной сети
description: Развертывание Azure Spring Cloud в виртуальной сети (внедрение виртуальной сети).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 979ecf77fe53238dfd377c5fd2baf394de985c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892892"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Руководство по развертыванию Azure Spring Cloud в виртуальной сети (внедрение виртуальной сети)

**Эта статья применима к:** ✔️ Java ✔️ C#

В этом учебнике объясняется, как развернуть экземпляр службы Azure Spring Cloud в виртуальной сети. Иногда это называют внедрением виртуальной сети.  

Развертывание включает:

* изоляцию приложений и среды выполнения служб Azure Spring Cloud от Интернета в корпоративной сети;
* взаимодействие Azure Spring Cloud с системами в локальных центрах обработки данных и (или) службах Azure в других виртуальных сетях;
* предоставление клиентам возможности контролировать входящие и исходящие сетевые подключения для Azure Spring Cloud.

## <a name="prerequisites"></a>Предварительные требования
Необходимо зарегистрировать поставщика ресурсов Azure Spring Cloud `Microsoft.AppPlatform` в соответствии с инструкциями [регистрации поставщика ресурсов на портале Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) или путем выполнения следующей команды az CLI:

```azurecli
az provider register --namespace Microsoft.AppPlatform
```
## <a name="virtual-network-requirements"></a>Требования к виртуальной сети
Виртуальная сеть, в которой развертывается экземпляр службы Azure Spring Cloud, должна соответствовать приведенным ниже требованиям.

* **Расположение.** Виртуальная сеть должна находиться в том же расположении, что и экземпляр службы Azure Spring Cloud.
* **Подписка**: Виртуальная сеть должна находиться в той же подписке, что и экземпляр службы Azure Spring Cloud.
* **Подсети**. Виртуальная сеть должна содержать две подсети, выделенные для экземпляра службы Azure Spring Cloud: 
    * Одна для среды выполнения службы.
    * Одна для приложений для микрослужб Spring Boot. 
    * Между этими подсетями и экземпляром службы Azure Spring Cloud существует связь "один к одному". Для каждого развертываемого экземпляра службы нужно использовать новую подсеть. Каждая такая подсеть может содержать только один экземпляр службы.
* **Диапазон адресов**: Один блок CIDR до /28 для подсети среды выполнения службы, а другой блок CIDR до /24 для подсети приложений для микрослужбы Spring Boot.
* **Таблица маршрутов**. Подсети не должны быть связаны с имеющейся таблицей маршрутизации.

В следующих процедурах описывается настройка виртуальной сети для хранения экземпляра Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Создание виртуальной сети
Если у вас уже есть виртуальная сеть для размещения экземпляра службы Azure Spring Cloud, пропустите шаги 1, 2 и 3. Вы можете начать с шага 4, чтобы подготовить подсети для виртуальной сети.

1. В меню портала Azure выберите **Создать ресурс**. В Azure Marketplace выберите **Сети** > **Виртуальная сеть**.

1. В диалоговом окне **Создать виртуальную сеть** введите или выберите приведенную ниже информацию.

    |Параметр          |Значение                                             |
    |-----------------|--------------------------------------------------|
    |Подписка     |Выберите свою подписку.                         |
    |Группа ресурсов   |Создайте группу ресурсов или выберите имеющуюся.  |
    |Название             |Введите *azure-spring-cloud-vnet*                   |
    |Расположение         |Выберите **Восточная часть США**.                                |

1. Щелкните **Далее: IP-адреса >** . 
 
1. В поле диапазона IPv4-адресов введите 10.1.0.0/16.

1. Выберите **Добавление подсети**, а затем введите *service-runtime-subnet* для параметра **Имя подсети** и 10.1.0.0/24 для параметра **Диапазон адресов подсети**. Нажмите кнопку **Добавить**.

1. Снова выберите **Добавление подсети**, а затем введите *apps-subnet* для параметра **Имя подсети** и 10.1.1.0/24 для пункта **Диапазон адресов подсети**.  Нажмите кнопку **Добавить**.

1. Щелкните **Review + create** (Просмотреть и создать). Сохраните остальные значения по умолчанию и нажмите кнопку **Создать**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Предоставление службе разрешения на доступ к виртуальной сети

Выберите виртуальную сеть *azure-spring-cloud-vnet*, созданную ранее.

1. Выберите **Управление доступом (IAM)** , а затем — **Добавить > Добавить назначение ролей**.

    ![Управление доступом к виртуальной сети](./media/spring-cloud-v-net-injection/access-control.png)

2. В диалоговом окне **Добавить назначение ролей** введите или выберите следующую информацию:

    |Параметр  |Значение                                             |
    |---------|--------------------------------------------------|
    |Роль     |Выберите роль **Владелец**                                  |
    |Выберите пункт   |Введите *Azure Spring Cloud Resource Provider* (Поставщик ресурсов Azure Spring Cloud)      |

    Затем выберите *Azure Spring Cloud Resource Provider* (Поставщик ресурсов Azure Spring Cloud) и нажмите кнопку **Сохранить**.

    ![Предоставление поставщику ресурсов Azure Spring Cloud виртуальной сети](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Этого можно также достичь, выполнив следующую команду az CLI.

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>Развертывание экземпляра службы Azure Spring Cloud в виртуальной сети

1. Откройте портал Azure по адресу https://ms.portal.azure.com.

1. В поле поиска сверху введите **Azure Spring Cloud** и выберите **Azure Spring Cloud** из результатов.

1. На странице **Azure Spring Cloud** выберите элемент **+ Добавить**.

1. Заполните форму на странице **создания** Azure Spring Cloud. 

1. Выберите ту же группу ресурсов и регион, в которых находится виртуальная сеть.

1. В разделе **Сведения о службе** в поле **Имя** выберите *azure-spring-cloud-vnet*.

1. На вкладке **Сеть** выберите следующее:

    |Параметр                                |Значение                                             |
    |---------------------------------------|--------------------------------------------------|
    |Deploy in your own virtual network (Развертывание в собственной виртуальной сети)     |Выберите **Да**.                                    |
    |Виртуальная сеть                        |Выберите *azure-spring-cloud-vnet*                  |
    |Service runtime subnet (Подсеть среды выполнения службы)                 |Выберите *service-runtime-subnet*                   |
    |Spring Boot microservice apps subnet (Подсеть приложений для микрослужб Spring Boot)   |Выберите *apps-subnet*                              |

    ![Вкладка создания сети](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Щелкните **Проверка и создание**.

1. Проверьте спецификации и щелкните **Создать**.

После развертывания в подписке будут созданы две дополнительные группы ресурсов для размещения сетевых ресурсов для экземпляра службы Azure Spring Cloud.  Перейдите на **главную** страницу затем выберите вверху пункт меню **Группы ресурсов**, чтобы найти следующие новые группы ресурсов.

Группа ресурсов с именем *azure-spring-cloud-service-runtime_{имя экземпляра службы}_{регион экземпляра службы}* содержит сетевые ресурсы для среды выполнения службы экземпляра службы.

  ![Среда выполнения службы](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Группа ресурсов с именем *azure-spring-cloud-service-runtime_{имя экземпляра службы}_{регион экземпляра службы}* содержит сетевые ресурсы для приложений для микрослужб Spring Boot экземпляра службы.

  ![Группа ресурсов приложений](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Эти сетевые ресурсы подключены к созданной ранее виртуальной сети.

  ![Виртуальная сеть с подключенным устройством](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Группы ресурсов полностью управляются службой Azure Spring Cloud. НЕ следует вручную удалять или изменять ресурсы внутри.

## <a name="next-steps"></a>Дальнейшие действия

[Развертывание приложения в Azure Spring Cloud в вашей виртуальной сети](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>См. также раздел

- [Устранение неполадок Azure Spring Cloud в виртуальной сети](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Обязательства клиентов в отношении запуска Azure Spring Cloud в виртуальной сети](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
