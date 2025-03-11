---
title: Configuration using environment variables
description: 
weight: 1
---

You may not want to directly have configuration values inside the configuration. Typically: either the entry is a secret (such as a password), but the configuration needs to be public, or the entry needs to be dynamically generated (e.g.: a cookie, a uniquely generated URL, etc.) at the time of running RapiDAST, and it's an inconvenient to always having to modify the configuration file for each run.

To avoid this, RapiDAST proposes 2 ways to provide a value for a given configuration entry. For example, to provide a value for the entry general.authentication.parameters.rtoken, you can either (in order of priority):

Create an entry in the configuration file (this is the usual method)
Create an entry in the configuration file pointing to the environment variable that actually contains the data, by appending _from_var to the entry name: general.authentication.parameters.rtoken_from_var=RTOKEN (in this example, the token value is provided by the $RTOKEN environment variable)