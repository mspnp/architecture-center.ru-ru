---
title: Крупное предприятие. Эволюция согласованности ресурсов
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Крупное предприятие. Эволюция согласованности ресурсов
author: BrianBlanchard
ms.openlocfilehash: 9fc6ca015310c02338463d3c8aa53ddfc74699df
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901636"
---
# <a name="large-enterprise-resource-consistency-evolution"></a><span data-ttu-id="f270c-103">Крупное предприятие. Эволюция согласованности ресурсов</span><span class="sxs-lookup"><span data-stu-id="f270c-103">Large enterprise: Resource Consistency evolution</span></span>

<span data-ttu-id="f270c-104">В этой статье описывается добавление в MVP средств обеспечения согласованности ресурсов для поддержки критически важных приложений.</span><span class="sxs-lookup"><span data-stu-id="f270c-104">This article evolves the narrative by adding Resource Consistency controls to the governance MVP to support mission-critical applications.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="f270c-105">Эволюция описания</span><span class="sxs-lookup"><span data-stu-id="f270c-105">Evolution of the narrative</span></span>

<span data-ttu-id="f270c-106">Команды по внедрению облака выполнили все требования для перемещения защищаемых данных.</span><span class="sxs-lookup"><span data-stu-id="f270c-106">The cloud adoption teams have met all requirements to move protected data.</span></span> <span data-ttu-id="f270c-107">С этими приложениями поставляются Соглашения об уровне обслуживания для бизнеса, и для этих приложений необходима поддержка со стороны отдела ИТ-операций.</span><span class="sxs-lookup"><span data-stu-id="f270c-107">With those applications come SLA commitments to the business and need for support from IT Operations.</span></span> <span data-ttu-id="f270c-108">Когда соответствующая команда перенесет два центра обработки данных, множество команд по разработке приложений и команд бизнес-аналитики будут готовы к разработке новых решений.</span><span class="sxs-lookup"><span data-stu-id="f270c-108">Right behind the team migrating the two datacenters, multiple app dev and BI teams are ready to begin launching new solutions into production.</span></span> <span data-ttu-id="f270c-109">Отдел ИТ-операций еще не знаком с облачными операциями, и им необходим способ, позволяющий быстро интегрировать существующие рабочие процессы.</span><span class="sxs-lookup"><span data-stu-id="f270c-109">IT Operations is new to the thought of cloud operations and needs a way to quickly integrate existing operational processes.</span></span>

### <a name="evolution-of-current-state"></a><span data-ttu-id="f270c-110">Эволюция текущего состояния</span><span class="sxs-lookup"><span data-stu-id="f270c-110">Evolution of current state</span></span>

- <span data-ttu-id="f270c-111">ИТ-отдел активно перемещает рабочие нагрузки с защищенными данными в Azure.</span><span class="sxs-lookup"><span data-stu-id="f270c-111">IT is actively moving production workloads with protected data into Azure.</span></span> <span data-ttu-id="f270c-112">Несколько рабочих нагрузок с низким приоритетом обрабатывают рабочий трафик.</span><span class="sxs-lookup"><span data-stu-id="f270c-112">A number of low priority workloads are serving production traffic.</span></span> <span data-ttu-id="f270c-113">Как только отдел ИТ-операций подтвердит готовность поддержать рабочие нагрузки, можно будет перенести большее их количество.</span><span class="sxs-lookup"><span data-stu-id="f270c-113">More can be cut over, as soon as IT Operations signs off on readiness to support the workloads.</span></span>
- <span data-ttu-id="f270c-114">Команды по разработке приложений готовы к рабочему трафику.</span><span class="sxs-lookup"><span data-stu-id="f270c-114">The application development teams are ready for production traffic.</span></span>
- <span data-ttu-id="f270c-115">Команда бизнес-аналитики готова интегрировать прогнозы и аналитику в системы, которые выполняют операции для трех бизнес-подразделений.</span><span class="sxs-lookup"><span data-stu-id="f270c-115">The BI team is ready to integrate predictions and insights into the systems that run operations for the three business units.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="f270c-116">Эволюция будущего состояния</span><span class="sxs-lookup"><span data-stu-id="f270c-116">Evolution of the future state</span></span>

