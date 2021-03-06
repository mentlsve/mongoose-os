---
title: Run time - factory, vendor, user layers
---

Device configuration is stored on the filesystem in several files:

- `conf0.json` - factory defaults layer
- `conf1.json` to `conf8.json` - vendor layers
- `conf9.json` - user layer

When Mongoose OS boots, it reads those files in exactly that order,
merges into one, and initializes in-memory C configuration structure
reflects that on-flash configuration. So, at boot time,
`struct sys_config` is intialised in the following order:

- First, the struct is zeroed.
- Second, defaults from `conf0.json` are applied.
- Third, _vendor configuration layers_ 1 through 8 are loaded one after another.
- The _user configuration file_, `conf9.json`, is applied on as the last step.

The result is the state of the global `struct sys_config`.
Each step (layer) can override some, all or none of the values.
Defaults must be loaded and it is an error if the file does not exist
at the time of boot. But, vendor and user layers are optional.

Note that a vendor configuration layer is not present by default.
It is to facilitate post-production configuration: devices can be
customised by uploading a single file (e.g. via HTTP POST to `/upload`)
instead of performing a full reflash.
Vendor configuration is not reset by the "factory reset", whether via GPIO or web.
