# databaseDesign-standards

PostgreSQL database design and configuration standards.

## Overview

This document defines database design and configuration standards targeting PostgreSQL v17.

The standards are based on long-term practical experience and a **Data-Oriented Approach (DOA)**, aiming to build robust and maintainable data structures that are independent of application-specific constraints.

### Concepts
* **Data-Oriented Approach (DOA)**
  Define data structures based on business meaning rather than application convenience.

* **Up-to-date PostgreSQL features**
  Incorporates major improvements up to PostgreSQL v17.

* **Practical grounding**
  Derived from real-world operational experience, including failure cases and maintenance insights.
  

### Scope

* Target: OLTP-based business systems
* Scale: Medium to large-scale systems

## Documents

* Japanese: [DatabaseDesignStandard.md](DatabaseDesignStandard.md)
* English: [DatabaseDesignStandard_EN.md](DatabaseDesignStandard_EN.md)

## Quick Start / Templates
* [CREATE TABLE Snippet](Snippet-CreateTable.sql)
* [postgresql.conf.basic](postgresql.conf.basic): Baseline configuration template

## Nature of This Standard

This document includes both:

* **Mandatory rules (standards)**
* **Recommended practices (guidelines)**

Each item defines its priority and applicability where relevant.

## Notes

* Originally created around 2008 and modernized to PostgreSQL v17 using AI-assisted review.
* Based on accumulated practical knowledge from the author's technical blog:
 Reference: http://kashi.way-nifty.com/jalan/
* This standard may be updated along with PostgreSQL version upgrades.
* The legacy version (around 2008) is included as a reference.
  - [DatabaseDesignStandard_legacy.md](DatabaseDesignStandard_legacy.md)

## AI-assisted Development
This standard was developed with the support of multiple AI tools (Claude, ChatGPT, Gemini, DeepL). 
For detailed insights and our collaboration process, please refer to:
* [AI-assisted Development Insights](AI-INSIGHTS.md)

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
© 2026 かっしい(Kashi)
