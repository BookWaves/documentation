# BookWaves

BookWaves is an RFID solution for libraries ("library" as in "buildings with books in them") designed especially
for UHF-RFID and network-based-devices. More defails can be found at https://www.bookwaves.de

The code of BookWaves has been released but is currently invite-only. If you want access, feel free to contact
us at info@bookwaves.de

This documentation is still WIP.

If you have questions, please feel free to use the Discussions feature https://github.com/BookWaves/documentation/discussions of this repo.

## Stack demo
Some precompiled images are available to test the BooWaves application stack. However, they are only meant for demo purposes, since they are not customized for your specific environment

### Starting the demo stack
- prepare a Linux x86 VM wih Docker and Docker compose
- `docker create network traefik`
- start the traefik proxy in the `traefik` directory with `docker compose up -d`
- for the tagging application, customize the compose file in `tagging` and then start it with `docker compose up -d`
    - it will be reachable at http://localhost/tagging/gui/index.html as configured via the traefik rules
    - the adminer for the postgres DB will be here: http://localhost:8999/?pgsql=db&username=foo&db=rfid
- for the other BookWaves stack applications, customize the compose file in `rfid-stack` and then start it with `docker compose up -d
    - after the db and one other app have been started for the first time, the DB schemes have been created
    - the adminer to configure your devices will be at: http://localhost:8998/?pgsql=rfid-production-db&username=rfiddbuser&db=rfid
    - after that, the individual applications will be, e.g., at http://localhost/production/checkout/1/ as configures via the traefik rules

## A brief how-to

### Building the stack

There are several Java libraries that need to be pre-build before you can start any of the BookWaves applications. The repos are:

- `tagformat`
- `database-binding`
- `rfid-interface`
    - `reader-simulation` (for a simulated RFID reader)
    - `rfid-sense` (for Feig UHF devices via TCP)

After that, you can build the main Java applications of BookWaves from the following repos:

- `checkout-service`
- `gate-service`
- `alma-service`
- `dashboard-service`

In addition, there are some Angular web apps that are the frontends for the services. Their repos are:

- `checkout-web`
- `gate-web`
- `dashboard-web`

### Starting the stack

For the `*-service` and `*-web` applications you can then deploy them either by creating Docker containers (see the respective Dockerfiles) and starting them with a compose file (exmple can be foudn in repo `rfid-deployment`) or by individually starting the JARs on your server and hosting the HTML for the frontends.

A companion browser addon for Alma is located in `almapp`.

### Tagging application

The RFID tagging application is standalone and does not depend on the BookWaves stack. It can be found in `rfid-reader`.
