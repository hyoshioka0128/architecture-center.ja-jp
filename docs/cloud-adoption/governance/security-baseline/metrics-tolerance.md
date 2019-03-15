---
title: CAF:セキュリティ ベースラインのメトリック、インジケーター、およびリスク許容度
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: セキュリティ ベースラインのメトリック、インジケーター、およびリスク許容度
author: BrianBlanchard
ms.openlocfilehash: 30deafca59b2e09c78432ad3b59d328fb27a1e2c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901822"
---
# <a name="security-baseline-metrics-indicators-and-risk-tolerance"></a><span data-ttu-id="99dbf-103">セキュリティ ベースラインのメトリック、インジケーター、およびリスク許容度</span><span class="sxs-lookup"><span data-stu-id="99dbf-103">Security Baseline metrics, indicators, and risk tolerance</span></span>

<span data-ttu-id="99dbf-104">この記事は、セキュリティ ベースラインに関連したビジネス リスク許容度の数値化を支援することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="99dbf-104">This article is intended to help you quantify business risk tolerance as it relates to Security Baseline.</span></span> <span data-ttu-id="99dbf-105">メトリックとインジケーターを定義すると、セキュリティ ベースライン規範の成熟度への投資を行うためのビジネス ケースの作成に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="99dbf-105">Defining metrics and indicators helps you create a business case for making an investment in maturing the Security Baseline discipline.</span></span>

## <a name="metrics"></a><span data-ttu-id="99dbf-106">メトリック</span><span class="sxs-lookup"><span data-stu-id="99dbf-106">Metrics</span></span>

<span data-ttu-id="99dbf-107">セキュリティ ベースラインは、一般に、クラウド デプロイの潜在的脆弱性を特定することに焦点を合わせています。</span><span class="sxs-lookup"><span data-stu-id="99dbf-107">Security Baseline generally focuses on identifying potential vulnerabilities in your cloud deployments.</span></span> <span data-ttu-id="99dbf-108">リスク分析の一部として、セキュリティ環境に関連したデータを収集して、直面しているリスクの程度、および計画されているクラウド デプロイにとってのセキュリティ ベースラインへの投資の重要性を判別できます。</span><span class="sxs-lookup"><span data-stu-id="99dbf-108">As part of your risk analysis you'll want to gather data related to your security environment to determine how much risk you face, and how important investment in Security Baseline governance is to your planned cloud deployments.</span></span>

<span data-ttu-id="99dbf-109">すべての組織には、異なるセキュリティ環境と要件、および異なるセキュリティ データの潜在ソースがあります。</span><span class="sxs-lookup"><span data-stu-id="99dbf-109">Every organization has different security environments and requirements and different potential sources of security data.</span></span> <span data-ttu-id="99dbf-110">セキュリティ ベースライン規範内のリスク許容度の評価に役立てるために収集する必要がある有用なメトリックの例を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="99dbf-110">The following are examples of useful metrics that you should gather to help evaluate risk tolerance within the Security Baseline discipline:</span></span>