- <span data-ttu-id="f270c-117">Отдел ИТ-операций еще не знаком с облачными операциями, и им необходим способ, позволяющий быстро интегрировать существующие рабочие процессы.</span><span class="sxs-lookup"><span data-stu-id="f270c-117">IT operations is new to the thought of cloud operations and needs a way to quickly integrate existing operational processes.</span></span>

<span data-ttu-id="f270c-118">Изменения в текущем и будущем состояниях представляют новые риски, требующие новых положений политики.</span><span class="sxs-lookup"><span data-stu-id="f270c-118">The changes to current and future state expose new risks that will require new policy statements.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="f270c-119">Эволюция реальных рисков</span><span class="sxs-lookup"><span data-stu-id="f270c-119">Evolution of tangible risks</span></span>

<span data-ttu-id="f270c-120">**Прерывание работы бизнеса**. Существует неизбежный риск того, что любая новая платформа может вызвать прерывание работы критически важных бизнес-процессов.</span><span class="sxs-lookup"><span data-stu-id="f270c-120">**Business Interruption**: There is an inherent risk of any new platform causing interruptions to mission-critical business processes.</span></span> <span data-ttu-id="f270c-121">Команда ИТ-операций и команды, внедряющие различные облачные решения, относительно неопытны в работе с облачными операциями.</span><span class="sxs-lookup"><span data-stu-id="f270c-121">The IT Operations team and the teams executing on various cloud adoptions are relatively inexperienced with cloud operations.</span></span> <span data-ttu-id="f270c-122">Это повышает риск прерывания. Нужно уменьшить этот риск и реализовать механизм управления им.</span><span class="sxs-lookup"><span data-stu-id="f270c-122">This increases the risk of interruption and must be mitigated and governed.</span></span>

<span data-ttu-id="f270c-123">Бизнес-риск можно разделить на несколько технических рисков:</span><span class="sxs-lookup"><span data-stu-id="f270c-123">This business risk can be expanded into several technical risks:</span></span>

