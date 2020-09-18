###########
kube-hunter
###########

What is this?
=============

From the project page: "kube-hunter hunts for security weaknesses in Kubernetes clusters."
It contains its own vulnerability database. The list of vulnerabilities can be browsed at https://aquasecurity.github.io/kube-hunter/kbindex.html

It can:

* do passive scanning (from outside the cluster). See link at the bottom of the page for a full list of scans.
* give the perspective of an attacker (by running from inside the cluster)
* (optional) proceed to find further vulnerabilities by doing an active hunting (which could be harmful on an existing cluster)
* be extended by adding your own "hunter"s.

How does it fit the security ecosystem
======================================

It's a hunt (dynamic analysis) tool, not a static analysis one (like trivy for example).
It's not targetting misconfigurations of your cluster, which is tackled by `kube-bench`_ or `polaris`_.
It's not tracing what the containers are doing, like `tracee`_.

.. _kube-bench:  https://github.com/aquasecurity/kube-bench
.. _tracee: https://github.com/aquasecurity/tracee
.. _polaris: https://github.com/FairwindsOps/polaris

Where is the source and onboarding material?
============================================

The source for kube-hunter is here: https://github.com/aquasecurity/kube-hunter
It's written in python.

The code does not include all the extra proprietary bits that Aquasecurity adds into their kube-hunter container, which reports to https://kube-hunter.aquasec.com.
Note: https://kube-hunter.aquasec.com itself is proprietary too (or not public yet?)

The onboarding material, including how to build your own hunters is located here: https://github.com/aquasecurity/kube-hunter/blob/master/CONTRIBUTING.md

Activity
========

It only has around 600 commits since its creation in March 2018.
The first release was in November 2019. The day of this writing (September 2020), the last release was almost half a year ago, and there were only 15 patches in the master branch since the last release.

Use in CI
=========

As this is a dynamic analysis tool, there is almost no point of running this in CI. Automation to test new cluster deployments to ensure their safety is thinkable, but it's not the goal of this project.

Compare it with competitors
===========================

As far as I know, there is no real "kubernetes focused" alternative. Alternative is other pentest tools.

Air-gapped
==========

TBC: Just mirror the image.

See also
========

* https://blog.aquasec.com/kube-hunter-kubernetes-penetration-testing
* https://kube-hunter.aquasec.com/ for a description of the tool and a list of the passive and active tests!
