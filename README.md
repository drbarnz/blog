# blog.dbarnett.nz

This site is built using [zola](https://www.getzola.org/)

Using the theme [DeepThought](https://github.com/RatanShreshtha/DeepThought)

## Setup

Pre-requisites are:
 * git
 * `zola` (either by `cargo install zola` or package manager) or a `Containerfile` compatible container builder & runtime

To setup up for local work:
 1. `zola serve`
 1. navigate to [localhost:1111](http://localhost:1111)
 1. Happy writing

Or using `podman` / `nerdctl` / `docker`

```bash
$tool build --tag blog:local --target local .

$tool run --rm -p 1111:1111 -v "$PWD:/project" blog:local

# Ready for deployment run to run it through checks
$tool build --tag blog:prod --target prod .
```
