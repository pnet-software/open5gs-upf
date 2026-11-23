# Third-party charts

This chart is built from the following charts, under the Apache License, Version 2.0. A copy of the license is in
[LICENSE](LICENSE).

| In this repository | Source | Copyright |
|---|---|---|
| The chart itself | `charts/open5gs-upf` of the `open5gs` chart 2.2.6 of [Gradiant 5g-charts](https://github.com/Gradiant/5g-charts) (`oci://registry-1.docker.io/gradiant/open5gs`) | Copyright 2020 Gradiant |
| `charts/common` 1.17.1 | [Bitnami charts](https://github.com/bitnami/charts), as bundled in the same `open5gs` chart | Copyright © 2022 Bitnami |

## Changes

Upstream, the UPF is a subchart of Gradiant's `open5gs` umbrella chart, and its templates fixed values this deployment
must set: the UPF could not be pointed at an SMF in another release, so the UPF is taken out as a chart of its own and
those values are made configurable. Other changes adjust defaults.

Every changed file says so in its first line. The Bitnami chart is unchanged.

| File | Change | Why |
|---|---|---|
| `charts/common/`, `Chart.lock` | Copied in from the parent `open5gs` chart, unchanged; `Chart.lock` pins `common` 1.17.1 as the parent does | The UPF chart now stands alone, and it depends on `common` |
| `resources/config/upf.yaml` | The PFCP `client.smf` list is rendered from `config.upf.pfcp.smf`, each entry evaluated as a template. With an empty list the block is left out, as upstream. | Upstream's `client:` block was empty, so the UPF could not start the PFCP association with an SMF in another release |
| `values.yaml` | New `config.upf.pfcp.smf`, defaulting to the open5gs-em-stack SMF's PFCP Service | The SMF is in another release and namespace, so its address is written out in full |
| `values.yaml` | `config.upf.gtpu.advertise` defaults to `<fullname>-gtpu` instead of `""` | The advertised GTP-U address always matches the GTP-U Service name, without hard-coding the release name |
| `values.yaml` | `services.pfcp.clusterIP` is `"None"` (headless) instead of `""` | Open5GS identifies PFCP peers by IP. Through a Service virtual IP, messages the UPF starts come from the pod IP, and the SMF doesn't recognise them. |
| `Chart.yaml` | `home`, `maintainers` and the `gradiant` keyword removed; `version` set by CI from the release tag | The chart is published and maintained by P-NET under its own versions. Gradiant is credited here and in [NOTICE](NOTICE). |
| `README.md` | An introduction, Releases and License sections added; homepage and maintainers removed | As for `Chart.yaml` |

## Container images

The chart runs the `gradiant/open5gs` image as its publisher releases it on Docker Hub. It isn't part of this chart.
