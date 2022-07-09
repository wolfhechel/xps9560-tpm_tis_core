Linux 5.15.17 introduced a change to the tpm_tis module which breaks the TPM
module on Dell XPS 9560.

This PKGBUILD based of linux-hardened compiles the tpm_tis_core module with the
change reverted. The module is installed into `updates` in order to override
the default module.