- <span data-ttu-id="f270c-124">Несопоставленные рабочие процессы могут вызвать простои, которые не удается обнаружить или быстро исправить.</span><span class="sxs-lookup"><span data-stu-id="f270c-124">Misaligned operational processes might lead to outages that can’t be detected or remediated quickly.</span></span>
- <span data-ttu-id="f270c-125">Внешнее вторжение или атака типа "отказ в обслуживании" могут привести к прерыванию работы бизнеса.</span><span class="sxs-lookup"><span data-stu-id="f270c-125">External intrusion or denial of service attacks might cause a business interruption</span></span>
- <span data-ttu-id="f270c-126">Критически важные ресурсы могут быть обнаружены неправильно и, следовательно, могут работать ненадлежащим образом.</span><span class="sxs-lookup"><span data-stu-id="f270c-126">Mission-critical assets might not be properly discovered and therefore not properly operated.</span></span>
- <span data-ttu-id="f270c-127">Необнаруженные или неправильно помеченные ресурсы могут не поддерживаться существующими процессами операционного управления.</span><span class="sxs-lookup"><span data-stu-id="f270c-127">Undiscovered or mislabeled assets might not be supported by existing operational management processes.</span></span>
- <span data-ttu-id="f270c-128">Конфигурация развернутых ресурсов может не соответствовать ожидаемым показателям производительности.</span><span class="sxs-lookup"><span data-stu-id="f270c-128">Configuration of deployed assets might not meet performance expectations.</span></span>
- <span data-ttu-id="f270c-129">Журналы могут записываться ненадлежащим образом и сохраняться не в центральном расположении, что не позволяет исправлять проблемы с производительностью.</span><span class="sxs-lookup"><span data-stu-id="f270c-129">Logging might not be properly recorded and centralized to allow for remediation of performance issues.</span></span>
- <span data-ttu-id="f270c-130">Политики восстановления могут завершиться ошибкой или выполняться дольше, чем ожидается.</span><span class="sxs-lookup"><span data-stu-id="f270c-130">Recovery policies may fail or take longer than expected.</span></span>
- <span data-ttu-id="f270c-131">Несогласованные процессы развертывания могут привести к брешам в системе безопасности с последующими утечками данных и прерываниями работы.</span><span class="sxs-lookup"><span data-stu-id="f270c-131">Inconsistent deployment processes might result in security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="f270c-132">Изменения конфигурации и отсутствие требуемых обновлений могут привести к брешам в системе безопасности с последующими утечками данных и прерываниями работы.</span><span class="sxs-lookup"><span data-stu-id="f270c-132">Configuration drift or missed patches might result in unintended security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="f270c-133">Конфигурация может не требовать применения требований определенных Соглашений об уровне обслуживания или применения восстановления.</span><span class="sxs-lookup"><span data-stu-id="f270c-133">Configuration might not enforce the requirements of defined SLAs or committed recovery requirements.</span></span>
- <span data-ttu-id="f270c-134">Развернутые операционные системы или приложения могут не соответствовать требованиям ОС и требованиям по усилению безопасности приложения.</span><span class="sxs-lookup"><span data-stu-id="f270c-134">Deployed operating systems or applications might not meet OS and application hardening requirements.</span></span>
- <span data-ttu-id="f270c-135">Из-за того, что несколько команд работает в облаке, существует риск несогласованности.</span><span class="sxs-lookup"><span data-stu-id="f270c-135">There is a risk of inconsistency due to multiple teams working in the cloud.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="f270c-136">Эволюция положений политики</span><span class="sxs-lookup"><span data-stu-id="f270c-136">Evolution of the policy statements</span></span>

<span data-ttu-id="f270c-137">Описанные ниже изменения политики уменьшают новые риски и упрощают реализацию.</span><span class="sxs-lookup"><span data-stu-id="f270c-137">The following changes to policy will help mitigate the new risks and guide implementation.</span></span> <span data-ttu-id="f270c-138">Список выглядит длинным, но внедрять эти политики проще, чем кажется.</span><span class="sxs-lookup"><span data-stu-id="f270c-138">The list looks long, but the adoption of these policies may be easier than it would appear.</span></span>

