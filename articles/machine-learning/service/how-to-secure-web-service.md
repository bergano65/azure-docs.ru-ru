---
title: Защита веб-служб с помощью SSL
titleSuffix: Azure Machine Learning service
description: Узнайте, как защитить веб-службы развернуты в службе машинного обучения Azure, позволяя HTTPS. HTTPS обеспечивает защиту данных, отправленных клиентами с помощью безопасности транспортного уровня (TLS), является заменой слои безопасного сокетов (SSL). Он также используется клиентами для проверки удостоверения веб-службы.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 527f16e34e0f21d435fbd166328235566687bc88
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65852006"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Использование SSL для защиты веб-служб, развернутых с помощью Службы машинного обучения Azure

В этой статье вы узнаете, как защитить веб-службы, развернутые с помощью службы Машинного обучения Azure. Можно ограничить доступ к веб-служб и защиты данных, отправленных клиентами с помощью [гипертекстовый протокол безопасного (HTTPS)](https://en.wikipedia.org/wiki/HTTPS).

HTTPS используется для защиты обмена данными между клиентом и веб-службу, шифровать обмен данными между ними. Шифрование осуществляется с использованием [безопасности транспортного уровня (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Иногда TLS по-прежнему упоминается как Secure Sockets Layer (SSL), которая была создана до TLS.

> [!TIP]
> Пакета SDK для Azure Machine Learning используется термин «SSL» для свойств, связанных с включением защиты обмена данными. Это не означает, что TLS не используется веб-службой, так же, протокол SSL — это более запоминающееся термин для многих читателей.

TLS и SSL, используют __цифровые сертификаты__, которые используются для выполнения проверки, шифрования и идентификации. Дополнительные сведения о работы цифровых сертификатов, см. в статье Википедии на [инфраструктуры открытых ключей (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Если не включить и использовать протокол HTTPS для веб-службы, данные, отправляемые из службы и могут быть видны людям, в Интернете.
>
> HTTPS также позволяет клиенту проверки подлинности сервера, который соединяется с. Это позволяет защитить клиентов от [man-in--middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) атак.

Защита новой веб-службы или существующий общий процесс выглядит следующим образом:

1. Получите имя домена.

2. Получите цифровой сертификат.

3. Разверните или обновите веб-службу со включенным параметром SSL.

4. Обновите имя DNS, чтобы оно указывало на веб-службу.

> [!IMPORTANT]
> Если вы выполняете развертывание для службы Azure Kubernetes (AKS), можно предоставить собственный сертификат или используется сертификат, предоставленный корпорацией Майкрософт. Если используется предоставленный сертификат Microsoft, вы не обязательно должны получить доменное имя или SSL-сертификат. Дополнительные сведения см. в разделе [включить SSL и развернуть](#enable) раздел.

При защите веб-служб в разных [целевых объектах развертывания](how-to-deploy-and-where.md) есть небольшие отличия.

## <a name="get-a-domain-name"></a>Получение доменного имени

Если у вас еще нет имени домена, вы можете приобрести его у __регистратора доменных имен__. Процесс различается у разных регистраторов, как и стоимость. Регистратор также предоставляет вам инструменты для управления доменным именем. Эти инструменты используются для сопоставления полное доменное имя (например, www\.contoso.com) на IP-адрес размещения веб-службу.

## <a name="get-an-ssl-certificate"></a>Получите SSL-сертификат.

Существует много способов, чтобы получить SSL-сертификат (сертификат). Наиболее распространенным является покупка в одном из __центров сертификации__. Независимо от того, где вы получаете сертификат, вам нужны следующие файлы:

* __Сертификат__. Сертификат должен содержать полную цепочку сертификатов и должен быть в кодировке PEM.
* __Ключ__. Ключ должен быть в кодировке PEM.

При запросе сертификата вы должны предоставить полное доменное имя (FQDN) адреса, который вы планируете использовать для этой веб-службы. Например, www\.contoso.com. Адрес, указанный в сертификате, и адрес, используемый клиентами, сравниваются при проверке удостоверения веб-службы. Если адреса не совпадают, клиенты получат сообщение об ошибке.

> [!TIP]
> Если центр сертификации не может предоставить сертификат и ключ в виде файлов в кодировке PEM, вы можете использовать такую служебную программу, как [OpenSSL](https://www.openssl.org/), чтобы изменить формат.

> [!WARNING]
> Эти самозаверяющие сертификаты следует использовать только для разработки. Они не должны использоваться в рабочей среде. Самозаверяющие сертификаты могут вызвать проблемы в клиентских приложениях. Дополнительные сведения см. в документации для сетевых библиотек, используемых в клиентском приложении.

## <a id="enable"></a> Включить SSL и развертывание

Чтобы развернуть (или повторного развертывания) в службу с поддержкой протокола SSL, задайте `ssl_enabled` параметр `True`, везде, где это применимо. Установите для параметра `ssl_certificate` значение файла __сертификата__, а для параметра `ssl_key` значение __ключа__.

+ **Развертывание в службе Azure Kubernetes (AKS) и FPGA**

  > [!NOTE]
  > Сведения в этом разделе также применяются при развертывании безопасных веб-службы для визуального интерфейса. Если вы не знакомы с использованием пакета SDK для Python, см. в разделе [Обзор пакета SDK для Python машинного обучения Azure.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

  При развертывании в AKS, можно создать новый кластер AKS или присоединить имеющийся. Создание кластера использует [AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-) во время присоединения существующего кластера использует [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-). Обе возвращают объект конфигурации, который имеет `enable_ssl` метод.

  `enable_ssl` Метод можно использовать сертификат, предоставленный Майкрософт или тот, который вы указали.

  * При использовании сертификата __предоставленных корпорацией Майкрософт__, необходимо использовать `leaf_domain_label` параметра. С помощью этого параметра будет создать службу с помощью сертификата, предоставляемого корпорацией Майкрософт. `leaf_domain_label` Используется для создания DNS-имя для службы. Например, значение `myservice` создает доменное имя `myservice<6-random-characters>.<azureregion>.cloudapp.azure.com`, где `<azureregion>` — регион, который содержит службу. При необходимости можно использовать `overwrite_existing_domain` параметр для перезаписи существующих конечных метку домена.

    Чтобы развернуть (или развернуть повторно) службу со включенным SSL, установите для параметра `ssl_enabled` значение `True` везде, где это применимо. Установите для параметра `ssl_certificate` значение файла __сертификата__, а для параметра `ssl_key` значение __ключа__.

    > [!IMPORTANT]
    > При использовании сертификата, предоставляемого корпорацией Майкрософт, вы не обязательно должны приобрести собственное имя сертификата или домена.

    Следующий пример демонстрирует создание конфигурации, позволяющие сертификат SSL, созданные корпорацией Майкрософт:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * При использовании __сертификат, который вы приобрели__, использовать `ssl_cert_pem_file`, `ssl_key_pem_file`, и `ssl_cname` параметров. Следующий пример демонстрирует создание конфигурации, которые используют сертификат SSL, укажите с помощью `.pem` файлы:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  Дополнительные сведения о `enable_ssl`, см. в разделе [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) и [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

+ **Развертывание в Экземплярах контейнеров Azure (ACI)**

  При развертывании в ACI укажите значения для параметров, связанных с SSL, как показано во фрагменте кода:

    ```python
    from azureml.core.webservice import AciWebservice

    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  Дополнительные сведения см. в разделе [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>Обновление DNS

Теперь необходимо обновить DNS, чтобы оно указывало на веб-службу.

+ **Для ACI**:

  Используйте инструменты, предоставленные вашим регистратором доменных имен, чтобы обновить запись DNS для вашего доменного имени. Запись должна указывать на IP-адрес службы.

  В зависимости от регистратора и срока жизни, настроенного для имени домена, может потребоваться от нескольких минут до нескольких часов, прежде чем клиенты смогут разрешить имя домена.

+ **Для AKS**:

  > [!WARNING]
  > Если вы использовали `leaf_domain_label` для создания службы с помощью сертификата, предоставленных корпорацией Майкрософт, не обновить вручную значения DNS для кластера. Значение должно быть установлено автоматически.

  Обновите имя DNS на вкладке "Конфигурация" окна "Общедоступный IP-адрес" кластера AKS, как показано на изображении. Общедоступный IP-адрес можно найти как один из типов ресурсов в группе ресурсов, содержащей узлы агента AKS и другие сетевые ресурсы.

  ![Служба машинного обучения Azure. Защита веб-служб с помощью SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)



## <a name="next-steps"></a>Дальнейшие действия
Вы узнаете, как выполнять следующие задачи:
+ [Использование модели Машинного обучения, развернутой в качестве веб-службы](how-to-consume-web-service.md)
+ [Безопасным образом запускать эксперименты и вывод внутри виртуальной сети Azure](how-to-enable-virtual-network.md)

