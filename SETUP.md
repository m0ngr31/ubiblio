# Prerequesites (Linux):
1. Apache or other webserver set up to reverse-proxy to localhost -- so for example my.domain.com on port 443 redirect to localhost port 8000 where ubiblio runs.
2. redis-server must be installed (this is used for DDoS resistance)
3. gunicorn and uvicorn to launch it (gunicorn recommended for production)
4. sqlite must be installed
5. Around 100MB of memory (for a modest DB and number of users) and a very modest CPU. It will likely run on any computer faster than a potato. Even a raspberry pi is overkill.

NB: Users have reported success running ubiblio on Windows. I just don't know how to use Windows very well. If you would like to contribute setup instructions for Windows or other operating systems, please do so and I will include them.

# Setup (without Docker):
1. I wrote this for Python 3.10.12 (reported working up to Python 3.12). A good first step is to install that to a venv, like so:
   > python3.10 -m venv "ubiblio"
2. Enter the virtual environment with:
   > source ubiblio/bin/activate
3. cd into the directory and clone the github repository with:
   > git clone https://github.com/seanboyce/ubiblio/
4. cd into the newly created folder (also named ubiblio) and install the requirements.
   > pip install -r requirements.txt

## First Time Setup

There are a number of environment variables you can set to slightly change the behavior of ubiblio. You'll need these to create initial user accounts.

## Environment Variables
| Environment Variable | Description | Required? | Default |
|---|---|---|---|
| TOKEN_TTL | How many minutes should a session be valid? | No | 120 |
| CREATE_ADMIN_USER | Should create an admin user when GET `/user-setup` | No | False |
| ADMIN_USERNAME | Admin username to create | No | - |
| ADMIN_PASSWORD | Admin password to create | No | - |
| CREATE_USER | Should create an user when GET `/user-setup` | No | False |
| USER_USERNAME | Username to create | No | - |
| USER_PASSWORD | Password to create | No | - |
| USE_REDIS | Use Redis for DDOS protection? | No | False |
| REDIS_URI | External Redis URI | No | - |

## Launching

There are two supported ways to launch ubiblio -- uvicorn and gunicorn. Both are ridiculously overpowered for such a simple application, but asking why we need such power is a question for philosophers and cowards.

Presently I launch using tmux as a terminal multiplexer. I open up a new tmux instance, run the command, and detatch from it using 'CTRL+B' and then pressing 'd'. Then if I need to see the logs, I just reattach to the terminal. There are surely better ways to do this, but it works fine.

## Launching with Uvicorn

uvicorn is already quite fast and quite capable of supporting a fair number of ubiblio users. From the main ubiblio directory, you can run it with:

> uvicorn ubiblio.main:app --host 0.0.0.0 --port 8000 --reload

Note that this example above does not enable SSL. It will accept all incoming connections on port 8000 -- so unless you have a firewall blocking access, you ought to be able to access it from another machine on your LAN via the IP address. Your browser will likely complain about the lack of HTTPS, but you can ignore the warning in this case (or run it with a certificate as below).

## Launching with Gunicorn and SSL / HTTPS

gunicorn will launch multiple instances of uvicorn as needed to support even more concurrent users. You need to add the paths to your cert files and choose a port. Certs from letsencrypt are just fine :)

> python -m gunicorn ubiblio.main:app -b 127.0.0.1:8000 -k uvicorn.workers.UvicornWorker --certfile= --keyfile=

# Password Recovery

There is no password recovery -- this is by design. I realize this is a little inconvenient, but I wanted something fully self-hosted. Registration / password recovery generally depends on email, which is a pain to host myself.

If you absolutely need to recover a user, you can shut down ubiblio, open up the DB in a sqlite database browser, and manually clear the users out. Then add back the /setup endpoint to recreate the same users with a known password. Or just add new admin users. Don't forget to clear out the code block after. However, it's a lot less work to simply not lose your password!

# Backups:

This is handled by sqlite. Try the command:

> sqlite3 sql_app.db ".backup daily.db"

I've left this in a bash script in daily_backup.sh. You can set it as a cron job (e.g. with crontab -e).

# Docker Deployment

## Environment Variables
| Environment Variable | Description | Required? | Default |
|---|---|---|---|
| TOKEN_TTL | How many minutes should a session be valid? | No | 120 |
| CREATE_ADMIN_USER | Should create an admin user when GET `/user-setup` | No | False |
| ADMIN_USERNAME | Admin username to create | No | - |
| ADMIN_PASSWORD | Admin password to create | No | - |
| CREATE_USER | Should create an user when GET `/user-setup` | No | False |
| USER_USERNAME | Username to create | No | - |
| USER_PASSWORD | Password to create | No | - |
| USE_REDIS | Use Redis for DDOS protection? | No | False |
| REDIS_URI | External Redis URI | No | - |


To build:

```bash
> git clone https://github.com/seanboyce/ubiblio/
> cd ubiblio
> docker build -t ubiblio .
```

To run:

```bash
> docker run -p 8000:8000 -v config_dir:/app/config ubiblio
```

If you run into permissions issues:

```bash
> docker run -p 8000:8000 -v config_dir:/app/config -e PUID=$(id -u $USER) -e PGID=$(id -g $USER) ubiblio
```

Open the service in your web browser at `http://<ip>:8000`

Note: This launches by default with uvicorn and no https support. 

# Useful Guides

For setting up a reverse proxy: https://www.digitalocean.com/community/tutorials/how-to-use-apache-http-server-as-reverse-proxy-using-mod_proxy-extension

A good tutorial on production deployment with venv+fastapi+gunicorn: https://docs.vultr.com/how-to-deploy-fastapi-applications-with-gunicorn-and-nginx-on-ubuntu-20-04

If yo uare getting 'permission denied' errors from Docker after installing docker from the snap store: https://askubuntu.com/questions/941816/permission-denied-when-running-docker-after-installing-it-as-a-snap

# Notes on DDoS Resistance

DDoS resistance is provided by fastapi_limiter. It sets a pretty restrictive rate limit by IP address. It keeps these in a small, fast redis database in-memory. These rate limits are set by endpoint in main.py -- I have attempted to set agressive but reasonable limits, but they are easy to change if you need.

You can still get DDoSed if you put this system online. This just increases the resources required to perform an attack -- e.g. it limits the resources spent dealing with a particular IP address that's bombarding you. So far I've been fine without using any sort of CDN, but there are no guarantees here (short of keeping it on your LAN, I suppose).

This also limits login attempts per IP address per second quite aggressively.
