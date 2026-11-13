# azcopy-tool

This repository hosts the Linux `azcopy` binary that the Nextflow
[Azure plugin](https://github.com/nextflow-io/nextflow/tree/master/plugins/nf-azure) installs on Azure Batch nodes and tasks.

Microsoft distributes AzCopy for Linux only as a `.tar.gz` archive. Nextflow downloads a plain binary instead, so that `azure.batch.copyToolInstallMode = 'task'` works in task containers that do not provide `tar`.

See the [AzCopy documentation](https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10) for details about the tool.

## Versions

Each version is published as a tag. Nextflow downloads the binary from a tag URL:

```
https://github.com/nextflow-io/azcopy-tool/raw/<tag>/azcopy
```

| Tag | AzCopy version | Used by Nextflow |
|---|---|---|
| `linux_amd64_10.34.1` | 10.34.1 | Pending |
| `linux_amd64_10.13.0` | 10.13.0 | No |
| `linux_amd64_10.8.0` | 10.8.0 | Yes |

The `master` branch contains the latest binary. Do not reference `master` from Nextflow; always pin a tag.

Released Nextflow versions depend on these tags. Never move, rewrite, or delete an existing tag.

## Current binary

The binary on `master` is AzCopy 10.34.1, extracted unmodified from the official release archive:

- Source: https://github.com/Azure/azure-storage-azcopy/releases/download/v10.34.1/azcopy_linux_amd64_10.34.1.tar.gz
- Archive SHA-256: `a0021809e53f2b40705509626b8d44f2312fa8ef39deeb941fcffd35012bf6d1`
- Binary SHA-256: `987151829b0e4cd54774a890a33a5f45bead5c6b0b3edc9134bfb8957aa21228`

## Adding a new version

1. Download the release archive and extract the binary:

   ```bash
   VERSION=10.34.1
   curl -fsSL -o azcopy.tar.gz https://github.com/Azure/azure-storage-azcopy/releases/download/v${VERSION}/azcopy_linux_amd64_${VERSION}.tar.gz
   tar -xzf azcopy.tar.gz --strip-components=1 azcopy_linux_amd64_${VERSION}/azcopy
   ./azcopy --version
   ```

2. Commit the binary and update this README (versions table and checksums), then open a pull request against `master`.

3. After the pull request is merged, tag the merge commit and push the tag:

   ```bash
   git tag linux_amd64_${VERSION} <merge-commit>
   git push origin linux_amd64_${VERSION}
   ```

4. Update `AZCOPY_URL` in `plugins/nf-azure/src/main/nextflow/cloud/azure/batch/AzBatchService.groovy` in Nextflow to point at the new tag.
