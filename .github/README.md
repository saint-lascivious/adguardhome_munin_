# adguardhome_munin_

Munin plugins for monitoring various AdGuardHome statistics.

## Features

- Track top blocked domains, clients, and queried domains
- Monitor DNS query counts, blocked percentages, and processing times
- Graph upstream server usage and average response times
- Automatic detection of AdGuardHome presence for plugin suitability
- Handles empty data gracefully for robust Munin graphing

## Requirements

- [Munin](http://munin-monitoring.org/)
- [AdGuardHome](https://adguard.com/en/adguard-home/overview.html) running and accessible via its API
- `curl`, `jq`, and `mktemp` installed on the host

## Installation

1. Clone this repository:
    ```sh
    git clone https://github.com/saint-lascivious/adguardhome_munin_.git
    ```
2. Copy the desired plugin scripts `adguardhome_munin_` to your Munin plugins directory (usually `/usr/share/munin/plugins/`).
    ```sh
    cp adguardhome_munin_ /usr/share/munin/plugins/
    ```
    Make sure the script is executable:
    ```sh
    chmod +x /usr/share/munin/plugins/adguardhome_munin_
    ```
3. Create symlinks in `/etc/munin/plugins/` for each plugin you wish to enable:

    ```sh
    ln -s /usr/share/munin/plugins/adguardhome_munin_ /etc/munin/plugins/adguardhome_munin_blocked
    ln -s /usr/share/munin/plugins/adguardhome_munin_ /etc/munin/plugins/adguardhome_munin_clients
    ln -s /usr/share/munin/plugins/adguardhome_munin_ /etc/munin/plugins/adguardhome_munin_domains
    ```

    The full list of plugins can be found below.

4. Restart the Munin node:
    ```sh
    systemctl restart munin-node
    ```

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

## Usage

Each plugin can be run manually for testing:
```sh
munin-run adguardhome_munin_blocked config
munin-run adguardhome_munin_blocked fetch
```

## Plugins

- `blocked` – Top blocked domains
- `clients` – Top clients by query count
- `domains` – Top queried domains
- `percent` – Percentage of blocked queries
- `processing` – Average DNS processing time
- `queries` – DNS query statistics
- `status` – Protection status
- `upstreams` – Upstream server usage
- `upstreams_avg` – Upstream average response time

## License

This project is licensed under the [GNU GPL v3](https://www.gnu.org/licenses/gpl-3.0.html).

---

<p align="right"><sup><sub>© 2025, saint-lascivious (Hayden Pearce)</sub></sup></p>