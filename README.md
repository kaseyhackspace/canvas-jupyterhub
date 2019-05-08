# JupyterHub Enabled LMS
The purpose of this document is to create a bare-minimum learning management system that can offer interactive computing labs with the use of jupyterhub. From jupyterhub's documentation, jupyterhub can be used to serve sandboxed computing environments to students by the use of the Learning Tools Interoperability (LTI) standard. The LTI was created by MS Global Learning Consortium for connecting external service tools to learning management systems like openEdx, canvas, blackboard, moodle and etc. Officially, jupyterhub has only tested their LTI implementation on openeEdx and canvas. Since Canvas is a lighter system than the openEdx platform, it will be used in order to test the integration of Jupyterhub with an LMS.

There is a commercial software as a service (SAAS) that has an implementation of this, called [IllumiDesk](https://www.illumidesk.com/) with their pricing [here](https://www.illumidesk.com/pricing). For a video of how Illumidesk works, and how this testing environment should work similarly, check out the video [here.](https://vimeo.com/273773906)

## Setup: JupyterHub (Tested in DigitalOcean Droplet - 4GB 2VCPUs)
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
    ``` bash
    sudo echo 127.0.0.1 $(hostname) >> /etc/hosts
    ```
12. Start `jupyterhub` with specified `jupyterhub_config.py`:
    ``` bash
    jupyterhub -f /path/to/jupyterhub_config.py
    ```

## Setup: Canvas-LMS (Tested in DigitalOcean Droplet - 8GB 4VCPUs)
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

## Connecting Canvas-LMS with Jupyterhub (WIP: Canvas LMS Server still configuring)

## References:
- https://github.com/jupyterhub/ltiauthenticator

- https://www.slideshare.net/SamuelHinshaw1/integrating-canvas-jupyterhub

- https://github.com/UBC-DSCI/rudaux

- https://github.com/instructure/canvas-lms

