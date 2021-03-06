# terraform-install

A minimal terraform installer and transparent terraform wrapper.

## Installation

### MacOS

Install with homebrew.

```sh
brew tap reegnz/tap
brew install terraform-install
```

You should uninstall or unlink terraform and tfenv first, as they are clashing on the `terraform`
executable link.

### Manual

Put both scripts somewhere on your PATH, so that it shadows any existing
terraform install, eg:

```bash
INSTALL_DIR="${HOME}/.local/bin"
mkdir -p "${INSTALL_DIR}"
curl -s https://raw.githubusercontent.com/reegnz/terraform-install/v1.2.0/terraform  "${INSTALL_DIR}/terraform"
curl -s https://raw.githubusercontent.com/reegnz/terraform-install/v1.2.0/terraform-install > "${INSTALL_DIR}/terraform-install"
curl -s https://raw.githubusercontent.com/reegnz/terraform-install/v1.2.0/terraform-path > "${INSTALL_DIR}/terraform-path"
```

Make sure you have `$HOME/.local/bin` on your path, eg. put this in your .bashrc or .zshrc: 

```bash
PATH=$HOME/.local/bin:$PATH
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

### Installing all versions for a directory

`terraform-install` supports discovering all `.terraform-version` files in a
directory tree and installing all discovered versions in a single run.

```sh
terraform-install --all
```

### Defining a global version

Just put the default version you intend to use into  `$HOME/.terraform-version` and the
wrapper will pick it up as long as you're running the commands under `$HOME`.

### Usage with [direnv](https://direnv.net/)

Using direnv you can avoid using the wrapper altogether, by adding the
following to your `.envrc`

```bashrc
use_terraform() {
terraform="$(terraform-path)"
PATH_add "${terraform%/*}"
}

use terraform
```

This way any wrapper overhead is eliminated as the path to the `terraform`
binary is evaluated only once, when entering the directory.
Any subsequent calls to `terraform` use the selected terraform binary
directly without any wrapping.

Note that this approach assumes you have the proper terraform versions installed.
The installation takes far too long to run with direnv, so it's advised that
you install the right terraform versions before you wire things up with
direnv.

### Environment variables

- `TF_INSTALL_VERSIONS_DIR` - install terraform versions into a custom
  directory instead of `XDG_CACHE_HOME`. Default: `${XDG_CACHE_HOME}/terraform`
  if `XDG_CACHE_HOME` is set, otherwise `~/.cache/terraform`.
- `TF_INSTALL_CHECK_SUM` - if set to 1, performs SHA256SUM check on the
  downloaded zip. Disabled with `0`. Default: 1
- `TF_INSTALL_CHECK_GPG` - if set to 1, downloads hashicorp GPG key from
  keybase and then performs a signature validation on the SHA256SUMS file
  before checking the checksum. Default: 0
