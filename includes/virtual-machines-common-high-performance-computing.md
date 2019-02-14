---
title: включение файла
description: включение файла
services: virtual-machines-linux, virtual-machines-windows
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 01/15/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a1a6b31c1500c51dbbc32683d9e0e911b60dcae4
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56247112"
---
Организациям требуются крупномасштабные вычисления. Рабочие нагрузки крупномасштабных вычислений включают техническое проектирование и анализ, вычисление финансовых рисков, отрисовку образов, комплексное моделирование, моделирование методом Монте-Карло и другие задачи. 

Используйте облачные средства Azure для эффективного выполнения рабочих нагрузок Windows и Linux с ресурсоемкими вычислениями: от параллельных пакетных заданий до традиционного моделирования HPC. Выполняйте высокопроизводительные вычисления и пакетные рабочие нагрузки в инфраструктуре Azure, используя любые вычислительные службы, диспетчеры сеток, решения Marketplace и приложения, размещенные поставщиками (SaaS). Azure предоставляет гибкие решения для распределения нагрузки и увеличения масштаба до тысяч виртуальных машин или ядер, а также его уменьшения, если требуется меньше ресурсов. 

## <a name="solution-options"></a>Варианты решений

* **Самостоятельно созданные решения**
    * Настройка собственной кластерной среды на виртуальных машинах Azure или в [масштабируемых наборах виртуальных машин](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Перенос локального кластера или развертывание нового кластера в Azure для увеличения емкости. 
    * Использование шаблонов Azure Resource Manager для развертывания лучших [диспетчеров рабочих нагрузок](#workload-managers), инфраструктуры и [приложений](#hpc-applications). 
    * Выбор [размеров виртуальной машины с поддержкой графического процессора и HPC](#hpc-and-gpu-vm-sizes), которые включают специальное оборудование и сетевые подключения для рабочих нагрузок графического процессора или MPI. 
    * Добавление [высокопроизводительного хранилища](#hpc-storage) для рабочих нагрузок с большим количеством операций ввода-вывода.
* **Гибридные решения**
    * Расширение возможностей локального решения, позволяющее перенести пиковые рабочие нагрузки в инфраструктуру Azure.
    * Использование облачных вычислительных ресурсов по требованию с помощью существующего [диспетчера рабочих нагрузок](#workload-managers).
    * Возможность выбора [размеров виртуальной машины с поддержкой графического процессора и HPC](#hpc-and-gpu-vm-sizes) для рабочих нагрузок графического процессора или MPI.
* **Решения для больших вычислений как услуга**
    * Разработка настраиваемых решений и рабочих процессов для больших вычислений с помощью [Azure CycleCloud](#azure-cyclecloud), [пакетной службы Azure](#azure-batch) и связанных [служб Azure](#related-azure-services).
    * Запуск предоставляемых Azure решений для моделирования и проектирования от поставщиков, в том числе [Altair](http://www.altair.com/), [Rescale](https://www.rescale.com/azure/) и [Cycle Computing](https://cyclecomputing.com/) (компания уже [объединена с корпорацией Майкрософт](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
    * Использование [суперкомпьютера Cray](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure) в качестве службы, размещенной в Azure.
* **Решения Marketplace**
    * Использование ряда [приложений](#hpc-applications) и [решений для HPC](#marketplace-solutions), предлагаемых в [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 
    
Следующие разделы содержат дополнительные сведения о вспомогательных технологиях и ссылки на руководства.

## <a name="marketplace-solutions"></a>Решения Marketplace

Решения и образы виртуальных машин Windows и Linux, предназначенных для HPC, можно найти в [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Примеры приведены ниже.

* [HPC RogueWave на основе CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview)
* [SUSE Linux Enterprise Server для HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [TIBCO Grid Server Engine](https://azuremarketplace.microsoft.com/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview);
* [Виртуальные машины для анализа и обработки данных для Windows и Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [d3View](https://azuremarketplace.microsoft.com/marketplace/apps/xfinityinc.d3view-v5?tab=Overview);
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)

## <a name="hpc-applications"></a>Приложения HPC

Запустите пользовательские или коммерческие приложения HPC в Azure. Некоторые приложения из этого раздела могут эффективно масштабироваться с помощью дополнительных виртуальных машин или вычислительных ядер. Чтобы получить готовые к развертыванию решения, посетите [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace).

> [!NOTE]
> Проконсультируйтесь с поставщиками всех коммерческих приложений насчет лицензирования или иных ограничений на запуск приложений в облаке. Не все поставщики предлагают лицензирование с оплатой по мере использования. Для вашего решения может потребоваться сервер лицензий в облаке или локальный сервер лицензий.

### <a name="engineering-applications"></a>Проектирование приложений

* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

### <a name="graphics-and-rendering"></a>Графика и отрисовка

* [Рендеринг приложений](../articles/batch/batch-rendering-service.md) в пакетной службе Azure, включая Autodesk Maya, 3ds Max и Arnold, Chaos Group V-Ray и Blender.

### <a name="ai-and-deep-learning"></a>Искусственный интеллект и глубокое обучение

* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Виртуальная машина для глубокого обучения](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Инструкции Batch Shipyard для глубокого обучения](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

## <a name="hpc-and-gpu-vm-sizes"></a>Размеры виртуальной машины графического процессора и HPC

Azure предлагает различные размеры для виртуальных машин [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) и [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), в том числе размеры для рабочих нагрузок с ресурсоемкими вычислениями. Например, виртуальные машины H16r и H16mr могут подключаться к сети RDMA серверной части с высокой пропускной способностью. Эта облачная сеть может улучшить производительность тесно связанных параллельных приложений, выполняемых под управлением [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) или Intel MPI. 

Виртуальные машины серии N оснащены графическими процессорами NVIDIA и предназначены для приложений с ресурсоемкими вычислениями или графикой, в том числе для обучения искусственного интеллекта (AI) и визуализации. 

Дополнительные сведения

* Размеры виртуальных машин [Linux](../articles/virtual-machines/linux/sizes-hpc.md) и [Windows](../articles/virtual-machines/windows/sizes-hpc.md) для высокопроизводительных вычислений 
* Размеры виртуальных машин [Linux](../articles/virtual-machines/linux/sizes-gpu.md) и [Windows](../articles/virtual-machines/windows/sizes-gpu.md) с графическими процессорами. 

## <a name="azure-cyclecloud"></a>Azure CycleCloud

Эффективно и легко управляйте стандартными рабочими нагрузками при создании и оптимизации кластеров высокопроизводительных вычислений на виртуальных машинах Azure с помощью [Azure CycleCloud](https://docs.microsoft.com/azure/cyclecloud/overview).

Вы узнаете, как выполнять следующие задачи:

* [Установка и настройка CycleCloud с помощью шаблона Resource Manager](https://docs.microsoft.com/azure/cyclecloud/quickstart-install-cyclecloud)

* [Настройка CycleCloud вручную](https://docs.microsoft.com/azure/cyclecloud/installation)

## <a name="azure-batch"></a>Пакетная служба Azure

[Пакетная служба](../articles/batch/batch-technical-overview.md) — это служба платформы, которая позволяет эффективно работать с приложениями для крупномасштабных параллельных и высокопроизводительных вычислений (HPC) в облаке. Пакетная служба Azure планирует запуск ресурсоемких вычислительных задач в управляемом пуле виртуальных машин и автоматически масштабирует вычислительные ресурсы, учитывая требования заданий. 

Разработчики или поставщики SaaS могут использовать пакеты SDK для пакетной службы и средства для интеграции приложений HPC или контейнерных рабочих нагрузок с Azure, промежуточного хранения данных в Azure и создания конвейеров выполнения заданий. 

Вы узнаете, как выполнять следующие задачи:

* [Сведения о начале разработки с помощью пакетной службы](../articles/batch/quick-run-dotnet.md)
* [Примеры использования кода пакетной службы](https://github.com/Azure/azure-batch-samples)
* [Использование низкоприоритетных виртуальных машин в пакетной службе](../articles/batch/batch-low-pri-vms.md)
* [Сведения о запуске контейнерных рабочих нагрузок HPC с помощью Batch Shipyard](https://github.com/Azure/batch-shipyard)
* [Выполнение параллельных рабочих нагрузок R в пакетной службе](https://github.com/Azure/doAzureParallel)
* [Запуск заданий Spark по требованию в пакетной службе](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>Диспетчеры рабочих нагрузок

Ниже приведены примеры диспетчеров рабочих нагрузок и кластеров, которые могут выполняться в инфраструктуре Azure. Создавайте автономные кластеры на виртуальных машинах Azure или переносите нагрузки из локального кластера на виртуальные машины Azure. 
* [Alces Flight Compute](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM Spectrum Symphony и Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org);
* [Пакет высокопроизводительных вычислений Microsoft](https://docs.microsoft.com/powershell/high-performance-computing/overview) 

## <a name="hpc-storage"></a>Хранилище HPC

Масштабные рабочие нагрузки пакетной службы и HPC требуют ресурсов для хранения данных и доступа, которые превышают возможности традиционных файловых систем в облаке. 

Дополнительные сведения

* [Общие сведения об Avere vFXT для Azure](../articles/avere-vfxt/avere-vfxt-overview.md) 
* [Parallel Virtual File Systems on Microsoft Azure](https://azure.microsoft.com/resources/parallel-virtual-file-systems-on-microsoft-azure/) (Параллельная виртуальная файловая система в Microsoft Azure), включая [Lustre](http://lustre.org/) и [BeeGFS](http://www.beegfs.com/content/).

## <a name="related-azure-services"></a>Связанные службы Azure

Виртуальные машины Azure, масштабируемые наборы виртуальных машин и связанные вычислительные службы являются основой для большинства решений Azure HPC. Но вы можете использовать для своего решения другие связанные службы Azure. Ниже приведен неполный список этих служб.

### <a name="storage"></a>Хранилище

* [Хранилище BLOB-объектов, таблиц и очередей](../articles/storage/storage-introduction.md)
* [Хранилище файлов](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Данные и аналитика

* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Фабрика данных](../articles/data-factory/introduction.md)
* [Azure Data Lake Storage 1-го поколения](../articles/data-lake-store/data-lake-store-overview.md)
* [Databricks](../articles/azure-databricks/what-is-azure-databricks.md)
* [База данных SQL](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-machine-learning"></a>ИИ и машинное обучение

* [Служба машинного обучения](../articles/machine-learning/service/overview-what-is-azure-ml.md)
* [Genomics](../articles/genomics/overview-what-is-genomics.md)

### <a name="networking"></a>Сеть

* [Виртуальная сеть](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Контейнеры

* [Служба Azure Kubernetes (AKS)](../articles/aks/intro-kubernetes.md)
* [Реестр контейнеров](../articles/container-registry/container-registry-intro.md)
* [Экземпляры контейнеров](../articles/container-instances/container-instances-overview.md)

## <a name="customer-stories"></a>Истории клиентов

Примеры клиентов, которые выполнили свои бизнес-задачи с помощью решений Azure HPC:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [EFS](https://customers.microsoft.com/story/efs-professionalservices-azure)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://customers.microsoft.com/story/metlife-insurance-azure-cloud-services-windows-server-analytics-platform-server-en)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [NeuroInitiative](https://customers.microsoft.com/story/neuroinitiative-health-provider-azure)
* [Schlumberger](https://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)

## <a name="next-steps"></a>Дополнительная информация

* Подробнее см. на страницах [Высокопроизводительные вычисления](https://azure.microsoft.com/solutions/high-performance-computing/), [Отрисовка с помощью Azure](https://azure.microsoft.com/solutions/big-compute/rendering/) и [Azure для банков и рынков ценных бумаг](https://finance.azure.com/), а также см. сведения о геномике [здесь](https://enterprise.microsoft.com/industries/health/genomics/).

* См. страницу [Архитектуры решений Azure](https://azure.microsoft.com/solutions/architecture/?solution=high-performance-computing).
