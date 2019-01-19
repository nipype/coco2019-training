class: center middle

# niflows - Reuse, Create, and Package your own Workflows
### Christopher J. Markiewicz
#### Center for Reproducible Neuroscience
#### Stanford University

###### [effigies.github.io/niflows-intro](https://effigies.github.io/niflows-intro)

---
name: footer
layout: true

<div class="slide-slug">Coastal Coding - Miami, FL - Jan 2019</div>
---
layout: true
template: footer

.left-column[
# niflows
### Audience
]

---
.right-column[

## Who shares workflows?

### Study authors

* Workflow sharing as method disclosure
* Running workflows on source data should reproduce published results

Study authors are interested in the *replicability* of their workflows.

]

---
.right-column[

## Who shares workflows?

### Study authors

* Workflow sharing as method disclosure
* Running workflows on source data should reproduce published results

Study authors are interested in the *replicability* of their workflows.

### Method developers

* Workflow sharing is the research product
* Workflows should be runnable on novel data
* Workflows should be runnable on varied environments
<!--
Nipype-based workflows may be published as independent projects (*e.g.*,
[fMRIPrep](https://fmriprep.readthedocs.io/), [Arcana](https://arcana.readthedocs.io/),
[C-PAC](https://fcp-indi.github.io/)) or as contributions to
[`nipype.workflows`](https://github.com/nipy/nipype/tree/master/nipype/workflows).
-->

Method developers are interested in the *reusability* of their workflows.

]

---
layout: true
template: footer

.left-column[
# niflows
### Audience
### Problem
]

---

.right-column[

## Workflows are fragile

A workflow can be invalidated by changes in:

* The wrapped tools - new versions of FSL, AFNI, etc. may have different calling
  conventions, or produce different outputs

]

---

.right-column[

## Workflows are fragile

A workflow can be invalidated by changes in:

* The wrapped tools - new versions of FSL, AFNI, etc. may have different calling
  conventions, or produce different outputs

* Nipype and its dependencies - breaks in backwards compatibility may be introduced
  intentionally or unintentionally

]

---

.right-column[

## Workflows are fragile

A workflow can be invalidated by changes in:

* The wrapped tools - new versions of FSL, AFNI, etc. may have different calling
  conventions, or produce different outputs

* Nipype and its dependencies - breaks in backwards compatibility may be introduced
  intentionally or unintentionally

* Runtime environment - see, *e.g.*,
  [Gronenschild, et al. (2012)](https://doi.org/10.1371/journal.pone.0038234)
  or [Glatard, et al. (2015)](https://doi.org/10.3389/fninf.2015.00012)

]

---

.right-column[

## Workflows are fragile

A workflow can be invalidated by changes in:

* The wrapped tools - new versions of FSL, AFNI, etc. may have different calling
  conventions, or produce different outputs

* Nipype and its dependencies - breaks in backwards compatibility may be introduced
  intentionally or unintentionally

* Runtime environment - see, *e.g.*,
  [Gronenschild, et al. (2012)](https://doi.org/10.1371/journal.pone.0038234)
  or [Glatard, et al. (2015)](https://doi.org/10.3389/fninf.2015.00012)

### How can we make workflows accessible, reusable, and less breakable?

]

---
layout: true
template: footer

.left-column[
# niflows
### Audience
### Problem
### Goals
]

---

.right-column[

## How can we make workflows accessible, reusable, and less breakable?

* Simple, uniform publication

* Containerized workflows

* Comprehensive dependency specification

* Automated testing infrastructure

]

---

.right-column[
## Simple, uniform workflow publication

```Shell
$ nfm install nipype1-examples
```

Pipelines will be available through the shell, or more flexibly through
Python:

```Shell
$ niflow-nipype1-examples fsl_feeds MultiProc /data l1pipeline
```

```Python
>>> from niflow.nipype1.examples.fmri_fsl import l1pipeline
>>> l1pipeline.run(plugin='MultiProc')
```

## Containerized workflows

Workflow recipes 
will be constructable with 

```Shell
$ nfm build --singularity effigies-syllable_repetition
$ singularity run niflow-effigies-syllable_repetition.simg /data
```

]

---
.right-column[

## Comprehensive dependency descriptions

niflows will allow workflow maintainers to specify compatible versions of:

* neuroimaging tools - `FreeSurfer > 5.3`, `FSL < 6.0`
* OS-level libraries - `libc == 2.23`
* Python libraries - `nipype >= 2.0`, `dipy == 0.15.0`
* Other niflows - `miykael-preprocessing`

Methods developers may work to make these as loose as possible, to reduce
constraints on users, while study authors may choose to simply report the environment
they used.

]

---
.right-column[

## Automated testing infrastructure

niflows will take advantage of the [testkraken](https://github.com/ReproNim/testkraken)
framework to run and validate the same workflow in different environments, with different
dependencies.

```Shell
nfm test nipype1-examples
```

### Recipe excerpt
```YAML
env:
  base:
  - {image: ubuntu:16.04, pkg-manager: apt}
  - {image: centos:7, pkg-manager: yum}
  fsl:
  - {version: 5.0.9}
  - {version: 5.0.10}
  miniconda:
  - {conda_install: [python=3.5, nipype, pandas, requests]}
```

]

---
layout: true
template: footer

.left-column[
# niflows
### Audience
### Problem
### Goals
### Hands-on
]

---
name: handson

.right-column[
## Install the `niflow-manager`

```Shell
pip install niflow-manager
```

This will add the `nfm` command to your system.

## Open some references:

* This presentation: https://effigies.github.io/niflows-intro/#handson
* The [niflow draft specification](https://docs.google.com/document/d/16eWuOy7rIRKtlCBtnGO5dw4u8o6A0y3ieD0DpduS_zQ)

## Strategize

We'll each make a workflow, and then combine into groups to make a larger workflow
that connects the subworkflows.

Form groups of 2 or 3, and plan the workflows you'd like to attempt.

]

---
.right-column[

## Create a new niflow

```Shell
nfm init coastalcoding-example --language python
```

This produces the following basic directory tree:

```
niflow-coastalcoding-example
├── README.md
├── doc
│   └── README.md
├── package
│   ├── niflow
│   │   ├── __init__.py
│   │   └── coastalcoding
│   │       ├── __init__.py
│   │       └── example
│   │           ├── __init__.py
│   │           ├── cli.py
│   │           └── workflow.py
│   ├── setup.cfg
│   └── setup.py
└── test
    └── README.md
```

]

---
.right-column[

## Install the stub niflow

`nfm install` isn't implemented, yet. For now, we'll use `pip` directly.

```Shell
pip install niflow-coastalcoding-example/package
```

## Test the stub workflow

```Shell
niflow-coastalcoding-example
```

]
