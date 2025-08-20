# adguardhome_munin_

[Munin](https://munin-monitoring.org) [plugins](https://gallery.munin-monitoring.org) for monitoring various [AdGuard Home](https://github.com/AdguardTeam/AdGuardHome) statistics.

---

- [Overview](#overview)
- [Features](#features)
- [Requirements](#requirements)
- [Install](#install)
- [Configuration](#configuration)
- [Usage](#usage)
- [Uninstall](#uninstall)
- [Compatibility](#compatibility)
- [Example Graph Gallery](#example-graph-gallery)
    - [blocked](#blocked)
    - [clients](#clients)
    - [domains](#domains)
    - [percent](#percent)
    - [processing](#processing)
    - [queries](#queries)
    - [status](#status)
    - [upstreams](#upstreams)
    - [upstreams_avg](#upstreams_avg)
- [Related Projects](#related-projects)
    - [pihole_munin_](#pihole_munin_)
- [License](#license)

---

## Overview

`adguardhome_munin_` is a POSIX shell Munin wildcard plugin, providing a suite of graphs for monitoring AdGuard Home instances.

It supports multiple graphs, each tracking different aspects of AdGuard Home's performance and statistics, such as blocked domains, clients, queried domains, DNS query counts, blocked percentages, processing times, upstream server usage, and average response times.

---

## Features

- Track the top N blocked domains, clients, and queried domains
- Monitor DNS query counts, blocked percentages, and processing times
- Graph upstream server usage and average response times
- Display AdGuard Home's protection status
- Handles empty data gracefully
- State files persist dynamic data (such as top N graph items)

---

## Requirements

- [Munin](http://munin-monitoring.org/)
    `munin` and `munin-node` configured and running (not necessarily on the same host as each other)
- [AdGuard Home](https://github.com/AdguardTeam/AdGuardHome)
    `AdGuardHome` configured, running and accessible via its API (not necessarily on the same host as Munin)
- [curl](https://curl.se/), [jq](https://jqlang.org/), and [mktemp](https://www.gnu.org/software/coreutils/manual/html_node/mktemp-invocation.html) installed on the system where the plugin is run.
    These are used for API calls, JSON parsing, and temporary file handling respectively.

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

    The full list of plugins is:
    `adguardhome_munin_blocked`, `adguardhome_munin_clients`, `adguardhome_munin_domains`, `adguardhome_munin_percent`, `adguardhome_munin_processing`, `adguardhome_munin_queries`, `adguardhome_munin_status`, `adguardhome_munin_upstreams` and `adguardhome_munin_upstreams_avg`.

    You can symlink all of them at once with:
    ```sh
        # Create symbolic links for all plugins at once
        for plugin in blocked clients domains percent processing queries status upstreams upstreams_avg; do
            ln -s /usr/share/munin/plugins/adguardhome_munin_ \
                /etc/munin/plugins/adguardhome_munin_"${plugin}"
        done
    ```

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
    - Protocol to use for AdGuard Home API requests.
    - Can be set to `https` if your AdGuard Home instance has HTTPS enabled and a valid SSL certificate.
    - Default: `http`
- `host`
    - This is the IP address or hostname of your AdGuard Home instance.
    - Default: `127.0.0.1`
- `port`
    - The port on which your AdGuard Home instance is running.
    - Default: `80` (use `443` for HTTP).
- `username`
    - Required.
    - The username for AdGuard Home API authentication.
    - Default: `""` (empty).
- `password`
    - Required.
    - The password for AdGuard Home API authentication.
    - Default: `""` (empty).
- `top_n`
    - The number of top items to display in graphs (e.g., top blocked domains, clients, etc.).
    - Large values may degrade performance.
    - Maximum `20`, minimum `1` (greater or lower values will be adjusted to these limits).
    - Default: `10`

Example for `/etc/munin/plugin-conf.d/adguardhome_munin_`:

```ini
[adguardhome_munin_*]
    # These fields are required
    env.username your_agh_username
    env.password your_agh_password

    # These fields are optional, unless you want to override the defaults
    # as shown below

    # Protocol to use for AdGuard Home API requests
    env.proto https
    # IP address or hostname of your AdGuard Home instance
    env.host 192.168.1.123
    # Use `https` if your AdGuard Home instance has HTTPS enabled
    # and a valid SSL certificate.
    env.port 443
    # Set the number of top items to display in graphs
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

## Uninstall

1. Remove plugin symlinks from `/etc/munin/plugins/`

    ```sh
        # Remove all symbolic links for the plugin
        rm /etc/munin/plugins/adguardhome_munin_*
    ```

2. Remove the plugin script from `/usr/share/munin/plugins/`

    ```sh
        # Remove the plugin script
        rm /usr/share/munin/plugins/adguardhome_munin_
    ```

3. Restart the Munin node

    ```sh
        # Restart the Munin node service to apply changes
        # This command may vary based on your system's init system
        systemctl restart munin-node
    ```

---

## Compatibility
- AdGuard Home >= v0.107.0
- Munin >= 2.0

---

## Example Graph Gallery

### blocked
<p align="center">
    <a href="https://github.com/saint-lascivious/adguardhome_munin_">
        <img src="https://raw.githubusercontent.com/saint-lascivious/adguardhome_munin_/master/images/gallery/adguardhome_munin_blocked-day.png"
             alt="This graph shows the top N AdGuard Home blocked domains."
             style="max-width: 1000px; width: 100%; height: auto;" />
    </a>
</p>

This graph shows the top N AdGuard Home blocked domains.

### clients
<p align="center">
    <a href="https://github.com/saint-lascivious/adguardhome_munin_">
        <img src="https://raw.githubusercontent.com/saint-lascivious/adguardhome_munin_/master/images/gallery/adguardhome_munin_clients-day.png"
             alt="This graph shows the top N AdGuard Home clients."
             style="max-width: 1000px; width: 100%; height: auto;" />
    </a>
</p>

This graph shows the top N AdGuard Home clients.

### domains
<p align="center">
    <a href="https://github.com/saint-lascivious/adguardhome_munin_">
        <img src="https://raw.githubusercontent.com/saint-lascivious/adguardhome_munin_/master/images/gallery/adguardhome_munin_domains-day.png"
             alt="This graph shows the top N AdGuard Home domains."
             style="max-width: 1000px; width: 100%; height: auto;" />
    </a>
</p>

This graph shows the top N AdGuard Home domains.

### percent
<p align="center">
    <a href="https://github.com/saint-lascivious/adguardhome_munin_">
        <img src="https://raw.githubusercontent.com/saint-lascivious/adguardhome_munin_/master/images/gallery/adguardhome_munin_percent-day.png"
             alt="This graph shows AdGuard Home's blocked query percentage."
             style="max-width: 1000px; width: 100%; height: auto;" />
    </a>
</p>

This graph shows AdGuard Home's blocked query percentage.

### processing
<p align="center">
    <a href="https://github.com/saint-lascivious/adguardhome_munin_">
        <img src="https://raw.githubusercontent.com/saint-lascivious/adguardhome_munin_/master/images/gallery/adguardhome_munin_processing-day.png"
             alt="This graph shows AdGuard Home's average DNS processing time."
             style="max-width: 1000px; width: 100%; height: auto;" />
    </a>
</p>

This graph shows AdGuard Home's average DNS processing time.

### queries
<p align="center">
    <a href="https://github.com/saint-lascivious/adguardhome_munin_">
        <img src="https://raw.githubusercontent.com/saint-lascivious/adguardhome_munin_/master/images/gallery/adguardhome_munin_queries-day.png"
             alt="This graph shows AdGuard Home's DNS query statistics."
             style="max-width: 1000px; width: 100%; height: auto;" />
    </a>
</p>

This graph shows AdGuard Home's DNS query statistics.

### status
<p align="center">
    <a href="https://github.com/saint-lascivious/adguardhome_munin_">
        <img src="https://raw.githubusercontent.com/saint-lascivious/adguardhome_munin_/master/images/gallery/adguardhome_munin_status-day.png"
             alt="This graph shows AdGuard Home's protection status."
             style="max-width: 1000px; width: 100%; height: auto;" />
    </a>
</p>

This graph shows AdGuard Home's protection status.

### upstreams
<p align="center">
    <a href="https://github.com/saint-lascivious/adguardhome_munin_">
        <img src="https://raw.githubusercontent.com/saint-lascivious/adguardhome_munin_/master/images/gallery/adguardhome_munin_upstreams-day.png"
             alt="This graph shows the top N AdGuard Home upstreams."
             style="max-width: 1000px; width: 100%; height: auto;" />
    </a>
</p>

This graph shows the top N AdGuard Home upstreams.

### upstreams_avg
<p align="center">
    <a href="https://github.com/saint-lascivious/adguardhome_munin_">
        <img src="https://raw.githubusercontent.com/saint-lascivious/adguardhome_munin_/master/images/gallery/adguardhome_munin_upstreams_avg-day.png"
             alt="This graph shows average response time for the top N AdGuard Home upstreams."
             style="max-width: 1000px; width: 100%; height: auto;" />
    </a>
</p>

This graph shows average response time for the top N AdGuard Home upstreams.

---

## Related Projects

### pihole_munin_
- [pihole_munin_](https://github.com/saint-lascivious/pihole_munin_)

A Munin plugin for monitoring Pi-hole statistics, similar to `adguardhome_munin_` but tailored for Pi-hole instances.

---

## License

This project is licensed under the [GNU GPL v3](https://www.gnu.org/licenses/gpl-3.0.html).

---

<p align="right"><sup><sub>© 2025, saint-lascivious (Hayden Pearce)</sub></sup></p>
