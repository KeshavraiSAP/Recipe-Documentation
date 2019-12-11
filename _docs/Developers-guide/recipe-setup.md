---
title: Recipe Module Setup
description: Overview about Recipe Module Setup.
---


# Recipe Module Setup

## Local Development Setup

<a href="https://github.wdf.sap.corp/pages/ADI/cockpitDocs/getting-started/local-development-setup/" target="_blank">Cockpit set up</a>

## Recipe Module Structure

```text
.
└── cockpit-module-recipe
    └── src
        ├── main
        │   ├── java
        │   └── js
        │        └── cockpit
        │            └── recipe
        │                ├── core
        │                ├── cis
        |                   |──public
        |                     |──tasks
        |                     |──api
        │                ├── iot
        │                └── mobile
        |                └── ...
        └── test
            ├── java
            └── js
```
