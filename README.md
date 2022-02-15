# terraform-install

A minimal terraform installer and transparent terraform wrapper.

## Installation

Put both scripts somewhere on your PATH, so that it shadows any existing
terraform install, eg:

```bash
INSTALL_DIR="${HOME}/.local/bin"
mkdir -p "${INSTALL_DIR}"
curl -s https://raw.githubusercontent.com/reegnz/terraform-install/v1.0.0/terraform  "${INSTALL_DIR}/terraform"
curl -s https://raw.githubusercontent.com/reegnz/terraform-install/v1.0.0/terraform-install > "${INSTALL_DIR}/terraform-install"
```

## Usage

Put a `.terraform-version` file next to your terraform code and put the
desired terraform version in there, like this:

```text
1.1.5
```

Then run `terraform-install` to have it install the desired version. The
binaries are cached in your `XDG_CACHE_HOME` (defaulting to `~/.cache`)
under `$XDG_CACHE_HOME/terraform`.

You can customize the install location with the `TF_INSTALL_VERSIONS_DIR` 
environment variable.

If you want to enable gpg signature checking, you can set the
`TF_INSTALL_CHECK_GPG` environment variable.

Once the desired version is installed, you can use the `terraform` wrapper to
pick up the correct binary based on the version file, and invoke the cached 
binary passing all args.

### Environment variables

- `TF_INSTALL_VERSIONS_DIR` - install terraform versions into a custom
  directory instead of `XDG_CACHE_HOME`. Default: `${XDG_CACHE_HOME}/terraform`
  if `XDG_CACHE_HOME` is set, otherwise `~/.cache/terraform`.
- `TF_INSTALL_CHECK_SUM` - if set to 1, performs SHA256SUM check on the
  downloaded zip. Disabled with `0`. Default: 1
- `TF_INSTALL_CHECK_GPG` - if set to 1, downloads hashicorp GPG key from
  keybase and then performs a signature validation on the SHA256SUMS file
  before checking the checksum. Default: 0

