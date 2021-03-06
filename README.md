# ![](/docs/icon.png) Vue-dom

[![license: AGPLv3](https://img.shields.io/badge/license-AGPLv3-blue.svg)](https://www.gnu.org/licenses/agpl-3.0)
[![GitHub release](https://img.shields.io/github/release/nioc/vue-dom.svg)](https://github.com/nioc/vue-dom/releases/latest)
[![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/nioc/vue-dom)](https://hub.docker.com/r/nioc/vue-dom/builds)

Vue-dom is a web front end for home automation software (currently only support [Jeedom](https://www.jeedom.com)).

## Key features
-    display rooms with summary,
-    display room with logical equipments,
-    display logical equipments by tags,
-    display info statistics (min, average and max) and history chart,
-    display and start scenarios,
-    display system notifications,
-    ask query (interaction),
-    user authentication with login and password (not stored in application),
-    communicate with your back-end software through JSON-RPC API and [websocket](https://github.com/nioc/jeedom-websocket), authorized with user API key,
-    reduce data traffic,
-    responsive,
-    progressive web app (offline access).

Check out the [live demo](https://nioc.github.io/vue-dom/) (since using mock data, there is no effective action and inconsistencies may occur).

## Installation

### As a local Apache/Nginx virtual host

For basic use, you just have to:
-    download [latest release](https://github.com/nioc/vue-dom/releases/latest) archive,
-    unarchive in `/var/www/vue-dom/`,
-    add following lines in Apache virtual hosts `/etc/apache2/sites-enabled/default-ssl.conf` and `/etc/apache2/sites-enabled/000-default.conf`, inside `<VirtualHost>` section:
      ``` conf
              alias /vue-dom/ /var/www/vue-dom/
              <Directory /var/www/vue-dom>
                      Options -Indexes -FollowSymLinks -MultiViews -ExecCGI
                      AllowOverride none
                      Order allow,deny
                      allow from all
              </Directory>
      ```
-   set your back end (Jeedom) url in `/var/www/vue-dom/local.js`.

### As docker container

If you are using Docker, you can pull the [Docker image](https://hub.docker.com/r/nioc/vue-dom) and run with your own url:
```
docker run -p 80:80 --rm \
-e TITLE="VueDom - Home" \
-e PROVIDER="system: 'jeedom', jsonRpcApiUrl: 'https://192.168.1.50/core/api/jeeApi.php', websocketUrl: 'wss://192.168.1.50/socket/', statisticsPeriod: 86400000, trendPeriod: 7200000, trendThreshold: 0.1," \
-e COMPONENTS="SynologyRouterManager: 'SynologyRouterManager', NetatmoSecurity: 'NetatmoSecurity'," \
--name vue-dom-1 \
nioc/vue-dom:latest
```

Or run service in a `docker-compose.yml` file:
``` yml
version: "3.4"
services:
  vue-dom:
    image: nioc/vue-dom:latest
    ports:
      - "80:80"
    environment:
      TITLE: VueDom - Home
      PROVIDER: "system: 'jeedom', jsonRpcApiUrl: 'https://192.168.1.50/core/api/jeeApi.php', websocketUrl: 'wss://192.168.1.50/socket/', statisticsPeriod: 86400000, trendPeriod: 7200000, trendThreshold: 0.1,"
      COMPONENTS: "SynologyRouterManager: 'SynologyRouterManager', NetatmoSecurity: 'NetatmoSecurity',"
```

For more advanced use (adding your own component, style, ...), you have to follow the [contributing guide](CONTRIBUTING.md) and edit Vue code.

## Versioning

Vue-dom is maintained under the [semantic versioning](https://semver.org/) guidelines.

See the [releases](https://github.com/nioc/vue-dom/releases) on this repository for changelog.

## Contributing

If you have a suggestion for a feature you think would enhance this product, please submit a [feature request](https://github.com/nioc/vue-dom/issues/new?labels=enhancement&template=feature_request.md).
Pull requests are welcomed. See [contributing](CONTRIBUTING.md).

## Credits

* **[Nioc](https://github.com/nioc/)** - *Initial work*

See also the list of [contributors](https://github.com/nioc/vue-dom/contributors) to this project.

This project is powered by the following components:
- [VueJS](https://vuejs.org/) (MIT)
- [Bulma](https://bulma.io/) (MIT)
- [Buefy](https://buefy.github.io) (MIT)
- [Font Awesome](https://github.com/FortAwesome/Font-Awesome/) (Font Awesome Free License)
- [axios](https://github.com/axios/axios ) (MIT)
- [normalizr](https://github.com/paularmstrong/normalizr) (MIT)
- [Chart.js](https://www.chartjs.org/) (MIT)
- [vue-chartjs](https://vue-chartjs.org/) (MIT)
- [vuex-persist](https://github.com/championswimmer/vuex-persist) (MIT)
- [Moment.js](https://momentjs.com/) (MIT)
- [vue-moment](https://github.com/brockpetrie/vue-moment) (MIT)

## License

This project is licensed under the GNU Affero General Public License v3.0 - see the [LICENSE](LICENSE.md) file for details
