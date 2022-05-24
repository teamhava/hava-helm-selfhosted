## Usage

[Helm](https://helm.sh) must be installed to use the charts.  Please refer to
Helm's [documentation](https://helm.sh/docs) to get started.

Once Helm has been set up correctly, add the repo as follows:

  helm repo add hava https://teamhava.github.io/hava-helm-selfhosted

If you had already added this repo earlier, run `helm repo update` to retrieve
the latest versions of the packages.  You can then run `helm search repo
hava` to see the charts.

To install the hava chart:

    helm install hava hava/hava

To uninstall the chart:

    helm delete hava