- <span data-ttu-id="99dbf-111">**データ分類**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-111">**Data classification**.</span></span> <span data-ttu-id="99dbf-112">組織のプライバシー、コンプライアンス、またはビジネス インパクトの基準に従って分類されていない、クラウドに格納されたデータとサービスの数。</span><span class="sxs-lookup"><span data-stu-id="99dbf-112">Number of cloud-stored data and services that are unclassified according to on your organization's privacy, compliance, or business impact standards.</span></span>
- <span data-ttu-id="99dbf-113">**機密性の高いデータ ストアの数** - 機密データが含まれていて、保護する必要があるストレージ エンドポイントまたはデータベースの数。</span><span class="sxs-lookup"><span data-stu-id="99dbf-113">**Number of sensitive data stores** - Number of storage end points or databases that contain sensitive data and should be protected.</span></span>
- <span data-ttu-id="99dbf-114">**暗号化されていないデータ ストアの数** - 暗号化されていない機密性の高いデータ ストアの数。</span><span class="sxs-lookup"><span data-stu-id="99dbf-114">**Number of unencrypted data stores** - Number of sensitive data stores that are not encrypted.</span></span>
- <span data-ttu-id="99dbf-115">**攻撃対象領域**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-115">**Attack surface**.</span></span> <span data-ttu-id="99dbf-116">クラウドでホストされるデータ ソース、サービス、およびアプリケーションの合計数。</span><span class="sxs-lookup"><span data-stu-id="99dbf-116">How many total data sources, services, and applications will be cloud-hosted.</span></span> <span data-ttu-id="99dbf-117">それらのデータ ソースの何パーセントが機密情報に分類されるか。</span><span class="sxs-lookup"><span data-stu-id="99dbf-117">What percentage of these data sources are classified as sensitive?</span></span> <span data-ttu-id="99dbf-118">それらのアプリケーションとサービスのうち何パーセントがミッション クリティカルか。</span><span class="sxs-lookup"><span data-stu-id="99dbf-118">What percentage of these applications and services are mission-critical?</span></span>
- <span data-ttu-id="99dbf-119">**対象となる基準**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-119">**Covered Standards**.</span></span> <span data-ttu-id="99dbf-120">セキュリティ チームによって定義されたセキュリティ基準の数。</span><span class="sxs-lookup"><span data-stu-id="99dbf-120">Number of security standards defined by the Security team.</span></span>
- <span data-ttu-id="99dbf-121">**対象となるリソース**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-121">**Covered Resources**.</span></span> <span data-ttu-id="99dbf-122">セキュリティ基準の対象となるデプロイされた資産。</span><span class="sxs-lookup"><span data-stu-id="99dbf-122">Deployed assets that are covered by security standards.</span></span>
- <span data-ttu-id="99dbf-123">**全体的な基準のコンプライアンス**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-123">**Overall Standards Compliance**.</span></span> <span data-ttu-id="99dbf-124">セキュリティ基準へのコンプライアンス準拠の比率。</span><span class="sxs-lookup"><span data-stu-id="99dbf-124">Ratio of compliance adherence to security standards.</span></span>
- <span data-ttu-id="99dbf-125">**重大度別の攻撃**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-125">**Attacks by Severity**.</span></span> <span data-ttu-id="99dbf-126">分散型サービス拒否 (DDoS) 攻撃などを通じて、クラウドでホストされるサービスを妨害する組織的攻撃がインフラストラクチャに対して試行された回数。</span><span class="sxs-lookup"><span data-stu-id="99dbf-126">How many coordinated attempts to disrupt your cloud-hosted services, such as through Distributed Denial of Service (DDoS) attacks, does your infrastructure experience?</span></span> <span data-ttu-id="99dbf-127">それらの攻撃の大きさと重大度。</span><span class="sxs-lookup"><span data-stu-id="99dbf-127">What is the size and severity of these attacks?</span></span>
- <span data-ttu-id="99dbf-128">**マルウェア対策**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-128">**Malware protection**.</span></span> <span data-ttu-id="99dbf-129">必要なすべてのマルウェア対策、ファイアウォール、またはその他のセキュリティ ソフトウェアがインストールされているデプロイ済み仮想マシン (VM) の割合。</span><span class="sxs-lookup"><span data-stu-id="99dbf-129">Percentage of deployed virtual machines (VMs) that have all required anti-malware, firewall, or other security software installed.</span></span>
- <span data-ttu-id="99dbf-130">**修正プログラムの待ち時間**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-130">**Patch latency**.</span></span> <span data-ttu-id="99dbf-131">VM で OS とソフトウェアの修正プログラムが適用されてからの経過時間。</span><span class="sxs-lookup"><span data-stu-id="99dbf-131">How long has it been since VMs have had OS and software patches applied.</span></span>
- <span data-ttu-id="99dbf-132">**セキュリティの正常性に関する推奨事項**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-132">**Security health recommendations**.</span></span> <span data-ttu-id="99dbf-133">デプロイ済みリソースの正常性の基準を解決するためのセキュリティ ソフトウェアの推奨事項の数 (重大度別)。</span><span class="sxs-lookup"><span data-stu-id="99dbf-133">Number of security software recommendations for resolving health standards for deployed resources, organized by severity.</span></span>

## <a name="risk-tolerance-indicators"></a><span data-ttu-id="99dbf-134">リスク許容度インジケーター</span><span class="sxs-lookup"><span data-stu-id="99dbf-134">Risk tolerance indicators</span></span>

<span data-ttu-id="99dbf-135">クラウド プラットフォームには、小規模のデプロイ チームが、大規模な追加計画を行わずに基本的なセキュリティ設定を構成できるようにする、機能のベースライン セットが用意されています。</span><span class="sxs-lookup"><span data-stu-id="99dbf-135">Cloud platforms provide a baseline set of features that enable small deployment teams to configure basic security settings without extensive additional planning.</span></span> <span data-ttu-id="99dbf-136">その結果、機密データを含まない小規模な開発/テストまたは実験用の最初のワークロードは、相対的に低いリスク レベルを表し、正式なセキュリティ ベースライン ポリシーに関して多くを必要としない可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="99dbf-136">As a result, small Dev/Test or experimental first workloads that do not include sensitive data represent a relatively low level of risk, and will likely not need much in the way of formal Security Baseline policy.</span></span> <span data-ttu-id="99dbf-137">ただし、重要なデータやミッション クリティカルな機能がクラウドに移動されるとすぐにセキュリティ リスクが増加し、それらのリスクの許容度は急速に縮小します。</span><span class="sxs-lookup"><span data-stu-id="99dbf-137">However, as soon as important data or mission-critical functionality is moved to the cloud, security risks increase, while tolerance for those risks diminishes rapidly.</span></span> <span data-ttu-id="99dbf-138">クラウドにデプロイされるデータと機能が増えるほど、セキュリティ ベースライン規範への投資を増加する必要性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="99dbf-138">As more of your data and functionality is deployed to the cloud, the more likely you need an increased investment in the Security Baseline discipline.</span></span>

