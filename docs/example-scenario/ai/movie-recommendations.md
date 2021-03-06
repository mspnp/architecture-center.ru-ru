---
title: Рекомендации фильмов в Azure
description: Использование машинного обучения для автоматизированного предоставления рекомендаций по фильмам, товарам и т. п. путем обучения модели в Azure с помощью машинного обучения и виртуальных машин для обработки и анализа данных (DSVM) Azure.
author: njray
ms.date: 01/09/2019
ms.custom: azcat-ai, AI
social_image_url: /azure/architecture/example-scenario/ai/media/architecture-movie-recommender.png
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.openlocfilehash: be7959c1201e3a2aaf92c192d73a0ca47a990934
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249649"
---
# <a name="movie-recommendations-on-azure"></a>Рекомендации фильмов в Azure

Здесь рассматривается пример сценария, в котором предприятие применяет машинное обучение для автоматизации рекомендаций товаров своим клиентам. Виртуальная машина для обработки и анализа данных (DSVM) Azure используется для обучения в Azure модели, которая рекомендует пользователям фильмы на основе оценок, присвоенных этим фильмам.

Такие рекомендации будут полезны в разных отраслях — от розничной торговли до новостей и СМИ. Например, вы можете рекомендовать товары в виртуальном магазине, новостные статьи или записи в блогах, а также музыкальные композиции. Раньше предприятиям приходилось нанимать и обучать консультантов для того, чтобы предоставлять клиентам персонализированные рекомендации. Но теперь мы можем осуществлять такие действия в широком масштабе, используя Azure для обучения моделей, учитывающих предпочтения клиентов.

## <a name="relevant-use-cases"></a>Варианты соответствующего использования

Рассмотрите этот сценарий для следующих вариантов использования:

* рекомендации фильмов на веб-сайте;
* рекомендации по потребительским товарам в мобильном приложении;
* рекомендации новостей в вещательных сетях.

## <a name="architecture"></a>Архитектура

![Архитектура модели машинного обучения для создания рекомендаций к фильмам][architecture]

Этот сценарий посвящен обучению и оценке модели машинного обучения с помощью алгоритма Spark [чередующихся наименьших квадратов][als] (ALS) по набору данных с оценками фильмов. В этом сценарии применяются следующие действия.

1. Интерфейсный веб-сайт или служба приложений собирает исторические данные о взаимодействиях пользователей с фильмами и размещает их в таблице с кортежами пользователя, элемента и числовой оценки.

2. Собранные исторические данные сохраняются в хранилище BLOB-объектов.

3. DSVM часто используется для экспериментов с моделями рекомендаций на основе алгоритма Spark ALS и публикации готовых моделей. Модель ALS обучается с использованием учебного набора данных, который извлекается из общего набора данных путем применения наиболее подходящей стратегии разбиения данных. Например, набор данных можно разделить на несколько наборов случайным образом, в хронологическом порядке или по определенному условию, в зависимости от конкретных бизнес-требований. Как и для других задач машинного обучения, система рекомендаций проверяется по метрикам оценки (например, precision\@*k*, recall\@*k*, [MAP][map], [nDCG\@k][ndcg]).

4. Служба машинного обучения Azure берет на себя координирование экспериментов, в том числе перебор гиперпараметров и управление моделями.

5. Обученная модель сохраняется в Azure Cosmos DB. Ее можно применить для получения *k* лучших фильмов, которые можно рекомендовать конкретному пользователю.

6. Затем эта модель развертывается на сайте или в службе приложений с использованием экземпляров контейнеров Azure или службы Azure Kubernetes.

Подробное руководство по разработке и масштабированию службы рекомендаций вы найдете в статье [Создание API рекомендаций в режиме реального времени в Azure][ref-arch].

### <a name="components"></a>Компоненты

* [Виртуальная машина для обработки и анализа данных][dsvm] (DSVM) — это виртуальная машина Azure с платформами для глубокого обучения и средствами для машинного обучения, а также обработки и анализа данных. DSVM комплектуется изолированной средой Spark, в которой можно выполнять ALS.

* В [хранилище BLOB-объектов][blob] хранится набор данных для рекомендаций фильмов.

* [Служба машинного обучения Azure][mls] используется для ускорения разработки, управления и развертывания моделей машинного обучения.

* [Azure Cosmos DB][cosmosdb] предоставляет многомодельное глобально распределенное хранилище на основе баз данных.

* [Экземпляры контейнеров Azure][aci] применяются для развертывания обученных моделей в веб-службах или службах приложений, в том числе с применением [службы Azure Kubernetes][aks].

### <a name="alternatives"></a>Альтернативные варианты

[Azure Databricks][databricks] — это управляемый кластер Spark, в котором выполняются обучение и оценка моделей. Вы можете настроить управляемую среду Spark всего за несколько минут и [автоматически масштабировать][autoscale] ее, сокращая затраты ресурсов и денежных средств, связанные с ручным масштабированием кластеров. Еще один способ экономить ресурсы — настроить автоматическое завершение работы неактивных [кластеров][clusters].

