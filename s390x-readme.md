# OpenShift Pipelines E2E tests on Local

  

Validation of OpenShift Pipeline releases using [Gauge](https://docs.gauge.org/getting_started/installing-gauge.html) 

  

### Prerequisites 
  
* [Go](https://golang.org/) 

   
* Clone this repository 

  
* Need Openshift cluster based on s390x

  
* Download latest [OpenShift Client](https://mirror.openshift.com/pub/openshift-v4/clients/oc/latest/) for your operating system 

   
* Install latest [tkn](https://github.com/tektoncd/cli/releases) cli. 

  
### Installation instructions for s390x platform 


Gauge does not have support for s390x platform by default, so we need to build from source.  
  

* Clone gauge repository into go src directory and Build 

  ``` 
  mkdir -p "$GOPATH"/src/github.com/getgauge 
  cd "$GOPATH"/src/github.com/getgauge
  git clone https://github.com/getgauge/gauge.git
  go run build/make.go 
  go run build/make.go --install 
  ``` 

### Install plugins on s390x platform 

gauge plugins are not supported by default on s390x, so build from source. 

* Building xml-report plugin from source 

``` 
mkdir -p "$GOPATH"/src/github.com/getgauge 

cd "$GOPATH"/src/github.com/getgauge 

git clone https://github.com/getgauge/xml-report.git 

cd xml-report/ 

go run build/make.go 

go run build/make.go --install  

``` 

* Building gauge_screenshot plugin from source 

``` 

mkdir -p "$GOPATH"/src/github.com/getgauge 

cd "$GOPATH"/src/github.com/getgauge 

git clone https://github.com/getgauge/gauge_screenshot.git 

cd gauge_screenshot/ 

go run build/make.go 

go run build/make.go --install 

``` 

* Building html-report plugin from source 

``` 

mkdir -p "$GOPATH"/src/github.com/getgauge 

cd "$GOPATH"/src/github.com/getgauge 

git clone https://github.com/getgauge/html-report.git 

cd html-report/ 

go run build/make.go 

go run build/make.go --install 

``` 

* Building gauge-go from source 

``` 

mkdir -p "$GOPATH"/src/github.com/getgauge-contrib 

cd "$GOPATH"/src/github.com/getgauge-contrib 

git clone https://github.com/getgauge/gauge-go.git 

cd gauge-go/ 

go run build/make.go 

go run build/make.go --install 
``` 

## Run tests for OpenShift Pipelines 

  

Majority of tests assume that they run on an OpenShift cluster with already installed OpenShift Pipelines operator   

### Operator installation, upgrade and uninstallation 

  

Operator installation tests have to run as `admin` user 

  

 

 

### Login to OCP cluster with API token  

``` 

oc login --token=<sha token> --server=https://api.addon.test.multiarch:6443 

``` 


  

> Notes:  

> - `CATALOG_SOURCE` - catalog source name, `redhat-operators` for released versions, `custom-operators` for nightly builds 

> - `CHANNEL` - channel to which the installation test is supposed to subscribe, e.g. `latest` or `pipelines-1.9` 

  

### Most common test sub-suites 

  

The following tests have to run as `admin` user 

  

``` 

gauge run --log-level=debug --verbose --tags e2e specs/metrics 

gauge run --log-level=debug --verbose --tags 'e2e & tls' specs/triggers/eventlistener.spec 

gauge run --log-level=debug --verbose --tags e2e specs/clustertasks/clustertask.spec 

gauge run --log-level=debug --verbose --tags 'e2e & linux/amd64' specs/clustertasks/clustertask-multiarch.spec 

gauge run --log-level=debug --verbose --tags e2e specs/operator/rbac.spec 

gauge run --log-level=debug --verbose --tags e2e specs/operator/auto-prune.spec 

gauge run --log-level=debug --verbose --tags e2e specs/operator/addon.spec 

``` 

  

The following tests can run as both `admin` and regular/non-admin user 

  

``` 

gauge run --log-level=debug --verbose --tags e2e specs/pipelines 

gauge run --log-level=debug --verbose --tags 'e2e & !tls' specs/triggers 

gauge run --log-level=debug --verbose --tags disconnected-e2e specs/clustertasks/clustertask.spec 

gauge run --log-level=debug --verbose --tags 'e2e & !skip_linux/s390x' specs/clustertasks/clustertask-s2i.spec 

``` 

  