1. <span data-ttu-id="f270c-139">Все развернутые ресурсы необходимо классифицировать по важности и типу данных.</span><span class="sxs-lookup"><span data-stu-id="f270c-139">All deployed assets must be categorized by criticality and data classification.</span></span> <span data-ttu-id="f270c-140">Перед развертыванием в облаке классификации должны быть проверены командой управления облачными решениями и владельцем приложения.</span><span class="sxs-lookup"><span data-stu-id="f270c-140">Classifications are to be reviewed by the Cloud Governance team and the application owner before deployment to the cloud.</span></span>
2. <span data-ttu-id="f270c-141">Подсети, содержащие критически важные приложения, должны быть защищены с помощью решения брандмауэра, которое может обнаруживать вторжения и реагировать на атаки.</span><span class="sxs-lookup"><span data-stu-id="f270c-141">Subnets containing mission-critical applications must be protected by a firewall solution capable of detecting intrusions and responding to attacks.</span></span>
3. <span data-ttu-id="f270c-142">Средства управления должны выполнять аудит и обеспечивать соблюдение требований сетевой конфигурации, определенных командой по базовой системе безопасности.</span><span class="sxs-lookup"><span data-stu-id="f270c-142">Governance tooling must audit and enforce network configuration requirements defined by the Security Baseline team.</span></span>
4. <span data-ttu-id="f270c-143">Средства управления должны проверять, что все ресурсы, связанные с критически важными приложениями или защищенными данными, включены в мониторинг для оптимизации и наблюдения за истощением ресурсов.</span><span class="sxs-lookup"><span data-stu-id="f270c-143">Governance tooling must validate that all assets related to mission-critical applications or protected data are included in monitoring for resource depletion and optimization.</span></span>
5. <span data-ttu-id="f270c-144">Средства управления должны проверять, что данные журнала соответствующего уровня собираются для всех критически важных приложений или защищенных данных.</span><span class="sxs-lookup"><span data-stu-id="f270c-144">Governance tooling must validate that the appropriate level of logging data is being collected for all mission-critical applications or protected data.</span></span>
6. <span data-ttu-id="f270c-145">Процесс управления должен проверять правильную реализацию резервного копирования, восстановления и соблюдения Соглашению об уровне обслуживания для критически важных приложений и защищенных данных.</span><span class="sxs-lookup"><span data-stu-id="f270c-145">Governance process must validate that backup, recovery, and SLA adherence are properly implemented for mission-critical applications and protected data.</span></span>
7. <span data-ttu-id="f270c-146">Средства управления должны ограничивать развертывание виртуальных машин, разрешая использование только утвержденных образов.</span><span class="sxs-lookup"><span data-stu-id="f270c-146">Governance tooling must limit virtual machine deployment to approved images only.</span></span>
8. <span data-ttu-id="f270c-147">Средства управления должны **предотвращать** автоматические обновления во всех развернутых ресурсах, поддерживающих критически важные приложения.</span><span class="sxs-lookup"><span data-stu-id="f270c-147">Governance tooling must enforce that automatic updates are **prevented** on all deployed assets that support mission-critical applications.</span></span> <span data-ttu-id="f270c-148">Нарушения должны быть рассмотрены с привлечением команд операционного управления и устранены в соответствии с политиками операций.</span><span class="sxs-lookup"><span data-stu-id="f270c-148">Violations must be reviewed with operational management teams and remediated in accordance with operations policies.</span></span> <span data-ttu-id="f270c-149">Ресурсы, которые не обновляются автоматически, необходимо включить в процессы, которые выполняет отдел ИТ-операций.</span><span class="sxs-lookup"><span data-stu-id="f270c-149">Assets that are not automatically updated must be included in processes owned by IT operations.</span></span>
9. <span data-ttu-id="f270c-150">Средства управления должны проверять теги, относящиеся к классификации затрат, уровню важности, Соглашений об уровне обслуживания, приложений и данных.</span><span class="sxs-lookup"><span data-stu-id="f270c-150">Governance tooling must validate tagging related to cost, criticality, SLA, application, and data classification.</span></span> <span data-ttu-id="f270c-151">Все значения должны совпадать с предустановленными значениями, управляемыми командой управления облачными решениями.</span><span class="sxs-lookup"><span data-stu-id="f270c-151">All values must align to predefined values managed by the Cloud Governance team.</span></span>
10. <span data-ttu-id="f270c-152">Процессы управления должны включать в себя аудит в точке развертывания, а также регулярный аудит через определенные периоды времени для обеспечения согласованности всех ресурсов.</span><span class="sxs-lookup"><span data-stu-id="f270c-152">Governance processes must include audits at the point of deployment and at regular cycles to ensure consistency across all assets.</span></span>
11. <span data-ttu-id="f270c-153">Тенденции и уязвимости, которые могут повлиять на облачные развертывания, должны регулярно проверяться командой по обеспечению безопасности для подготовки обновлений средств базовой системы безопасности, используемых в облаке.</span><span class="sxs-lookup"><span data-stu-id="f270c-153">Trends and exploits that could affect cloud deployments should be reviewed regularly by the security team to provide updates to Security Baseline tooling used in the cloud.</span></span>
12. <span data-ttu-id="f270c-154">До запуска в рабочей среде все критически важные приложения и защищенные данные должны быть добавлены в указанное решение для операционного мониторинга.</span><span class="sxs-lookup"><span data-stu-id="f270c-154">Prior to release into production, all mission-critical applications and protected data must be added to the designated operational monitoring solution.</span></span> <span data-ttu-id="f270c-155">Ресурсы, которые не могут быть обнаружены в выбранном средстве ИТ-операций, нельзя выпускать для использования в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="f270c-155">Assets that cannot be discovered by the chosen IT operations tooling cannot be released for production use.</span></span> <span data-ttu-id="f270c-156">Чтобы обеспечить возможность обнаружения ресурсов в будущих развертываниях, любые изменения, которые нужны для обеспечения такой возможности, должны быть внесены в соответствующие процессы развертывания.</span><span class="sxs-lookup"><span data-stu-id="f270c-156">Any changes required to make the assets discoverable must be made to the relevant deployment processes to ensure assets will be discoverable in future deployments.</span></span>
13. <span data-ttu-id="f270c-157">Команды операционного управления должны проверить размер обнаруженного ресурса, чтобы убедиться в соответствии ресурса требованиям к производительности.</span><span class="sxs-lookup"><span data-stu-id="f270c-157">Upon discovery, asset sizing is to be validated by operational management teams to validate that the asset meets performance requirements.</span></span>
14. <span data-ttu-id="f270c-158">Средства развертывания должны быть утверждены командой управления облачными решениями для обеспечения непрерывного управления развернутыми ресурсами.</span><span class="sxs-lookup"><span data-stu-id="f270c-158">Deployment tooling must be approved by the Cloud Governance team to ensure ongoing governance of deployed assets.</span></span>
15. <span data-ttu-id="f270c-159">Сценарии развертывания должны храниться в центральном репозитории, который доступен команде управления облачными решениями для периодической проверки и аудита.</span><span class="sxs-lookup"><span data-stu-id="f270c-159">Deployment scripts must be maintained in central repository accessible by the Cloud Governance team for periodic review and auditing.</span></span>
16. <span data-ttu-id="f270c-160">В рамках процессов проверки управления необходимо проверять, чтобы развернутые ресурсы были настроены в соответствии с Соглашением об уровне обслуживания и требованиями к восстановлению.</span><span class="sxs-lookup"><span data-stu-id="f270c-160">Governance review processes must validate that deployed assets are properly configured in alignment with SLA and recovery requirements.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="f270c-161">Эволюция рекомендаций</span><span class="sxs-lookup"><span data-stu-id="f270c-161">Evolution of the best practices</span></span>

