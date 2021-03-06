---
id: digitalocean
title: DigitalOcean Marketplace
---

Launch a Droplet using [erxes](https://marketplace.digitalocean.com/apps/erxes) in the DigitalOcean Marketplace.  
Once you have created the Droplet, you will then have erxes up and running and it will be accessible by public IP address of the Droplet.

## Create an admin user

Connect to your Droplet instance via ssh.

Run the following commands.

```sh
sudo su erxes
cd ~/erxes-api
export MONGO_URL=mongodb://localhost/erxes?replicaSet=rs0
```

The following will create an admin user admin@erxes.io with a random password (check your console to grab the password)

```
yarn initProject
```

## Load initial data

The below command will create initial permission groups, permissions, growth hack templates, email templates and some sample data and reset the admin password (check your console to grab the password)

```
yarn loadInitialData
```

If do not want to load sample data then you can run the following command just to load permissions.

```
yarn loadPermission
```

Now you can access erxes by your Droplet IP address.

## Use your own domain

To be able to use your own domain with erxes, you will need to do a few steps.

1. Update your domain DNS records - point your domain to your Droplet public IP address. The DNS changes may take up to 72 hours to propagate worldwide.

2. Log in to your server as `erxes` via `ssh`.

3. Edit `/home/erxes/erxes/ui/build/js/env.js` file where env vars for frontend app are stored.
   The content of the file should be as follows:

   ```javascript
   window.env = {
     PORT: 3000,
     NODE_ENV: "production",
     REACT_APP_API_URL: "http://your_domain/api",
     REACT_APP_API_SUBSCRIPTION_URL: "ws://your_domain/api/subscriptions",
     REACT_APP_CDN_HOST: "http://your_domain/widgets",
   };
   ```

4. Update all env vars with HTTP url in the `/home/erxes/ecosystem.json` file.

5. Now, you need to restart pm2 erxes processes by running the following command:

   ```sh
   pm2 restart ecosystem.json
   ```

6. Switch to `root` user and edit your nginx config file located in `/etc/nginx/sites-available/default` and replace `server_name` with your domain.

7. Lastly reload your nginx service by running `systemctl reload nginx`

Now you can use erxes with your own domain.
