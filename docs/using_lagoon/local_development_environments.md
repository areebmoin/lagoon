# Local Development Environments

Even though Lagoon has only a hard dependency on Docker and [Docker Compose](https://docs.docker.com/compose/) (which is mostly shipped with Docker) there are some things which are nice for local development that are not included in Docker:

- a HTTP Reverse Proxy for nice URLs and HTTPs offloading
- DNS System so we don't have to remember IP Addresses
- SSH Agents to use SSH keys within Containers
- a system that receives and displays mail locally

Lagoon currently works best with `pygmy` which is the amazee.io flavored system of the above tools and works out of the box with Lagoon.

`pygmy` is a [Ruby](https://www.ruby-lang.org/en/) gem, so to install it, run: `gem install pygmy`.

For detailed usage info on `pygmy`, see the [documentation of pygmy](https://pygmy.readthedocs.io/)

We are evaluating adding support for other systems like Lando, Docksal, DDEV, and Docker4Drupal, and will possibly add full support for these in the future. If you do have Lagoon running with a system like these, we would love for you to submit a [PR on Github](https://github.com/amazeeio/pygmy).