<span data-ttu-id="99dbf-139">クラウド導入の初期段階で、IT セキュリティ チームおよびビジネス利害関係者と協力して、セキュリティに関連した[ビジネス リスク](business-risks.md)を特定し、セキュリティ リスク許容度の受け入れ可能なベースラインを判断してください。</span><span class="sxs-lookup"><span data-stu-id="99dbf-139">In the early stages of cloud adoption, work with your IT security team and business stakeholders to identify [business risks](business-risks.md) related to security, then determine an acceptable baseline for security risk tolerance.</span></span> <span data-ttu-id="99dbf-140">CAF のこのセクションでは例を提供しますが、お客様の会社またはデプロイの詳細なリスクおよびベースラインは異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="99dbf-140">This section of the CAF provides examples, but the detailed risks and baselines for your company or deployments may be different.</span></span>

<span data-ttu-id="99dbf-141">ベースラインが決まったら、特定したリスクでの許容できない増加を表す最小ベンチマークを確立します。</span><span class="sxs-lookup"><span data-stu-id="99dbf-141">Once you have a baseline, establish minimum benchmarks representing an unacceptable increase in your identified risks.</span></span> <span data-ttu-id="99dbf-142">これらのベンチマークは、それらのリスクを軽減するためのアクションが必要となるタイミングを知るためのトリガーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="99dbf-142">These benchmarks act as triggers for when you need to take action to mitigate these risks.</span></span> <span data-ttu-id="99dbf-143">前述のようなセキュリティ メトリックがセキュリティ ベースライン規範への投資の増加をどのように正当化できるかについてのいくつかの例を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="99dbf-143">The following are a few examples of how security metrics, such as those discussed above, can justify an increased investment in the Security Baseline discipline.</span></span>

