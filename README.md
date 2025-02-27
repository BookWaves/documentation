# BookWaves

BookWaves is an RFID solution for libraries ("library" as in "buildings with books in them") designed especially
for UHF-RFID and network-based-devices. More defails can be found at https://www.bookwaves.de

The code of BookWaves has been released but is currently invite-only in a private GitHub repo. If you want access, feel free to contact
us at info@bookwaves.de

This documentation is WIP.

If you have questions, please feel free to use the Discussions feature https://github.com/BookWaves/documentation/discussions of this repo.

## Stack demo
Some precompiled images are available to test the BooWaves application stack. However, they are only meant for demo purposes, since they are not customized for your specific environment, e.g. branding or the library card that student use to authenticate themselves.

### Starting the demo stack
- Make sure to have Docker installed
- `git clone https://github.com/BookWaves/documentation`
- `cd docker/all-in-one-demo`
- Edit the `seed.sql`, which initializes the config-db with sample config
  - Change the media IDs 12345 and 6789 to some IDs that exist in your Alma. This way the demo can simulate having RFID tags laying on simulated devices. That is quite nice to have for a demo.
  - Replace TODO_ALMA_CIRC_DESK and TODO_ALMA_LIBRARY with working IDs. This determines at which location in your organization your demo self-checkout-machine will borrow and return books. Its not necessary to do this, but otherwise the last step of loaning/borrowing will fail.
  - Change 1.2.3.4 to your current IP. You can also define a whole subnet. This way your Alma session will be mapped to one of the simulated RFID readers to try the Alma integration. Only necessary if you do the SSL config (see below). If you don't do this right away, don't worry, an error message along the way will tell your you current IP while testing.
- Edit the `docker-compose.yml`
  - There are 3 places where you must add an Alma API key with "Users - Sandbox Read/write" and "Bibs - Sandbox Read/write" and "Configuration - Sandboy Read-only" permissions.
  - Also, be sure to read the comments in that file to get an understanding of the different containers.
- OPTIONAL: SSL config
  - Place your SSL certs into the `ssl` folder and edit the Traefik configuration to use SSL. Refer to traefiks documentation. TODO: explain in more detail
  - You can skip this step, however, the Alma integration requires running this on a HTTPS-enabled server reachable by your browser
- `docker compose up -d`
- check with `docker ps` whether all containers started successfully and not reboot
- Done

### Trying out the demo stack
We assume that you started this on your local machine. If you started it on a server, replace `localhost` with the server IP.
If you did the SSL setup, be sure to also try out the HTTPS URL.

There is also a web GUI to look at the pre-initialized SQL db. You can also edit values easily this way. It is reachable at http://localhost:8999/?pgsql=rfid-production-db&username=rfiddbuser&db=rfid password `foobar`

#### Checkout device / Selbstverbucher
- Your demo checkout device is reachable on http://localhost/production/checkout/1/
- To check for errors, see http://localhost/production/checkout/1/admin/healthcheck
- In the GUI, you can:
  - Lend items, which opens a basic login popup. Insert a valid Alma user id (primary or secondary) to login
  - Return items
  - Check wich items are on the reader
- The reader is the simulated reader populated by the media IDs in the SQL table

#### Gate
- Your gate is reachable at localhost/production/gate/1/
- Its healthcheck is at localhost/production/gate/1/admin/healthcheck
- The GUI only show secured (= not borrowed) items and those only briefly. Thus you will likely not see the items pup up again when you visit the gui.
  - Nb. the boolean flag for the simulated media IDs is their secured status.
- You can go into the SQL database and check table `gatetagdetection` to see the registered events

#### Alma-Service
- If you did not configure SSL, best you can do is check http://localhost/production/alma-service/getItems to see if the service return an Alma-RFID-XML message
  - Like `Kein RFID Reader für den Rechner gefunden.` or (when your IP is correctly configured) the XML structure of the simulated tag when successfully read
- There is also an healthcheck at http://localhost/production/alma-service/admin/healthcheck
- If you did configure SSL, check if your service is reachable at https://yoursubdomain.yourhost.com/production/alma-service/admin/healthcheck
  - Then go into your Alma admin UI -> General -> Integration profiles -> New Integration profile
  - Type: RFID, System: Other
  - Active: true, Server-URL: https://yoursubdomain.yourhost.com/production/alma-service, Multiple...: true, Edit...: true
  - Leave the rest as is and save the profile
  - From now on, your staff can enable RFID interaction with an icon in the top bar of the normal Alma UI
  - Refer to the official documentation for more info: https://knowledge.exlibrisgroup.com/Alma/Product_Documentation/Alma_Online_Help_(Deutsch)/090Integrationen_mit_externen_Systemen/040Erf%C3%BCllung/080RFID-Support

#### Dashboard
- The dashboard is reachable at http://localhost/production/dashboard/ and shows the current status of the services and devices
- You can change (some of) the settings of the devices. Those will reflect in the SQL database
- You also see the device statistics and the last events

#### Tagging
- It will be reachable at http://localhost/tagging/gui/index.html
- As it does not support the simulated reader (writing to nothing would be kinda pointless), you can only look at the GUI for now
- The tagging will be logged to a database
  - You can use this GUI http://localhost:8999/?pgsql=db&username=foo&db=rfid password is `bar`

## Using a real RFID reader
BookWaves currently support LAN-based UHF readers from Feig, like the LRU1002 or the MRU102. Be sure to buy the LAN edition!
### Reader setup
- TODO

## Building the stack
(TODO this part is slightly outdated)

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
