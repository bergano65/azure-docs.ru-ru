---
title: 'Служба контейнеров Azure: часто задаваемые вопросы (не рекомендуется)'
description: Ответы на часто задаваемые вопросы о Службе контейнеров Azure — службе, которая упрощает создание и настройку кластера виртуальных машин, использующегося для запуска контейнерных приложений Docker, а также управления им.
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: conceptual
ms.date: 03/28/2017
ms.author: mlearned
ms.custom: H1Hack27Feb201
ms.openlocfilehash: 0165ace1d373d267658c78e020356816fb9cc02f
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286470"
---
# <a name="deprecated-container-service-frequently-asked-questions"></a>Часто задаваемые вопросы о службе контейнеров (не рекомендуется)

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

## <a name="orchestrators"></a>Оркестраторы

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Какие оркестраторы контейнеров поддерживает Служба контейнеров Azure? 

Служба контейнеров Azure поддерживает компоненты DC/OS, Docker Swarm и Kubernetes с открытым кодом. Дополнительные сведения см. в статье [Общие сведения о Службе контейнеров Azure](../kubernetes/container-service-intro-kubernetes.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Поддерживается ли режим Docker Swarm? 

Сейчас режим Swarm не поддерживается, но мы планируем добавить его поддержку в будущем. 

### <a name="does-azure-container-service-support-windows-containers"></a>Поддерживает ли Служба контейнеров Azure контейнеры Windows?  

Сейчас для всех оркестраторов поддерживаются только контейнеры Linux. Поддержка контейнеров Windows в Kubernetes находится в на этапе предварительной версии.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Советуете ли вы использовать конкретный оркестратор в Службе контейнеров Azure? 
Обычно мы не советуем использовать конкретный оркестратор. Если вы уже работали с одним из поддерживаемых оркестраторов, вы можете применить этот опыт в Службе контейнеров Azure. Но на основе тенденций в данных DC/OS эффективно использовать для рабочих нагрузок Интернета вещей и больших данных, Kubernetes подходит для облачных рабочих нагрузок, а Docker Swarm отличается возможностью интеграции со средствами Docker и своей простотой.

В зависимости от сценария использования вы также можете создавать пользовательские решения по работе с контейнерами и управлять ими с помощью других служб Azure. К этим службам относятся [Виртуальные машины](../../virtual-machines/linux/overview.md), [Service Fabric](../../service-fabric/service-fabric-overview.md), [веб-приложения](../../app-service/overview.md) и [пакетная служба](../../batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Какова разница между Службой контейнеров Azure и модулем ACS? 
Служба контейнеров Azure — это служба Azure с поддержкой соглашения об уровне обслуживания, различными возможностями на портале Azure, программами командной строки и API-интерфейсами Azure. Она позволяет быстро развертывать кластеры, на которых запущены стандартные средства оркестрации контейнеров, и управлять ими, не требуя при этом сложных конфигураций. 

[Модуль ACS](https://github.com/Azure/acs-engine) — это проект с открытым кодом, который позволяет опытным пользователям настраивать конфигурацию кластера на каждом уровне. Эта возможность изменения конфигурации инфраструктуры и программного обеспечения означает, что мы не предлагаем соглашение об уровне обслуживания для модуля ACS. Поддержка осуществляется через проект с открытым исходным кодом на GitHub, а не через официальные каналы Майкрософт. 

Дополнительные сведения см. в нашей [политике поддержки контейнеров](https://support.microsoft.com/en-us/help/4035670/support-policy-for-containers).

## <a name="cluster-management"></a>Управление кластерами

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Как создавать ключи SSH для кластера?

Чтобы создать для кластера пару ключей SSH RSA (открытый и закрытый), используемых для проверки подлинности виртуальных машин, можно воспользоваться стандартными средствами операционной системы. Пошаговые инструкции см. в руководстве для [OS X, Linux](../../virtual-machines/linux/mac-create-ssh-keys.md) или [Windows](../../virtual-machines/linux/ssh-from-windows.md). 

Если для развертывания кластера службы контейнеров используются Azure CLI команды, для кластера могут быть автоматически созданы ключи SSH.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Как создать субъект-службу для кластера Kubernetes?

Чтобы создать кластер Kubernetes в Службе контейнеров Azure, требуется идентификатор и пароль субъекта-службы Azure Active Directory. Дополнительные сведения см. в статье [о субъекте-службе для кластера Kubernetes](../../container-service/kubernetes/container-service-kubernetes-service-principal.md).

Если для развертывания кластера Kubernetes используются [команды Azure CLI](../../container-service/dcos-swarm/container-service-create-acs-cluster-cli.md), то учетные данные субъекта-службы создаются автоматически.

### <a name="how-large-a-cluster-can-i-create"></a>Кластер какого размера можно создать?
Можно создать кластер с 1, 3 или 5 основными узлами. Можно выбрать до 100 узлов агентов.

> [!IMPORTANT]
> Для больших кластеров и в зависимости от размера виртуальных машин, выбранных для узлов, может потребоваться увеличить квоту на число ядер в подписке. Чтобы увеличить квоту, [отправьте запрос в службу поддержки](../../azure-supportability/how-to-create-azure-support-request.md). Это бесплатная услуга. Если вы используете [бесплатную учетную запись Azure](https://azure.microsoft.com/free/), вам доступно ограниченное количество вычислительных ядер Azure.
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Как увеличить количество главных узлов после создания кластера? 
После создания кластера число главных узлов фиксировано. Его изменить нельзя. Во время создания кластера рекомендуется выбрать несколько главных узлов. Это позволит обеспечить высокую доступность.

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Как увеличить количество агентов после создания кластера? 
Количество агентов в кластере можно изменить с помощью портала или программ командной строки Azure. Дополнительные сведения см. в статье [Масштабирование кластера Службы контейнеров Azure](../../container-service/kubernetes/container-service-scale.md).

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Что такое URL-адреса главных узлов и агентов? 
URL-адреса ресурсов кластера в Службе контейнеров Azure состоят из указанного префикса DNS-имени и имени региона Azure, выбранного для развертывания. Например, полное доменное имя главного узла имеет следующий формат:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Часто используемые URL-адреса кластера можно просмотреть с помощью портала Azure, обозревателя ресурсов Azure и других средств Azure.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>Как узнать, какая версия оркестратора используется в кластере?

* сведения о версии, используемой в DC/OS, см. в [документации по Mesosphere](https://docs.mesosphere.com/1.7/usage/cli/command-reference/);
* в Docker Swarm используется `docker version`;
* в Kubernetes используется `kubectl version`.

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>Как обновить оркестратор после развертывания?

В настоящее время служба контейнеров Azure не предоставляет средства для обновления версии оркестратора, развернутого в кластере. Если служба контейнеров поддерживает более позднюю версию, можно развернуть новый кластер. Другим вариантом является использование специальных средств оркестратора (если они доступны) для обновления кластера на месте. Для примера можно ознакомиться с документацией об [обновлении DC/OS](http://docs.mesosphere.com/1.12/installing/production/upgrading).
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Где найти строку SSH-подключения к кластеру?

Строку подключения можно просмотреть на портале Azure или с помощью программ командной строки Azure. 

1. На портале перейдите к группе ресурсов, используемой для развертывания кластера.  

2. Щелкните **Обзор**, а затем в разделе **Основные компоненты** щелкните ссылку **Развертывания**. 

3. В колонке **История развертывания** выберите развертывание, имя которого начинается с **microsoft-acs**, а затем следует дата развертывания. Например, microsoft-acs-201701310000.  

4. На странице **Сводка** в разделе **Выходные данные** доступно несколько ссылок на кластеры. **SSHMaster0** предоставляет строку SSH-подключения к первому главному узлу в кластере службы контейнеров. 

Как уже отмечалось, для поиска полного доменного имени главного узла можно также использовать средства Azure. Установите SSH-подключение к главному узлу, используя его полное доменное имя и имя пользователя, указанное при создании кластера. Например,

```bash
ssh userName@masterFQDN –A –p 22 
```

Дополнительные сведения см. в статье [Подключение к кластеру службы контейнеров Azure](../../container-service/kubernetes/container-service-connect.md).

### <a name="my-dns-name-resolution-isnt-working-on-windows-what-should-i-do"></a>В Windows не работает разрешение имени DNS. Что делать?

В Windows есть некоторые известные проблемы с DNS, исправления которых по-прежнему находятся в активном состоянии. Убедитесь, что вы используете наиболее обновленную версию ядра ACS и Windows (с установленными [KB4074588](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4074588) и [KB4089848](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4089848) ), чтобы среда могла использовать преимущества. В противном случае см. инструкции по устранению проблем в следующей таблице:

| Симптом DNS | Возможное решение  |
|-------------|-------------|
|Если контейнер рабочей нагрузки нестабилен и аварийно завершает работу, пространство имен очищается | Повторно разверните все затронутые службы |
| Доступ к виртуальному IP-адресу службы отсутствует | Настройте [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/), чтобы одна обычная (непривилегированная) группа pod всегда находилась в рабочем состоянии |
|Если узел, в котором работает контейнер, становится недоступным, запросы DNS могут завершаться ошибкой, что приведет к наличию отрицательной записи кэша | Выполните следующую команду внутри затронутых контейнеров: <ul><li> `New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxCacheTtl -Value 0 -Type DWord`</li><li>`New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxNegativeCacheTtl -Value 0 -Type DWord`</li><li>`Restart-Service dnscache` </li></ul><br> Если проблема все еще не решена, попробуйте полностью отключить кэширование DNS: <ul><li>`Set-Service dnscache -StartupType disabled`</li><li>`Stop-Service dnscache`</li></ul> |

## <a name="next-steps"></a>Дополнительная информация

* Узнайте больше о [Службе контейнеров Azure](../../container-service/kubernetes/container-service-intro-kubernetes.md).
* Разверните кластер службы контейнеров с помощью [портала](../../container-service/dcos-swarm/container-service-deployment.md) или [Azure CLI](../../container-service/dcos-swarm/container-service-create-acs-cluster-cli.md).

