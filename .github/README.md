# adguardhome_munin_

Munin plugins for monitoring various AdGuardHome statistics.

---

## Overview

`adguardhome_munin_` is a POSIX shell Munin wildcard plugin, providing a suite of metrics for monitoring AdGuardHome instances.

## Features

- Track top blocked domains, clients, and queried domains
- Monitor DNS query counts, blocked percentages, and processing times
- Graph upstream server usage and average response times
- Automatic detection of AdGuardHome presence for plugin suitability
- Handles empty data gracefully for robust Munin graphing

---

## Requirements

- [Munin](http://munin-monitoring.org/)
    `munin`, `munin-node` (not necessarily on the same host as each other)
- [AdGuardHome](https://adguard.com)
    AdGuardHome running and accessible via its API (not necessarily on the same host as Munin)
- [curl](https://curl.se/), [jq](https://jqlang.org/), and [mktemp](https://www.gnu.org/software/coreutils/manual/html_node/mktemp-invocation.html)
    These are used for API calls, JSON parsing, and temporary file handling.

---

## Install

1. Clone this repository
    ```sh
        # Clone the repository containing the plugin
        git clone https://github.com/saint-lascivious/adguardhome_munin_.git
        # Navigate to the cloned directory
        cd adguardhome_munin_
    ```
2. Copy `adguardhome_munin_` to your Munin plugins directory (usually `/usr/share/munin/plugins/`).
    ```sh
        # Copy the plugin script to the Munin plugins directory
        cp adguardhome_munin_ /usr/share/munin/plugins/
        # Make sure the script is executable
        chmod +x /usr/share/munin/plugins/adguardhome_munin_
    ```
3. Create symlinks in `/etc/munin/plugins/` for each plugins you wish to enable

    ```sh
        # Create symbolic links for each plugin you want to enable
        ln -s /usr/share/munin/plugins/adguardhome_munin_ \
            /etc/munin/plugins/adguardhome_munin_blocked
        ln -s /usr/share/munin/plugins/adguardhome_munin_ \
            /etc/munin/plugins/adguardhome_munin_clients
        ln -s /usr/share/munin/plugins/adguardhome_munin_ \
            /etc/munin/plugins/adguardhome_munin_domains
        # ...repeat for other plugins as desired
    ```

    The full list of plugins is: `adguardhome_munin_blocked`, `adguardhome_munin_clients`, `adguardhome_munin_domains`, `adguardhome_munin_percent`, `adguardhome_munin_processing`, `adguardhome_munin_queries`, `adguardhome_munin_status`, `adguardhome_munin_upstreams` and `adguardhome_munin_upstreams_avg`.

4. Restart the Munin node
    ```sh
        # Restart the Munin node service to apply changes
        # This command may vary based on your system's init system
        systemctl restart munin-node
    ```

5. …Wait
    Wait, around five minutes.

    Munin nodes run their plugins periodically, by default at 5 minute intervals past the hour.
    After the first run, you should see the new plugins in your Munin web interface.

---

## Configuration

Set the following environment variables as needed (defaults shown):

- `proto`
    - Protocol to use for AdGuardHome API requests.
    - Can be set to `http` if your AdGuardHome instance does not use HTTPS.
    - Default: `https`
- `host`
    - This is the IP address or hostname of your AdGuardHome instance.
    - Default: `127.0.0.1`
- `port`
    - The port on which your AdGuardHome instance is running.
    - Default: `443` (use `80` for HTTP).
- `username`
    - Required.
    - The username for AdGuardHome API authentication.
    - Default: `""` (empty).
- `password`
    - Required.
    - The password for AdGuardHome API authentication.
    - Default: `""` (empty).
- `top_n`
    - The number of top items to display in graphs (e.g., top blocked domains, clients, etc.).
    - Large values may degrade performance.
    - Maximum `100`, minimum `1` (greater or lower values will be adjusted to these limits).
    - Default: `10`

Example for `/etc/munin/plugin-conf.d/adguardhome_munin_`:

```ini
[adguardhome_munin_*]
    # These fields are required
    env.username your_agh_username
    env.password your_agh_password

    # These fields are optional, unless you want to override the defaults
    # as shown below
    env.proto http
    env.host 192.168.1.123
    env.port 80
    env.top_n 20
```

---

## Usage

Each plugin can be run manually for testing:
```sh
    # Generate plugin configuration
    munin-run adguardhome_munin_blocked config
    # Fetch data for the plugin
    munin-run adguardhome_munin_blocked fetch
```

Older versions of Munin may not recognise the `fetch` command, in which case it should be omitted and the plugin should be run without arguments in order to fetch data:

```sh
    # Generate plugin configuration
    munin-run adguardhome_munin_blocked config
    # Fetch data for the plugin
    munin-run adguardhome_munin_blocked
```

---

## Compatibility
- AdGuardHome >= v0.107.0
- Munin >= 2.0

---

## License

This project is licensed under the [GNU GPL v3](https://www.gnu.org/licenses/gpl-3.0.html).

---

<p align="right"><sup><sub>© 2025, saint-lascivious (Hayden Pearce)</sub></sup></p>