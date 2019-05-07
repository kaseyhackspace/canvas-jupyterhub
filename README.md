# JupyterHub Enabled LMS

## Setup: JupyterHub (Tested in Mac OSX)
1. Create virtualenv `env` for running sandboxed `jupyterhub`:
   ``` bash
    virtualenv -ppython3 env
   ```
2. Enter `env` environment:
    ``` bash
    source env/bin/activate
   ```
3. Install `jupyterhub` for multiuser-session jupyter notebooks:
   ``` bash
    pip install jupyterhub
   ```
4. Install `jupyterhub-ltiauthenticator` for multiuser-session jupyter notebooks:
   ``` bash
    pip install jupyterhub-ltiauthenticator
   ```
5. Install `nbgrader` for auto-grading jupyter notebooks:
   ``` bash
    pip install git+git://github.com/samhinshaw/nbgrader.git
   ```
6. Install `nbgrader` for pulling jupyter notebooks from git repositories:
   ``` bash
    pip install nbgitpuller
   ```
7. Generate `LTI_CLIENT_KEY` and `LTI_CLIENT_SECRET` then save to `.env`:
   ``` bash
    LTI_CLIENT_KEY=(`openssl rand -hex 32`)
    LTI_CLIENT_SECRET=(`openssl rand -hex 32`)
    echo LTI_CLIENT_KEY=$LTI_CLIENT_KEY > .env
    echo LTI_CLIENT_SECRET=$LTI_CLIENT_SECRET >> .env
    echo export LTI_CLIENT_KEY >> .env
    echo export LTI_CLIENT_SECRET >> .env
    source .env
   ```
8. Generate config file for `jupyterhub`:
   ``` bash
    jupyterhub --generate-config
   ``` 
9. Set `ltiauthenticator` as `jupyterhub`'s authenticator class in `jupyter_config.py` generated above:
    ``` python
    #c.JupyterHub.authenticator_class = 'jupyterhub.auth.PAMAuthenticator'
    import os
    c.JupyterHub.authenticator_class = 'ltiauthenticator.LTIAuthenticator'
    c.LTIAuthenticator.consumers = {
        os.environ['LTI_CLIENT_KEY']: os.environ['LTI_CLIENT_SECRET']
    }
    ```
10. install `configurable-http-proxy` as it is needed by `jupyterhub`:
    ```bash
    sudo npm install -g configurable-http-proxy
    ```
11. Add machine's hostname to `/etc/hosts` :
    `` bash
    sudo echo 127.0.0.1 $(hostname) >> /etc/hosts
    ```
12. Start `jupyterhub` with specified `jupyterhub_config.py`:
    ``` bash
    jupyterhub -f /path/to/jupyterhub_config.py
    ```

## Setup: Canvas
1. Clone `canvas-lms` repository:
   ``` bash
    git clone https://github.com/instructure/canvas-lms
   ```
2. Enter `canvas-lms` directory:
   ``` bash
    cd canvas-lms
   ```
3. Perform quick install script:
   ``` bash
    ./script/docker_dev_setup.sh
   ```

## References:
https://github.com/jupyterhub/ltiauthenticator
https://www.slideshare.net/SamuelHinshaw1/integrating-canvas-jupyterhub
https://github.com/UBC-DSCI/rudaux
https://github.com/instructure/canvas-lms
