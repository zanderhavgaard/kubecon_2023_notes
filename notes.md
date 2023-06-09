# ArgoCon

## Keynotes

Argo is currently the CNCF project with the 3rd highest momentum (with k8s as #1 and Opentelemetry as #2)

Argo became a graduated CNCF project in 2022

Close to 500 industry leader companies as self-reported adopters of argo

Devs <3 argocd

Argo has been security audited.

Strong community of both open-source contributors and vendors backing the argo project

### Argo and gitops in a managed services world

"platform engineering"

What is a managed service?
... like an IDP (for devs)

Everything is being bundled in under PE

Gitops is what enables all of automation that enables PE

... You must talk to stakeholders ...

### Argo project: the cloud native key ingredient

Free argo courses:

- https://academy.akuity.io
- https://academy.akuity.io/courses/gitops-argocd-intro

Kargo - new open source project from akuity

https://github.com/akuity/kargo
https://docs-kargo-akuity-io.netlify.app/

Helps with promotion of workloads across environments

## Leveraging Argo workflowtemplates within a data platform

https://colocatedeventseu2023.sched.com/event/1Jo9d/leveraging-argo-workflowtemplates-within-a-data-platform-jp-zivalich-pipekit-yao-lin-bloomberg

## Managing artifacts at scale for ci and data processing

https://colocatedeventseu2023.sched.com/event/1Jo9j/managing-artifacts-at-scale-for-ci-and-data-processing-caelan-urquhart-pipekit-julie-vogelman-intuit

## Lightning talk: telefonistka

https://colocatedeventseu2023.sched.com/event/1Jo9m/cl-lightning-talk-telefonistka-safe-and-controlled-gitops-promotion-across-environmentsfailure-domains-as-code-oded-ben-ozer-wayfair

https://github.com/wayfair-incubator/telefonistka

You have many clusters, and you have a gitops repo with envs per cluster ...

Put stuff in folders, push, works, create pr, review, merge ... repeat n times for n envs ...

telefonistka can automate creating PRs to promote changes from env to env ... by copying files from folder to folder - genius ...

can spot drift between envs

"RY is the new DRY"

## Lightning talk: using kustomize krm function to enhance argocd application deployments

https://colocatedeventseu2023.sched.com/event/1Jo9s/cl-lightning-talk-using-kustomize-krm-functions-to-enhance-argo-cd-application-deployments-jan-heylen-nokia

## Lightning talk: merging best practices with reality: one repo for code and helm

https://colocatedeventseu2023.sched.com/event/1Jo9y/cl-lightning-talk-merging-best-practices-with-reality-one-repository-for-code-and-helm-omer-kahani-snyk

application code in one repo
helm code in seperate repo
... is not alwasy optimal

say, you add an env var, and then want to deploy it with helm ... you then also need to change the helm chart

---

## lunch

---

## argocd as the enginge of a brownfield migration to k8s

https://colocatedeventseu2023.sched.com/event/1JoA1/argocd-as-the-engine-of-a-brownfield-migration-to-kubernetes-john-keates-wehkamp

they did migration from mesos to k8s (eks)

requirements? what features to you really need?

talking to people is hard and scary

migration requirements:

- dev delivers ci result (container image)
- platform returns reachable microservice
- don't make anything worse
- don't make everyone rewrite their code

complicated ingress setup ...

they had all of their env config stuff in one repo, this was not great ...

they split that into 3:

- things that are somewhat specialized, but shared
  - k8s, argocd
- stuff that is the same for everything
  - control-plane for control-plane
  - argo of argos ?!
  - many clusters
- application-specific things

For the platform team, every env is production

use Argo applicationset matrices for defining workloads, across envs

they went from terraform to crossplane and then back to terraform

- crossplane providers installing hundres of crds created sync problems with argo, this is to be fixed soon, by allowing only installing the needed crds.

## how to preview and diff your argocd deployments

https://colocatedeventseu2023.sched.com/event/1JoA7/how-to-preview-and-diff-your-argo-cd-deployments-kostis-kapelonis-codefresh

kustomize overlays can be confusing ... many layers of kustomize

we only really care about the final manifest

helm is just as bad, trying to figure out what changes to helm templates actually result in ...

argocd cli has a diff command to diff resources in the cluster with something else

- use with `--local` option to diff against a local file
- great for development

pre-rendering manifests and storing the rendered output in git

- but then you need two git repos, one with templates/not-rendered, and one with not-rendered
- you need automation to render the templates in the first into the second
- makes changes complicated - you need to make changes two places, with different implications, makes PR/review more complicated, probably needs some rules/culture of changing first repo before second

render manifests on-the-fly

- use changes in kustomize/helm to render the actual changes in downstream uses, and show those diffs on the PRs of the changes

https://github.com/kostis-codefresh/manifest-refactoring

## nextgen argo: elevating CD with easy to use plugins

https://colocatedeventseu2023.sched.com/event/1JoAD/nextgen-argo-elevating-continuous-delivery-with-easy-to-use-plugins-michael-crenshaw-sai-sindhu-chakradari-intuit

configmanagement plugins + improvements to argocd

They run about 40 argo instances and 20000 (?) applications

repo-server with plugin?

why use a configmanamentplugin (CMP / plugin)

- for example: use unsupported templating tool
- trigger side-effects, events

new feature for dynamic parameters for CMPs

parameters can be "announced" by a CMP

## revolutionizing CD: how databricks integrates argorollouts to achieve zero downtime releases

https://colocatedeventseu2023.sched.com/event/1JoAM/revolutionizing-continuous-deployment-how-databricks-integrates-argorollouts-to-achieve-zero-downtime-releases-rohit-agrawal-gavin-kliger-databricks-inc

goals with rollouts:

- smallest blast radius
- rollback as quickly as possible

standard k8s rollouts are not enough

- scale from one replicaset to another
- human needs to start rollback

what is a rollout?
the argo kind `Rollout` is a drop-in replacement for a kind `Deployment`, which adds:

- staged released with pauses
- background health checks
- this is enabled by the `strategy` key
  - everything else stays the same as `Deployment`

to enable the strategy, we use an `AnalysisTemplate`, which could for example monitor a prometheus metric and rollback based on a bad value over a given amount of time.

rolling out an update using a `Rollout` uses a canary deployments, with a canary replicaset

managing health checks:

- create default library of parameterized health checks that works for your services
- make it easy for teams to add their own health checks for their workloads
- implement a good feedback loop

new feature are dry-run checks, which can be used when implementing new health checks:

- when doing a rollout, the `wet-run` checks determine whether the rollout is a success
- devs can add `dry-run` checks, and receive feedback from real rollouts, but without impacting the result of them

argo rollouts can be integrated with istio, for weight-based routing to canary rollouts

best practice is make rollouts as `atomic` as possible

## scaling argo security and multi-tenancy in aws eks at new york times

https://colocatedeventseu2023.sched.com/event/1JoAP/scaling-argo-security-and-multi-tenancy-in-aws-eks-at-the-new-york-times-david-grizzanti-luke-philips-the-new-york-times

building an IDP

- devs have to do many things as things "shift left"
- try to help by building a "platform"

solution:

- have few shared, multi-tenant clusters: dev, stage, prod
- feature teams deploy to the clusters
- platform team manages/operates clusters

CD with argo

multi-tenant argo architectures:

- each clusters has it's own argo instance
  - each cluster works on its own
  - easy isolation and security concersn
- managment cluster / argo
  - one argo manages many clusters
  - easy to manage
  - difficult security management
- argo per group (tenant?)
  - argo instances run in management cluster
  - manages workloads in downstream clusters

they chose to go with management clusters with seperate argo instances for sandbox envs

challenges with scaling the architecture:

- recommend using HA and autoscaling from the helm chart

## gitops me some of that! managing hundres of cluster with argocd

https://colocatedeventseu2023.sched.com/event/1JoAV/gitops-me-some-of-that-managing-hundreds-of-clusters-with-argo-cd-mike-tougeron-adobe-inc

# KubeCon Day 1

## A CI/CD platform in the palm of your hand

https://kccnceu2023.sched.com/event/1HyVW/a-cicd-platform-in-the-palm-of-your-hand-claudia-beresford-weaveworks?iframe=no&w=100%&sidebar=yes&bg=no

introduction to microvms:

- firecracker
- middle ground between normal vms and containers
- quick start time < 125ms
- rapid deployment

what is liquid metal?

- https://github.com/weaveworks-liquidmetal
- set of tools to provision k8s on microvms
- maintained by weaveworks
- flintlock
  - tools to provision and manage microvms for k8s on bare metal
- clusterapi for provisioning clusters
- containerd for provisioning components (kernel, os, etc.)

use cases:

- edge computing
- low resource system
- bare metal
- ci self-hosted runners

Proof-of-concept CI/CD platform:

- self hosted github actions
- liquid metal cluster
- on dedicated ad-hoc microvms
- actions runner controller:
  - kube controller
  - pool of runners as pods
- https://github.com/weaveworks-liquidmetal/microvm-action-runner

benefits (vs. traditional ci platforms)

- more performant
  - higher utilization of machines
- more security
  - container builds, but with own kernel
  - no risk of cross-pollution as everthing runs in ephemeral vm
- can run things in kernel space, eg. eBPF
- fast builds
- possible on lower end hardware, lower power hardware, hence lower cost

## How we securely scaled multi-tenancy with vcluster, crossplane and argocd

https://kccnceu2023.sched.com/event/1HyYu/how-we-securely-scaled-multi-tenancy-with-vcluster-crossplane-and-argo-cd-ilia-medvedev-kostis-kapelonis-codefresh?iframe=no&w=100%&sidebar=yes&bg=no

Goal was to build a SaaS argocd solution, where each customer gets an argo instance

argo architectures:

- single cluster with many argo instances
  - namespace bases, argocd instance in each namespace
  - easy to manage
  - same cluster, same crd, noisy neighbor problems
- customer per cluster
  - total isolation
  - slow to setup
  - much more complicated to operate
- vcluster per customer, with argocd instance

Implementation:

- using vcluster
- crossplane

## Choose your own adventure: the treachorous trek to development

https://kccnceu2023.sched.com/event/1HyWm/choose-your-own-adventure-the-treacherous-trek-to-development-whitney-lee-vmware-viktor-farcic-upbound?iframe=no&w=100%&sidebar=yes&bg=no

https://www.youtube.com/watch?v=GDInFocQJTU

## state of backstage in 2023

https://kccnceu2023.sched.com/event/1HyUD/the-state-of-backstage-in-2023-ben-lambert-patrik-oldsberg-spotify?iframe=no&w=100%&sidebar=yes&bg=no

backstage is 3 years old!

now an incubating cncf project

more than 100 opensource plugins

new governance:

- project areas
- contributor ladder
  - core maintainer
  - project area maintainer
  - plugin maintainer
  - organization member
  - contributor
  - the more you contribute, the higher on the ladder you can climb
  - based on standard cncf template for governing contributions

new project areas

why software templates?
- creation of new software components
- every company is different
- best practices built in

the next priority on the backstage roadmap is doing declarative (no code) plugin integration
- top of roadmap
- plugin installation without typescript

## Operate multi-tenancy service mesh with argocd in production

https://kccnceu2023.sched.com/event/1Hyd1/operate-multi-tenancy-service-mesh-with-argocd-in-production-lin-sun-soloio-faseela-k-ericsson-software-technology?iframe=no&w=100%&sidebar=yes&bg=no

envoy proxy is not great for multi-tenancy

service mesh evolution is moving towards sidecar-less

multi-tenancy with istio:
- single cluster, single mesh, multiple teams
- single cluster, multi mesh
- multi cluster, multi mesh

## cilium updates, news, roadmap and in the wild

https://kccnceu2023.sched.com/event/1HyTU/cilium-updates-news-roadmap-and-in-the-wild-liz-rice-isovalent-andy-allred-eficode-richard-hartmann-grafana-labs?iframe=no&w=100%&sidebar=yes&bg=no

# Day 3

## 1 milion lines of yaml: wrapping kubernetes configurtaion for hundres of teams

https://kccnceu2023.sched.com/event/1Hycs/1m-lines-of-yaml-wrangling-kubernetes-configuration-for-hundreds-of-teams-katrina-verey-shopify?iframe=no&w=100%&sidebar=yes&bg=no

## paved paths leading the way to compliance

https://kccnceu2023.sched.com/event/1Hyan/paved-paths-leading-the-way-to-compliance-kasper-borg-nissen-brian-nielsen-lunar?iframe=no&w=100%&sidebar=yes&bg=no