<span data-ttu-id="f270c-162">В этом разделе статьи в проект MVP управления будут включены новые политики Azure и реализована служба "Управление затратами Azure".</span><span class="sxs-lookup"><span data-stu-id="f270c-162">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="f270c-163">Совокупно такие изменения проекта станут новыми положениями корпоративной политики.</span><span class="sxs-lookup"><span data-stu-id="f270c-163">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

<span data-ttu-id="f270c-164">Следуя этому вымышленному примеру, предполагается, что эволюция защищенных данных уже произошла.</span><span class="sxs-lookup"><span data-stu-id="f270c-164">Following the experience of this fictional example, it is assumed that the Protected Data evolution has already happened.</span></span> <span data-ttu-id="f270c-165">Исходя из этой рекомендации, ниже будут добавлены требования к мониторингу операций, подготавливающие подписку для критически важных приложений.</span><span class="sxs-lookup"><span data-stu-id="f270c-165">Building on that best practice, the following will add operational monitoring requirements, readying a subscription for mission-critical applications.</span></span>

<span data-ttu-id="f270c-166">**Подписка для корпоративных ИТ-отделов**. Добавьте следующие элементы в подписку корпоративных ИТ-отделов, которая выступает в качестве концентратора.</span><span class="sxs-lookup"><span data-stu-id="f270c-166">**Corporate IT Subscription**: Add the following to the Corporate IT subscription, which acts as a hub.</span></span>

