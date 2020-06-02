# orocommerce
Deploy OROCommerce with docker-compose

```
$ docker-compose up -d

```

Install application and admin user with Installation Wizard by opening install.php in the browser or from CLI:

```
$ docker-compose exec orocommerce sh -c "cd orocommerce && php bin/console oro:install --env=prod --timeout=10000"

```

# Silent Installation

For silent installation, use -n (no interaction) and -q (silence the output messages) parameters, and set the required parameters value, like in the example below. Replace items in bold with the information specific to your deployment.

```
$ php bin/console oro:install 
   --application-url=<URL that is configured as an entry point for Oro application>
   --env=prod
   --user-name=admin
   --user-email=admin@example.com
   --user-firstname=John
   --user-lastname=Doe
   --user-password=admin
   --sample-data=y
   --organization-name="Acme, Inc"
   --language=en
   --formatting-code=en
   --timeout=10000

```


## Quick deployment to cloud
##### Amazon AWS, Digital Ocean, Hetzner and others
[<img src="https://img.shields.io/badge/quick%20deploy-%40try.direct-brightgreen.svg">](https://try.direct/server/user/deploy/Im9yb2NvbW1lcmNlfDZ8MzQi.EIJLoA.C2UnHhbUvk1hLFruZ1Nvdp5FbC4/)

