---
title: Защита веб-служб с помощью SSL
titleSuffix: Azure Machine Learning service
description: Узнайте, как защитить веб-службы, развернутой посредством службы машинного обучения Azure, позволяя HTTPS. HTTPS обеспечивает защиту данных от клиентов с помощью безопасности транспортного уровня (TLS), является заменой слои безопасного сокетов (SSL). Клиенты также используют протокол HTTPS для проверки удостоверения веб-службы.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: c176458cfc404a9d55d7fb71a36ea63110b3a6d6
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657950"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Использовать SSL для защиты веб-службы через машинное обучение Azure

В этой статье показано, как защитить веб-службы, развернутой посредством службы машинного обучения Azure.

Использовании [HTTPS](https://en.wikipedia.org/wiki/HTTPS) для ограничения доступа к веб-службам и защиты данных, который клиенты будут передавать. HTTPS защищенный обмен данными между клиентом и веб-службы позволяет шифровать обмен данными между ними. Шифрование использует [безопасности транспортного уровня (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS по-прежнему иногда называется *Secure Sockets Layer* (SSL), которая была предшественником TLS.

> [!TIP]
> Пакета SDK для Azure Machine Learning используется термин «SSL» для свойств, связанных с безопасной связи. Это не значит, что веб-службы не использует *TLS*. SSL — это просто более узнаваемых термин.

TLS и SSL, используют *цифровые сертификаты*, которые помогают с проверкой подлинности шифрования и идентификации. Дополнительные сведения о работы цифровых сертификатов, см. в разделе Википедии [инфраструктуры открытых ключей](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Если вы не используете HTTPS для веб-службы, данные, который отправляется в службу и из нее могут быть видны другим пользователям в Интернете.
>
> HTTPS также позволяет клиенту проверки подлинности сервера, который соединяется с. Эта функция защищает клиентов от [man-in--middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) атак.

Это общий процесс защиты веб-службы:

1. Получите имя домена.

2. Получите цифровой сертификат.

3. Развертывание или обновление веб-службы с поддержкой протокола SSL.

4. Обновите имя DNS, чтобы оно указывало на веб-службу.

> [!IMPORTANT]
> При развертывании в службе Azure Kubernetes (AKS), можно приобрести собственный сертификат или использовать сертификат, предоставляемый корпорацией Майкрософт. Если используется сертификат от корпорации Майкрософт, не нужно получить имя домена или SSL-сертификат. Дополнительные сведения см. в разделе [включить SSL и развернуть](#enable) этой статьи.

Существуют небольшие различия, при защите веб-служб в [целевых объектов развертывания](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Получение доменного имени

Если вы еще не владеете доменное имя, приобрести у *регистратора доменных имен*. Процесс и цены отличаются регистраторов. Регистратор предоставляет средства для управления имя домена. Использовать эти средства для сопоставления полное доменное имя (FQDN) (например, www\.contoso.com) на IP-адрес, на котором размещена веб-службу.

## <a name="get-an-ssl-certificate"></a>Получите SSL-сертификат.

Существует много способов, чтобы получить SSL-сертификат (сертификат). Чаще всего это связано покупке одного из *сертификации* (ЦС). Независимо от того, где можно получить сертификат необходимы следующие файлы:

* **Сертификат**. Сертификат должен содержать полную цепочку сертификатов, и он должен быть «Кодировке PEM.»
* **Ключ**. Ключ также должен быть в кодировке PEM.

При запросе сертификата, необходимо указать полное ДОМЕННОЕ имя, адрес, который вы планируете использовать веб-службы (например, www\.contoso.com). Адрес, который записывается в сертификат и адрес, который клиенты используют сравниваются для проверки удостоверения веб-службы. Если эти адреса не совпадают, клиент получает сообщение об ошибке.

> [!TIP]
> Если центр сертификации не может предоставить сертификат и ключ, как файлы в кодировке PEM, можно использовать программу, например [OpenSSL](https://www.openssl.org/) для изменения формата.

> [!WARNING]
> Используйте *самозаверяющий* сертификаты только для разработки. Не используйте их в рабочих средах. Самозаверяющие сертификаты могут вызвать проблемы в клиентских приложениях. Дополнительные сведения см. в документации по библиотекам сети, которые использует клиентское приложение.

## <a id="enable"></a> Включить SSL и развертывание

Чтобы развернуть (или повторного развертывания) в службу с поддержкой протокола SSL, задайте *ssl_enabled* параметра значение «True», везде, где это применимо. Задайте *ssl_certificate* параметр значению *сертификат* файл. Задайте *ssl_key* значению *ключ* файл.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Развертывание AKS и перепрограммируемые пользователем компоненты массива (FPGA)

  > [!NOTE]
  > Сведения в этом разделе также применяются при развертывании безопасных веб-службы для визуального интерфейса. Если вы не знакомы с использованием пакета SDK для Python, см. в разделе [что такое Azure Machine Learning SDK для Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

При развертывании AKS, можно создать новый кластер AKS или присоединить имеющийся.
  
-  При создании нового кластера, используйте  **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** .
- Если вы подключаете существующий кластер, используйте  **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Обе возвращают объект конфигурации, который имеет **enable_ssl** метод.

**Enable_ssl** метод можно использовать сертификат, предоставляемый корпорацией Майкрософт или сертификат, который вы оплачиваете.

  * При использовании сертификата от корпорации Майкрософт, необходимо использовать *leaf_domain_label* параметра. Этот параметр приводит к возникновению ошибки DNS-имя для службы. Например, значение «myservice» создает доменное имя «myservice\<шести случайных символов >.\< azureregion >. cloudapp.azure.com», где \<azureregion > — регион, который содержит службу. При необходимости можно использовать *overwrite_existing_domain* параметр для перезаписи существующего *leaf_domain_label*.

    Чтобы развернуть (или повторного развертывания) в службу с поддержкой протокола SSL, задайте *ssl_enabled* параметра значение «True», везде, где это применимо. Задайте *ssl_certificate* параметр значению *сертификат* файл. Задайте *ssl_key* значению *ключ* файл.

    > [!IMPORTANT]
    > При использовании сертификата от корпорации Майкрософт, не нужно приобретать собственное имя сертификата или домена.

    В следующем примере демонстрируется создание конфигурацией, которая обеспечивает SSL-сертификат от корпорации Майкрософт:

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

  * При использовании *сертификат, который вы приобрели*, использовании *ssl_cert_pem_file*, *ssl_key_pem_file*, и *ssl_cname* параметры. Следующий пример демонстрирует, как использовать *PEM-файл* файлы, чтобы создать конфигурацию, которая использует SSL-сертификат, приобретенный:

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

Дополнительные сведения о *enable_ssl*, см. в разделе [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) и [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Развертывание в экземплярах контейнеров Azure

При развертывании экземпляры контейнеров Azure, можно указать значения для параметров, связанных с SSL, как показано в следующем фрагменте кода:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Дополнительные сведения см. в разделе [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-).

## <a name="update-your-dns"></a>Обновление DNS

Теперь необходимо обновить DNS, чтобы оно указывало на веб-службу.

+ **Для экземпляров контейнеров:**

  Используйте средства из регистратора доменных имен для обновления записи DNS для доменного имени. Записи должен указывать на IP-адрес службы.

  Возможна задержка минут или часов, прежде чем клиенты могут преобразовать имя домена, в зависимости от регистратора и «срок жизни» (TTL), настроенного для доменного имени.

+ **Для AKS:**

  > [!WARNING]
  > Если вы использовали *leaf_domain_label* для создания службы с помощью сертификата от корпорации Майкрософт, не вручную обновлять значения DNS для кластера. Значение должно быть установлено автоматически.

  Обновите DNS на **конфигурации** вкладке общедоступный IP-адрес кластера AKS. (См. ниже). Общедоступный IP-адрес — это тип ресурса, который создается в группе ресурсов, содержащий узлам агентов AKS, а также другие сетевые ресурсы.

  ![Служба машинного обучения Azure. Защита веб-служб с помощью SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Следующие шаги
Вы узнаете, как выполнять следующие задачи:
+ [Использование модели Машинного обучения, развернутой в качестве веб-службы](how-to-consume-web-service.md)
+ [Безопасным образом запускать эксперименты и вывод внутри виртуальной сети Azure](how-to-enable-virtual-network.md)
