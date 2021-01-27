---
title: Часто задаваемые вопросы о поддержке конфиденциальных узлов в службе Kubernetes Azure (AKS)
description: Найдите ответы на некоторые распространенные вопросы о службе Azure Kubernetes Service (AKS) & поддержки узлов конфиденциальных вычислений Azure (ACC).
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: amgowda
ms.openlocfilehash: 51e2095b0df8fdc965926c6c612c45f582c9b9d2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874490"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Часто задаваемые вопросы о конфиденциальных вычислительных узлах в службе Kubernetes Azure (AKS)

В этой статье рассматриваются часто задаваемые вопросы о конфиденциальных вычислительных узлах Intel SGX в службе Azure Kubernetes (AKS). Если у вас возникнут дополнительные вопросы, отправьте сообщение по адресу acconaks@microsoft.com .

## <a name="what-service-level-agreement-sla-and-azure-support-is-provided-during-the-preview"></a>Какие Соглашение об уровне обслуживания (SLA) и поддержка Azure предоставляются во время предварительной версии? 

Соглашение об уровне обслуживания не предоставляется в предварительной версии продукта, как указано [здесь](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Однако поддержка продуктов предоставляется через службу поддержки Azure.

## <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>Что такое аттестация и как можно выполнить аттестацию приложений, работающих в енклавес? 

Аттестация — это процесс демонстрации и проверки правильности создания экземпляра программного обеспечения на конкретной аппаратной платформе. Это также гарантирует, что его свидетельство будет проверено, чтобы гарантировать, что оно выполняется на защищенной платформе и не было фальсифицировано. [Узнайте больше](attestation.md) о том, как аттестация выполняется для приложений анклава.

## <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>Можно ли включить ускоренную сеть с использованием кластеров AKS для конфиденциальных вычислений Azure? 

Нет. Ускоренная сеть не поддерживается на узлах конфиденциальных вычислений в AKS. Убедитесь, что в развертывании отключена функция ускорения сети. 

## <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>Можно ли переносить существующие контейнерные приложения и запускать их в AKS с помощью конфиденциальных вычислений в Azure? 

Да, дополнительные сведения о платформе созидателями см. на [странице конфиденциальные контейнеры](confidential-containers.md) .

## <a name="what-intel-sgx-driver-version-is-installed-in-the-aks-image"></a>Какая версия драйвера Intel SGX установлена в образе AKS? 

Сейчас виртуальные машины с конфиденциальными данными Azure DCSv2 устанавливаются с Intel SGX ДКАП 1,33. 

## <a name="can-i-open-an-azure-support-ticket-if-i-run-into-issues"></a>Можно ли открыть запрос в службу поддержки Azure, если я буду столкнуться с проблемами? 

Да. Поддержка Azure предоставляется в рамках предварительной версии. Соглашение об уровне обслуживания не подключено, так как продукт находится на этапе предварительной версии.

## <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>Можно ли внедрить сценарии после установки или настроить драйверы для узлов, подготовленных с помощью AKS? 

Нет. [AKSные вычислительные узлы на основе модуля](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) поддерживают конфиденциальные вычислительные узлы, позволяющие выполнять выборочную установку.

## <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Следует ли использовать базовый образ DOCKER для начала работы с анклава приложениями? 

Различные созидателями (независимые поставщики программного обеспечения и проекты OSS) предоставляют способы включения конфиденциальных контейнеров. Дополнительные сведения и отдельные ссылки на реализации см. на [странице конфиденциальные контейнеры](confidential-containers.md) .

## <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>Можно ли запускать узлы ACC с другими стандартными номерами SKU AKS (сборка кластера разнородных узлов)? 

Да, вы можете запускать разные пулы узлов в одном кластере AKS, включая ACC Nodes. Чтобы назначить приложения анклава в определенном пуле узлов, рассмотрите возможность добавления селекторов узлов или применения ограничений EPC. Дополнительные сведения см. в [статье](confidential-nodes-aks-get-started.md)краткое руководство по конфиденциальным узлам.

## <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>Можно ли запускать узлы Windows и контейнеры Windows с помощью ACC? 

На данный момент нет. Свяжитесь с нами, если у вас есть узлы Windows или контейнеры. 

## <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>Что делать, если размер контейнера превышает доступную память EPC? 

Память EPC относится к части приложения, которая программируется на выполнение в анклава. Общий размер контейнера не является верным способом сравнения с максимальным объемом доступной памяти EPC. На самом деле, DCSv2 машины с SGX, допускайте максимальный объем памяти виртуальной машины 32 ГБ, где будет использоваться ненадежная часть приложения. Тем не менее, если контейнер потребляет больше доступной памяти EPC, это может повлиять на производительность части программы, работающей в анклава.

Чтобы лучше управлять памятью EPC на рабочих узлах, рассмотрим управление ограничениями на память EPC с помощью Kubernetes. Выполните приведенный ниже пример в виде справочника.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```

## <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>Что произойдет, если моя анклава потребляет больше максимальной доступной памяти EPC? 

Общая доступная память EPC является общей для приложений анклава на тех же виртуальных машинах или рабочих узлах. Если приложение использует память EPC больше, чем доступно, это может повлиять на производительность приложения. По этой причине мы рекомендуем настроить допустимость для каждого приложения в файле развертывания YAML, чтобы лучше управлять доступной памятью EPC на рабочие узлы, как показано в приведенных выше примерах. Кроме того, вы всегда можете выбрать размер виртуальной машины пула рабочих узлов или добавить дополнительные узлы. 

## <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Почему не удается выполнить несколько процессов в приложении анклава? 

Сейчас виртуальные машины с конфиденциальным расчетом в Azure DCsv2 SKU поддерживают одно адресное пространство для программы, выполняющейся в анклава. Один процесс — это текущее ограничение, разработанное для обеспечения высокого уровня безопасности. Однако конфиденциальные созидателями контейнеров могут иметь альтернативные реализации для преодоления этого ограничения.

## <a name="do-you-automatically-install-any-additional-daemonsets-to-expose-the-sgx-drivers"></a>Вы автоматически устанавливаете дополнительные daemonset для предоставления драйверов SGX? 

Да. Имя демона — SGX-Device-plugin и SGX-quote-Helper. Дополнительные сведения см. [здесь](confidential-nodes-aks-overview.md).  

## <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>Какой номер SKU виртуальной машины следует выбрать для конфиденциальных вычислительных узлов? 

Номера SKU DCSv2. [Номера SKU DCSv2](../virtual-machines/dcv2-series.md) доступны в [поддерживаемых регионах](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

## <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Можно ли по-прежнему планировать и запускать контейнеры, отличные от анклава, на конфиденциальных вычислительных узлах? 

Да. Виртуальные машины также имеют обычную память, которая может выполнять стандартные рабочие нагрузки контейнера. Прежде чем выбирать модели развертывания, рассмотрите модель безопасности и угроз для приложений.

## <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Можно ли подготавливать AKS с помощью пулов узлов DCSv2 с помощью портал Azure? 

Да. Azure CLI также можно использовать в качестве альтернативы, как описано [здесь](confidential-nodes-aks-get-started.md).

## <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Какие версии Ubuntu и виртуальные машины поддерживаются? 

18,04 на Gen 2. 

## <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>Можно ли изменить текущую версию Intel SGX ДКАП драйвера на AKS? 

Нет. Для выполнения выборочной установки рекомендуется выбрать [AKSные вычислительные ресурсы для рабочих узлов с конфиденциальной подсистемой](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) . 

## <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>Какую версию Kubernetes вы поддерживаете и рекомендуете? 

Мы поддерживаем и рекомендуем Kubernetes версии 1,16 и выше. 

## <a name="what-are-the-known-current-limitation-or-technical-limitations-of-the-product-in-preview"></a>Каковы известные текущие ограничения или технические ограничения продукта в предварительной версии? 

- Поддерживает только узлы виртуальных машин Ubuntu 18,04 поколения 2 
- Нет поддерживаемых узлов Windows или поддержка контейнеров Windows
- Автомасштабирование на основе памяти EPC по горизонтали не поддерживается. Поддерживается масштабирование с использованием ЦП и обычной памяти.
- Пространства разработки в AKS для конфиденциальных приложений в настоящее время не поддерживаются

## <a name="next-steps"></a>Next Steps
Дополнительные сведения о конфиденциальных контейнерах см. на [странице конфиденциальные контейнеры](confidential-containers.md) .