1. <span data-ttu-id="f270c-167">В качестве внешней зависимости команде облачных операций нужно будет определить средство операционного мониторинга, средство обеспечения непрерывности бизнес-процессов и аварийного восстановления (BCDR) и средство автоматического исправления.</span><span class="sxs-lookup"><span data-stu-id="f270c-167">As an external dependency, the Cloud Operations team will need to define operational monitoring tooling, Business Continuity/Disaster Recovery (BCDR) tooling and automated remediation tooling.</span></span> <span data-ttu-id="f270c-168">Затем команда управления облачными решениями сможет поддерживать необходимые процессы обнаружения.</span><span class="sxs-lookup"><span data-stu-id="f270c-168">The Cloud Governance team can then support necessary discovery processes.</span></span>
    1. <span data-ttu-id="f270c-169">В этом варианте использования команда облачных операций выбирает Azure Monitor в качестве основного средства мониторинга критически важных приложений.</span><span class="sxs-lookup"><span data-stu-id="f270c-169">In this use case, the Cloud Operations team chose Azure Monitor as the primary tool for monitoring mission-critical applications.</span></span>
    2. <span data-ttu-id="f270c-170">Команда также выбрала Azure Site Recovery в качестве основного средства BCDR.</span><span class="sxs-lookup"><span data-stu-id="f270c-170">The team also chose Azure Site Recovery as the primary BCDR tooling.</span></span>
2. <span data-ttu-id="f270c-171">Реализация Azure Site Recovery.</span><span class="sxs-lookup"><span data-stu-id="f270c-171">Azure Site Recovery implementation</span></span>
    1. <span data-ttu-id="f270c-172">Определите и разверните хранилище Azure для резервного копирования и восстановления.</span><span class="sxs-lookup"><span data-stu-id="f270c-172">Define and deploy Azure Vault for backup and recovery processes</span></span>
    2. <span data-ttu-id="f270c-173">Создайте шаблон управления ресурсами Azure для создания хранилища в каждой подписке.</span><span class="sxs-lookup"><span data-stu-id="f270c-173">Create an Azure Resource Management template for creation of a vault in each subscription</span></span>
3. <span data-ttu-id="f270c-174">Реализация Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="f270c-174">Azure Monitor implementation</span></span>
    1. <span data-ttu-id="f270c-175">После определения критически важной подписки можно создать рабочую область Log Analytics с помощью PowerShell.</span><span class="sxs-lookup"><span data-stu-id="f270c-175">Once a mission-critical subscription is identified, a log analytics workspace can be created using PowerShell.</span></span> <span data-ttu-id="f270c-176">Это процесс подготовки к развертыванию.</span><span class="sxs-lookup"><span data-stu-id="f270c-176">This is a pre-deployment process.</span></span>

<span data-ttu-id="f270c-177">**Отдельная подписка для внедрения облака**. Ниже приведены действия, которые помогут обеспечить возможность обнаружения каждой подписки с помощью решения мониторинга и ее готовности к включению в операции BCDR.</span><span class="sxs-lookup"><span data-stu-id="f270c-177">**Individual cloud adoption subscription**: The following will ensure that each subscription is discoverable by the monitoring solution and ready to be included in BCDR practices.</span></span>

1. <span data-ttu-id="f270c-178">Служба "Политика Azure" для критически-важных узлов.</span><span class="sxs-lookup"><span data-stu-id="f270c-178">Azure Policy for mission-critical nodes</span></span>
    1. <span data-ttu-id="f270c-179">Выполняйте аудит и применяйте только стандартные роли.</span><span class="sxs-lookup"><span data-stu-id="f270c-179">Audit and enforce use of standard roles only.</span></span>
    2. <span data-ttu-id="f270c-180">Выполняйте аудит и обеспечьте шифрование всех учетных записей хранения.</span><span class="sxs-lookup"><span data-stu-id="f270c-180">Audit and enforce application of encryption for all storage accounts.</span></span>
    3. <span data-ttu-id="f270c-181">Выполняйте аудит и обеспечивайте использование утвержденного набора подсети и виртуальной сети для каждого сетевого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="f270c-181">Audit and enforce use of approved network subnet and VNet per network interface.</span></span>
    4. <span data-ttu-id="f270c-182">Выполняйте аудит и применяйте ограничения определенных пользователем таблиц маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="f270c-182">Audit and enforce the limitation of user-defined routing tables.</span></span>
    5. <span data-ttu-id="f270c-183">Выполняйте аудит и обеспечивайте развертывание агентов Log Analytics для виртуальных машин Windows и Linux.</span><span class="sxs-lookup"><span data-stu-id="f270c-183">Audit and enforce the deployment of Log Analytics agents for Windows and Linux virtual machines.</span></span>
