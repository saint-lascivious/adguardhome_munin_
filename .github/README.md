# adguardhome_munin_

Munin plugins for monitoring various AdGuardHome statistics.

---

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

## Installation

1. Clone this repository:
    ```sh
    git clone https://github.com/saint-lascivious/adguardhome_munin_.git
    ```
2. Copy `adguardhome_munin_` to your Munin plugins directory (usually `/usr/share/munin/plugins/`).
    ```sh
    # Navigate to the cloned directory
    cd adguardhome_munin_
    # Copy the plugin script to the Munin plugins directory
    cp adguardhome_munin_ /usr/share/munin/plugins/
    # Make sure the script is executable
    chmod +x /usr/share/munin/plugins/adguardhome_munin_
    ```
3. Create symlinks in `/etc/munin/plugins/` for each plugins you wish to enable:

    ```sh
    ln -s /usr/share/munin/plugins/adguardhome_munin_ /etc/munin/plugins/adguardhome_munin_blocked
    ln -s /usr/share/munin/plugins/adguardhome_munin_ /etc/munin/plugins/adguardhome_munin_clients
    ln -s /usr/share/munin/plugins/adguardhome_munin_ /etc/munin/plugins/adguardhome_munin_domains
    # ...repeat for other plugins as needed
    ```

    The full list of plugins is:
    `adguardhome_munin_blocked`, `adguardhome_munin_clients`, `adguardhome_munin_domains`, `adguardhome_munin_percent`, `adguardhome_munin_processing`, `adguardhome_munin_queries`, `adguardhome_munin_status`, `adguardhome_munin_upstreams` and `adguardhome_munin_upstreams_avg`.

4. Restart the Munin node:
    ```sh
    systemctl restart munin-node
    ```

---

## Configuration

Set the following environment variables as needed (defaults shown):

- `proto` (default: `https`)
- `host` (default: `localhost`)
- `port` (default: `443`)
- `username` and `password` for AdGuardHome API authentication
- `top_n` (default: `20`, for top N items in graphs)

Example for `/etc/munin/plugin-conf.d/adguardhome_munin_`:
```
[adguardhome_munin_*]
env.proto http
env.host 192.168.1.123
env.port 80
env.username your_agh_username
env.password your_agh_password
env.top_n 10
```

---

## Usage

Each plugin can be run manually for testing:
```sh
munin-run adguardhome_munin_blocked config
munin-run adguardhome_munin_blocked fetch
```

---

## Compatibility
- Munin Version Compatible with Munin >= 2.0.

---

## License

This project is licensed under the [GNU GPL v3](https://www.gnu.org/licenses/gpl-3.0.html).

---

<p align="right"><sup><sub>© 2025, saint-lascivious (Hayden Pearce)</sub></sup></p>