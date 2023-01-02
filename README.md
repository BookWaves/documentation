# BookWaves

BookWaves is an RFID solution for libraries ("library" as in "buildings with books in them") designed especially
for UHF-RFID and network-based-devices. More defails can be found at https://www.bookwaves.de

The code of BookWaves has been released but is currently invite-only. If you want access, feel free to contact
us at info@bookwaves.de

This documentation is still WIP.

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