2. <span data-ttu-id="f270c-184">Схема Azure.</span><span class="sxs-lookup"><span data-stu-id="f270c-184">Azure blueprint</span></span>
    1. <span data-ttu-id="f270c-185">Создайте схему Azure с именем `mission-critical-workloads-and-protected-data`.</span><span class="sxs-lookup"><span data-stu-id="f270c-185">Create a blueprint named `mission-critical-workloads-and-protected-data`.</span></span> <span data-ttu-id="f270c-186">Эта схема будет применять ресурсы в дополнение к схеме защищенных данных.</span><span class="sxs-lookup"><span data-stu-id="f270c-186">This blueprint will apply assets in addition to the protected data blueprint.</span></span>
    2. <span data-ttu-id="f270c-187">Добавьте в схему новые политики Azure.</span><span class="sxs-lookup"><span data-stu-id="f270c-187">Add the new Azure policies to the blueprint.</span></span>
    3. <span data-ttu-id="f270c-188">Применяйте схему к любой подписке, которая должна размещать критически важное приложение.</span><span class="sxs-lookup"><span data-stu-id="f270c-188">Apply the blueprint to any subscription that is expected to host a mission-critical application.</span></span>

## <a name="conclusion"></a><span data-ttu-id="f270c-189">Заключение</span><span class="sxs-lookup"><span data-stu-id="f270c-189">Conclusion</span></span>

<span data-ttu-id="f270c-190">Добавление этих процессов и изменений в MVP управления позволяет снизить множество рисков, связанных с управлением ресурсом.</span><span class="sxs-lookup"><span data-stu-id="f270c-190">Adding these processes and changes to the governance MVP helps mitigate many of the risks associated with resource governance.</span></span> <span data-ttu-id="f270c-191">Вместе они добавляют элементы управления восстановлением, масштабированием и мониторингом, необходимые для расширения возможностей операций с использованием облака.</span><span class="sxs-lookup"><span data-stu-id="f270c-191">Together, they add the recovery, sizing, and monitoring controls necessary to empower cloud-aware operations.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f270c-192">Дополнительная информация</span><span class="sxs-lookup"><span data-stu-id="f270c-192">Next steps</span></span>

<span data-ttu-id="f270c-193">Так как внедрение облака продолжает развиваться и предоставлять дополнительные бизнес-преимущества, механизмы управления рисками и облачными операциями также нужно совершенствовать.</span><span class="sxs-lookup"><span data-stu-id="f270c-193">As cloud adoption continues to evolve and deliver additional business value, the risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="f270c-194">Для рассматриваемой вымышленной компании следующий переломный этап настанет, когда в облаке будет развернуто более 1000 ресурсов или расходы превысят сумму в 10 000 долл. США в месяц.</span><span class="sxs-lookup"><span data-stu-id="f270c-194">For the fictional company in this journey, the next trigger is when the scale of deployment exceeds 1,000 assets to the cloud or monthly spending exceeds $10,000 USD per month.</span></span> <span data-ttu-id="f270c-195">На этом этапе команде управления облачными решениями нужно добавить элементы управления затратами.</span><span class="sxs-lookup"><span data-stu-id="f270c-195">At this point, the Cloud Governance team adds Cost Management controls.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f270c-196">Эволюция управления затратами</span><span class="sxs-lookup"><span data-stu-id="f270c-196">Cost Management evolution</span></span>](./cost-management-evolution.md)