A Docker Wordpress development environment by the team at [Visible](https://visible.vc/) and some awesome [contributors](https://github.com/visiblevc/wordpress-starter/graphs/contributors). [See the original repository](https://github.com/visiblevc/wordpress-starter).

# Quickstart

## Running the services

#### 1. Init project. 

The following lines will clone this repo without git history. Feel free to change the project name in the command (`my-wordpress-project`)

```sh
# Project's name
WPNAME=my-wordpress-project

# Clone the repo
git clone --depth=1 https://github.com/pataiadam/wordpress-starter.git $WPNAME

# Remove the .git directory
cd $WPNAME && rm -rf .git
```

Or just clone the whole repository.

#### 2. Configure (optional)

You can change passwords, add default plugins and/or themes. See the *Options* section in this README.

#### 3. Run
```sh
docker-compose up -d && docker-compose logs -f wordpress
```

#### 4. Visit wordpress site

When the build is finished, hit <kbd>ctrl</kbd>-<kbd>c</kbd> to detach from the logs.
 
Visit `localhost:8080` in your browser. Default admin login (you can change the password in `docker-compose.yml`):

```text
site:           http://localhost:8080/wp-admin
email:          admin@wordpress.com
password:       27DLZd5uckTg2J23
```

## Working with Databases

If you have an exported `.sql` file from an existing website, drop the file into the `data/` folder.
The first time you run the container, it will detect the SQL dump and use it as a database. If it
doesn't find one, it will create a fresh database.

If the SQL dump changes for some reason, you can reload the database by running:

```sh
docker-compose exec wordpress /bin/bash -c 'wp db import $(find /data/*.sql | head -n 1) --allow-root'
```

If you want to create a dump of your development database, you can run:

```sh
docker-compose exec wordpress /bin/bash -c 'wp db export /data/dump.sql --allow-root'
```

## Plugins / themes

To install plugins/themes, use the comma-separated PLUGINS / THEMES environment variables in docker-compose.yml

If you have an own plugins/themes then put them into the plugins/themes directory and update the volumes in docker-compose.yml

Example: you created an `awesome` plugin directory in the plugins folder, then add the following line to the wordperss's volumes:

```yaml
services:
  wordpress:
    volumes:
     - ./plugins/awesome:/app/wp-content/plugins/awesome
```

## Options

### Default Database Credentials

| Credential        | Value                  | Notes                                                                                              |
| ----------------- | ---------------------- | -------------------------------------------------------------------------------------------------- |
| **Hostname**      | `db`                   | Can be changed with the `DB_HOST` environment variable **NOTE:**: Must match database service name |
| **Username**      | `root`                 |                                                                                                    |
| **Password**      |                        | Must be set using the `DB_PASS` environment variable                                               |
| **Database Name** | `wordpress`            | Can be changed with the `DB_NAME` environment variable                                             |
| **Admin Email**   | `admin@${DB_NAME}.com` |                                                                                                    |

### Service Environment Variables

**Notes:**

* Variables marked with ✅ are required
* Single quotes must surround `boolean` environment variables

#### `wordpress`

| Variable           | Default Value                    | Description                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ------------------ | -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DB_USER`          | `root`                           | Username for both the database and the WordPress installation (if not importing existing)                                                                                                                                                                                                                                                                                                                                                |
| `DB_PASS`✅        |                                  | Password for the database. Value must match `MYSQL_ROOT_PASSWORD` set in the `db` service                                                                                                                                                                                                                                                                                                                                                |
| `DB_HOST`          | `db`                             | Hostname for the database                                                                                                                                                                                                                                                                                                                                                                                                                |
| `DB_NAME`          | `wordpress`                      | Name of the database                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `DB_PREFIX`        | `wp_`                            | Prefix for the database                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `SERVER_NAME`      | `localhost`                      | Set this to `<your-domain-name>.<your-domain-extension>` if you plan on obtaining SSL certificates                                                                                                                                                                                                                                                                                                                                       |
| `ADMIN_EMAIL`      | `admin@${DB_NAME}.com`           | Administrator email address                                                                                                                                                                                                                                                                                                                                                                                                              |
| `WP_DEBUG`         | `'false'`                        | [Click here](https://codex.wordpress.org/WP_DEBUG) for more information                                                                                                                                                                                                                                                                                                                                                                  |
| `WP_DEBUG_DISPLAY` | `'false'`                        | [Click here](https://codex.wordpress.org/WP_DEBUG#WP_DEBUG_DISPLAY) for more information                                                                                                                                                                                                                                                                                                                                                 |
| `WP_DEBUG_LOG`     | `'false'`                        | [Click here](https://codex.wordpress.org/WP_DEBUG#WP_DEBUG_LOG) for more information                                                                                                                                                                                                                                                                                                                                                     |
| `WP_VERSION`       | `latest`                         | Specify the WordPress version to install. Accepts any valid semver number, `latest`, or `nightly` for beta builds.                                                                                                                                                                                                                                                                                                                       |
| `THEMES`           |                                  | Comma-separated list of themes you want to install in either of the following forms<ul><li>`theme-slug`: Used when installing theme direct from WordPress.org</li><li>`[theme-slug]https://themesite.com/theme.zip`: Used when installing theme from URL</li></ul>                                                                                                                                                                       |
| `PLUGINS`          |                                  | Comma-separated list of plugins you want to install in either of the following forms:<ul><li>`plugin-slug`: Used when installing plugin direct from WordPress.org.</li><li>`[plugin-slug]http://pluginsite.com/plugin.zip`: Used when installing plugin from URL.</li></ul>                                                                                                                                                              |
| `MULTISITE`        | `'false'`                        | Set to `'true'` to enable multisite                                                                                                                                                                                                                                                                                                                                                                                                      |
| `PERMALINKS`       | `/%year%/%monthnum%/%postname%/` | A valid WordPress permalink [structure tag](https://codex.wordpress.org/Using_Permalinks#Structure_Tags)                                                                                                                                                                                                                                                                                                                                 |
| `URL_REPLACE`      |                                  | Comma-separated string in the form of `current-url,replacement-url`<ul><li>When defined, `current-url` will be replaced with `replacement-url` on build (useful for development environments utilizing a database copied from a live site)<li>**Note:** If you are running Docker using Docker Machine, your replacement url MUST be the output of the following command: `echo $(docker-machine ip <your-machine-name>):8080`</li></ul> |

#### `db`

| Variable                | Default Value | Description                                     |
| ----------------------- | ------------- | ----------------------------------------------- |
| `MYSQL_ROOT_PASSWORD`✅ |               | Must match `DB_PASS` of the `wordpress` service |
