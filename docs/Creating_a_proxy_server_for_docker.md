# Serving with a proxy server and HTTPS
By default the docker application doesn't come with an https server. It is also likely that you want to serve other applications from your server.
If you want to serve the docker application via https or serve multiple applications via nginx/apache then you will also need a proxy server.

To change the port from which OwnRecipes is served you will need to update the left side of the port configuration in the `docker-prod.override.yml` file.
- Open the docker-prod.override.yml (your docker-compose configuration) file.
- To serve the app via port 7000:
``` yml
version: '3.1'
services:
  nginx:
    ports:
      - "7000:80"
```

- Restart your docker containers

  - `sudo docker compose -f docker-prod.yml -f docker-prod.override.yml -f docker-prod.version.yml restart`
  - OR
  - Just run the `quick-start` script again: `./quick-start`
- Load localhost:7000 to confirm it worked.


It is highly recommended that you serve your content over https. [Let's Encrypt](https://letsencrypt.org/getting-started/) provides a super easy, automated, and free process for serving your content over https. If you are using nginx, they will also automatically update your plain http vhost files.

For more info on how to use proxies, see:
https://www.nginx.com/resources/admin-guide/reverse-proxy/
