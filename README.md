# FullStackLessonPlanApp

Full stack solution for WSPA Lesson Plan Wrapper

## Prerequisites

*   [Docker](https://docs.docker.com/get-docker/)
*   [Docker Compose](https://docs.docker.com/compose/install/)

## Deployment Instructions

Follow these steps to deploy the application using Docker Compose:

1.  **Prepare Files:**
    *   Ensure the following two files are present in the deployment folder:
        *   `docker-compose.yml` (provided in the repository)
        *   `.env` (created from `.env.template`)

2.  **Create and Configure `.env`:**
    *   Copy the template file:
        ```bash
        cp .env.template .env
        ```
    *   Edit the `.env` file and provide values for **at least** the following variables:
        *   `EMAIL`: Your login email for `puw.wspa.pl` (must have access to lesson plans).
        *   `PASSWORD`: Your password for `puw.wspa.pl`.
    *   Review other variables in `.env` and adjust if necessary (see comments in the file for details).

3.  **Configure `docker-compose.yml`:**
    *   **Set WG_HOST:**
        *   Locate the `wg-easy` service definition in `docker-compose.yml`.
        *   Find the line `WG_HOST= PUBLIC_IP`.
        *   Replace `PUBLIC_IP` with the external IP address of the server where you are deploying the application.
    *   **Generate and Set PASSWORD_HASH:**
        *   Generate a password hash for the WireGuard UI access using the following Docker command (replace `'YOUR_PASSWORD'` with a strong password):
            ```bash
            docker run --rm -it ghcr.io/wg-easy/wg-easy wgpw 'YOUR_PASSWORD'
            ```
        *   The command will output a hash string similar to `$2a$12$...`.
        *   **Important:** Before pasting the hash into `docker-compose.yml`, you **must double every dollar sign (`$`)**.
          *   Example Input Hash: `$2a$12$y06bZ7CSwDw1b9FQ1s45reKwKyDkAvjAmQfUHYhcaQ2qgMPxE0N`
          *   Example Value for `docker-compose.yml`: `$$2a$$12$$y06bZ7CSwDw1b9FQ1s45reKwKyDkAvjAmQfUHYhcaQ2qgMPxE0N`
        *   Locate the line `PASSWORD_HASH= PASSWORD_HASH` in the `wg-easy` service definition.
        *   Replace `PASSWORD_HASH` with your correctly escaped hash.
    *   **Other variables in `docker-compose.yml` should generally remain unchanged.**

4.  **Understand Port Usage:**
    The application stack uses the following ports. Ensure they are not already in use and are allowed through your firewall:
    *   **`81` (TCP):** Main frontend application access. Configured in `docker-compose.yml` around line 70 (`ports: - "81:5000"`).
    *   **`51820` (UDP):** WireGuard VPN connection port. Configured in `docker-compose.yml` around lines 13 and 21.
    *   **`51821` (TCP):** WireGuard UI (wg-easy) web panel for managing VPN clients. Configured in `docker-compose.yml` around lines 14 and 22.
        *   **Security Recommendation:** After generating the necessary VPN configuration files for users via the panel at `http://<SERVER_IP>:51821`, consider commenting out or removing the port mapping (`- "51821:51821"`) in `docker-compose.yml` and restarting the stack (`docker-compose up -d --force-recreate`) to disable external access to the panel.

5.  **Start the Application Stack:**
    *   Navigate to the directory containing `docker-compose.yml` and `.env` in your terminal.
    *   Run the following command to build images (if necessary) and start all services in the background:
        ```bash
        docker-compose up -d
        ```

6.  **Access Services:**
    *   Frontend Application: `http://<SERVER_IP>:81`
    *   WireGuard UI (if enabled): `http://<SERVER_IP>:51821`

## Stopping and Logs

*   To stop all services:
    ```bash
    docker-compose down
    ```
*   To view logs for all services:
    ```bash
    docker-compose logs -f
    ```
*   To view logs for a specific service (e.g., backend):
    ```bash
    docker-compose logs -f backend
    ```

## Services

The `docker-compose.yml` file defines the following services:

*   `wg-easy`: WireGuard VPN server and management UI.
*   `backend`: The main application backend service.
*   `mongodb`: MongoDB database service.
*   `frontend`: The web frontend service.


## License

Ten projekt udostępniany jest na licencji [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/deed.pl)  
Możesz używać, kopiować i udostępniać ten kod wyłącznie w celach niekomercyjnych.  
Wykorzystanie komercyjne (w tym sprzedaż, wdrażanie w firmach, oferowanie jako usługa) jest zabronione.