- <span data-ttu-id="99dbf-144">**ミッション クリティカルなワークロードのトリガー**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-144">**Mission-critical workloads trigger**.</span></span> <span data-ttu-id="99dbf-145">ミッション クリティカルなワークロードをクラウドにデプロイする企業は、潜在的なサービスの中断や機密データの開示を防ぐためにセキュリティ ベースライン規範に投資する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99dbf-145">A company deploying mission-critical workloads to the cloud should invest in the Security Baseline discipline to prevent potential disruption of service or sensitive data exposure.</span></span>
- <span data-ttu-id="99dbf-146">**保護されたデータのトリガー**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-146">**Protected data trigger**.</span></span> <span data-ttu-id="99dbf-147">機密データや個人データに分類できるデータ、またはそれ以外の規制対象となるデータをクラウドでホストしている企業。</span><span class="sxs-lookup"><span data-stu-id="99dbf-147">A company hosting data on the cloud that can be classified as confidential, private, or otherwise subject to regulatory concerns.</span></span> <span data-ttu-id="99dbf-148">そのデータが失われたり、公開されたり、盗まれたりしないように、セキュリティ ベースライン規範が必要です。</span><span class="sxs-lookup"><span data-stu-id="99dbf-148">They need a Security Baseline discipline to ensure that this data is not subject to loss, exposure, or theft.</span></span>
- <span data-ttu-id="99dbf-149">**外部からの攻撃のトリガー**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-149">**External attacks trigger**.</span></span> <span data-ttu-id="99dbf-150">ネットワーク インフラストラクチャに対する重大な攻撃を 1 か月につき X 回経験している企業は、セキュリティ ベースライン規範を活用できます。</span><span class="sxs-lookup"><span data-stu-id="99dbf-150">A company that experiences serious attacks against their network infrastructure X times per month could benefit from the Security Baseline discipline.</span></span>  
- <span data-ttu-id="99dbf-151">**基準のコンプライアンスのトリガー**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-151">**Standards compliance trigger**.</span></span> <span data-ttu-id="99dbf-152">リソースの X% がセキュリティ基準のコンプライアンスを順守していない企業は、IT インフラストラクチャ全体にわたって基準が一貫して適用されるようにするために、セキュリティ ベースライン規範に投資する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99dbf-152">A company with more than X% of resources out of security standards compliance should invest in the Security Baseline discipline to ensure standards are applied consistently across your IT infrastructure.</span></span>
- <span data-ttu-id="99dbf-153">**クラウド資産サイズのトリガー**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-153">**Cloud estate size trigger**.</span></span> <span data-ttu-id="99dbf-154">X を超える数のアプリケーション、サービス、またはデータ ソースをホストしている企業。</span><span class="sxs-lookup"><span data-stu-id="99dbf-154">A company hosting more than X number of applications, services, or data sources.</span></span> <span data-ttu-id="99dbf-155">大規模なクラウド デプロイは、攻撃対象領域全体が、不正アクセスやその他の外部の脅威から適切に保護されるようにするために、セキュリティ ベースライン規範への投資を活用できます。</span><span class="sxs-lookup"><span data-stu-id="99dbf-155">Large cloud deployments can benefit from investment in the Security Baseline discipline to ensure that their overall attack surface is properly protected against unauthorized access or other external threats.</span></span>
- <span data-ttu-id="99dbf-156">**セキュリティ ソフトウェア コンプライアンスのトリガー**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-156">**Security software compliance trigger**.</span></span> <span data-ttu-id="99dbf-157">すべての必要なセキュリティ ソフトウェアがインストールされているデプロイ済み仮想マシンが X % 未満の企業。</span><span class="sxs-lookup"><span data-stu-id="99dbf-157">A company where less than X% of deployed virtual machines have all required security software installed.</span></span> <span data-ttu-id="99dbf-158">すべてのソフトウェアで一貫してソフトウェアがインストールされるようにするために、セキュリティ ベースライン規範を使用できます。</span><span class="sxs-lookup"><span data-stu-id="99dbf-158">A Security Baseline discipline can be used to ensure software is installed consistently on all software.</span></span>
- <span data-ttu-id="99dbf-159">**修正プログラムのトリガー**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-159">**Patching trigger**.</span></span> <span data-ttu-id="99dbf-160">デプロイ済み仮想マシンまたはサービスに、OS やソフトウェアの修正プログラムが過去 X 日間適用されていない企業。</span><span class="sxs-lookup"><span data-stu-id="99dbf-160">A company where deployed virtual machines or services where OS or software patches have not been applied in the last X number of days.</span></span> <span data-ttu-id="99dbf-161">必要なスケジュール内で修正プログラムが最新状態になるようにするためにセキュリティ ベースライン規範を使用できます。</span><span class="sxs-lookup"><span data-stu-id="99dbf-161">A Security Baseline discipline can be used to ensure patching is kept up-to-date within a required schedule.</span></span>
- <span data-ttu-id="99dbf-162">**セキュリティ重視**。</span><span class="sxs-lookup"><span data-stu-id="99dbf-162">**Security focused**.</span></span> <span data-ttu-id="99dbf-163">一部の企業には、テストや実験的なワークロードについても、強力なセキュリティおよびデータ機密性の要件があります。</span><span class="sxs-lookup"><span data-stu-id="99dbf-163">Some companies will have strong security and data confidentiality requirements even for test and experimental workloads.</span></span> <span data-ttu-id="99dbf-164">それらの企業は、デプロイを開始する前に、セキュリティ ベースライン規範に投資する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99dbf-164">These companies will need to invest in the Security Baseline discipline before any deployments can begin.</span></span>

<span data-ttu-id="99dbf-165">リスク許容度とセキュリティ ベースライン規範への投資レベルを測定するために使用する正確なメトリックとトリガーは組織固有のものとなりますが、上記の例は、クラウド ガバナンス チーム内での協議を行うための有用な開始点として機能するはずです。</span><span class="sxs-lookup"><span data-stu-id="99dbf-165">The exact metrics and triggers you use to gauge risk tolerance and the level of investment in the Security Baseline discipline will be specific to your organization, but the examples above should serve as a useful base for discussion within your Cloud Governance team.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="99dbf-166">次の手順</span><span class="sxs-lookup"><span data-stu-id="99dbf-166">Next steps</span></span>

<span data-ttu-id="99dbf-167">[クラウド管理テンプレート](./template.md)を使用して、現在のクラウド導入計画に合ったメトリックと許容度のインジケーターを文書化します。</span><span class="sxs-lookup"><span data-stu-id="99dbf-167">Using the [Cloud Management template](./template.md), document metrics and tolerance indicators that align to the current cloud adoption plan.</span></span>

<span data-ttu-id="99dbf-168">リスクと許容度に基づいて、セキュリティ ベースライン ポリシーの順守を管理および伝達するためのプロセスを確立します。</span><span class="sxs-lookup"><span data-stu-id="99dbf-168">Building on risks and tolerance, establish a process for governing and communicating Security Baseline policy adherence.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="99dbf-169">ポリシーのコンプライアンスのプロセスを確立する</span><span class="sxs-lookup"><span data-stu-id="99dbf-169">Establish policy compliance processes</span></span>](compliance-processes.md)