# Docker-drupal
# Drupal Docker Development Environment

This project provides a Docker-based development environment for Drupal using `docker-compose`. It includes services for Apache, MySQL 8, phpMyAdmin, MailHog, and Varnish.

## 📦 Services Included

### 1. **Apache (`www`)**
- Serves the Drupal site using Apache.
- Configured with Xdebug support (can be disabled via `XDEBUG_MODES=off`).
- Customizable using Docker build args.
- Mounts your Drupal project directory (`./www`) into the container.

### 2. **MySQL 8 (`db`)**
- MySQL 8 configured with native authentication plugin.
- Automatically creates a database `drupal` and user `user` with password `test`.
- Mounts:
  - `./dump` → Used to initialize the database on first run via `.sql` scripts.
  - `persistent` volume → Stores actual MySQL data.
  - `./containers/mysql` → MySQL configuration files.

### 3. **phpMyAdmin**
- Accessible at `http://localhost:8000`.
- Connects to the MySQL `db` container for visual DB management.

### 4. **MailHog**
- Captures all outbound emails sent from the Drupal site.
- SMTP on `localhost:1025`, Web UI at `http://localhost:8025`.

### 5. **Varnish**
- A caching layer in front of Apache (port `8080`).
- Uses custom VCL config from `./containers/varnish/default.vcl`.

---

## 🛠 Configuration

### Environment Variables (for Apache)

- `REMOTE_HOST`: IP of your host machine (default: `host.docker.internal`).
- `REMOTE_PORT`: Port for Xdebug (default: `9003`).
- `IDE_KEY`: IDE filter key for Xdebug (e.g., `VSCODE`).
- `APP_FOLDER`: Drupal web root directory (`web`).
- `APP_CRON`: Enable or disable cron (default: `disable`).
- `APP_MEMCACHED`: Enable or disable memcached (default: `enable`).

---

## 🚀 Quick Start

1. **Build and start the containers:**

```bash
docker-compose up -d --build
