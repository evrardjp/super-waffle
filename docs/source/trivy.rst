#####
trivy
#####

Check their `Github page`_ file for more info. Otherwise, the most relevant stuff is on this document.

.. _`Github page`: https://github.com/aquasecurity/trivy

Where to get it
===============

I've packaged trivy on my `home project`_. You can simply install the binary from here. Otherwise, building from source is actually not that hard (just call :code:`go install` from the :code:`cmd/trivy` directory).

.. _`home project`: https://build.opensuse.org/package/show/home:mssola/trivy

CI goodies
==========

Trivy is specifically tailored for CI environments (even if it can also be used in other environments). To this end, there are some features that are quite remarkable.

First of all, and before the CI stage, it's worth mentioning that it works with other formats besides Docker images, such as tar files produced by skopeo_ or buildah_. Thus, it might also be useful when building our base images.

.. _skopeo: https://github.com/containers/skopeo
.. _buildah: https://github.com/containers/buildah.

You can specify the :code:`--exit-code`, which is pretty nice when you combine it with with the :code:`--severity` flag:

.. code-block:: bash

  $ trivy image --exit-code 0 --severity MEDIUM,HIGH ruby:2.4.0
  $ trivy image --exit-code 1 --severity CRITICAL ruby:2.4.0

Similarly, you can ignore vulnerabilities by adding a :code:`.trivyignore` file. You can also ignore vulnerabilities marked as "not fixed" with the :code:`--ignore-unfixed` flag. Finally, you can also filter vulnerabilities via OPA, as it's been described in here_. All combined, these are options that can be helpful when processing acceptable vulnerabilities.

.. _here: https://github.com/aquasecurity/trivy#filter-the-vulnerabilities-by-open-policy-agent-policy

And, where does trivy take the vulnerabilities from? Well, from a database that is to be downloaded on the first run. That being said, this can be tuned. For example, you can avoid this stage by providing the :code:`--skip-update` flag (which is pretty important in air-gapped environments, as discussed below). This database is downloaded into a cache directory, which can be specified by the user. Furthermore, this database can be tuned so only the relevant information is downloaded for CI environments with the :code:`--light` flag. This way, we save some space (from 18MiB to 11MiB as of September 2020).

Moreover, trivy is super fast when analyzing images after the first run. This is because the results are cached by :code:`image:tag`. That being said, if we are using different code with the same :code:`image:tag` (e.g. :code:`latest`), you can use the :code:`--clear-cache` to clear the cache before analyzing such images. With the cache, analyzing a base image (:code:`opensuse/leap:15.2`) takes around 2s, while without the cache it takes 7s for this same image.

To improve performance you can pass further flags such as :code:`--skip-dirs`. This way, you can skip directories that contain stuff that we don't own or that it has already been checked by some other tool (e.g. system ruby gems).

If we have an initialization stage for the CI, we can tell to download the database beforehand with: :code:`trivy image --download-db-only`. This way, when running tests, we can simply run trivy commands with the :code:`--skip-update` flag, being a bit faster overall.

As a cherry on top, they have a bunch of templates for configuring trivy on Github Actions, Travis CI, Circle CI, AWS, etc.

Comparing it with Clair
=======================

To sum things up:

- Trivy claims to fill up their database from different places which contain more CVEs (e.g. their Alpine database is larger). Take this with a grain of salt.
- Trivy can analyze middle layers while Clair, by design, can't. This allows trivy to detect vulnerabilities in cases such as this (while Clair can't):

.. code-block:: Dockerfile

  RUN apk add --no-cache sqlite-dev \
   && wget https://xxx/yyy.tar.gz \
   && tar zxvf yyy.tar.gz && cd yyy \
   && make && make install \
   && apk del sqlite-dev

- Trivy can be run standalone: you are not forced to use always the server-client schema as it happens with Clair (even if it's also possible).
- Trivy can detect vulnerabilities in development environments, by checking files such as :code:`Gemfile.lock` or :code:`yarn.lock`. That being said, they do not provide Go modules support.

They also provide a comparison with Anchore, but it's more of the same as already described.

You can check more on their documentation_.

.. _documentation: https://github.com/aquasecurity/trivy#vs-clair

Air-gapped
==========

They also provide documentation for `air gapped environments`_. The steps are quite trivial, really:

1. You download the database with :code:`--download-db-only`.
2. You upload said database wherever you have your air gapped environment by following your workflow. Make sure to place it inside of trivy's cache (can be checked with :code:`trivy -h`).
3. Then, you only need to run every command with :code:`--skip-update`.

.. _`air gapped environments`: https://github.com/aquasecurity/trivy/blob/master/docs/air-gap.md

Blog entries
============

According to `this entry`_, each scanner provides a different output as for the found vulnerabilities. Moreover, `this other entry`_ highlights how Anchore is a bit behind of Clair and Trivy, while Trivy is considered to be easier to setup and use. With this in mind, my conclusion is that trivy is actually pretty reliable, but it might be a good idea to have also Clair in place just in case.

.. _`this entry`: https://boxboat.com/2020/04/24/image-scanning-tech-compared/
.. _`this other entry`: https://www.a10o.net/devsecops/docker-image-security-static-analysis-tool-comparison-anchore-engine-vs-clair-vs-trivy/
