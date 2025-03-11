---
title: Exporting to Google Cloud Storage
description: 
weight: 5
---

This simply stores the data as a compressed tarball in a Google Cloud Storage bucket.

```yaml
config:
  # Defect dojo configuration
  googleCloudStorage:
    keyFile: "/path/to/GCS/key"                           # optional: path to the GCS key file (alternatively: use GOOGLE_APPLICATION_CREDENTIALS)
    bucketName: "<name-of-GCS-bucket-to-export-to>"       # Mandatory
    directory: "<override-of-default-directory>"          # Optional directory where the credentials have write access, defaults to `RapiDAST-<product>`
```

Once this is set, scan results will be exported to the bucket automatically. The tarball file will include:

 1. metadata.json - the file that contains scan_type, uuid and import_data(could be changed later. Currently this comes from the previous DefectDojo integration feature)
 2. scans - the directory that contains scan results