# Changelog Axway API-Management Helm-Chart

All notable changes to this project will be documented in this file.

## [2.1.0] 2021-11-22
### Added
- Value.yaml; added openshift specif configurations
- anm
    - added openshift deployment annotation
    - modified image reference when openshift
    - added resources for init container (necessary for limited projects)
    - removed security context when openshift
    - added imagestream definition yaml
    - modified route, added host definition
- apimgrui
    - added openshift deployment annotation
    - modified image reference when openshift
    - added resources for init container (necessary for limited projects)
    - removed security context when openshift
    - added imagestream definition yaml
    - modified route, added host definition
    - modified route, changed from passthrou to reencrypt
- apitraffic
    - added openshift deployment annotation
    - modified image reference when openshift
    - added resources for init container (necessary for limited projects)
    - removed security context when openshift
    - modified route, added host definition
    - modified route, changed from passthrou to reencrypt
- license
    - removed the license content from the value.yaml
    - added the possibility to add a license file in the Secret folder and declare it into the value.yaml
- BuildConfig
    - OPENSHIFT ONLY
    - added buildconfig configurations
