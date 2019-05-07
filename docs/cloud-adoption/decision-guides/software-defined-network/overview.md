---
title: CAF. Программно-конфигурируемые сети
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Описание программно-конфигурируемых сетей, которые играют важнейшую роль при миграции в Azure
author: rotycenh
ms.openlocfilehash: d164ba488552715dc97719329ae9de3fcf5d83ed
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243345"
---
# <a name="caf-software-defined-network-decision-guide"></a><span data-ttu-id="b15ca-103">CAF. Руководство по принятию решений относительно программно-конфигурируемой сети</span><span class="sxs-lookup"><span data-stu-id="b15ca-103">CAF: Software Defined Network decision guide</span></span>

<span data-ttu-id="b15ca-104">Программно-конфигурируемая сеть (SDN) — это сетевая архитектура, поддерживающая функции виртуализированных сетей, которую можно централизованно администрировать, настраивать и изменять с помощью ПО.</span><span class="sxs-lookup"><span data-stu-id="b15ca-104">Software Defined Networking (SDN) is a network architecture designed to allow virtualized networking functionality that can be centrally managed, configured, and modified through software.</span></span> <span data-ttu-id="b15ca-105">SDN обеспечивает уровень абстракции поверх физической сетевой инфраструктуры, предоставляя виртуализированный эквивалент физических маршрутизаторов, брандмауэров и другого сетевого оборудования, которое обычно находится в локальной сети.</span><span class="sxs-lookup"><span data-stu-id="b15ca-105">SDN provides an abstraction layer over the physical networking infrastructure, and enables the virtualized equivalent to physical routers, firewalls, and other networking hardware you would find in an on-premises network.</span></span>

<span data-ttu-id="b15ca-106">С помощью SDN ИТ-специалисты могут настроить и развернуть сетевые структуры и возможности, поддерживающие требования рабочих нагрузок с использованием виртуальных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="b15ca-106">SDN allows IT staff to configure and deploy network structures and capabilities that support workload needs using virtualized resources.</span></span> <span data-ttu-id="b15ca-107">Благодаря гибкости управления программным развертыванием SDN позволяет быстро изменять сетевые ресурсы и поддерживает и гибкую, и традиционную модели развертывания.</span><span class="sxs-lookup"><span data-stu-id="b15ca-107">The flexibility of software-based deployment management enables rapid modification of networking resources and allows the ability to support both agile and traditional deployment models.</span></span> <span data-ttu-id="b15ca-108">Виртуальные сети, созданные на базе технологии SDN, крайне необходимы для создания безопасных сетей на общедоступной облачной платформе.</span><span class="sxs-lookup"><span data-stu-id="b15ca-108">Virtualized networks created with SDN technology are critical to creating secure networks on a public cloud platform.</span></span>

## <a name="networking-decision-guide"></a><span data-ttu-id="b15ca-109">Схема по принятию решений относительно сети</span><span class="sxs-lookup"><span data-stu-id="b15ca-109">Networking decision guide</span></span>

![Схема вариантов сети, отсортированных в порядке увеличения сложности, со ссылками для быстрого перехода](../../_images/discovery-guides/discovery-guide-sdn.png)