## <a name="considerations"></a>Рекомендации

### <a name="availability"></a>Доступность

Приложения на основе машинного обучения содержат два компонента ресурсов: ресурсы для обучения и ресурсы для обслуживания. Ресурсам для обучения обычно не требуется высокий уровень доступности, так как запросы из производственной среды не обращаются к ним напрямую. Ресурсам для обслуживания важен высокий уровень доступности, что позволяет быстро обслуживать запросы клиентов.

DSVM для обучения доступны в [нескольких регионах][regions] по всему миру, и для них предоставляются гарантии [соглашения об уровне обслуживания][sla] для виртуальных машин. Для целей обслуживания служба Azure Kubernetes предлагает инфраструктуру [с высоким уровнем доступности][ha]. К узлам агентов также применяется [соглашение об уровне обслуживания][sla-aks] для виртуальных машин.

### <a name="scalability"></a>Масштабируемость

Если вы используете данные большого объема, попробуйте масштабировать DSVM для сокращения времени обучения. Для масштабирования виртуальной машины можно [увеличивать и уменьшать][vm-size] ее размер. Выберите объем памяти, достаточный для размещения всего набора данных, и большое количество виртуальных ЦП, чтобы уменьшить количество времени на обучение.

### <a name="security"></a>Безопасность

В этом сценарии можно применить Azure Active Directory для аутентификации [доступа пользователей к DSVM][dsvm-id], где хранятся код приложения, модели и данные (в памяти). Данные сохраняются в службе хранилища Azure, а затем передаются на DSVM, где они автоматически шифруются с помощью [шифрования службы хранилища][storage-security]. Управлять разрешениями можно с помощью аутентификации Azure Active Directory или управления доступом на основе ролей.

## <a name="deploy-this-scenario"></a>Развертывание этого сценария

**Предварительные требования**: Необходимо иметь учетную запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись][free], прежде чем начинать работу.

Весь код для этого сценария размещен в [репозитории Microsoft Recommenders][github].

Выполните следующие действия, чтобы запустить [записную книжку для начала работы с ALS][notebook]:

1. [Создайте виртуальную машину для обработки и анализа данных][dsvm-ubuntu] на портале Azure.

2. Клонируйте репозиторий в папку Notebooks.

    ```shell
    cd notebooks
    git clone https://github.com/Microsoft/Recommenders
    ```

3. Установите зависимости conda, выполнив действия, описанные в файле [SETUP.md][setup].

4. Откройте в браузере виртуальную машину jupyterlab и перейдите к `notebooks/00_quick_start/als_pyspark_movielens.ipynb`.

5. Выполните записную книжку.

## <a name="related-resources"></a>Связанные ресурсы

Подробное руководство по разработке и масштабированию службы рекомендаций вы найдете в статье [Создание API рекомендаций в режиме реального времени в Azure][ref-arch]. Руководства и примеры систем рекомендаций можно найти в [репозитории Microsoft Recommenders][github].

[architecture]: ./media/architecture-movie-recommender.png
[aci]: /azure/container-instances/container-instances-overview
[aad]: /azure/active-directory-b2c/active-directory-b2c-overview
[aks]: /azure/aks/intro-kubernetes
[als]: https://spark.apache.org/docs/latest/ml-collaborative-filtering.html
[autoscale]: https://docs.azuredatabricks.net/user-guide/clusters/sizing.html#autoscaling
[blob]: /azure/storage/blobs/storage-blobs-introduction
[clusters]: https://docs.azuredatabricks.net/user-guide/clusters/configure.html
[cosmosdb]: /azure/cosmos-db/introduction
[databricks]: /azure/azure-databricks/what-is-azure-databricks
[dsvm]: /azure/machine-learning/data-science-virtual-machine/overview
[dsvm-id]: /azure/machine-learning/data-science-virtual-machine/dsvm-common-identity
[dsvm-ubuntu]: /azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro
[free]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[github]: https://github.com/Microsoft/Recommenders
[ha]: /azure/aks/container-service-quotas
[map]: https://en.wikipedia.org/wiki/Evaluation_measures_(information_retrieval)
[mls]: /azure/machine-learning/service/
[n-tier]: /azure/architecture/reference-architectures/n-tier/n-tier-cassandra
[ndcg]: https://en.wikipedia.org/wiki/Discounted_cumulative_gain
[notebook]: https://github.com/Microsoft/Recommenders/notebooks/00_quick_start/als_pyspark_movielens.ipynb
[ref-arch]: /azure/architecture/reference-architectures/ai/real-time-recommendation
[regions]: https://azure.microsoft.com/en-us/global-infrastructure/services/?products=virtual-machines&regions=all
[resiliency]: /azure/architecture/resiliency/
[sec-docs]: /azure/security/
[setup]: https://github.com/Microsoft/Recommenders/blob/master/SETUP.md%60
[sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_8/
[sla-aks]: https://azure.microsoft.com/en-us/support/legal/sla/kubernetes-service/v1_0/
[storage-security]: /azure/storage/common/storage-service-encryption
[vm-size]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
