---
title: "Разработка с учетом бизнес-потребностей"
description: "Каждое решение в процессе разработки должно определяться бизнес-требованиями."
author: MikeWasson
layout: LandingPage
ms.openlocfilehash: 110a441ae74334d212a717da2cb038d60b24bb1f
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2017
---
# <a name="build-for-the-needs-of-the-business"></a>Разработка с учетом бизнес-потребностей

## <a name="every-design-decision-must-be-justified-by-a-business-requirement"></a>Каждое решение в процессе разработки должно определяться бизнес-требованиями.

Этот принцип может показаться очевидным, но им не всегда руководствуются при разработке решений. У вас будут миллионы пользователей или всего несколько тысяч? Допустим ли часовой перерыв в работе приложения? Ожидаются ли резкие всплески трафика или рабочая нагрузка будет предельно прогнозируемой? Любое решение по разработке должно в конечном счете соответствовать бизнес-требованиям. 

## <a name="recommendations"></a>Рекомендации

**Определите бизнес-цели**, в том числе целевое время восстановления (RTO), целевую точку восстановления (RPO) и максимальное допустимое время простоя (MTO). Эти значения и станут критериями при выборе архитектуры. Например, если вам нужно низкое значение RTO, применяйте автоматическую отработку отказа в дополнительный регион. Но если для вашего решения допустимо более высокое значение RTO, такая степень избыточности не нужна.

**Задокументируйте соглашения об уровне обслуживания (SLA) и цели уровня обслуживания (SLO)**, включив в них показатели доступности и производительности. Допустим, вы создали решение с гарантированным уровнем доступности 99,95 %. Это много или мало? Ответ зависит от принятых бизнес-решений. 

**Моделируйте приложение на основе предметной области бизнеса.** Для начала проанализируйте бизнес-требования. На основе этих требований создайте модель приложения. Попробуйте применить разработку на основе домена (DDD) и подготовьте [предметные модели][domain-model], отражающие важные бизнес-процессы и методы использования. 

**Составьте список всех функциональных и нефункциональных требований.** Функциональные требования позволяют убедиться, что в приложении выполняются правильные действия. Нефункциональные требования позволяют проверить, насколько *хорошо* они выполняются. В частности, уделите особое внимание всем требованиям к масштабируемости, доступности и задержкам. Эти требования повлияют на выбор технологий и решения в процессе разработки.

**Распределите рабочие нагрузки.** Термин "рабочая нагрузка" в этом контексте означает дискретную возможность или вычислительную задачу, которая может быть логически отделена от других задач. Разные рабочие нагрузки будут иметь разные требования к доступности, масштабируемости, согласованности данных и аварийному восстановлению. 

**Учитывайте темпы роста.** Возможно, решение полностью соответствует текущим потребностями в плане количества пользователей, объема транзакций, хранилища данных и т. д. Но действительно надежное приложение должно выдерживать определенный рост без существенных изменений архитектуры. См. также статьи [Design to scale out](scale-out.md) (Разработка с учетом горизонтального масштабирования) и [Partition around limits](partition.md) (Разделение для обхода ограничений). Кроме того, не забывайте, что бизнес-модель и бизнес-требования могут со временем измениться. Если в приложении используются слишком жесткие модели службы и данных, его будет трудно развивать для использования новых сценариев и методов работы. См. статью [Design for evolution](design-for-evolution.md) (Разработка с учетом будущих изменений).

**Контролируйте затраты.** При работе с традиционными локальными приложениями вы оплачиваете все оборудование заранее (это капитальные затраты). Но в облачных приложениях вы платите только за используемые ресурсы. Хорошо изучите модель ценообразования для всех используемых служб. Общая стоимость определяется с учетом пропускной способности сети, объема хранилища, IP-адресов, используемых служб и других факторов. Дополнительные сведения см. на странице с [ценами Azure][pricing]. Также учтите затраты на эксплуатацию. В облаке вам не нужно управлять оборудованием или другой инфраструктурой, но по-прежнему требуется выполнять обслуживание приложения: DevOps, реагирование на инциденты, аварийное восстановление и т. д. 

[domain-model]: https://martinfowler.com/eaaCatalog/domainModel.html
[pricing]: https://azure.microsoft.com/pricing/