<span data-ttu-id="b15ca-111">Перейти к разделу: [Только PaaS](paas-only.md)  | [Полностью облачные решения](cloud-native.md) | | [Сеть периметра в облаке](cloud-dmz.md) [Гибридная среда](hybrid.md) | [Звездообразная модель](hub-spoke.md) | [Дополнительные сведения](#learn-more)</span><span class="sxs-lookup"><span data-stu-id="b15ca-111">Jump to: [PaaS Only](paas-only.md) | [Cloud native](cloud-native.md) | | [Cloud DMZ](cloud-dmz.md) [Hybrid](hybrid.md) | [Hub/Spoke model](hub-spoke.md) | [Learn more](#learn-more)</span></span>

<span data-ttu-id="b15ca-112">SDN предоставляет несколько вариантов различной стоимости и сложности.</span><span class="sxs-lookup"><span data-stu-id="b15ca-112">SDN provides several options with varying degrees of pricing and complexity.</span></span> <span data-ttu-id="b15ca-113">На представленной выше схеме кратко изложены эти варианты, чтобы вы могли быстро сопоставить их с конкретными технологическими и бизнес-стратегиями.</span><span class="sxs-lookup"><span data-stu-id="b15ca-113">The above discovery guide provides a reference to quickly personalize these options to best align with specific business and technology strategies.</span></span>

<span data-ttu-id="b15ca-114">Важными факторами при выборе в этом руководстве являются несколько ключевых решений, которые приняла ваша команда по облачной стратегии, прежде чем принять решение о сетевой архитектуре.</span><span class="sxs-lookup"><span data-stu-id="b15ca-114">The inflection point in this guide depends on several key decisions that your Cloud Strategy team have made before making decisions about networking architecture.</span></span> <span data-ttu-id="b15ca-115">Наиболее важные ключевые решения связаны с [определением цифровых активов](../../digital-estate/overview.md) и [проектом подписки](../subscriptions/overview.md) (на которые также могут влиять решения, касающиеся учета облачных затрат и глобальных маркетинговых стратегий).</span><span class="sxs-lookup"><span data-stu-id="b15ca-115">Most important among these are decisions involving your [Digital Estate definition](../../digital-estate/overview.md) and [Subscription Design](../subscriptions/overview.md) (which may also require inputs from decisions made related to your cloud accounting and global markets strategies).</span></span>

<span data-ttu-id="b15ca-116">Эти факторы едва ли влияют на небольшие развертывания в одном регионе (менее 1000 виртуальных машин).</span><span class="sxs-lookup"><span data-stu-id="b15ca-116">Small, single region deployments of less than 1,000 VMs are less likely to be significantly affected by this inflection point.</span></span> <span data-ttu-id="b15ca-117">И наоборот, выбранный вариант SDN и эти факторы могут существенно влиять на скорость и сложность внедрения при наличии более чем 1000 виртуальных машин, нескольких подразделений или геополитических рынков.</span><span class="sxs-lookup"><span data-stu-id="b15ca-117">Conversely, large adoption efforts with more than 1,000 VMs, multiple business units, or multiple geo-politic markets, could be substantially affected by your SDN decision and this key inflection point.</span></span>

## <a name="choosing-the-right-virtual-networking-architectures"></a><span data-ttu-id="b15ca-118">Выбор подходящих архитектур виртуальных сетей</span><span class="sxs-lookup"><span data-stu-id="b15ca-118">Choosing the right virtual networking architectures</span></span>

<span data-ttu-id="b15ca-119">Этот раздел дополняет схему по принятию решений, чтобы помочь вам выбрать подходящие архитектуры виртуальных сетей.</span><span class="sxs-lookup"><span data-stu-id="b15ca-119">This section expands on the decision guide to help you choose the right virtual networking architectures.</span></span>

<span data-ttu-id="b15ca-120">Реализовать технологии SDN для создания виртуальных сетей в облаке можно множеством способов.</span><span class="sxs-lookup"><span data-stu-id="b15ca-120">There are many ways to implement SDN technologies to create cloud-based virtual networks.</span></span> <span data-ttu-id="b15ca-121">То, как вы структурируете виртуальные сети, используемые при миграции, и как эти сети взаимодействуют с вашей существующей ИТ-инфраструктурой, будет зависеть от сочетания требований к рабочим нагрузкам и системе управления.</span><span class="sxs-lookup"><span data-stu-id="b15ca-121">How you structure the virtual networks used in your migration and how those networks interact with your existing IT infrastructure will depend on a combination of the workload requirements and your governance requirements.</span></span>

<span data-ttu-id="b15ca-122">При выборе архитектуры или сочетания архитектур виртуальных сетей во время планирования миграции в облако ответьте на следующие вопросы, чтобы определить правильное решение для вашей организации.</span><span class="sxs-lookup"><span data-stu-id="b15ca-122">When planning which virtual networking architecture or combination of architectures to consider when planning your cloud migration, consider the following questions to help determine what's right for your organization:</span></span>

| <span data-ttu-id="b15ca-123">Вопрос</span><span class="sxs-lookup"><span data-stu-id="b15ca-123">Question</span></span> | <span data-ttu-id="b15ca-124">Только PaaS</span><span class="sxs-lookup"><span data-stu-id="b15ca-124">PaaS Only</span></span> | <span data-ttu-id="b15ca-125">Полностью облачные решения</span><span class="sxs-lookup"><span data-stu-id="b15ca-125">Cloud Native</span></span> | <span data-ttu-id="b15ca-126">Сеть периметра в облаке</span><span class="sxs-lookup"><span data-stu-id="b15ca-126">Cloud DMZ</span></span> | <span data-ttu-id="b15ca-127">Гибридная среда</span><span class="sxs-lookup"><span data-stu-id="b15ca-127">Hybrid</span></span> | <span data-ttu-id="b15ca-128">Звездообразная</span><span class="sxs-lookup"><span data-stu-id="b15ca-128">Hub and Spoke</span></span> |
|-----|-----|-----|-----|-----|-----|
| <span data-ttu-id="b15ca-129">Будет ли ваша рабочая нагрузка использовать только службы PaaS и не требовать других сетевых возможностей, которые эти службы не предоставляют?</span><span class="sxs-lookup"><span data-stu-id="b15ca-129">Will your workload only use PaaS services and not require networking capabilities beyond those provided by the services themselves?</span></span> | <span data-ttu-id="b15ca-130">Yes</span><span class="sxs-lookup"><span data-stu-id="b15ca-130">Yes</span></span> | <span data-ttu-id="b15ca-131">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-131">No</span></span> | <span data-ttu-id="b15ca-132">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-132">No</span></span> | <span data-ttu-id="b15ca-133">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-133">No</span></span> | <span data-ttu-id="b15ca-134">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-134">No</span></span> |
| <span data-ttu-id="b15ca-135">Требует ли рабочая нагрузка интеграции с локальными приложениями?</span><span class="sxs-lookup"><span data-stu-id="b15ca-135">Does your workload require integration with on-premises applications?</span></span> | <span data-ttu-id="b15ca-136">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-136">No</span></span> | <span data-ttu-id="b15ca-137">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-137">No</span></span> | <span data-ttu-id="b15ca-138">Yes</span><span class="sxs-lookup"><span data-stu-id="b15ca-138">Yes</span></span> | <span data-ttu-id="b15ca-139">Да</span><span class="sxs-lookup"><span data-stu-id="b15ca-139">Yes</span></span> | <span data-ttu-id="b15ca-140">Yes</span><span class="sxs-lookup"><span data-stu-id="b15ca-140">Yes</span></span> |
| <span data-ttu-id="b15ca-141">Определили ли вы подходящие политики безопасности и установили ли безопасное подключение между локальными и облачными сетями?</span><span class="sxs-lookup"><span data-stu-id="b15ca-141">Have you established mature security policies and secure connectivity between your on-premises and cloud networks?</span></span> | <span data-ttu-id="b15ca-142">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-142">No</span></span> | <span data-ttu-id="b15ca-143">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-143">No</span></span> | <span data-ttu-id="b15ca-144">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-144">No</span></span> | <span data-ttu-id="b15ca-145">Yes</span><span class="sxs-lookup"><span data-stu-id="b15ca-145">Yes</span></span> | <span data-ttu-id="b15ca-146">Yes</span><span class="sxs-lookup"><span data-stu-id="b15ca-146">Yes</span></span> |
| <span data-ttu-id="b15ca-147">Требуются ли для вашей рабочей нагрузки службы проверки подлинности, не поддерживаемые облачными службами идентификации, или вам нужен прямой доступ к локальным контроллерам доменов?</span><span class="sxs-lookup"><span data-stu-id="b15ca-147">Does your workload require authentication services not supported through cloud identity services, or do you need direct access to on-premises domain controllers?</span></span> | <span data-ttu-id="b15ca-148">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-148">No</span></span> | <span data-ttu-id="b15ca-149">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-149">No</span></span> | <span data-ttu-id="b15ca-150">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-150">No</span></span> | <span data-ttu-id="b15ca-151">Yes</span><span class="sxs-lookup"><span data-stu-id="b15ca-151">Yes</span></span> | <span data-ttu-id="b15ca-152">Yes</span><span class="sxs-lookup"><span data-stu-id="b15ca-152">Yes</span></span> |
| <span data-ttu-id="b15ca-153">Нужно ли вам развертывать большое количество виртуальных машин и рабочих нагрузок и управлять ими?</span><span class="sxs-lookup"><span data-stu-id="b15ca-153">Will you need to deploy and manage a large number of VMs and workloads?</span></span> | <span data-ttu-id="b15ca-154">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-154">No</span></span> | <span data-ttu-id="b15ca-155">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-155">No</span></span> | <span data-ttu-id="b15ca-156">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-156">No</span></span> | <span data-ttu-id="b15ca-157">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-157">No</span></span> | <span data-ttu-id="b15ca-158">Yes</span><span class="sxs-lookup"><span data-stu-id="b15ca-158">Yes</span></span> |
| <span data-ttu-id="b15ca-159">Нужно ли вам обеспечивать централизованное управление и локальное подключение, делегируя контроль над ресурсами отдельным группам рабочих нагрузок?</span><span class="sxs-lookup"><span data-stu-id="b15ca-159">Will you need to provide centralized management and on-premises connectivity while delegating control over resources to individual workload teams?</span></span> | <span data-ttu-id="b15ca-160">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-160">No</span></span> | <span data-ttu-id="b15ca-161">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-161">No</span></span> | <span data-ttu-id="b15ca-162">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-162">No</span></span> | <span data-ttu-id="b15ca-163">Нет </span><span class="sxs-lookup"><span data-stu-id="b15ca-163">No</span></span> | <span data-ttu-id="b15ca-164">Yes</span><span class="sxs-lookup"><span data-stu-id="b15ca-164">Yes</span></span> |

## <a name="virtual-networking-architectures"></a><span data-ttu-id="b15ca-165">Архитектуры виртуальных сетей</span><span class="sxs-lookup"><span data-stu-id="b15ca-165">Virtual networking architectures</span></span>

<span data-ttu-id="b15ca-166">Ниже подробно описаны основные архитектуры программно-конфигурируемых сетей.</span><span class="sxs-lookup"><span data-stu-id="b15ca-166">Learn more about the primary software defined networking architectures:</span></span>

- <span data-ttu-id="b15ca-167">[**Только PaaS**](paas-only.md). Платформа как услуга (PaaS) поддерживает ограниченный набор встроенных сетевых функций и может не требовать явно определенной программно-конфигурируемой сети для поддержки требований к рабочей нагрузке.</span><span class="sxs-lookup"><span data-stu-id="b15ca-167">[**PaaS Only**](paas-only.md): Platform as a service (PaaS) products support a limited set of built-in networking features and may not require an explicitly defined software defined network to support workload requirements.</span></span>
- <span data-ttu-id="b15ca-168">[**Полностью облачная сеть**](cloud-native.md). Полностью облачная виртуальная сеть — это стандартная архитектура программно-конфигурируемой сети при развертывании ресурсов на облачной платформе.</span><span class="sxs-lookup"><span data-stu-id="b15ca-168">[**Cloud Native**](cloud-native.md): A cloud native virtual network is the default software defined networking architecture when deploying resources to a cloud platform.</span></span>
- <span data-ttu-id="b15ca-169">[**Сеть периметра в облаке**](cloud-dmz.md). Обеспечивает ограниченную связь между локальной и облачной сетью, которая защищена с помощью реализации сети периметра в облачной среде.</span><span class="sxs-lookup"><span data-stu-id="b15ca-169">[**Cloud DMZ**](cloud-dmz.md): Provides limited connectivity between your on-premises and cloud network which is secured through the implementation of a demilitarized zone on the cloud environment.</span></span>
- <span data-ttu-id="b15ca-170">[**Гибридная сеть**](hybrid.md). Гибридная облачная сетевая архитектура позволяет виртуальным сетям получать доступ к вашим локальным ресурсам и наоборот.</span><span class="sxs-lookup"><span data-stu-id="b15ca-170">[**Hybrid**](hybrid.md): The hybrid cloud network architecture allows virtual networks to access your on-premises resources and vice versa.</span></span>
- <span data-ttu-id="b15ca-171">[**Звездообразная архитектура**](hub-spoke.md). Позволяет централизованно управлять внешними подключениями и общими службами, изолировать отдельные рабочие нагрузки и преодолевать потенциальные ограничения на подписку.</span><span class="sxs-lookup"><span data-stu-id="b15ca-171">[**Hub and Spoke**](hub-spoke.md): The hub and spoke architecture allows you to centrally manage external connectivity and shared services, isolate individual workloads, and overcome potential subscription limits.</span></span>

## <a name="learn-more"></a><span data-ttu-id="b15ca-172">Подробнее</span><span class="sxs-lookup"><span data-stu-id="b15ca-172">Learn more</span></span>

<span data-ttu-id="b15ca-173">Ознакомьтесь со следующими статьями, чтобы получить дополнительные сведения о программно-конфигурируемых сетях на платформе Azure.</span><span class="sxs-lookup"><span data-stu-id="b15ca-173">See the following for more information about software defined networking in the Azure platform.</span></span>

- <span data-ttu-id="b15ca-174">[Что такое виртуальная сеть Azure?](/azure/virtual-network/virtual-networks-overview)</span><span class="sxs-lookup"><span data-stu-id="b15ca-174">[Azure Virtual Network](/azure/virtual-network/virtual-networks-overview).</span></span> <span data-ttu-id="b15ca-175">В Azure основная возможность SDN предоставляется виртуальной сетью Azure, которая действует как облачный аналог физических локальных сетей.</span><span class="sxs-lookup"><span data-stu-id="b15ca-175">On Azure, the core SDN capability is provided by Azure Virtual Network, which acts as a cloud analog to physical on-premises networks.</span></span> <span data-ttu-id="b15ca-176">Виртуальные сети также выступают в качестве стандартной границы, изолирующей ресурсы на платформе.</span><span class="sxs-lookup"><span data-stu-id="b15ca-176">Virtual networks also act as a default isolation boundary between resources on the platform.</span></span>
- <span data-ttu-id="b15ca-177">[Рекомендации по обеспечению безопасности в сети Azure](/azure/security/azure-security-network-security-best-practices).</span><span class="sxs-lookup"><span data-stu-id="b15ca-177">[Azure Network Security Best Practices](/azure/security/azure-security-network-security-best-practices).</span></span> <span data-ttu-id="b15ca-178">Рекомендации от группы безопасности Azure по настройке виртуальных сетей для минимизации уязвимостей безопасности.</span><span class="sxs-lookup"><span data-stu-id="b15ca-178">Recommendations from the Azure Security team on how to configure your virtual networks to minimize security vulnerabilities.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b15ca-179">Дополнительная информация</span><span class="sxs-lookup"><span data-stu-id="b15ca-179">Next steps</span></span>

<span data-ttu-id="b15ca-180">Узнайте, операционные команды используют журналы, мониторинг и отчетность для управления работоспособностью облачных рабочих нагрузок и их соответствием политике.</span><span class="sxs-lookup"><span data-stu-id="b15ca-180">Learn how logs, monitoring, and reporting are used by operations teams to manage the health and policy compliance of cloud workloads.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b15ca-181">Руководство по принятию решений о ведении журнала и созданию отчетов</span><span class="sxs-lookup"><span data-stu-id="b15ca-181">Logs and Reporting</span></span>](../log-and-report/overview.md)