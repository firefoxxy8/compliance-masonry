# Compliance Masonry
[![Go Report Card](https://goreportcard.com/badge/github.com/opencontrol/compliance-masonry-go)](https://goreportcard.com/report/github.com/opencontrol/compliance-masonry-go)
[![Coverage Status](https://coveralls.io/repos/github/opencontrol/compliance-masonry-go/badge.svg?branch=master)](https://coveralls.io/github/opencontrol/compliance-masonry-go?branch=master)
[![Circle CI](https://circleci.com/gh/opencontrol/compliance-masonry-go/tree/master.svg?style=svg)](https://circleci.com/gh/opencontrol/compliance-masonry-go/tree/master)

Compliance Masonry is a CLI that allows users to construct certification documentation using the [OpenControl Schema](https://github.com/opencontrol/schemas). See [Benefits](#benefits) for more explanation.

# Quick Start with CLI

### Installing
0. Install Go
0. Install the tool
```bash
go get github.com/opencontrol/compliance-masonry-go
```
0. Run the CLI
```
compliance-masonry-go
```

### Development
First, ensure [$GOPATH is set correctly](https://golang.org/doc/code.html#GOPATH)

This project uses [glide](https://github.com/Masterminds/glide) to manage vendored dependencies.

## Creating an OpenControl project
1. Start a fresh directory
  ```bash
  mkdir your-project-name && cd your-project-name
  ```
2. Create an opencontrol.yaml files
  ```bash
  touch opencontrol.yaml
  ```
3. Edit the opencontrol.yaml to contain the following data  
   ```yaml
  schema_version: "1.0.0" # 1.0.0 is the current opencontrol.yaml schema version
  name: Project_Name # Name of the project
  metadata:
    description: "A description of the system"
    maintainers:
      - maintainer_email@email.com
  components: # A list of paths to components written in the opencontrol format for more information view: https://github.com/opencontrol/schemas
    - ./component-1  
  certifications: # An optional list of certifications for more information visit: https://github.com/opencontrol/schemas
    - ./cert-1.yaml
  standards: # An optional list of standards for more information visit: https://github.com/opencontrol/schemas
    - ./standard-1.yaml
  dependencies:
    certifications: # An optional list of certifications stored remotely
      - url: github.com/18F/LATO
        revision: master
    systems:  # An optional list of repos that contain an opencontrol.yaml stored remotely
      - url: github.com/18F/cg-complinace
        revision: master
    standards:   # An optional list of remote repos containing standards info that contain an opencontrol.yaml
      - url: github.com/18F/NIST-800-53
        revision: master
  ```

4. Collect dependencies
  ```bash
  compliance-masonry-go get
  ```
  The `get` command will retrieve  dependencies needed to compile documentation.


## Creating Gitbook Documentation
1. Update dependencies
  ```bash
  compliance-masonry-go get
  ```
2. Run the gitbook command
  ```bash
  compliance-masonry-go docs gitbook LATO
  # Or
  compliance-masonry-go docs gitbook FedRAMP-low
  ```

The `gitbook` command by default will create a folder called `exports` that contains the files needed to create a gitbook. Visit the [gitbook documentation](https://github.com/GitbookIO/gitbook-cli) for more information on creating gitbooks via the cli

## Create Docx template
1. Create a Word Document template that uses the following template tag format  

  ```txt
  Documentation for Standard: NIST-800-53 and Control: CM-2 will be rendered below
  {{ getControl "NIST-800-53@CM-2"}}

  Documentation for Standard: NIST-800-53 and Control: AC-2 will be rendered below
  {{ getControl "NIST-800-53@AC-2"}}
  ```
2. Run the docx command
  ```bash
  compliance-masonry-go docs docx -t path/to/template.docx
  ```
  Running the `docx` command will by default create a file named `export.docx` in the local directory.

# Install dependencies
$ cd $GOPATH/src/github.com/opencontrol/compliance-masonry-go
$ glide install
```

To install dependencies locally without glide
```
$ cd $GOPATH/src/github.com/opencontrol/compliance-masonry-go
$ go get -t ./...
```

### Tests
Install dependencies
```
$ go get github.com/onsi/ginkgo/ginkgo
$ go get github.com/onsi/gomega
```

Run all tests:
```
$ cd $GOPATH/src/github.com/opencontrol/compliance-masonry-go
$ go test
$ cd models
$ go test ./...
```

### Examples
Compliance Masonry examples in the wild:
* [cloud.gov compliance data repository](https://github.com/18F/cg-compliance) + [documentation generated by Compliance Masonry](https://compliance.cloud.gov/)

# Documentation Format
Compliance Masonry uses the [OpenControl v2 Schema](https://github.com/opencontrol/schemas).

## Benefits
Modern applications are built on existing systems such as S3, EC2, and Cloud Foundry. Documentation for how these underlying systems fulfill NIST controls or PCI SSC Data Security Standards is a prerequisite for receiving authorization to operate (ATO). Unlike most [System Security Plan documentation](http://csrc.nist.gov/publications/nistpubs/800-18-Rev1/sp800-18-Rev1-final.pdf), Compliance Masonry documentation is built using [OpenControl Schema](https://github.com/opencontrol/schemas), a machine readable format for storing compliance documentation.

Compliance Masonry simplifies the process of certification documentations by providing:
1. a data store for certifications (ex FISMA), standards (ex NIST-800-53), and the individual system components (ex AWS-EC2).
2. a way for government project to edit existing files and also add new control files for their applications and organizations.
3. a pipeline for generating clean and standardized certification documentation.

### Development
This project requires [go1.6](https://github.com/moovweb/gvm) and uses [glide](https://github.com/Masterminds/glide) to manage vendored dependencies.

#### Project setup
```bash
# Create directories
mkdir -p compliance-masonry-go/src/github.com/opencontrol/
mkdir compliance-masonry-go/bin
# Clone compliance-masonry-go
git clone git@github.com:opencontrol/compliance-masonry-go.git compliance-masonry-go/src/github.com/opencontrol/
# Set $GOPATHs
export GOPATH=`pwd` && export PATH=$PATH:$GOPATH/bin
cd src/github.com/opencontrol/compliance-masonry-go
# Install dependencies
go get github.com/Masterminds/glide
$GOPATH/bin/glide install
```

#### Running tests
```bash
# Get test dependencies
go get -t ./...
# Run tests
ginkgo
```
